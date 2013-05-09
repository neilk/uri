# uri

Uri parser library in JavaScript. Parses, constructs, and modifies relative and absolute URIs.

Do not expect full RFC 3986 compliance. Intended to be minimal, but featureful.
The use cases we have in mind are constructing 'next page' or 'previous page' URLs,
detecting whether we need to use cross-domain proxies for an API, constructing
simple URL-based API calls, etc.

Intended to compress very well if you use a JS-parsing minifier.

Dependencies: jQuery

## Example

    var uri = new Uri('http://foo.com/mysite/mypage.php?quux=2');

    if (uri.host == 'foo.com') {
        uri.host = 'www.foo.com';
        uri.extend({ bar: 1 });

        $('a#id1').attr('href', uri);
        // anchor with id 'id1' now links to http://foo.com/mysite/mypage.php?bar=1&quux=2

        $('a#id2').attr('href', uri.clone().extend({ bar: 3, pif: 'paf' }));
        // anchor with id 'id2' now links to http://foo.com/mysite/mypage.php?bar=3&quux=2&pif=paf
    }

Parsing here is regex based, so may not work on all URIs, but is good enough for most.

Given a URI like

    http://usr:pwd@www.test.com:81/dir/dir.2/index.htm?q1=0&&test1&test2=&test3=value+%28escaped%29&r=1&r=2#top

The returned object will have the following properties:

     protocol  'http'
     user      'usr'
     password  'pwd'
     host      'www.test.com'
     port      '81'
     path      '/dir/dir.2/index.htm'
     query     {
                   q1: 0,
                   test1: null,
                   test2: '',
                   test3: 'value (escaped)'
                   r: [1, 2]
               }
     fragment  'top'

n.b. 'password' is not technically allowed for HTTP URIs, but it is possible with other
sorts of URIs.

You can modify the properties directly. Then use the toString() method to extract the
full URI string again, e.g.:

    var u = new Uri('http://example.com/foo/bar?page=3');
    u.query.port = 8080;
    u.query.page++;
    console.log(u.toString());   // http://example.com:8080/foo/bar?page=4

## Relative Uris

This is probably obscure, but in the interest of complete documentation:

You can also generate "relative" URIs - these are primarily useful for URIs which share the same
domain or protocol, not for normalizing a path like `../../index.html`.i

The `UriRelative` constructor returns a `Uri` constructor, initialized with the properties of that
Uri.

    var ExampleComUri = new UriRelative('https://example.com/foo')
    var x = new ExampleComUri({path:'/bar'});
    console.log(x.toString());  // https://example.com/bar

In a browser, the main `Uri` object is just initialized relative to the current document URL.

UriRelative()
-------------
We use a factory to inject a document location, for relative URLs, including protocol-relative URLs.
so the library is still testable & purely functional.


Uri(uri, Object)
----------------
Constructs URI object. Throws error if arguments are illegal/impossible, or otherwise don't parse.
Object must have non-blank 'protocol', 'host', and 'path' properties.
This parameter is optional. If omitted (or set to undefined, null or empty string), then an object will be created
for the default uri of this constructor (e.g. document.location for mw.Uri in MediaWiki core).
- {boolean} strictMode Trigger strict mode parsing of the url. Default: false
- {boolean} overrideKeys Wether to let duplicate query parameters override eachother (true) or automagically
convert to an array (false, default).


**Parameters**

**uri**:  *Object|string*,  URI string, or an Object with appropriate properties (especially another URI object to clone).

**Object**:  *Object|boolean*,  with options, or (backwards compatibility) a boolean for strictMode

Uri.encode(s)
---------
Standard encodeURIComponent, with extra stuff to make all browsers work similarly and more compliant with RFC 3986
Similar to rawurlencode from PHP and our JS library mw.util.rawurlencode, but we also replace space with a +


**Parameters**

**s**:  *string*,  String to encode.

**Returns**

*string*,  Encoded string for URI.

Uri.decode(s)
---------
Standard decodeURIComponent, with '+' to space.


**Parameters**

**s**:  *string*,  String encoded for URI.

**Returns**

*string*,  Decoded string.


## Methods of uri objects

getUserInfo()
-------------
Returns user and password portion of a URI.


getHostPort()
-------------
Gets host and port portion of a URI.


getAuthority()
--------------
Returns the userInfo and host and port portion of the URI.
In most real-world URLs, this is simply the hostname, but it is more general.


getQueryString()
----------------
Returns the query arguments of the URL, encoded into a string
Does not preserve the order of arguments passed into the URI. Does handle escaping.


getRelativePath()
-----------------
Returns everything after the authority section of the URI


toString()
----------
Gets the entire URI string. May not be precisely the same as input due to order of query arguments.


**Returns**

*string*,  The URI string.

clone()
-------
Clone this URI


**Returns**

*Object*,  new URI object with same properties

extend(query)
-------------
Extend the query -- supply query parameters to override or add to ours


**Parameters**

**query**:  *Object*,  parameters in key-val form to override or add

**Returns**

*Object*,  this URI object

    var uri = new Uri('http://example.com/foo?a=b')
    uri.extend( { a: 'c', x: 'y'});
    console.log(uri.toString())   // http://example.com/foo?a=c&x=y 




## Credits

Parsing based on parseUri 1.2.2 (c) Steven Levithan <stevenlevithan.com> MIT License
http://stevenlevithan.com/demo/parseuri/js/

Based on mediawiki.Uri.js, also primarily by Neil Kandalgaonkar and distributed with MediaWiki
(http://mediawiki.org/).
 
