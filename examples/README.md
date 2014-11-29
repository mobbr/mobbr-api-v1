#Examples

- [Preparing a web page] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#preparing-a-webpage)
- [Crowdfunding a task] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#crowdfunding-a-task)
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

The script can be either static or dynamically generated, based on some algorithm or statistic that is relevant to the payment or collaboration.

##Crowdfunding a task

The API supports crowdfunding (accumulating payments) and crowdpaying (paying many at once). For this functionality the web page of the task needs to have a Mobbr script as described above.

To enable crowdfunding on an URL, change the value of the `type` field to `pledge`.

    <html>
        <head>
        
        <!-- the Mobbr payment script, example, generate this dynamically based on contribution/participation ->
        <meta name="participation" content='
            {
                "type":"pledge",
                ...
            }'>
        
        ...
        
    </html>

While in crowdfunding mode, all payments are kept in escrow by Mobbr until the value is set to `"type":"payment"`. 
 
Changes won't be noticed by our API until it is notified of the script changes. Triggering the pledge (distribution of the accumulated pledges among participants) can be done by:
- Any of the pledgers can explicitely trigger the payment from the MOBBR.COM dashboard
- A call to API-method [`PUT /api_v1/payments/claim`](https://github.com/mobbr/mobbr-api-v1/tree/master/payments#claim-payments)

##Payment by API

To do a payment using the API requires two calls:
- 