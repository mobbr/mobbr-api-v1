#Payments API

Make payments and pledges to usernames, email-addresses, OAUTH profiles, payment scripts and URL's.

- [create payment *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#create-payment)
- [confirm payment] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#confirm-payment)
- [list payments] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-payments)
- [list payments for domain] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-payments-for-domain)
- [list payments for URL] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-payments-for-url)
- [inspect payment *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#inspect-payment)
- [list unclaimed shares] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-unclaimed-shares)
- [revoke unclaimed shares] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#revoke-unclaimed-shares)
- [list claimable payments] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-claimable-payments)
- [claim payments *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/payments#claim-payment)
- [list pledges](https://github.com/mobbr/mobbr-api-v1/tree/master/payments#list-pledges)
- [delete pledges](https://github.com/mobbr/mobbr-api-v1/tree/master/payments#delete-pledges)

##Create payment

Generates a payment preview which lists the actual payment properties and recipients that would be made if it were to be confirmed. Use `hash` from the result to confirm the payment with `/api_v1/payments/confirm` within 10 minutes.

    POST /api_v1/payments/preview

**Arguments**

    data                : email,URL,JSON,username
    amount (=NULL)      : positive floating point
    currency (=NULL)    : ISO-code
    invoiced (=FALSE)   : TRUE or FALSE, will only pay recipients that have complete profiles
    annotated (=TRUE)   : TRUE or FALSE, leaves internal bookkeeping in (fields starting with a .)
    referrer (=NULL)    : URL, the origin of the payment

**The data-argument**

This method accepts the following recipient-types/id's in the data argument:

- an username, e.g. `Patrick`
- an email address, e.g. `me@mail.com`, if the recipient is no member yet, a registration link will be sent.
- a twitter username, e.g. `@patricksavalle`, if the recipient is no member yet, instructions will be tweeted.
- a telephone number, e.g. `+3106275638965`, if the recipient is no member yet, an registration link will be texted. 
- any other ID register with Mobbr, such as online profiles, e.g. `https://github.com/patricksavalle`, use the [USER API](https://github.com/mobbr/mobbr-api-v1/tree/master/user) to list, delete, add or confirm id's to the profile of an user.

The preview method can also prepare complex multi-recipient payments by sending it a payment script in the data argument

- a payment script, see [specification](https://github.com/mobbr/mobbr-api-v1/tree/master/specifications) and [examples](https://github.com/mobbr/mobbr-api-v1/tree/master/examples)

All these types of payments are 'normal' payments that do not show up in any public method of the API. Public payments to tasks are done by sending the API an URL of a HTML page with Mobbr support: 

- an URL of an HTML page e.g. `https://github.com/mobbr/mobbr-api-v1` (any URL that has Mobbr support)

In this case the API will do a callback to the URL to discover the payment script in the metadata of the HTML. See https://github.com/mobbr/mobbr-api-v1/tree/master/specifications

![callback mechanism](https://mobbr.com/img/scheme.png)

**The preview**

The result of this method is the actual payment that can be confirmed. Unpayable recipients will be ommitted, reasons recipients can not be paid include:
- their share is below the threshold / too small
- non-existent or unresolvable
- insufficient KYC-levels / incomplete profile
- no invoicing information in combination with an invoiced payment

A preview can be requested even if no amount or currency are supplied. 

**Example 1**, previewing only recipients of a payment to a Github URL, no amount or currency specified.

Request data

    {
        "data":"https://github.com/identifi/identifi"
    }

Request

    curl 
    -X POST 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"data":"https://github.com/identifi/identifi"}' 
    https://test-api.mobbr.com/api_v1/payments/preview

Response

    {
        "result": 
        {
            "hash": null,
            "script": 
            {
                "image": "https://avatars3.githubusercontent.com/u/3898718?v=3&s=400",
                "url": "https://github.com/identifi/identifi",
                "language": "EN",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "participants": 
                [
                    {
                        "id": "mailto:patrick@mobbr.com",
                        "role": "Platform-owner",
                        "share": "1",
                        ".x-id": "Patrick",
                        ".percentage": "...",
                        ".gravatar": "7194e8d48fa1d2b689f99443b767316c"
                    }
                ],
                "keywords": 
                [
                    "software development"
                ],
                "message": "text",
                "type": "donation",
                ".script-type": 
                [
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
    
*Note that in the response is the normalized, canonical URL that was actually paid! This URL can be different from the the input URL. The API will follow the canonical links in pages and remove unnecessary characters from URL's.*    
    
**Example 2**, preparing actual payment to a username, amount and currency specified.

Request data

    {
        "data":"Patrick", 
        "amount":10, 
        "currency":"GBP"
    }

Request

    curl 
    -X POST 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"data":"Patrick", "amount":10, "currency":"GBP"}' 
    https://test-api.mobbr.com/api_v1/payments/preview

Response

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

The data argument can also contain JSON scripts that describe a complex payment, for example, if this is the payment script:

    {
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
    }
    
Then the request becomes (you need to escape the quotes in the script and remove the line breaks):
    
Request data

    {
        "amount": 10,
        "currency": "GBP",
        "data": "{\"participants\" : [{\"id\": \"mailto:patman@mobbr.com\",\"role\": \"author\",\"share\": \"3\"},{\"id\": \"mailto:johnny@mobbr.com\",\"role\": \"author\",\"share\": \"3\"},{\"id\": \"mailto:info@zaplog.nl\",\"role\": \"platform\",\"share\": \"1\"}]}"
    }
    
Request
  
    curl 
    -X POST 
    -H "Accept: application/json" 
    -H "Content-Type: application/json" 
    -d '{"amount":10,"currency":"GBP","data":"{\"participants\" : [{\"id\": \"mailto:patman@mobbr.com\",\"role\": \"author\",\"share\": \"3\"},{\"id\": \"mailto:johnny@mobbr.com\",\"role\": \"author\",\"share\": \"3\"},{\"id\": \"mailto:info@zaplog.nl\",\"role\": \"platform\",\"share\": \"1\"}]}"}' 
    http://api.mobbr.dev/api_v1/payments/preview    

Response
    
    {
        "result": {
            "hash": "11d9c687e1c49977ae097c4f5fb34a96",
            "script": {
                "participants": [
                    {
                        "id": "mailto:patman@mobbr.com",
                        "role": "author",
                        "share": "3",
                        ".x-id": "b3f8ba59ce297638b64febad1885a0ca",
                        ".amount": "4.2857142857143",
                        ".percentage": "42.857142857143",
                        ".gravatar": "2343276557bfc20f7604aa2293aa4867"
                    },
                    {
                        "id": "mailto:johnny@mobbr.com",
                        "role": "author",
                        "share": "3",
                        ".x-id": "81f4f9edfdbaeed554a09111ff5a9b03",
                        ".amount": "4.2857142857143",
                        ".percentage": "42.857142857143",
                        ".gravatar": "94ebc4049da2e0f17648c19acc8c3632"
                    },
                    {
                        "id": "mailto:info@zaplog.nl",
                        "role": "platform",
                        "share": "1",
                        ".x-id": "f7daa9c87202a04c3c84b35afd2b1618",
                        ".amount": "1.4285714285714",
                        ".percentage": "14.285714285714",
                        ".gravatar": "41404736646e0c4a861ddfaa8579522e"
                    }
                ],
                "type": "donation",
                "message": "Consider making a donation to this item.",
                ".amount": 10,
                ".currency": "GBP",
                ".invoiced": false,
                ".referrer": null
            },
            "url": null
        },
        "message": null
    }

For the full specification, see: https://github.com/mobbr/mobbr-api-v1/tree/master/specifications

##Confirm payment

Confirms a payment preview, actually distributing money and informing the recipients. Returns the confirmed payment.

    PUT /api_v1/payments/confirm
   
**Arguments**

    hash    : the hash that was returned by /api_v1/payments/preview
    
**Example**

Request data

    {
        "hash":"178faa2f3d321a662e86cc42e2acd508"
    }

Request
  
    curl 
    -X PUT 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -d '{"hash":"178faa2f3d321a662e86cc42e2acd508"}' 
    https://test-api.mobbr.com/api_v1/payments/confirm
    
Response

    {
        "result": 
        {
            "payment_id": "302302b4dd1280e2e9a38793de92f210",
            "payment_script": 
            {
                "image": "https://avatars3.githubusercontent.com/u/3898718?v=3&s=400",
                "url": "https://github.com/identifi/identifi",
                "language": "EN",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "participants": 
                [
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
                "keywords": 
                [
                    "github.com"
                ],
                "message": "Text.",
                "type": "donation",
                ".script-type": 
                [
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

##List payments

List all payment for the authenticated user.

    GET /api_v1/payments

**Arguments**

    search(=null)       : part of keyword, recipient or date
    from_date(=null)    : YYYY-MM-DD HH:MM:SS
    to_date(=null)      : YYYY-MM-DD HH:MM:SS
    offset(=0)          
    limit(=100)    

**Example**

Request
  
    curl 
    -X GET 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments?search=Yoursearchterm&from_date=2014-10-28%2001:12  

Response

    {
        "result": 
        [
            {
                "id": "302302b4dd1280e2e9a38793de92f210",
                "url": "https://github.com/identifi/identifi",
                "datetime": "2014-11-20 13:36:22",
                "amount": "-1.00000000",
                "currency_iso": "EUR",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "memo": null,
                "invoiced": "0",
                "copyright": null,
                "language": "EN",
                "since_last_login": "1"
            }
        ]
    }
    
##List payments for domain

List all payment for the specified domain / host.

    GET /api_v1/payments/domain

**Arguments**

    domain      : the domain (host) for which to list the payments
    offset(=0)          
    limit(=100)    

**Example**

Request
  
    curl 
    -X GET 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/domain=github.com

Response

    {
        "result": 
        [
            {
                "url": "https://github.com/identifi/identifi",
                "datetime": "2014-11-20 13:36:22",
                "amount": "1.00000000",
                "currency_iso": "EUR",
                "title": "Github repository identifi/identifi"
            }
        ],
        "message": null
    }
    
##List payments for URL

List all payment for the specified URL.

    GET /api_v1/payments/uri

**Arguments**

    url
    offset(=0)          
    limit(=100)    

**Example**

Request
  
    curl 
    -X GET 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/uri?url=https://github.com/identifi/identifi

Response

    {
        "result": 
        [
            {
                "id": "302302b4dd1280e2e9a38793de92f210",
                "url": "https://github.com/identifi/identifi",
                "datetime": "2014-11-20 13:36:22",
                "amount": "1.00000000",
                "currency_iso": "EUR",
                "is_pledge": "0",
                "receiver": [
                    {
                        "gravatar": "7194e8d48fa1d2b689f99443b767316c",
                        "username": "Ernesto"
                    }
                ],
                "senders": [
                    {
                        "gravatar": "e6032c3bbb3ece98d2782862594b08c2",
                        "username": "Patrick"
                    }
                ]
            }
        ],
        "message": null
    }
    
##Inspect payment
 
Extended payment information

    GET /api_v1/payments/info
    
**Arguments**

    id                          : The UUID of the payment
    include_receivers(=true)    : Return list of recipients
    include_senders(=true)      : Return list of senders 
    include_keywords(=true)     : List all keywords
    
**Example**

Request

    curl 
    -X GET 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/info?id=302302b4dd1280e2e9a38793de92f210

Response

    {
        "result": 
        {
            "id": "302302b4dd1280e2e9a38793de92f210",
            "uri": "https://github.com/identifi/identifi",
            "ref_uri": null,
            "img_uri": "https://images.weserv.nl?url=ssl:avatars3.githubusercontent.com%2Fu%2F3898718%3Fv%3D3%26s%3D400&h=150&w=150&t=square&trim=20",
            "paiddatetime": "2014-11-20 13:36:22",
            "approveddatetime": null,
            "amount": "-1.00000000",
            "currency_iso": "EUR",
            "invoiced": "0",
            "callback_url": null,
            "callback_datetime": null,
            "title": "Github repository identifi/identifi",
            "description": "Identifi implementation built on Bitcoin code",
            "copyright": null,
            "language_iso": "EN",
            "mime_type": null,
            "memo": null,
            "domain": "github.com",
            "favicon": "https://www.google.com/s2/favicons?domain=github.com",
            "keywords": 
            [
                {
                    "keyword": "github.com",
                    "language_iso": "EN"
                }
            ],
            "receivers": 
            [
                {
                    "gravatar": "e30d5696b25f0dcd1bdf609753602977",
                    "username": "me@email.com",
                    "share_id": "3264",
                    "amount": "0.71657143",
                    "currency_iso": "EUR",
                    "share": "71.65714286",
                    "role": "Contributor",
                    "unclaimed": "https://github.com/mmalmi",
                    "unclaim_id": "3264"
                }
            ],
            "senders": 
            [
                {
                    "gravatar": "e6032c3bbb3ece98d2782862594b08c2",
                    "username": "Patrick",
                    "share_id": null,
                    "amount": "-1.00000000",
                    "currency_iso": "EUR",
                    "share": "100.00000000",
                    "role": "Payer"
                }
            ]
        },
        "message": null
    }
    
*notes* 

- *Payment shares that have an unclaim_id have not yet been claimed by the recipient and can be deleted by the payer using `DELETE /api_v1/payments/unclaimed_shares`*

##List unclaimed shares

List the shares that are not yet claimed by their recipients and can still be reclaimed. Shares are unclaimed when paid to users that are not yet member of Mobbr. Once the users registers the shares are assigned to his/her account and can not longer be revoked.

	GET	/api_v1/payments/unclaimed_shares	
	
**Example**
	
Request

    curl 
    -X GET 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/unclaimed_shares
	
Response

    {
        "result": 
        [
            {
                "currency_iso": "EUR",
                "amount": "0.71657143",
                "share_id": "3264",
                "recipient_id": "mailto:siriuis@dfriki.if",
                "payment_id": "302302b4dd1280e2e9a38793de92f210",
                "url": "https://github.com/identifi/identifi",
                "datetime": "2014-11-20 13:36:22",
                "title": "Github repository identifi/identifi",
                "description": "Identifi implementation built on Bitcoin code",
                "memo": null,
                "username": "0b2d4a0e4cbaa624002495e884d2cfa0",
                "gravatar": "e30d5696b25f0dcd1bdf609753602977"
            }
        ]
    }
	
##Revoke unclaimed shares

Reclaim unclaimed shares / revoking shares. Only shares listed by `GET /api_v1/payments/unclaimed_shares` can be revoked. 

    DELETE /api_v1/payments/unclaimed_shares
    
**Arguments**

    array share_ids

**Example**

Request

    curl 
    -X DELETE 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/unclaimed_shares?share_ids=345345345&share_ids=3454564

Response

    {
        "result": [],
        "message": 
        {
            "text": "0 share(s) revoked",
            "type": "info"
        }
    }
    
##List claimable payments

List all 'claimable payments' (pledges) for an URL. These payments can be claimed by calling `PUT /api_v1/payments/claim` or by registering with the specified email address

    GET /api_v1/payments/claimable

**Arguments**

    url_or_email : the URL, ID (=URL) or email for which the pledges or unclaimed payments are listed

**example**

Request

    curl 
    -X GET 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/claimable?url_or_email=https://github.com/mobbr/mobbr-frontend/issues/189

Response

    {
        "result": 
        [
            {
                "url": "https://github.com/mobbr/mobbr-frontend/issues/189",
                "title": "Github repository mobbr/mobbr-frontend, issue #189",
                "description": "Sharing section on TASKS",
                "id": "b75170c7fb697c4613168cb7b87522aa",
                "invoiced": "0",
                "ref_uri": "https://mobbr.com/#/task/aHR0cHM6Ly9naXRodWIuY29tL21vYmJyL21vYmJyLWZyb250ZW5kL2lzc3Vlcy8xODk=/pay",
                "amount": "-150.00000000",
                "currency_iso": "EUR"
            }
        ],
        "message": null
    }

##Claim payments

Trigger all pledges for an URL. The API will do a callback to the URL to discover the payment script and use the script to pay all recipients listed. If the script is still in pledge mode, nothing will happen.

    PUT /api_v1/payments/claim

**Arguments**

    URL : the URL for which all pledges or unclaimed payments are to be paid out to the designated recipients
    
**Example**

Request data

    {
        "url":"https://github.com/mobbr/mobbr-frontend/issues/189"
    }

Request
  
    curl 
    -X PUT 
    -H "Content-Type: application/json" 
    -d '{"url":"https://github.com/mobbr/mobbr-frontend/issues/189"}' 
    https://test-api.mobbr.com/api_v1/payments/claim
    
Response

    {
        "result":null,
        "message": 
        {
            "type":"error",
            "text":"Cannot claim this pledge because task is still in pledging mode"
        }
    }

##List pledges

List all payments that can still be deleted revoked by the user (such as pledges). 

    GET /api_v1/payments/pledged

**Arguments**

    offset(=0)          
    limit(=100)    

Request

    curl 
    -X GET 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/pledged

Response

    {
        "result": 
        [
            {
                "id": "006d0653d76f7fa398cef8e0c0fcb315",
                "domain": null,
                "url": null,
                "paiddatetime": "2014-11-14 11:43:05",
                "amount": "-1.00000000",
                "currency_iso": "EUR",
                "title": null,
                "description": null,
                "callback_url": null
            }
        ],
        "message": null
    }

##Delete pledges

Delete / revoke a pledge this user made to an URL. The API will do a callback to all URL's whose pledges are revoked to notify all current participants of this event.

    DELETE	/api_v1/payments/unclaimed_shares
    
**Arguments**

    array ids   : ID's of payments (have a MD5 format)

**Example**

Request

    curl 
    -X DELETE 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/payments/unclaimed_shares?ids=006d0653d76f7fa398cef8e0c0fcb315&ids=006d0653d76f7fa398cef8e0c0fcb315

Response

    {
        "result": [],
        "message": 
        {
            "text": "0 payments(s) revoked",
            "type": "info"
        }
    }
    
