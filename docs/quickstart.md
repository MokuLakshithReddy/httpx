## Redirection and History

By default, HTTPX will **not** follow redirects for all HTTP methods, although
this can be explicitly enabled.

For example, GitHub redirects all HTTP requests to HTTPS.

```pycon
>>> r = httpx.get('http://github.com/')
>>> r.status_code
301
>>> r.history
[]
>>> r.next_request
<Request('GET', 'https://github.com/')>
```

You can modify the default redirection handling with the `follow_redirects` parameter:

```pycon
>>> r = httpx.get('http://github.com/', follow_redirects=True)
>>> r.url
URL('https://github.com/')
>>> r.status_code
200
>>> r.history
[<Response [301 Moved Permanently]>]
```

When following a redirect to a different origin, HTTPX removes the
`Authorization` header so that credentials are not forwarded to another
host, scheme, or port. The exception is a direct HTTP-to-HTTPS upgrade on the
same host using the default ports (80 and 443), where the header is retained.
A redirect from HTTPS to HTTP on the same host does not qualify for this
exception, and the header is removed.

The `history` property of the response can be used to inspect any followed redirects.
It contains a list of any redirect responses that were followed, in the order
in which they were made.

## Timeouts
