---
layout: post
title: "A first look at Ruby threads"
date: 2023-03-29
categories:
  - ruby
description: A first look at threads in Ruby. What are they? When and how to use them? Where to go from here?
---

Sometimes ago a came accross a great blog post from Thoughbot about Ruby threads. The full blog post can be found [here](https://thoughtbot.com/blog/untangling-ruby-threads#%C2%A0an-example-performing-http-requests-concurrently). This note is a shorten version of the original blog post with a clearer code examples.

Before looking into threads, we need to have some high level understanding of MRI and Global Interpreter Lock.

Ruby comes with many different “flavors” (interpreters) and MRI is one of the options you can have. 

Another famous Ruby flavor is JRuby which provides a completely different threading behavior than Ruby with MRI.

When looking into threads in Ruby, it is important to know which underlying interpreter is present.

## MRI (Matz's Ruby Integreter) Ruby
MRI comes with a Global Interpreter Lock (a.k.a GIL). The GIL prevents multiple Ruby threads from executing at the same time. In short, **MRI does not truly provide a multi-threaded environment** for your Ruby code to run. This means no matter how many CPU cores you have, or how many threads to spawn, the threads will never be executed concurrently.

### How does “multi-threaded” work in MRI Ruby?

A typical Ruby program spends a great amount of time waiting for I/O, responses, database queries, etc. 

MRI takes advantage of the waiting time to switch/start/resume between different existing Ruby threads. When a waiting happens, GIL will allow another thread to run.

### When to use threads?

A good scenario to think about using threads is when you have different codes running in a sequence, but they are independent from each other. 

**E.g.**  You have different database queries to look for some data in different places, but the results are not dependent on each other. You can run Query 1, then Query 2, then Query 3, but also can run Query 2, Query 1, and Query 3, etc. 

If we implement our code to run them in sequence, then the runtime will be the sum of the query runtimes. However, if we implement the same logic with each thread handling a query, then the runtime might be reduced significantly to the longest run time query.

![sequnce_vs_threaded_runtime](/assets/img/sequence_and_threaded_runtime.png)

### Some code sample

It is resonably simple to demo threads in action with the simple code below.

```ruby
require 'benchmark'

def query_one
  sleep 1
end

def query_two
  sleep 3
end

def query_three
  sleep 5
end

# methods called in sequence
def query
  query_one
  query_two
  query_three
end

# methods called with threads for each method
def query_with_threads
  threads = []
  threads << Thread.new { query_one }
  threads << Thread.new { query_two }
  threads << Thread.new { query_three }

  # make the main thread wait for these subthreads to complete
  threads.each { |t| t.join }
end

# Calling bencmark for different query methods
Benchmark.bm(1) do |b|
  b.report("query:") { query }  
  b.report("query_with_threads:") { query_with_threads }  
end
```

The result of the bench mark is displayed below. As we can see in this senario, even though both `query` and `query_with_threads` accomplish the same goal, the threaded version provide a better runtime.

```
                    user     system      total        real
query:              0.000115   0.000029   0.000144 (  9.004647)
query_with_threads: 0.001778   0.000000   0.001778 (  5.019764)
```

## Summary
Topic about threads is widely discussed in the tech communities, but often than not threads are different depending on the context. We can see that even in the same language such as Ruby, MRI and JRuby provide completely different appoarch to threading. We should be very specific about the context in which we want to discuss threads.

Futhre more, this note is a summary and an introduction to thread and basic usage in Ruby with MRI. However, to have deeper understanding on how to use thread, we need to to look at:
1. The properties of a Ruby thread
2. How to handle errors raised when one or multiples are running
3. Some other best practices for threads A








