---
layout: post
title:  "Retry Strategies for Transient Failures"
number: 43
date:   2017-08-16 0:00
categories: development
---
Every now and then we have to rely on remote resources when working with our application, and these remote resources are prone to failures. I wanted to talk about a certain kind of these failures, called transient or temporary failures, and how to handle them.

## Transient Failures
Transient failures are those that occur temporarily and for a short period of time. It is possible that a remote resource may only be temporarily unavailable due to network congestion or high system load, and it is entirely possible that this situation may correct itself quickly. In this situation, it becomes important for us to wait for some time and retry fetching the resource before calling it quits and considering it a permanent failure. The normal process might be this: we fetch the resource, and if we find it we proceed with processing it. If not, we wait for some time and fetch the resource again, and continue doing so until we have the resource. We obviously can’t wait for the resource forever, so after a fixed amount of retries we consider it a permanent failure and handle it in our code appropriately.

An example of a transient failure might be throttling. Many third-party APIs, including the Amazon Web Services APIs, have a limit to the number of requests that can be made per second to their API for performing a particular task. For example, Route 53 has a limit of 5 requests per second, although they do allow batching multiple operations in one request. When this limit is hit, you will most likely receive some kind of `Rate Limit Exceeded` error, indicating that you’ve exceeded the threshold of the allowed number of requests. What do you do at this point? Do you consider it a failure and handle that in your application? You could in fact wait for a brief interval of time and retry the operation. There are many strategies where you “back off” for some time and retry the operation after a delay.

## Backoff Strategies
Let us consider a hypothetical `do_something()` function, in which your code performs some operation which is prone to transient failures. We will consider the strategies using examples, and the source code can be found [here](https://github.com/ayush-sharma/infra_helpers/blob/master/general/backoff_strategies.py).

### No Backoff
This is the default scenario. If things go bad, you can retry immediately and don’t wait at all. Remember to cap your attempts using maximum allowed retries otherwise this might go on forever. The code might look something like this:

```python
def no_backoff(max_retry: int):
    """ No backoff. If things fail, retry immediately. """
    
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            print('> No Backoff > Retry number: %s' % counter)
        
        counter += 1
```

### Constant Backoff
Constant backoff adds a fixed delay after every attempt. In this case, we wait a fixed 1 second after every attempt.

```python
def constant_backoff(max_retry: int):
    """ Constant backoff. If things fail, retry after a fixed amount of delay. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = 1
            print('> Constant Backoff > Retry number: %s, sleeping for %s seconds.' % (counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Constant Backoff > Total delay: %s seconds.' % total_delay)
```

### Linear Backoff
In linear backoff, the delay increases along with every attempt, following a linear curve.

```python
def linear_backoff(max_retry: int):
    """ Linear backoff. If things fail, retry with linearly increasing delays. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = counter
            print('> Linear Backoff > Retry number: %s, sleeping for %s seconds.' % (counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Linear Backoff > Total delay: %s seconds.' % total_delay)
```

### Fibonacci Backoff
We can also have delays based on the sum of the Fibonacci series of the retry counter.

```python
def fibonacci_backoff(max_retry: int):
    """ Fibonacci backoff. If things fail, retry with delays increasing by fibonacci numbers. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = get_fib(counter)
            print(
                    '> Fibonacci Backoff > Retry number: %s, sleeping for %s seconds.' % (
                        counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Fibonacci Backoff > Total delay: %s seconds.' % total_delay)
```

### Quadratic Backoff
The delays can also follow a quadratic curve.

```python
def quadratic_backoff(max_retry: int):
    """ Quadratic backoff. If things fail, retry with polynomially increasing delays. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = counter ** 2
            print(
                    '> Quadratic Backoff > Retry number: %s, sleeping for %s seconds.' % (
                        counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Quadratic Backoff > Total delay: %s seconds.' % total_delay)
```

### Exponential Backoff
The delays can also follow an exponential curve, as in the example below.

```python
def exponential_backoff(max_retry: int):
    """ Exponential backoff. If things fail, retry with exponentially increasing delays. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = 2 ** counter
            print(
                    '> Exponential Backoff > Retry number: %s, sleeping for %s seconds.' % (
                        counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Exponential Backoff > Total delay: %s seconds.' % total_delay)
```

### Polynomial Backoff
The delays can also follow a polynomial function.

```python
def polynomial_backoff(max_retry: int):
    """ Polynomial backoff. If things fail, retry with polynomially increasing delays. """
    
    total_delay = 0
    counter = 0
    while counter < max_retry:
        
        response = do_something()
        if response:
            
            return True
        else:
            
            sleepy_time = counter ** 3
            print(
                    '> Polynomial Backoff > Retry number: %s, sleeping for %s seconds.' % (
                        counter, sleepy_time))
            total_delay += sleepy_time
            sleep(sleepy_time)
        
        counter += 1
    
    print('> Polynomial Backoff > Total delay: %s seconds.' % total_delay)
```

## Total Delays
The strategy you use will depend on the kind of delays you want. Here is a run of the above algorithms with the total delay they cause in seconds, for a variety of maximum retries.

```
Starting experiments with maximum 1 retries.
> Constant Backoff > Total delay: 1 seconds.
> Linear Backoff > Total delay: 0 seconds.
> Fibonacci Backoff > Total delay: 0 seconds.
> Quadratic Backoff > Total delay: 0 seconds.
> Exponential Backoff > Total delay: 1 seconds.
> Polynomial Backoff > Total delay: 0 seconds.

Starting experiments with maximum 3 retries.
> Constant Backoff > Total delay: 3 seconds.
> Linear Backoff > Total delay: 3 seconds.
> Fibonacci Backoff > Total delay: 2 seconds.
> Quadratic Backoff > Total delay: 5 seconds.
> Exponential Backoff > Total delay: 7 seconds.
> Polynomial Backoff > Total delay: 9 seconds.

Starting experiments with maximum 5 retries.
> Constant Backoff > Total delay: 5 seconds.
> Linear Backoff > Total delay: 10 seconds.
> Fibonacci Backoff > Total delay: 7 seconds.
> Quadratic Backoff > Total delay: 30 seconds.
> Exponential Backoff > Total delay: 31 seconds.
> Polynomial Backoff > Total delay: 100 seconds.

Starting experiments with maximum 10 retries.
> Constant Backoff > Total delay: 10 seconds.
> Linear Backoff > Total delay: 45 seconds.
> Fibonacci Backoff > Total delay: 88 seconds.
> Quadratic Backoff > Total delay: 285 seconds.
> Exponential Backoff > Total delay: 1023 seconds.
> Polynomial Backoff > Total delay: 2025 seconds.

Starting experiments with maximum 20 retries.
> Constant Backoff > Total delay: 20 seconds.
> Linear Backoff > Total delay: 190 seconds.
> Fibonacci Backoff > Total delay: 10945 seconds.
> Quadratic Backoff > Total delay: 2470 seconds.
> Exponential Backoff > Total delay: 1048575 seconds.
> Polynomial Backoff > Total delay: 36100 seconds.
```

## Capping/Truncating Delays
When using algorithms to add delay to your retry logic, remember to cap those delays. In our example, we’re limiting/capping our retry attempts to 10 retries, which might be sufficient. Remember that the total amount of delay we end up adding depends on the time taken by our application to actually run `do_something()` and reach it for the next iteration, and also on the algorithm we use. 10 retries adds 45 seconds in the case of linear backoff, but over 30 minutes using the polynomial backoff strategy. It is important to run scenarios as to the actual delay added before you pick an approach.
Also, instead of capping delays by number of retries, it might be better to cap delays by the maximum allowed delay, so that they level off after a while. You could check the value of the `sleepy_time` variable and ensure it is never set to a value greater than a threshold you specify.

## The Case for Jitter/Randomness
If you find that even after using the above approach there are cases where multiple clients are contending for the same resources and facing transient failures, considering adding some randomness to `sleepy_time` to spread out the times the calls are made. The AWS architecture link mentioned below has more information.

## Additional Resources
- [This code on GitHub](https://github.com/ayush-sharma/infra_helpers/blob/master/general/backoff_strategies.py).
- [Exponential backoff on AWS](https://www.awsarchitectureblog.com/2015/03/backoff.html).
