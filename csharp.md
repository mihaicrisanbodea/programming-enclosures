## C#

- [HttpContext.Current](#httpcontextcurrent)
- [MemoryStream](#memorystream)
- [async/await](#asyncawait)
- [Razor](#Razor)


### HttpContext.Current

**Accessing** `HttpContext.Current` in a multithreaded scenario is a pitfall.<br/>
This will result in a `System.NullReferenceException` because this data is available only on the main worker's local storage.<br/>
Using anything from the current context must be done on the main thread.
___

**Faking** `HttpContext.Current` in a non-web application can be troublesome.<br/>
Implementing `HttpWorkerRequest` can be a quick solution in controlling the output of `HttpContext.Current.Request.UserHostAddress` call output for example.<br/>
For the above, the only remaining steps are to return dummy strings for `GetRawUrl`, `GetHttpVerbName` and `GetHttpVersion` methods, before returning the desired IP address from the `GetLocalAddress` method.
___


### MemoryStream

**Reading** from a `MemoryStream` after doing other operations, like writing to it, can cause unexpected behaviour.<br/>
The reason is the current Position within the stream has changed.<br/>
By setting the position to the beginning of the stream using `memStream.Seek(0, SeekOrigin.Begin)`, reading from it works as expected.
___


### async/await

**Blocking an asynchronous operation** in a web application causes a deadlock.<br/>
Calling `.Result` for an async method is one of the pitfalls.<br/> 
This happens because the thread running the async method is waiting for the context to be available, while the context thread is waiting for the async method to complete.<br/>
In order to avoid this situation, the best way is to propagate async/await all the way to the top, making all the methods including the controller action async in case of a web application.<br/>
Because in some cases this is not possible, the async method can be ran in a synchronous fashion by calling `.ConfigureAwait(false)` on top of it.
___

**Asynchronous methods with `void` return type** are bad news.<br/>
Such methods cannot be awaited, because they don't return a task.
That's why, asynchronous methods should only have `Task` or `Task<T>` as return types, and `void` should be used only for event handlers.
___

**Async/Await in Parallel.ForEach** brings us to the `void` return type problem once more. This is because the lambda used to call the async method is going to be converted to `async void`.
___

**Awaiting inside a lock** is not allowed because it would produce a deadlock.<br/>
A better approach to allow only one thread at a time to execute certain asynchronous operations can be done by keeping other threads busy. In terms of implementation, this can be achieved very easy.
```csharp
 int numberOfThreadsToAllowConcurentAccess = 1;
 SemaphoreSlim _semaphoreSlim = new SemaphoreSlim(numberOfThreadsToAllowConcurentAccess);
 
 await _semaphoreSlim.WaitAsync();
 
 try
 {
   await GetValuesAsync();
 }
 finally
 {
   _semaphoreSlim.Release();
 }
```
___


### Razor

**{** vs **(** syntax. In Razor syntax, between parentheses only explicit expressions can be defined.<br/>
On the other hand, between curly braces, an entire code block can be defined. In this case, the code needs to have result assignation, and the lines of code need to end in semicolon.<br/>
parentheses:
```csharp
@(Model.CreatedDate == null ? string.Empty : string.Format(myDateFormat, Model.CreatedDate.Value))
```
curly braces:
```csharp
@{
  var createdDate = Model.CreatedDate;
}
```
___

**HTML inside Razor**
There are two ways of adding HTML content inside Razor syntax.<br/>
single (entire) line:
```csharp
@foreach(var item in Model)
{
  @:Hello world
}
```
multiple lines:
```csharp
@foreach(var item in Model)
{
  <text>
  Hello 
  world
  </text>
}
```
___
