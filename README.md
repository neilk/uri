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
full URI string again.

## Relative Uris

You can also generate "relative" URIs - these are primarily useful for URIs which share the same
domain or protocol, not for normalizing a path like `../../index.html`.i

The `UriRelative` constructor returns a `Uri` constructor, initialized with the properties of that
Uri.

    var ExampleComUri = new UriRelative('https://example.com/foo')
    var x = new ExampleComUri({path:'/bar'});
    console.log(x.toString());  // https://example.com/bar

In a browser, the main `Uri` object is just initialized relative to the current document URL.



## Credits

Parsing based on parseUri 1.2.2 (c) Steven Levithan <stevenlevithan.com> MIT License
http://stevenlevithan.com/demo/parseuri/js/

Based on mediawiki.Uri.js, also primarily by Neil Kandalgaonkar and distributed with MediaWiki
(http://mediawiki.org/).
 
