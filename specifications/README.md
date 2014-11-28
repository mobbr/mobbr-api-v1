<div>
<h1>Payment script syntax</h1>

<p>
    Simple script example:
</p>
<pre><code>{
    "title" : "The iPhony6",
    "description" : "Article about some fictitious planned obsolescence device",
    "language" : "EN",
    "keywords":
    [
        "planned obsolescence", "iphony"
    ]
    "participants" : 
    [
        {
            "id": "mailto:patman@mobbr.com",
            "role": "author",
            "share": "3"
        },
        {
            "id": "mailto:johnny@mobbr.com",
            "role": "author",
            "share": "3"
        },
        {
            "id": "mailto:info@zaplog.nl",
            "role": "platform",
            "share": "1"
        }
    ]
}</code></pre>
<p>
    Usage and explanation of the script:
</p>
<ul>
    <li>
        <a href="#api">API-arguments</a>
    </li>
    <li>
        <a href="#button=-scripts">Button-scripts</a>
    </li>
    <li>
        <a href="#page-inline">Inline page-scripts</a>
    </li>
    <li>
        <a href="#page-external">External page-scripts</a>
    </li>
    <li>
        <a href="#domain">Domain-scripts</a>
    </li>
    <li>
        <a href="#cascading">Combining scripts</a>
    </li>
    <li>
        <a href="#xhtml">Additional HTML elements</a>
    </li>
    <li>
        <a href="#specs">Elements</a>
    </li>
    <li>
        <a href="#ids">ID's</a>
    </li>
</ul>

<p>
    <a id="api"></a>
</p>

<h2>API-arguments</h2>

<p>
    The <code>POST /api_v1/payments/preview</code> API-method accepts a payment script for creating complex payments. 
</p>
<p>
    See <a href="https://github.com/mobbr/mobbr-api-v1/tree/master/payments#create-payment">https://github.com/mobbr/mobbr-api-v1/tree/master/payments#create-payment</a>. 
</p>

<p>
    <a id="button"></a>
</p>

<h2>Button-scripts</h2>

<p>
    The Mobbr javascript-button for web pages accepts a payment script as argument. 
</p>
<p>
    See <a href="https://github.com/mobbr/mobbr-api-v1/tree/master/examples#buttons">https://github.com/mobbr/mobbr-api-v1/tree/master/examples#buttons</a>. 
</p>

<p>
    <a id="page-inline"></a>
</p>

<h2>Page-scripts (inline)</h2>

<p>
    To turn your web pages into payable objects you need to place a payment script in the content-part of a <code>&lt;meta&gt;</code> tags in the <code>&lt;head&gt;</code>
    of your web page.
</p>

<pre><code>&lt;html&gt;
&lt;head&gt;
    <b>&lt;meta name="participation" content='{
        "title" : "The iPhony4",
        "description" : "Article about some fictious planned obsolescence device",
        "keywords":
        [
            "planned obsolescence"
        ]
        "participants" : 
        [
            {
                "id": "mailto:patman@mobbr.com",
                "role": "author",
                "share": "3"
            },
            {
                "id": "mailto:johnny@mobbr.com",
                "role": "author",
                "share": "3"
            },
            {
                "id": "mailto:info@zaplog.nl",
                "role": "platform",
                "share": "1"
            }
        ]
    }'/&gt;</b></code></pre>

<p>
    <i>
        <small>Note: because JSON requires double quotes, the entire JSON definition itself
            (the content-attribute) should be enclosed in single quotes.
        </small>
    </i>
</p>
<p>
    <i>
        <small>Note: this construction is W3C valid.</small>
    </i>
</p>

<p>
    <a id="page-external"></a>
</p>

<h2>Page-scripts (external)</h2>

<p>
    As an alternative to an inline script, an HTML-LINK can be used, linking to an external JSON-description.
</p>

<pre><code>&lt;html&gt;
&lt;head&gt;
    <b>&lt;link rel="participation" type="application/json"
        href="https://mobbr.com/mobbr-payment_info.json"/&gt;</b></code></pre>

<p>In which case the external link should be to a script</p>

<p><code>HTTPS://MOBBR.COM/MOBBR-PAYMENT_INFO.JSON</code></p>

<pre><code>{
    "title" : "The iPhony6",
    "description" : "Article about some fictitious planned obsolescence device",
    "language" : "EN",
    "keywords":
    [
        "planned obsolescence", "iphony"
    ]
    "participants" : 
    [
        {
            "id": "mailto:patman@mobbr.com",
            "role": "author",
            "share": "3"
        },
        {
            "id": "mailto:johnny@mobbr.com",
            "role": "author",
            "share": "3"
        },
        {
            "id": "mailto:info@zaplog.nl",
            "role": "platform",
            "share": "1"
        }
    ]
}</code></pre>

<p>
    <a id="domain"></a>
</p>

<h2>Domain description (domain script)</h2>

<p>
    Instead of tagging individual pages with metadata, a domain
    or website can specify global settings in a /PARTICIPATION.TXT file. This
    file needs to be placed in the root of the domain, like for
    instance the <a href="http://www.robotstxt.org/">ROBOTS.TXT</a>
    file.
</p>

<p>
    In this file the default and/or global participation properties can be
    defined.
</p>

<p>
    When used together with page-scripts, cascading-rules apply, see below.
</p>

<p>
    The format is straightforward: the file defines sets of
    scripts that are applied to the URL's that match the
    wildcard-pattern. The scripts will be applied to every URL
    that matches the pattern in order of declaration. The wildcard character is
    '*', matching zero, one or many arbitrary characters. Any '*' that occurs
    naturally in the URL (which is very rare), must be escaped (preceded) with a '\'.
</p>

<p>
    The following example describes the properties for a complete
    website.
</p>
    <pre><code>[
    {
        "url-pattern": "*",
        "script": 
        {
            "description":"Some default description",                                  
            "language":"EN",
            "participants": 
            [
               {
                   "id": "https://mobbr.com/#/person/john",
                   "role": "website-owner",
                   "share": "1"
               }
            ]
        }
    },
    {
        "url-pattern": "/article/*",
        "script": 
        {
            "participants": 
            [
                {
                    "id": "mailto:patman@mobbr.com",
                    "role": "author",
                    "share": "3"
                },
                {
                    "id": "mailto:johnny@mobbr.com",
                    "role": "author",
                    "share": "3"
                },
                {
                    "id": "mailto:info@zaplog.nl",
                    "role": "platform",
                    "share": "1"
                }
            ]
        }
    },
    {
        "url-pattern": "/article/*/picture",
        "script": 
        {
            "participants": 
            [
                {
                   "id": "https://mobbr.com/#/person/susy",
                   "role": "photographer",
                   "share": "10%"
                }
            ]
        }
    }
]</code></pre>

<p>
    It is allowed to have different patterns match the same URL. Properties are than cascaded 
    or overwritten, combining all matching rules into a single resulting script.
    This cascading may lead to duplicate participants being inserted which is allowed as long
    as their 'roles' differ.
</p>

<p>
    <a id="cascading"></a>
</p>

<h2>Cascading-rules</h2>

<p>
    It is possible to supply a page script as well as a domain script. In this case the
    processor will cascade (combine) the scripts. Duplicate fields are overwritten by order of declaration. 
</p>

<p>
        Participants can never be overwritten through cascading scripts, to combine participants with the same ID they need
        to have different roles. This makes it possible to specify global participants in the /PARTICIPATION.TXT that can never be excluded
    by button or page scripts. For instance the website owner and such. If such a recipient such has a percentage share,
    his share can never be reduced nor excluded.
</p>

<p>
    <a id="xhtml"></a>
</p>

<h2>Helpful (X)HTML elements</h2>

<p>Additional metadata / properties that can help the payments system to make intelligent decisions:</p>
<ul>
    <li>The HTML &lt;title&gt; element.</li>
    <li>A metadata name="description" <a href="http://www.w3schools.com/tags/tag_meta.asp">element</a>.</li>
    <li>A metadata name="canonical" <a
            href="http://googlewebmastercentral.blogspot.com/2009/02/specify-your-canonical.html">element</a>.
    </li>
    <li>A metadata name="original-source" <a
            href="http://googlenewsblog.blogspot.com/2010/11/credit-where-credit-is-due.html">element(s)</a>.
    </li>
    <li>A metalink rel="copyright" <a href="http://htmlhelp.com/reference/html40/head/link.html">element</a>, defining
        sharing and derivation rights
    </li>
    <li>
        A correct set of <a href="http://ogp.me/">OG-properties</a>, as
        used by Facebook.
    </li>
</ul>
<p>This is not Mobbr specific though, it is good web design practice in general. If possible make your pages W3C
    valid.</p>
    
    
<p>
    <a id="specs"></a>
</p>

<h2>Specification</h2>

<p>Possible script tags:</p>
<ul>
    <li>
        <code><b>url</b></code>
        - The URL of the collaboration or product. Forbidden in page or domain scripts. 
    </li>
    <li>
        <code><b>type</b></code>
        - The type of transaction, if none is given, "payment" will be assumed. If type "pledge" is used, the script is in crowdfunding mode and payments are escrowed until the type is set to "payment".
    </li>
    <li>
        <code><b>image</b></code>
        - The URL or BASE64 encoded content of an image for this product.
    </li>
    <li>
        <code><b>participants</b></code>
        - A JSON-array of one or more participants. 
        <ul>
            <li>
                <code><b>id</b></code>
                - The ID (email, username, OAUTH profile) of the recipient. 
            </li>
            <li>
                <code><b>role</b></code>
                - Description of the role or part the recipient played in getting this product to you.
            </li>
            <li>
                <code><b>share</b></code>
                - Positive integer or percentage. Integer shares of the payment or payment relative to the other shares.
                '1' will be used as default.
            </li>
            <li>
                <code><b>comment</b></code>
                - Will be ignored though size limits can be imposed.
            </li>
        </ul>
    </li>
    <li>
        <code><b>title</b></code>
        - Name of the product. The HTML-tag <code>&lt;title&gt;</code> of the page will be used if no name is given.
    </li>
    <li>
        <code><b>description</b></code>
        - Description of the product. If not present, the HTML-metatag
        <code>&lt;meta name="description" content="..." /&gt; </code>
        of the page will be used. Else the OG:description tag could
        be used as fallback.
    </li>
    <li>
        <code><b>comment</b></code>
        - Will be ignored though size limits can be imposed.
    </li>
    <li>
        <code><b>language</b></code>
        - To ISO code of a language. The HTML metadata will be analysed and used if none is given
    </li>
    <li>
        <code><b>keywords</b></code>
        - One or many keywords or tags. The HTML metadata keywords will be added to these.
    </li>
</ul>
<p>
    <a id="ids"></a>
</p>

<h2>ID's</h2>

<p>
    Recipients are identified by id's that, in principle, must be full URL's. Email addresses, twitternames or Mobbr usernames are allowed for compactness. The URL's must be
    of user profiles of sites that have Mobbr OAUTH support.
</p>
<p>
    Also possible are bitcoin addresses. These offer relative anonymity and are directly payable in bitcoin. Also restricted
    to bitcoin payments only.
</p>

<p>
    Below some examples of a JSON transaction description with various external ID's. First ID's that are <b>not</b> allowed (because an ID must be an URL)
</p>
    <pre><code>{
   "title": "The iPhony4",
   "description": "Article about some fictious planned obscolescence device",
   "participants": 
   [
       {
           "id": "Paramatman",
           "comment": "Invalid ID, not an URL, use: https://mobbr.com/#/person/Paramatman",
       },
       {
           <b>"id": "patman@zaplog.nl",
           </b>"comment": "Invalid ID, not an URL, use: mailto:patman@zaplog.nl",
       },
       {
           <b>"id": "@patman",
           </b>"comment": "Invalid ID, not an URL, use: https://twitter.com/patman",
       },
   ]
}</code></pre>

<p>Examples of valid ID's:</p>

    <pre><code>{
   "title": "The iPhony4",
   "description": "Article about some fictious planned obscolescence device",
   "participants": 
   [
       {
           "id": "https://mobbr.com/#/person/Paramatman",
           "comment": "Absolute URL, in this case a Mobbr profile",
       },
       {
           <b>"id": "mailto:patman@zaplog.nl",
           </b>"comment": "email-address, will receive an email with a registration link",
       },
       {
           <b>"id": "https://gravatar.com/patricksavalle",
           </b>"comment": "A supported profile, must be verified by matching email inside",
       },
       {
           <b>"id": "https://github.com/patricksavalle",
           </b>"comment": "A supported profile, must be verified using OAUTH",
       },
       {
           <b>"id": "bitcoin:1NS17iag9jJgTHD1VXjvLCEnZuQ3rJED9L",
           </b>"comment": "A bitcoin address, will receive them payment directly",
       },
       {
           <b>"id": "tel:+31638677592",
           </b>"comment": "Telephone number of recipient, will receive an SMS with a registration link",
       }
   ]
}</code></pre>
</div>
