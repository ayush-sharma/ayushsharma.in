---
layout: post
title:  "Exporting Bitucket repositories and Pipelines with Python"
number: 81
date:   2019-06-13 12:00
categories: automation
---
I've been spending much time with Pipelines over the last few weeks, first [deploying a serverless project]({% post_url 2019-05-01-automating-serverless-framework-deployments-using-bitbucket-pipelines %}) and then another [Lambda function using Pipes]({% post_url 2019-05-01-automating-aws-lambda-deployments-using-bitbucket-pipelines-bitbucket-pipes %}). I even managed to migrate some projects in my company using those approaches successfully. However, when using pipelines for CI/CD at a large scale, one essential feature that you miss is a Pipelines wallboard.

There is an [on-going feature request to get this resolved](https://bitbucket.org/site/master/issues/12765/pipeline-wallboards), but in the meantime, I've made a Python script for it. Our goal is to authenticate with Bitbucket, use the API to download the information, and export it all to CSV.

## How to authenticate with Bitbucket in Python

We're going to need 2 things for our script to authenticate with Bitbucket: our account ID and our OAuth consumer credentials.

Getting the account ID is straight-forward: [just follow the steps mentioned here](https://stackoverflow.com/questions/53983942/how-to-find-bitbucket-account-uuid). If that doesn't work, go to your Bitbucket `View profile` page, and your account ID should be in the URL like this: `https://bitbucket.org/{account-uuid}`. Copy-paste the UUID for later use, including the curly braces.

Now let's get our OAuth credentials. For this, we'll create our very own OAuth consumer. The detailed steps for this are in [the Atlassian docs](https://confluence.atlassian.com/bitbucket/oauth-on-bitbucket-cloud-238027431.html). Remember the following when creating our consumer:

1. The callback URL should be exactly `https://localhost/`.
2. The permissions should be `Read` permissions for `Repositories` and `Pipelines` only, according to the principle of least privilege.

Your OAuth consumer should look like this:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitucket-repos-pipelines-oauth-authenticate-8.jpg" alt="Exporting Bitucket repositories and Pipelines with Python.">

Once you hit `Save` at the bottom, the next page should have the new `bb-reports` consumer. Save the client ID and client secret for our script.

## Running the script

You can download the full script from [GitHub](https://github.com/ayush-sharma/infra_helpers/blob/master/bitbucket/report_repos_pipelines.py).

The script requires two dependencies to work: `requests` and `oauthlib`. You can install both with `pip`:

```bash
pip install requests requests-oauthlib
```

After installing the dependencies, configure the credentials in the lines below:

```python
# Initialize Bitbucket secrets
c = ClientSecrets()
c.client_id = ''
c.client_secret = ''
account_id = ''
```

Paste your Bitbucket OAuth credentials and account ID and run the script, like this:

```bash
python report_repos_pipelines.py
```

The script outputs a URL which you need to open in your browser.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitucket-repos-pipelines-oauth-authenticate-4.png" alt="Exporting Bitucket repositories and Pipelines with Python.">

The URL will take you to a page where you need to grant the `bb-reports` consumer access to your Bitbucket account.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitucket-repos-pipelines-oauth-authenticate-7.jpg" alt="Exporting Bitucket repositories and Pipelines with Python.">

Once you grant access, the page redirects to the `https://localhost` callack which will display an error. Copy the full URL and paste it back at the terminal where your script is running.

After pasting the return URL, the script will continue, using Bitbucket's API to get the information. It will generate two files: `repos.csv` and `pipelines.csv`.

`repos.csv` contains some necessary information about all the repositories in your account.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitucket-repos-pipelines-oauth-authenticate-2.png" alt="Exporting Bitucket repositories and Pipelines with Python.">

`pipelines.csv` contains information about all the pipelines executed in that account.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitucket-repos-pipelines-oauth-authenticate-3.png" alt="Exporting Bitucket repositories and Pipelines with Python.">

## How does the code work?

The code to fetch the current repositories works like this: once authenticated, it fetches the first ten repositories in the account and saves their relevant information in a list of dictionaries. The script recurses over all pages until the last one.

```python
def get_all_repos(BBClient, next_page_url):

    data = []

    response = BBClient.get(next_page_url)

    try:

        response_dict = json.loads(response.content)

    except Exception, e:

        print(str(e))

    if 'values' in response_dict:

        for repo in response_dict['values']:

            data.append({

                'uuid': repo['uuid'],
                'name': repo['name'],
                'pr_url': repo['links']['pullrequests']['href'],
                'lang': repo['language'],
                'updated_on': repo['updated_on'],
                'size': repo['size'],
                'slug': repo['slug']
            })

    if 'next' in response_dict:

        data += get_all_repos(BBClient=BBClient,
                              next_page_url=response_dict['next'])

return data
```

Fetching Pipelines works the same way, except that we need to call the Pipelines API for every repository we're tracking. Just like fetching repositories, we'll save the relevant information in a list of dictionaries, and return the result once we've recursed over all the pages.

```python
def get_all_pipelines(BBClient, next_page_url):

    data = []

    response = BBClient.get(next_page_url)

    try:

        response_dict = json.loads(response.content)

    except Exception, e:

        print(str(e))

    if 'values' in response_dict:

        for repo in response_dict['values']:

            data.append({

                'uuid': repo['uuid'],
                'repo': repo['repository']['name'],
                'state': repo['state']['result']['name'],
                'build_number': repo['build_number'],
                'creator': repo['creator']['display_name'] + '/' + repo['creator']['username'],
                'target_type': repo['target']['ref_type'] if 'ref_type' in repo['target'] else '',
                'target_name': repo['target']['ref_name'] if 'ref_name' in repo['target'] else '',
                'trigger': str(repo['trigger']['name']),
                'duration': repo['duration_in_seconds'],
                'created_on': repo['created_on'],
                'completed_on': repo['completed_on']
            })

    if 'next' in response_dict:

        data += get_all_pipelines(BBClient=BBClient,
                                  next_page_url=response_dict['next'])

return data
```

## Conclusion

The script above will export all the relevant information to CSV. You should explore the Bitbucket API docs to add any additional options you may need in your final export. You can find the [API docs here](https://developer.atlassian.com/bitbucket/api/2/reference/).

One problem with the above approach is that it is difficult to automate, since pasting the OAuth callback requires manual intervention. You can use the older, depracated API with a user-specific API token, but the available endpoints in it are limited.

Another possile approach is to export all the above information to ElasticSearch and have Kibana do analytics and metrics reporting, since the responses are all JSON.

I hope this works for you until the wallboard feature request is resolved.

The full code used above is [available on GitHub](https://github.com/ayush-sharma/infra_helpers/blob/master/bitbucket/report_repos_pipelines.py).

## Resources
- [Doing the Bitbucket OAuth dance with Python - Atlassian Developer Blog](https://blog.developer.atlassian.com/bitbucket-oauth-with-python/).
- [report_repos_pipelines.py](https://github.com/ayush-sharma/infra_helpers/blob/master/bitbucket/report_repos_pipelines.py).
- [Bitbucket REST API docs](https://developer.atlassian.com/bitbucket/api/2/reference/).