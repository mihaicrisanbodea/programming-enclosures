# Programming enclosures  

Useful things to be aware of.
			
## C#

### HttpContext.Current

**Accessing** `HttpContext.Current` in a multithreaded scenario is a pitfall.<br/>
This will result in a `System.NullReferenceException` because this data is available only on the main worker's local storage.
___
	
**Faking** `HttpContext.Current` in a non-web application can be troublesome.<br/>
Implementing `HttpWorkerRequest` can be a quick solution in controlling the output of `HttpContext.Current.Request.UserHostAddress` call output for example.<br/>
For the above, the only thing required further is to return dummy strings for `GetRawUrl`, `GetHttpVerbName` and `GetHttpVersion` methods, before returning the desired IP address from the `GetLocalAddress` method.
___


### MemoryStream

**Reading** from a `MemoryStream` after doing other operations, like writing to it can cause unexpected behaviour.<br/>
The reason is the current Position within the stream has changed.<br/>
By setting the position to the beginning of the stream `memStream.Seek(0, SeekOrigin.Begin)`, everything works fine.

## JavaScript
		
	