Payments API
============

Payments to usernames, email-addresses, OAUTH profiles, payment scripts and URL's. 

Create payment
--------------

Generates a payment preview that can be confirmed within 10 minutes. It lists the actual payment properties and recipients. Use `hash` from the result to confirm the payment.

    POST /api_v1/payments/preview

#Arguments:

    data : email,url,json,username
    amount: positive floating point, optional
    currency: ISO-code, optional
    invoiced: boolean, will only pay recipients that have complete profiles, optional
    annotated: leaves internal bookkeeping in (fields starting with a .), optional
    referrer: the origin of the payment, optional

#Example 1, preparing a payment to a Github URL.

    curl -X POST -H 
    "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -H "Cache-Control: no-cache" 
    -d '{"data":"https:\/\/github.com\/identifi\/identifi", "amount":1000, "currency":"GBP"}' 
    https://api.mobbr.com/api_v1/payments/preview

#Result (arrays reduced to a single element):

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

Confirm payment
---------------

