#Examples

- [Preparing a web page] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#preparing-a-webpage)
- [Doing a payment by API] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#preparing-a-webpage)

##Preparing a webpage

The Mobbr API can 'pay' URL's. It will do a callback to the URL and read the payment data from a metadata-tag in the HTML. This is useful for collaboration platforms; the script can be generated dynamically based on the participation levels in the collaboration.

A very simple HTML page would look like this. 

    <html>
        <head>
        
        <!-- the Mobbr payment script, example, generate this dynamically based on contribution/participation ->
        <meta name="participation" content='
            {
                "type":"payment",
                "language":"EN",
                "title":"Mobbr demo page",
                "description":"Some descriptive test",
                "keywords":
                [
                    "keyword1",
                    "keyword2"
                ],
                "participants":
                [
                    {"id":"Patrick","share":"2","role":"administrator"},
                    {"id":"Ernesto","share":"1","role":"developer"},
                    {"id":"some@email.com","share":"10","role":"architect"},
                    {"id":"Patrick","role":"owner","share":"10%"}
                ]
            }'>
        
        <!-- The Mobbr javascript includes, just include AS IS -->
        <script type="text/javascript" src="https://mobbr.com/mobbr-button.js"></script>
        <script>
            mobbr.setUiUrl("https://mobbr.com/");
            mobbr.setApiUrl("https://api.mobbr.com/");
            mobbr.setLightboxUrl("https://mobbr.com/lightbox/#");
            mobbr.createDiv();
        </script>            
        
        </head>
        <body>
        
            <!-- the visible button, include AS IS -->
            <script type="text/javascript">mobbr.buttonMedium("", "EUR");</script>

        </body>
    </html>
    
This page would show a button. When the button is clicked the Mobbr lightbox opens over the site and allows users to do payments.
    
![Example of the opened lightbox]
(http://i.imgur.com/0xVTJcZ.png)

The button can also have another URL as argument in which case the API would callback that URL.

    <script type="text/javascript">mobbr.buttonMedium("https://some.other.url/page.html", "EUR");</script>
    
Or it could have a complete payment script, in which case the API won't make any callbacks:

    <script type="text/javascript">mobbr.buttonMedium("{
        "type":"payment",
        "language":"EN",
        "title":"Mobbr demo page",
        "description":"Some descriptive test",
        "keywords":
        [
           "keyword1", "keyword2"
        ],
        "participants":
        [
           {"id":"Patrick","share":"2","role":"administrator"},
           {"id":"Ernesto","share":"1","role":"developer"},
           {"id":"some@email.com","share":"10","role":"architect"},
           {"id":"Patrick","role":"owner","share":"10%"}
        ]
    }", "EUR");</script>

Supported button types:
- `buttonMedium`
- `buttonLarge`
- `buttonSlim`

Supported currencies:
- all FIAT currencies plus BTC

##Payment by API

To do a payment using the API requires two calls:
- 