#Payments API

Payments to usernames, email-addresses, OAUTH profiles, payment scripts and URL's. 

- [create payment *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#create-payment)
- [confirm payment] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#confirm-payment)
- [list payments] ()
- [inspect payment *public*] ()
- [list claimable payments] ()
- [claim payments *public*] ()
- [list pledges]()
- [delete pledges]()
- [list unclaimed shares] ()
- [delete unclaimed shares] ()

##Create payment

Generates a payment preview. It lists the actual payment properties and recipients. Use `hash` from the result to confirm the payment with `/api_v1/payments/confirm` within 10 minutes.

    POST /api_v1/payments/preview

**Arguments**

    data                 : email,URL,JSON,username
    amount *optional*    : positive floating point
    currency *optional*  : ISO-code
    invoiced *optional*  : TRUE or FALSE, will only pay recipients that have complete profiles
    annotated *optional* : TRUE or FALSE, leaves internal bookkeeping in (fields starting with a .)
    referrer *optional*  : URL, the origin of the payment

**Example 1**, previewing only recipients of a payment to a Github URL, no amount or currency specified.

Request

    curl 
    -X POST 
    -H 
    "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"data":"https:\/\/github.com\/identifi\/identifi"}' 
    https://api.mobbr.com/api_v1/payments/preview

Response, arrays reduced to a single element

    {
        "result": {
            "hash": null,
            "script": {
                "image": "https://avatars3.githubusercontent.com/u/3898718?v=3&s=400",
                "url": "https://github.com/identifi/identifi",
                "language": "EN",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "participants": [
                    {
                        "id": "mailto:octocat@github.com",
                        "role": "Platform-owner",
                        "share": "1",
                        ".x-id": "7194e8d48fa1d2b689f99443b767316c",
                        ".percentage": "1",
                        ".gravatar": "7194e8d48fa1d2b689f99443b767316c"
                    }
                ],
                "keywords": [
                    "software development"
                ],
                "message": "Payments done to this URL will immediately be divided (paid out) among currently listed contributors. Contributors can claim their share by registering with Mobbr and connecting their Github-account. Participation is rated based on additions, deletes, commits and recentness.",
                "type": "donation",
                ".script-type": [
                    "api"
                ],
                ".amount": null,
                ".currency": null,
                ".invoiced": false,
                ".referrer": null,
                ".favicon": "https://www.google.com/s2/favicons?domain=github.com",
                ".logo": "https://github.com/apple-touch-icon.png"
            },
            "url": "https://github.com/identifi/identifi"
        },
        "message": null
    }

**Example 2**, preparing actual payment to a username, amount and currency specified.

Request

    curl 
    -X POST 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"data":"Patrick", "amount":10, "currency":"GBP"}' 
    https://api.mobbr.com/api_v1/payments/preview

Response

    {
        "result": {
            "hash": "05b921e21ad2a78d00e7b1ec721baaa8",
            "script": {
                "id-base": "https://mobbr.com/#/person/",
                "type": "pay",
                "title": "Payment to 'https://mobbr.com/#/person/patrick'",
                "participants": [
                    {
                        "id": "https://mobbr.com/#/person/patrick",
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

**Example 3**, sending a multi-recipient payment script

The data argument can also contain JSON scripts that describe a complex payment, for example:

    {
        "image": "https://avatars3.githubusercontent.com/u/3898718?v=3&s=400",
        "language": "EN",
        "title": "Github repository identifi/identifi",
        "description": "Identifi implementation built on Bitcoin code",
        "participants": [
            {
                "id": "mailto:octocat@github.com",
                "role": "Platform-owner",
                "share": "1%"
            },
            {
                "id": "https://github.com/rippler",
                "role": "Contributor",
                "share": 24
            },
            {
                "id": "Patrick",
                "role": "Contributor",
                "share": 11
            }
        ],
        "keywords": [
            "keyword"
        ],
        "message": "Anything the user shoudl know before paying.",
        "type": "donation"
    }

Confirm payment
---------------

Confirms a payment preview, actually distributing money and informing the recipients. Returns the confirmed payment.

    PUT /api_v1/payments/confirm
   
**Arguments**

    hash : the hash that was returned by /api_v1/payments/preview
    
**Example**

Request
  
    curl 
    -X PUT 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -d '{"hash":"178faa2f3d321a662e86cc42e2acd508"}' https://test-api.mobbr.com/api_v1/payments/confirm
    
Response

    {
        "result": {
            "payment_id": "302302b4dd1280e2e9a38793de92f210",
            "payment_script": {
                "image": "https://avatars3.githubusercontent.com/u/3898718?v=3&s=400",
                "url": "https://github.com/identifi/identifi",
                "language": "EN",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "participants": [
                    {
                        "id": "mailto:octocat@github.com",
                        "role": "Platform-owner",
                        "share": "1%",
                        ".x-id": "7194e8d48fa1d2b689f99443b767316c",
                        ".amount": "0.01",
                        ".percentage": "1",
                        ".gravatar": "7194e8d48fa1d2b689f99443b767316c"
                    }
                ],
                "keywords": [
                    "github.com"
                ],
                "message": "Text.",
                "type": "donation",
                ".script-type": [
                    "api"
                ],
                ".amount": 1,
                ".currency": "EUR",
                ".invoiced": false,
                ".referrer": null,
                ".favicon": "https://www.google.com/s2/favicons?domain=github.com",
                ".logo": "https://github.com/apple-touch-icon.png"
            }
        },
        "message": {
            "text": "EUR 1 was divided among 5 recipient(s)",
            "type": "info"
        }
    }
