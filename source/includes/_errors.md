# Errors

The Bannerbear API uses the following error codes:


Error Code | Meaning
---------- | -------
202 | Accepted -- Your request is has been accepted for processing
200 | OK 
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
404 | Not Found -- The specified request could not be found.
429 | Too Many Requests -- Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

The Bannerbear API rate limit is 10 requests per 10 seconds.