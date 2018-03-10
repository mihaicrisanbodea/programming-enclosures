# Programming enclosures  

Useful things to be aware of.
			
## C#

### HttpContext.Current

Pitfall: accessing `HttpContext.Current` in a multithreaded scenario.
This will result in a System.NullReferenceException because this data is available only on the main worker's local storage.
___
	
Faking `HttpContext.Current` in a non-web application can be troublesome.
Implementing `HttpWorkerRequest` can be a quick solution for faking `HttpContext.Current.Request.UserHostAddress` for example.
For the above, the only thing required further is to return dummy strings for `GetRawUrl`, `GetHttpVerbName` and `GetHttpVersion` methods, before returning the desired IP address from the `GetLocalAddress` method.
___


## JavaScript
		
	