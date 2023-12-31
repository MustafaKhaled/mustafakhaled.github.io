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

Upon creating a _launch_ or async block, it returns a _Job_ instance. This instance represents the coroutine and manages its lifecycle.

Any _Job_ has states that reflect its lifecycle as follows:

*   _isActive_: any Job by default in the active state, _isActive = true_
*   _isCompleted_: After the coroutine completes its process, _isCompleted = true_
*   _isCancelled_: If the coroutine is cancelled, _isCanceled = true_

```
The job is Active: true  
Job is cancelled …  
Job is cancelled: true  
Job is completed: true
```

Note: we added the _job.isCompleted outside coroutineScope, so we are sure the coroutine completed its work._

**2.2. Coroutine Context**

It’s a set of elements that describe the coroutine and how it works, it is **essential** to create a coroutine/scope it contains:

*   **_Job_**: to manage the lifecycle of a coroutine
*   **_CoroutineName_**: by default CoroutineName value is “_coroutine_”, but you can override it
*   **_Dispatcher_**: it determines which thread would run the coroutine
*   **_CoroutineExceptionHandler_**: handle uncaught exceptions in the coroutine

**2.3. Coroutine Scope**

To create any coroutine with _launch_ or _async_ you need a _CoroutineScope._ Check the following definition:

CoroutineScope definition

To Create a _CoroutineScope_, we should pass a _CoroutineContext_ as a parameter. If you didn’t pass _Job to CoroutinesContext_, by default, it has _Job()_ instance for you.

**3\. Cancellation**

CoroutineScope can create multi coroutines which make a parents-children hierarchy. Furthermore, it keeps track of all coroutines created and can cancel them.

Parent-child hierarachy

When a child coroutine is cancelled, it **doesn’t affect** the other coroutines. Cancelled here means to call _cancel()_ method for a job, which would throw a _CancellationException._

```
Job1 is Active false  
Job2 is Active true  
Job2 is Active true
```

If the scope job is canceled, it will automatically cancel all the children’s coroutines, and it makes sense.

**4\. Exception**

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

Using **_SupervisorJob(),_** we can use this type of job instead of a normal **_Job()._** This would make work as we planned.

```
Job1 is Active false  
Job2 is Active true  
Job3 is Active true  
Parent scope job is Active true
```

So, if you need to cancel all coroutines hierarchy when a single coroutine failed, as they are dependent on each other you should use a normal **_Job()_** instance.

If there is no dependency on the children’s Jobs, use **_SupervisorJob()_** to survive other coroutines.

**5\. Conclusion**

In this article, we covered the basics of Job lifecycle, CoroutineContext, and CoroutineScopes. Furthermore, we covered coroutines cancellation and exception. We also covered the difference between **_Job()_** and **_SupervisorJob()_**_._

To know more about coroutines cancellation, check the [official documentation](https://kotlinlang.org/docs/cancellation-and-timeouts.html).
