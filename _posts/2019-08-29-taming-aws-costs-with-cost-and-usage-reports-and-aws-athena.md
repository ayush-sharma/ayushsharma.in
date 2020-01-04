---
layout: post
title:  "Taming AWS costs with Cost and Usage Reports + AWS Athena"
number: 83
date:   2019-08-29 00:00
categories: cloud databases
---
The [AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) auto-generates hourly or daily billing reports and pushes the data to an S3 bucket.

The Cost and Usage Report, or CUR, is the successor to the old Detailed Billing Report. Both CUR and DBR generate estimated costs based on AWS service, usage type, resource tags, etc., and push it to S3 for analysis. These can then be used to aggregate and analyze costs for better visibility and control of AWS spending.

The difference is that while DBR generates CSV files, CUR generates more database-friendly GZ or Parquet files. AWS Athena, AWS Redshift, and AWS QuickSight can then ingest these directly for analysis.

## Introduction to AWS Athena

Athena is a serverless query service. Data is stored as static files in S3 and read in real-time for analysis using Presto, which is an ANSI-standard SQL engine.

Athena retrieves the data using a feature called schema-on-read, meaning it superimposes the schema on the underlying data when you execute the query. No server software or daemon is running, hence the term "serverless".

The advantage of Athena is that it frees us from the usual database maintenance and performance-tuning activities. We can bank on S3's excellent reliability to keep data reliably and durably. The caveat is that the data format and partition structure becomes critical for query performance.

Overall, this architecture makes Athena very performant, reliable, and cost-effective.

## Setting up Cost and Usage Reports in AWS

Setting up CUR with Athena is very simple.

AWS provides a Cloudformation stack with everything ready to go. Just follow the instructions in the [documentation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/setting-up-athena.html) to enable CUR, configure an S3 bucket, and set up the Cloudformation stack.

Once the stack is ready, we can check the CUR status by going to our database and running the following query:

``` sql
SELECT status FROM cost_and_usage_data_status;
```

The above query will return either `READY` or `UPDATING` , the latter indicating that Athena may return incomplete results.

If the status is `READY` , we can verify it by counting the total number of items in our Athena database so far.

``` sql
SELECT

    COUNT(identity_line_item_id) AS Count
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4));
```

> The `MONTH(CURRENT_DATE)` statement will fetch the current month as an integer, and `CAST()` will convert it to a string.

The above query will return the number of rows currently in the billing table.

## Athena CUR columns for cost wrangling

Let's explore our dataset and identify the columns we will need for analyzing our costs. The [documentation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/enhanced-identity-columns.html) has relevant details of all the columns that CUR will populate. Here are a few examples:

**Billing columns**

* `line_item_blended_cost` : The [blended cost](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/con-bill-blended-rates.html#Blended_CB) of this line item.
* `line_item_line_item_type` : The type of charge covered by this line item. Possible values are: *Credit*, *DiscountedUsage*, *Fee*, *Refund*, *RIFee*, *Tax*, and *Usage*.
* `pricing_public_on_demand_cost` : The cost for the line item based on public on-demand instance rates.

**Resource columns**

* `line_item_resource_id` : The resource ID of this line item, if enabled in CUR. For example, an Amazon S3 storage bucket, an Amazon EC2 compute instance, or an Amazon RDS database can each have a resource ID.
* `line_item_line_item_description` : The description of the line item type.
* `resource_tags_user_name` : Contains the value of the `Name` resource tag.

**Configuration columns**

* `line_item_availability_zone` : The Availability Zone that hosts this line item, such as `us-east-1a` or `us-east-1b` .
* `product_instance_type` : Describes the instance type, size, and family, which define the CPU, networking, and storage capacity of your instance.
* `product_instance_family` : Describes your Amazon EC2 instance family.

**Usage columns**

* `line_item_usage_account_id` : The account ID that used this line item. For organizations, this can be either the master account or a member account. 
* `line_item_operation` : The specific AWS operation covered by this line item.
* `line_item_usage_type` : The usage details of this line item.
* `line_item_usage_start_date` , `line_item_usage_end_date` : The start and end dates for the corresponding line item in UTC. The format is YYYY-MM-DDTHH:mm:ssZ. The start date is inclusive, and the end date is exclusive.
* `month` , `year` : The month and year of this line item.
* `line_item_product_code` : The product code of this line item. For example, `AmazonEC2` is the product code for Amazon Elastic Compute Cloud.

## Breaking down the cost into components

First, let's get the total cost for the current month.

The `line_item_blended_cost` field will contain the charge for a line item. The `discount_total_discount` will include any discounts we will need to adjust to get our net cost.

``` sql
SELECT

    SUM(line_item_blended_cost + discount_total_discount)
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4));
```

Now let' find out the total cost incurred by individual accounts. This query is helpful if we have multiple member accounts under a master account.

``` sql
SELECT

    line_item_usage_account_id AS AccountID,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    
GROUP BY line_item_usage_account_id

ORDER BY Cost DESC;
```

Let's see the cost incurred by individual AWS services, from highest to lowest.

``` sql
SELECT

    line_item_product_code AS ProductCode,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    
GROUP BY line_item_product_code 

ORDER BY Cost DESC;
```

Putting it all together, we can retrieve cost incurred by individual accounts and the services as follows.

``` sql
SELECT

    line_item_usage_account_id AS AccountID,
    line_item_product_code AS ProductCode,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    
GROUP BY line_item_usage_account_id, line_item_product_code

ORDER BY Cost DESC;
```

Another great feature of CUR is that it also populates any resource tags we configure. We can use this to see costs grouped by individual cost centres.

Let's say we have a `Project` resource tag with values of different projects in our organization. We can get costs grouped by projects to identify where our spending is highest.

``` sql
SELECT

    resource_tags_user_project AS Project,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    
GROUP BY resource_tags_user_project

ORDER BY Cost DESC;
```

Combining the above query with resource IDs, and we can see which resources within which projects have the highest cost.

``` sql
SELECT

    line_item_resource_id AS ResourceID,
    resource_tags_user_project AS Project,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    
GROUP BY line_item_resource_id, resource_tags_user_project

ORDER BY Cost DESC;
```

## Identifying the most expensive resources within services

Let's say we want to find out the most expensive Lambda functions, EC2 servers, RDS instances, etc.

We can do this pretty quickly by using the `line_item_product_code` column and aggregating based on resource IDs.

For example, to find out the most expensive Lambda functions:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    AND line_item_product_code = 'AWSLambda'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

The query above is pretty simple: filter for all resources of the `AWSLambda` service, aggregate by resource IDs, and sort by the total cost of those resources. By combining this with resource tags, we can automate daily or weekly email reports to keep an eye on our project expenses.

Let's keep going :)

Most expensive EC2 resources:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    AND line_item_product_code = 'AmazonEC2'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

Most expensive RDS resources:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    AND line_item_product_code = 'AmazonRDS'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

Most expensive CloudWatch log groups:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE) AS varchar(4))
    AND line_item_product_code = 'AmazonCloudWatch'
    AND line_item_resource_id LIKE '%log-group%'
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

Most expensive DynamoDB instances:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE)  AS varchar(4))
    AND line_item_product_code = 'AmazonDynamoDB'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

Most expensive S3 buckets:

``` sql
SELECT

    line_item_resource_id AS Resource,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE)  AS varchar(4))
    AND line_item_product_code = 'AmazonS3'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id

ORDER BY Cost DESC

LIMIT 25;
```

Athena can help us identify the costliest operations within a service. For example, how do we identify runaway Glacier transition costs in S3?

Most expensive S3 bucket operations:

``` sql
SELECT

    line_item_resource_id AS Resource,
    line_item_operation AS Operation,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE)  AS varchar(4))
    AND line_item_product_code = 'AmazonS3'
    AND line_item_resource_id != ''
    
GROUP BY line_item_resource_id, line_item_operation

ORDER BY Cost DESC

LIMIT 25;
```

Another important use-case is to examine the cost of resources by their names.

Let's say I have several resources with the name of my project, `myfrontendproject` . These could be `ec2-myfrontendproject` , or `rds-myfrontendproject-master` , or `s3-assets-myfrontendproject` , etc. We can use the resource tags column to group costs based on names as well.

``` sql
SELECT

    resource_tags_user_name AS ResourceName,
    SUM(line_item_blended_cost + discount_total_discount) AS Cost
    
FROM my_cur_db.my_cur_table

WHERE
    MONTH = CAST(MONTH(CURRENT_DATE) AS varchar(4))
    AND YEAR = CAST(YEAR(CURRENT_DATE)  AS varchar(4))
    AND lower(resource_tags_user_name) LIKE '%myfrontendproject%'
    
GROUP BY resource_tags_user_name

ORDER BY Cost DESC

LIMIT 25;
```

## Things to remember when using Athena

The serverless nature of Athena provides tremendous cost and reliability benefits, but there a few considerations.

First, there is a **default concurrency limit of 20** which caps how many queries can be executed in parallel. Refer to the [service limits](https://docs.aws.amazon.com/athena/latest/ug/service-limits.html) page for more details.

Second, when using `SELECT` queries, we should limit the columns to what we need. **Athena charges based on the amount of data you process**, so limiting columns is an excellent performance and cost optimisation.

Third, the **data storage format will have a significant impact on query processing time**. Parquet formats will be more performant than CSV since they are columnar and can utilize Snappy compression.

Fourth, when joining multiple tables, keep the **larger table on the left of the join and the smaller one on the right**. Athena distributes the right table to worker nodes and streams the left one for the join.

Fifth, Athena does not support user-defined functions, stored procedures, indexes, prepared or `EXPLAIN` statements. A full list of limitations is [here](https://docs.aws.amazon.com/athena/latest/ug/other-notable-limitations.html).

## Conclusion

The Athena and CUR combination can help alleviate a lot of my-cloud-bill-is-a-huge-black-box problems. The Cost Explorer and Budget Reports are fine, but there are some problems only a heinous quadruple-join can solve.

CUR is also available for Redshift and QuickSight. If Athena's concurrency limits are causing issues or if you need a full-blown RDBMS for cost analysis, then Redshift is the way to do. For visualizations, QuickSight can use the columns directly but not the query results, so something like Redash or Tableau might be better for more complex dashboards.

## Resources

* [Getting Started with Amazon Athena - AWS Billing and Cost Management](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/setting-up-athena.html)
* [Identity Details - AWS Billing and Cost Management](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/enhanced-identity-columns.html)
* [Amortizing Reserved Instances - AWS Billing and Cost Management](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/amortizing-ri.html)
* [What Is AWS Billing and Cost Management? - AWS Billing and Cost Management](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html)
* [Understanding Consolidated Bills - AWS Billing and Cost Management](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/con-bill-blended-rates.html#Blended_CB)
* [How to Improve AWS Athena Performance: The Complete Guide](https://www.upsolver.com/blog/aws-athena-performance-best-practices-performance-tuning-tips).

