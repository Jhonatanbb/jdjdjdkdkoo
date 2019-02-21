# Site Analysis

To add sites to Sherlock is relatively straight forward.  But, there is some background that will help
you in your task.

Sherlock relies on the site's designers providing a unique URL for a registered username.  To determine
if a username is available, Sherlock queries that URL, and uses to response to understand if there is
a claimed username already there.

The [data.json](https://github.com/TheYahya/sherlock/blob/master/data.json) file contains all of the
information about which sites Sherlock will query usernames for, and how that query should be 
carried out.

Here is an illustrative entry for one site in the 
[data.json](https://github.com/TheYahya/sherlock/blob/master/data.json) file.  Following this, is a
more detailed description of each entry.

```
  "Some Cool Site That Everyone Loves": {
    "errorType": "status_code",
    "rank": 183,
    "regexCheck": "^[a-zA-Z][a-zA-Z0-9_-]*$",
    "url": "https://somecoolsitethateveryoneloves.com/members/{}",
    "urlMain": "https://somecoolsitethateveryoneloves.com",
    "username_claimed": "blue",
    "username_unclaimed": "noonewouldeverusethis7"
  },
```

- **errorType**

  Required field that defines the detection method.
  See the Detection Algorithms section below for more details about the options here.
- **rank**

  Optional field that indicates how popular the site is.
  Note that the developer does not have to fill this in manually...  There is an automated
  process that enters this information.
- **regexCheck**

  Optional field that specifies a regular expression that only matches on a valid username.
  This is used to flag usernames that may not be allowable on a given site.  Note that
  a username that is valid on one site may not be allowed on another site.
- **url**

  Required field that defines the format of a member's URL on the site.
  Use the curly brackets (i.e. "{}") to record where the username appears in the URL.
- **urlMain**

  Required field that defines the site's URL.
- **username_claimed**

  Optional field that defines a username that is claimed.  This is used for tests.
  Note that even though this field is optional for Sherlock functionality, the tests
  will fail if a proper value is not supplied.
- **username_unclaimed**

  Optional field that defines a username that has not been claimed.  This is used for tests.
  Note that even though this field is optional for Sherlock functionality, the tests
  will fail if a proper value is not supplied.

# Detection Algorithms
With the large variety of websites, there are many different ways that detection of the availability of a username can go off the rails.  So, it is important when a new site is added to configure the detection as robustly as possible.  Likewise, it is crucial that tests are written for the site so that we can understand if the detection starts failing for certain sites.

There are three detection algorithms supported at the current time.
## HTTP Status
This is the most reliably detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist if the desired username has been taken.  If the username is unclaimed, then the site returns an HTTP Status code (typically a [404 Not Found](https://en.wikipedia.org/wiki/HTTP_404)).

This is by far the most reliable method to detect if a username is available or not.  Unfortunately, it is not supported by all sites.

Here are some examples of what a site using this detection method would be configured:
* Claimed:   https://buzzfeed.com/blue
* Unclaimed: https://buzzfeed.com/noonewouldeverusethis7

Site JSON Data
```
  "BuzzFeed": {
    "errorType": "status_code",
    "rank": 294,
    "url": "https://buzzfeed.com/{}",
    "urlMain": "https://buzzfeed.com/",
    "username_claimed":   "blue",
    "username_unclaimed": "noonewouldeverusethis7"
  },
```
## Response URL
This is the next most reliable detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist if the desired username has been taken.  If the username is unclaimed, then the site returns an HTTP Status code (typically a [301 Moved Permanently](https://en.wikipedia.org/wiki/HTTP_301)).  Sites with this design will sometimes re-direct the user to the main site, or perhaps take the user to a sign-up page.

For this detection method, Sherlock will ignore any attempt to follow the re-direct, and inspect the first response.  If it is a redirect, then the username is determined to be unclaimed.

Here are some examples of what a site using this detection method would be configured:
* Claimed:   https://www.canva.com/blue
* Unclaimed: https://www.canva.com/noonewouldeverusethis7

Site JSON Data
```
  "Canva": {
    "errorType": "response_url",
    "errorUrl": "https://www.canva.com/{}",
    "rank": 214,
    "url": "https://www.canva.com/{}",
    "urlMain": "https://www.canva.com/",
    "username_claimed":   "blue",
    "username_unclaimed": "noonewouldeverusethis7"
  },
```

## Error Message
This is the least reliable detection method.  In this detection method, Sherlock does a request of the specific site URL which should exist if the desired username has been taken.  If the username is unclaimed, then the site does not return any discernible HTTP Status code.  Sites with this design will display some error message, or perhaps directly offer the user a chance to sign-up for the site.

For this detection method, Sherlock looks for specific error text.  Since the error message might change with minor updates on the site, this mechanism is the most fragile.

Here are some examples of what a site using this detection method would be configured:
* Claimed:   https://blue.basecamphq.com
* Unclaimed: https://noonewouldeverusethis7.basecamphq.com

Site JSON Data
```
  "Basecamp": {
    "errorMsg": "The account you were looking for doesn't exist",
    "errorType": "message",
    "rank": 1544,
    "url": "https://{}.basecamphq.com",
    "urlMain": "https://basecamp.com/",
    "username_claimed":   "blue",
    "username_unclaimed": "noonewouldeverusethis7"
  },
```