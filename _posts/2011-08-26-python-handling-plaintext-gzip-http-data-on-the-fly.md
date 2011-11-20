---
layout: post
title: Handling plain/gzip HTTP data on the fly (Python)
---

{{ page.title }}
================

So today I had a problem that a certain data provider would return either plain text or gziped data depending on the size of the content. The thing was that you didn't know beforehand. So here is how you handle these two datatype on the fly (note: I am using the [requests library](http://python-requests.org/) instead of [urllib](http://docs.python.org/library/urllib.html)/[urllib2](http://docs.python.org/library/urllib2.html)/h[ttplib](http://docs.python.org/library/httplib.html)):

{% highlight python %}
import requests
import logging, gzip
from cStringIO import StringIO

def download(self, query, extension):
    if not self.logged_in:
        self.login()

    r = requests.get(self.__class__.base_url + self.__class__.download_path + '?' 
                    + (query % extension) , cookies=self.cookies)

    logger.debug("Downloaded data of type: %s" % r.headers["content-type"])

    # Handle gzipped data
    if r.headers["content-type"] == "application/x-gzip":
        buf = StringIO(r.content)
        s = gzip.GzipFile(mode="rb", fileobj=buf)
        content = s.read(len(r.content))
    
    # Handle plain text
    elif r.headers["content-type"] == "text/plain":
        content = r.content

    # Raise value error in case of other mime type
    else:
        err = "Received data of invalid type '%s'" % r.headers["content-type"]
        logger.error(err)
        raise ValueError(err)

    return self.__class__.parse_content(content)
{% endhighlight %}