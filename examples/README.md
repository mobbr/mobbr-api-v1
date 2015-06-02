#Examples

- [Making a payment] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#making-a-payment)
- [Making any web page payable] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#preparing-a-webpage)
- [Adding crowdfunding to any web page] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#crowdfunding-a-task)
- [Doing a payment by API] (https://github.com/mobbr/mobbr-api-v1/tree/master/examples#preparing-a-webpage)

##Making a payment

Doing a 'simple' payment consists of two API-calls: one to prepare a payment preview and another to confirm the the payment. The payment preview will be invalidated automatically after 10 minutes.

To pay GBP 10.00 to an email address, first prepare the preview.

    curl 
    -X POST 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"data":"mailto:patrick@patricksavalle.com", "amount":10, "currency":"GBP"}' 
    https://test-api.mobbr.com/api_v1/payments/preview

The response can be shown to a user:

    {
        "result": 
        {
            "hash": "05b921e21ad2a78d00e7b1ec721baaa8",
            "script": 
            {
                "id-base": "https://mobbr.com/#/person/",
                "type": "pay",
                "title": "Payment to 'https://mobbr.com/#/person/patrick'",
                "participants": 
                [
                    {
                        "id": "mailto:patrick@patricksavalle.com",
                        "share": 1,
                        "role": "",
                        ".x-id": "Patrick",
                        ".amount": "10",
                        ".percentage": "100",
                        ".gravatar": "e6032c3bbb3ece98d2782862594b08c2"
                    }
                ],
                ".amount": 10,
                ".currency": "GBP",
                ".invoiced": false,
                ".referrer": null
            },
            "url": null
        },
        "message": null
    }

The preview shows the payment that can actually be made, which can differ from the requested payment, depending of different business rules (such as account limits and payment thresholds). If the payment is correct, it needs to be confirmed. Use the 'hash' value from the response.

    curl 
    -X PUT 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -d '{"hash":"05b921e21ad2a78d00e7b1ec721baaa8"}' 
    https://test-api.mobbr.com/api_v1/payments/confirm
    
That's it.

Note that requesting a payment preview does not need authentication.

Payments can be made to email adresses, twitter-ID's, URL's etc. See: https://github.com/mobbr/mobbr-api-v1/tree/master/payments#create-payment


##Preparing a webpage

The Mobbr API can send a payment to any URL. Mobbr support on a simple HTML page looks like this. 

    <html>
        <head>
        
        <!-- the Mobbr payment script, example, generate this dynamically based on contribution/participation ->
        <meta name="participation" content='
            {
                "type":"payment",
                "language":"EN",
                "title":"Mobbr demo page",
                "description":"Some descriptive text",
                "keywords":
                [
                    "keyword1",
                    "keyword2"
                ],
                "participants":
                [
                    {"id":"https://mobbr.com/#/person/Patrick","share":"2","role":"administrator"},
                    {"id":"https://mobbr.com/#/person/Ernesto","share":"1","role":"developer"},
                    {"id":"mailto:some@email.com","share":"10","role":"architect"},
                    {"id":"https://mobbr.com/#/person/Patrick","role":"owner","share":"10%"}
                ]
            }'>
        
        <!-- The Mobbr javascript includes, needed for the button -->
        <script type="text/javascript" src="https://mobbr.com/mobbr-button.js"></script>
        <script>
            mobbr.setUiUrl("https://mobbr.com/");
            mobbr.setApiUrl("https://api.mobbr.com/");
            mobbr.setLightboxUrl("https://mobbr.com/lightbox/#");
            mobbr.createDiv();
        </script>            
        
        </head>
        <body>
        
            <!-- the visible button -->
            <script type="text/javascript">mobbr.buttonMedium("", "EUR");</script>

        </body>
    </html>
    
This page will show a button. When the button is clicked the Mobbr lightbox opens over right side of the site. Like here:
    
![Example of the opened lightbox]
(http://i.imgur.com/0xVTJcZ.png)

The button can also have an URL as argument in which case Mobbr will send the payment to that URL.

    <script type="text/javascript">mobbr.buttonMedium("https://some.other.url/page.html", "EUR");</script>
    
Supported button types:
- `buttonMedium`
- `buttonLarge`
- `buttonSlim`

Supported currencies:
- all FIAT currencies plus BTC

Upon payment, the API will visit the URL ([try it](https://mobbr.com/#/task/aHR0cHM6Ly9naXRodWIuY29tL0JpdC1OYXRpb24vdG94Y29yZQ==/view)) and read the payment data from the metadata-tag in the HTML. This is useful for collaboration platforms; the script can be generated dynamically based on the participation levels in the collaboration.

To prevent the API from visiting the URL (for instance for private sites), enter complete payment data in the button-script:

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
            {"id":"https://mobbr.com/#/person/Patrick","share":"2","role":"administrator"},
            {"id":"https://mobbr.com/#/person/Ernesto","share":"1","role":"developer"},
            {"id":"mailto:some@email.com","share":"10","role":"architect"},
            {"id":"https://mobbr.com/#/person/Patrick","role":"owner","share":"10%"}
        ]
    }", "EUR");</script>

Testing the integration can be done by entering the URL in the box on the Mobbr frontpage https://mobbr.com

##Crowdfunding a task

The API / Mobbr supports crowdfunding (accumulating payments) and crowdpaying (paying many at once) on any web page. For this functionality the HTML-header of this web page needs to have a Mobbr-script as described above but with the value of the `type` field set to `pledge`.

To enable crowdfunding on any URL, this is the minimal script. 

    <html>
        <head>
        
        <!-- the Mobbr payment script, example, generate this dynamically based on contribution/participation ->
        <meta name="participation" content='
            {
                "type":"pledge"
            }'>
        
        ...
        
    </html>

While in crowdfunding mode, all payments are kept in escrow by Mobbr until the value is set to `"type":"payment"`. 
 
Changes in the script won't be noticed however by our API until it is notified of the script changes. Triggering the pledge (distributing the accumulated pledges among participants) can be done by:
- Any of the pledgers can trigger their payment from the MOBBR.COM dashboard, upon which the API will trigger them all
- A call to API-method [`PUT /api_v1/payments/claim`](https://github.com/mobbr/mobbr-api-v1/tree/master/payments#claim-payments) 

