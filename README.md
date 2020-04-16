"# Summary-on-Async" 

## Summary of an Article on Async Rust:
I think it’s safe to say that one of Rust’s most anticipated language features has finally landed. I’m assuming some base knowledge of Rust’s syntax and ecosystem. But before diving into the coding part, let’s cover some basic concepts of asynchronous programming with the definition of Async.
 
## Async Definition:
In Rust, Async means to not wait for another task to be finish because it can run code concurrently or multiple task at the same time on a single thread. Multithreading is like this but has distinct concept. Mostly Multithreading is used when you’ve got computationally intensive task(so-called CPU-bound tasks). IO bound are the tasks which spends a lot of time waiting, such as for a  response from the server. 

Asynchronous Programming is the which let us run multiple of the IO bound computation at the same time on the single thread without wasting any time because when they are waiting for a response they’re just idle,so by the help of async we can let the computer keep working.
 
## Why Async Rust:
A notable point about Rust is fearlessly concurrency. That is the notion that you should be empowerd to do concurrent things without giving up safety. Also Rust being a low-level Language, it’s about fearlessly concurrency without picking a specific implementation strategy. This means we must abstract over the strategy, to allow choice later, if we want to share code between users of different strategy

## Futures:
Futures abstract over computation. They describe “what”, independent of “where” and the “when”. For that they aim to break cod e into small, composable actions that can be executed by a part of our system.
Let’s took an example to make it clearer:
      
    use async_std::task;
    // ^ we need this for task spawning

    async fn negate_async(n: i32) -> i32 {
    println!("Negating {}", n);
    task::sleep(std::time::Duration::from_secs(5)).await;
    println!("Finished sleeping for {}!", n);
    n * -1
     }

    async fn f() -> i32 {
    let neg = negate_async(1);
    // ... nothing happens yet
    let neg_task = task::spawn(negate_async(2));
    // ^ this task /is/ started
    task::sleep(std::time::Duration::from_secs(1)).await;
    // we sleep for effect.

    neg.await + neg_task.await
    // ^ this starts the first task `neg`
    // and waits for both tasks to finish
    }  

So in the above little code snippet here’s what’s going on.  

•	The first line imports async_std::task.  
•	The async function negate_async  takes a input a signed integer, sleeps for 5 seconds and returns the negated version of that integer.  
•	The async function f is more interesting:  
  o	The first line (let neg ...) creates a Future of the negate_async function and assign it to the neg variable. Imortantly, it doesnot start executing yet.  
  o	The next line of code (let neg_task ...) uses the task::spawn function to start executing the Future returned by negate_async. Like with neg, the Future returned by negate_async is assigned to the neg_task variable.  
  o	Next: we sleep for a second. This is so that it will be obvious from the output when a task starts running.  
  o	Finally, we await both futures, add them together, and return them. By awaiting neg, we start executing the Future and run it to completion. Since neg_task has already been started, we just wait for it to finish.  

So what’s the result of this , then?

    Negating 2
    # <- there's a 1 second pause here
    Negating 1
    Finished sleeping for 2!
    Finished sleeping for 1!

As we can see, the second future, neg_task, started executing as soon as it was called—thanks to task::spawn —while neg did not start executing until it was awaited.

## How to use Async-await in Rust:

You may be familiar with the async-await from JavaScript or C#. Rust’s version of the feature is similar, nut with few key differences.

To use async-await, you start writing by async fn instead of fn.
async fn first_function() -> u32 { .. }

Unlike a regular Function, calling an async fn  doesn’t have any immediate  immediate effect. Instead, it returns a Future. This is a suspended computation that is waiting to be executed. To actually execute the future, use the . .await operator:

    async fn another_function() {
    // Create the future:
    let future = first_function();
    
    // Await the future, which will execute it (and suspend
    // this function if we encounter a need to wait for I/O): 
    let result: u32 = future.await;
    ...
    }

This example shows the first difference between Rust and other languages: we write future.await instead of await future. This syntax integrates better with Rust's ? operator for propagating errors (which, after all, are very common in I/O). You can simply write         future.await? to await the result of a future and propagate errors. It also has the advantage of making method chaining painless.

## Conclusion:
We believe that having async-await on stable Rust is going to be a key enabler for a lot of new and exciting developments in Rust. If you've tried Async I/O in Rust in the past and had problems -- particularly if you tried the combinator-based futures of the past -- you'll find async-await integrates much better with Rust's borrowing system
Hope it helps in understanding Asynchronous Programming in Rust and let me know if there is any feedback.       
                       Thank You.

## Resources: 
https://thomashartmann.dev/blog/async-rust/  
https://book.async.rs/concepts/futures.html
