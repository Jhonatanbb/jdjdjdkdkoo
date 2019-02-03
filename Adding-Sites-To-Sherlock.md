# Detection Algorithms
With the large variety of websites, there are many different ways that detection of the availability of a username can go off the rails.  So, it is important when a new site is added to configure the detection as robustly as possible.

There are three detection algorithms supported at the current time.
## HTTP Status
This is the most reliably detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist is the desired username has been taken.  If the username is unclaimed, then the site returns an HTTP Status code (typically a [404 Not Found](https://en.wikipedia.org/wiki/HTTP_404)).

This is by far the most reliable method to detect if a username is available or not.  Unfortunately, it is not supported by all sites.
## Response URL
This is the next most reliable detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist is the desired username has been taken.  If the username is unclaimed, then the site returns an HTTP Status code (typically a [301 Moved Permanently](https://en.wikipedia.org/wiki/HTTP_301)).  Sites with this design will sometimes re-direct the user to the main site, or perhaps take the user to a sign-up page.

For this detection method, Sherlock will ignore any attempt to follow the re-direct, and inspect the first response.  If it is a redirect, then the username is determined to be unclaimed.
## Error Message
This is the least reliable detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist is the desired username has been taken.  If the username is unclaimed, then the site does not return any discernible HTTP Status code.  Sites with this design will display some error message, or perhaps directly offer the user a chance to sign-up for the site.

For this detection method, Sherlock looks for specific error text.  Since the error message might change with minor updates on the site, this mechanism is the most fragile.
