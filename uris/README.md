#URIS (TASKS) API

Listing of tasks. In Mobbr an URL becomes a task once it received a pledge or payment. For URL's to receive payments they need to have the required metadata.

- [Get info *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/uris#get-info)
- [List task *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/uris#list-tasks)

##Get info

Get extended information on a URL (task). The info contains some statistics but also the public payin/deposit addresses of the task. Also returned are the payment script and the HTML metadata of the URL.

     GET /api_v1/uris/info

**Arguments**

- *url* 
- *base_currency*(='EUR')
- *include_statistics*(=true)
- *verbose*(=true)

**Example**

Request arguments

    url=https://github.com/mobbr/mobbr-frontend/issues/248

Request

    curl 
    -X GET 
    -H "Accept: application/json" 
    https://api.mobbr.com/api_v1/uris/info?url=https:%2F%2Fgithub.com%2Fmobbr%2Fmobbr-frontend%2Fissues%2F248

Response

    {
        "result": {
            "script": {
                "image": "https://images.weserv.nl?url=ssl:avatars0.githubusercontent.com%2Fu%2F710804%3Fv%3D3%26s%3D400&h=150&w=150&t=square&trim=20",
                "url": "https://github.com/mobbr/mobbr-frontend/issues/248",
                "title": "Github repository mobbr/mobbr-frontend, issue #248",
                "language": "EN",
                "description": "Tile 4 on TASK yellow while script has participants",
                "type": "payment",
                "keywords": [
                    "github.com"
                ],
                "participants": [
                    {
                        "id": "https://github.com/handijk",
                        "role": "Programmer",
                        "share": 26
                    }
                ],
                "message": "Payments done to this URL will immediately be divided (paid out) among currently listed contributors. Contributors can claim their share by registering with Mobbr and connecting their Github-account. Participation is rated based on number of commits",
                ".script-type": [
                    "api"
                ]
            },
            "statistics": {
                "lastpaiddatetime": "2014-11-23 14:10:46",
                "firstpaiddatetime": "2014-10-30 11:57:13",
                "num_recipients": 2,
                "num_senders": "2",
                "num_payments": "2",
                "num_roles": "4",
                "num_currencies": "2",
                "currencies": [
                    "BTC"
                ],
                "roles": [
                    "Issue closer"
                ],
                "participant_num_nationalities": "2",
                "nationalities": [
                    "NL"
                ],
                "participant_age_average": "34.8000",
                "participant_age_standard_deviation": "18.2253",
                "recipient_share_average": "50.000000000000",
                "recipient_share_standard_deviation": "46.497202023841",
                "recipient_amount_average": "6.588303614991542",
                "recipient_amount_standard_deviation": "10.641435907729207",
                "sender_share_average": "100.000000000000",
                "sender_share_standard_deviation": "0.000000000000",
                "sender_amount_average": "13.176608733558046",
                "sender_amount_standard_deviation": "11.823391266441954",
                "amount_total": "26.353217467116092",
                "amount_currency": "EUR",
                "is_pledge": "1",
                "has_unclaimed_shares": "0"
            },
            "metadata": {
                "json": null,
                "canonical": null,
                "title": "Tile 4 on TASK yellow while script has participants · Issue #248 · mobbr/mobbr-frontend · GitHub",
                "description": "mobbr-frontend - The angularjs based frontend app for Mobbr",
                "copyright": null,
                "language": null,
                "image": "https://avatars0.githubusercontent.com/u/710804?v=3&s=400",
                "keywords": null
            },
            "addresses": [
                {
                    "currency": "BTC",
                    "address": "1EHbFhzVpExhhJaewRH5iDQeB5yVNKnGGm"
                }
            ]
        },
        "message": null
    }

##List tasks

List URL's (task)

     GET /api_v1/uris

**Arguments**

- *domain*(=null)
- array *keywords*(=null)
- *language*(=null)
- *username*(=null)
- *base_currency*(='EUR')
- *sort*(=0)
- *offset*(=0)
-*limit*(=100)

**Example** list all tasks (URL's) that are tagged with keywords 'github' and 'php' on which user 'Patrick' was rewarded

Request arguments

    keywords=coding
    keywords=php
    username=Patrick

Request

    curl 
    -X GET 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/uris?keywords=github&keywords=php&username=Patrick

Response

    {
        "result": [
            {
                "url": "https://github.com/mobbr/mobbr-frontend/issues/258",
                "title": "Github repository mobbr/mobbr-frontend, issue #258",
                "description": "my birthday has disappeared again in settings/identity",
                "language_iso": "EN",
                "copyright": null,
                "img_url": "https://images.weserv.nl?url=ssl:avatars0.githubusercontent.com%2Fu%2F710804%3Fv%3D3%26s%3D400&h=150&w=150&t=square&trim=20",
                "currency_iso": "EUR",
                "amount_total": "3.00714992692465",
                "lastpaiddatetime": "2014-11-14 13:59:07",
                "firstpaiddatetime": "2014-11-14 13:59:07",
                "num_payments": "1",
                "match_percentage": "50",
                "num_keywords": "1",
                "num_currencies": "1",
                "currencies": [
                    "BTC"
                ],
                "is_pledge": "0",
                "domain": "github.com",
                "favicon": "https://www.google.com/s2/favicons?domain=github.com"
            }
        ],
        "message": null
    }
