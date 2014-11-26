<div>
<h1>Payment script syntax</h1>

<h2>Introduction</h2>

<p>
    In social collaboration every participant will have his own share of total added value. 
    The Mobbr script is used to distribute payments and donations among many participants in a collaboration by ratio of that share. 
</p>
<ul>
    <li>
        <a href="#page">Page-scripts</a>
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
        <a href="#specs">Specification</a>
    </li>
</ul>

<p>
    <a id="page"></a>
</p>

<h2>Page-scripts (inline)</h2>

<p>
    To turn your web pages into payable objects you need to add metadata to your page. Put a <a href="http://www.json.org/">JSON</a>
    description in the content-part of a <code>&lt;meta&gt;</code> tags in the <code>&lt;head&gt; </code>
    of your web page.
</p>

<pre><code>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt;
&lt;html xmlns=http://www.w3.org/1999/xhtml xml:lang="en" &gt;
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
                "id": "patman",
                "role": "author",
                "share": "3"
            },
            {
                "id": "johnny",
                "role": "author",
                "share": "3"
            },
            {
                "id": "zaplog",
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
    When used together with button- and / or domain-scripts, cascading-rules apply, see below.
</p>

<h2>Page-scripts (external)</h2>

<p>
    As an alternative a HTML-LINK can be used, linking to an external JSON-description. Like below:
</p>

<pre><code>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!DOCTYPE html
PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt; 
&lt;html xmlns=http://www.w3.org/1999/xhtml xml:lang="en" &gt;
&lt;head&gt;
    <b>&lt;link rel="participation" type="application/json"
        href="https://mobbr.com/mobbr-payment_info.json"/&gt;</b></code></pre>

<p>
    <a id="domain"></a>
</p>

<h2>Domain description (domain script)</h2>

<p>
    As an alternative to tagging individual pages with metadata, a domain
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
    When used together with button- and / or page-scripts, cascading-rules apply, see below.
</p>

<p>
    The format is straightforward: the file defines sets of
    properties that are applied to the URL's that match the
    wildcard-pattern. The properties will be applied to every URL
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
       "participation-info": 
       {
           "participants": 
           [
               {
                   "id": "John",
                   "role": "website-owner",
                   "share": "1"
               }
           ]
       }
   },
   {
       "url-pattern": "/article/*",
       "participation-info": 
        {
           "participants": 
           [
               {
                   "id": "John",
                   "role": "distributor",
                   "share": "10"
               },
               {
                   "id": "Hank",
                   "role": "author",
                   "share": "4"
               },
               {
                   "id": "Philip",
                   "role": "reviewer",
                   "share": "10"
               }
           ]
       }
   },
   {
       "url-pattern": "/article/*/picture",
       "participation-info": 
       {
           "participants": 
           [
               {
                   "id": "Susy",
                   "role": "photographer",
                   "share": "1"
               }
           ]
       }
   }
]</code></pre>

<p>
    It is allowed that different patterns match the same URL. Properties are then cascaded (if not present)
    or overwritten (if already present) in top to bottom order, combining all matching rules.
    This cascading may not lead to duplicate participants being inserted which is allowed as long
    as the roles differ.
</p>

<p>
    <a id="cascading"></a>
</p>

<h2>Cascading-rules</h2>

<p>
    It is possible to supply a page script as well as a domain script. In this case the
    processor will cascade (combine) the values in the following order:
</p>
<ol>
    <li>domain script</li>
    <li>page script</li>
</ol>
<p>
    The scripts are combined and duplicate values are overwritten in the order given above. 
    Participants can never be overwritten through cascading scripts, to combine participants with the same ID they need
    to have different roles.
</p>

<p>
    Using this logic it is possible to specify global participants in the /PARTICIPATION.TXT that can never be excluded
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

<img src="https://api.mobbr.com/images/open-micropayment_protocol-OuPP-json-spec.png" />

<p>The required properties for every page are:</p>

<p>Required for all cases that specify 'relative participants'.</p>
<ul>
    <li>
        <code>"id-base"</code>
        - URL (-prefix) that, combined with an ID, forms an URL uniquely identifying a person or member
        within that service. Together, the id-base and an relative ID should form
        a valid URL. If the id-base is ommitted, every recipient should be a full URL uniquely identifying the recipient, 
        other ID's are assumed to be Mobbr usernames.
    </li>
</ul>
<p>Required if scripts are used for fixed-price payments.</p>
<ul>
    <li>
        <code>"type"</code>
        - The <a href="#types">type</a>
        of the requested payment or payment, if none is given,
        "payment" will be assumed. If type "pledge" is used, payments are escrowed until the type is set to "payment".
    </li>
</ul>
<p>The optional properties for every page are:</p>
<ul>
    <li>
        <code>"url"</code>
        - The URL identifying the canonical URL if it is not the URL on which the
        button is placed. Not allowed / ignored for metatag-version of JSON.
    </li>
    <li>
        <code>"image"</code>
        - The URL or BASE64 encoded content of an image for this product.
    </li>
    <li>
        <code>"participants"</code>
        - A JSON-list of one or more participations. Not all
        payment-processors support multiple participants.
        <ul>
            <li>
                <code>"id"</code>
                - The ID with which the payment-processor can identify the recipient. Combined
                with the "id-base" is forms a valid URL. Most probably to the profile-page of the user. It can
                also be a full, valid URL in which case the contents (response) of the URL must be an email address.
            </li>
            <li>
                <code>"role"</code>
                - Description of the role or part the recipient played in getting this product to you.
            </li>
            <li>
                <code>"share"</code>
                - Positive integer or percentage. Integer shares of the payment or payment relative to the other shares.
                '1' will be used as default.
            </li>
        </ul>
    </li>
    <li>
        <code>"title"</code>
        - Name of the product. The HTML-tag <code>&lt;title&gt;</code> of the page will be used if no name is given.
    </li>
    <li>
        <code>"description"</code>
        - Description of the product. If not present, the HTML-metatag
        <code>&lt;meta name="description" content="..." /&gt; </code>
        of the page will be used. Else the OG:description tag could
        be used as fallback.
    </li>
    <li>
        <code>"comment"</code>
        - As it says: comment. Will be ignored though size limits can be imposed.
    </li>
    <li>
        <code>"language"</code>
        - To ISO code of a language.
    </li>
    <li>
        <code>"keywords"</code>
        - One or many keywors or tags
    </li>
</ul>
<p>
    <a id="ids"></a>
</p>

<h2>External ID's</h2>

<p>
    Recipients are identified by id's that, in principle, must be full URL's or email addresses or Mobbr usernames. The URL's must be
    of profiles of sites that have Mobbr OAUTH support.
</p>
<p>
    Also possible are bitcoin addresses. These offer good anonimity and are directly payable in bitcoin. Also restricted
    to bitcoins only.
</p>

<p>
    Below an example of a JSON transaction description with external ID's.
</p>
    <pre><code>{
   "id-base": "https://mobbr.com/#/person/",
   "title": "The iPhony4",
   "description": "Article about some fictious planned obscolescence device",
   "participants": 
   [
       {
           "id": "Paramatman",
           "comment": "Relative URL, relative to id-base",
           "role": "author",
           "share": "3"
       },
       {
           "id": "https://mobbr.com/#/person/Paramatman",
           "comment": "Absolute URL, in this case a Mobbr profile",
           "role": "author",
           "share": "3"
       },
       {
           <b>"id": "mailto:patman@zaplog.nl",
           </b>"comment": "Straight email-address URL",
           "role": "co-author",
           "share": "3"
       },
       {
           <b>"id": "http://gravatar.com/patricksavalle",
           </b>"comment": "This is a URL of a person outside the id-base",
           "role": "co-author",
           "share": "1"
       },
       {
           <b>"id": "https://github.com/patricksavalle",
           </b>"comment": "A supported profile",
           "role": "co-author",
           "share": "1"
       },
       {
           <b>"id": "bitcoin:1NS17iag9jJgTHD1VXjvLCEnZuQ3rJED9L",
           </b>"comment": "The bitcoin address to receive payments",
           "role": "co-author",
           "share": "1"
       }
   ]
}</code></pre>
</div>
