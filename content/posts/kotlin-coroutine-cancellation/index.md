---
title: "Cancellation Of Kotlin Coroutines"
date: 2023-11-30T16:33:50+01:00
draft: false

slug: "kotlin-coroutine-cancellation" 
tags: []
categories: []
---

Cancellation of Coroutines in Kotlin
====================================

1.  **Overview**

In this tutorial, we will understand the Cancellation of Coroutines that avoids doing unneeded processes that affect our resources negatively.

If we are using Coroutines, we should completely understand what’s cancellation in coroutines is and how it works. This would enable us to manage our coroutines and their lifecycles.

**2\. Basic Concepts**

Before going deeply into the coroutines cancellation, we should go through important concepts in Coroutines.

**2.1. Job**

Upon creating a launch or async block, it returns a  Job  instance. This instance represents the coroutine and manages its lifecycle.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*2JRDcmyhLikrNsVDQVDNGg.png)

Any  Job  has states that reflect its lifecycle as follows:

*    isActive : any Job by default in the active state,  isActive = true 
*    isCompleted : After the coroutine completes its process,  isCompleted = true 
*    isCancelled : If the coroutine is cancelled,  isCanceled = true 

<iframe src="https://gist.github.com/MustafaKhaled/db5acfe745bb6b9d6cd1fa18625dd482.js"></iframe>


```
The job is Active: true  
Job is cancelled …  
Job is cancelled: true  
Job is completed: true
```

Note: we added the  job.isCompleted outside coroutineScope, so we are sure the coroutine completed its work. 

**2.2. Coroutine Context**

It’s a set of elements that describe the coroutine and how it works, it is **essential** to create a coroutine/scope it contains:

*   ** Job **: to manage the lifecycle of a coroutine
*   ** CoroutineName **: by default CoroutineName value is “ coroutine ”, but you can override it
*   ** Dispatcher **: it determines which thread would run the coroutine
*   ** CoroutineExceptionHandler **: handle uncaught exceptions in the coroutine

**2.3. Coroutine Scope**

To create any coroutine with  launch  or  async  you need a  CoroutineScope.  Check the following definition:

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uEkp0GYWygxT69EtdRPdyA.png)
*CoroutineScope definition*


To Create a  CoroutineScope , we should pass a  CoroutineContext  as a parameter. If you didn’t pass  Job to CoroutinesContext , by default, it has  Job()  instance for you.

**3. Cancellation**

CoroutineScope can create multi coroutines which make a parents-children hierarchy. Furthermore, it keeps track of all coroutines created and can cancel them.

Parent-child hierarachy

When a child coroutine is cancelled, it **doesn’t affect** the other coroutines. Cancelled here means to call  cancel()  method for a job, which would throw a  CancellationException. 

```
Job1 is Active false  
Job2 is Active true  
Job2 is Active true
```

If the scope job is canceled, it will automatically cancel all the children’s coroutines, and it makes sense.

**4. Exception**

What if a child’s job throws an exception while doing its job?!

Unfortunately, when a child’s job throws an exception, it would cancel the job itself, and its parent’s job, which would cancel all other children’s jobs 😨

When a child-job throw an exception(not cancellation exception), it cancels it job and the parent-children jobs.

```
Job1 is Active false  
Job2 is Active false  
Job3 is Active false  
Parent scope job is Active false
```

We can make the child’s job throws an exception and fails independently. It wouldn’t affect other children’s jobs or their parents.

Using ** SupervisorJob(), ** we can use this type of job instead of a normal ** Job(). ** This would make work as we planned.

```
Job1 is Active false  
Job2 is Active true  
Job3 is Active true  
Parent scope job is Active true
```

So, if you need to cancel all coroutines hierarchy when a single coroutine failed, as they are dependent on each other you should use a normal ** Job() ** instance.

If there is no dependency on the children’s Jobs, use ** SupervisorJob() ** to survive other coroutines.

**5. Conclusion**

In this article, we covered the basics of Job lifecycle, CoroutineContext, and CoroutineScopes. Furthermore, we covered coroutines cancellation and exception. We also covered the difference between ** Job() ** and ** SupervisorJob() ** . 

To know more about coroutines cancellation, check the [official documentation](https://kotlinlang.org/docs/cancellation-and-timeouts.html).
