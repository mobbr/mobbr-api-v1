#Balances API

Retrieve the balances of the authenticated user, a domain or an URL (task).

- [Get user balance] (https://github.com/mobbr/mobbr-api-v1/tree/master/balances#get-user-balance)
- [Get domain balance] (https://github.com/mobbr/mobbr-api-v1/tree/master/balances#get-domain-balance)
- [Get URI balance] (https://github.com/mobbr/mobbr-api-v1/tree/master/balances#get-uri-balance)

##Get user balance

Retrieve the balance for every currency in the wallet of the user. The total amount and converted amounts are in the preferred currency as set in the users profile.

    /api_v1/balances/
    
**Example**

Request

    curl 
    -X GET 
    -H "Accept: application/json" 
    -H "Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" 
    https://test-api.mobbr.com/api_v1/balances

Response

    {
        "result": {
            "balances": [
                {
                    "updatedatetime": "2014-11-14 13:59:07",
                    "amount": "0.15924988",
                    "fee": "-0.00002941",
                    "spendable": "0.15922047",
                    "currency_iso": "BTC",
                    "exchange_rate": "291.6459929844175",
                    "converted_amount": "46.43601207659566",
                    "currency_description": "Bitcoin"
                }
            ],
            "total_amount": "404.5554716666",
            "total_currency_iso": "EUR"
        },
        "message": null
    }

##Get domain balance

Retrieve the balance for every payment in every currency done to an URL of the stated domain. 

    /api_v1/balances/domain
    
**Arguments**

    domain          : domain/host 
    base_currency   : ISO-code
    
**Example**

Request

    curl 
    -X GET 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/balances/domain?domain=github.com&base_currency=BTC

Response

    {
        "result": {
            "balances": [
                {
                    "updatedatetime": "2014-11-14 13:59:07",
                    "exchange_rate": "1",
                    "converted_amount": "0.52",
                    "amount": "0.52000000",
                    "currency_iso": "BTC",
                    "currency_description": "Bitcoin"
                },
                {
                    "updatedatetime": "2014-11-20 13:36:22",
                    "exchange_rate": "0.0034288144670426846",
                    "converted_amount": "36.489786439714955",
                    "amount": "10642.10000000",
                    "currency_iso": "EUR",
                    "currency_description": "Euro"
                }
            ],
            "total_amount": "37.009786439715",
            "total_currency_iso": "BTC"
        },
        "message": null
    }
    
##Get domain balance

Retrieve the balance for every payment in every currency done to an URL 

    /api_v1/balances/uri
    
**Arguments**

    url              
    base_currency   
    
**Example**

Request

    curl 
    -X GET 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/balances/uri?url=https://github.com/identifi/identifi

Response

    {
        "result": {
            "balances": [
                {
                    "updatedatetime": "2014-11-14 13:59:07",
                    "exchange_rate": "1",
                    "converted_amount": "0.52",
                    "amount": "0.52000000",
                    "currency_iso": "BTC",
                    "currency_description": "Bitcoin"
                },
                {
                    "updatedatetime": "2014-11-20 13:36:22",
                    "exchange_rate": "0.0034288144670426846",
                    "converted_amount": "36.489786439714955",
                    "amount": "10642.10000000",
                    "currency_iso": "EUR",
                    "currency_description": "Euro"
                }
            ],
            "total_amount": "37.009786439715",
            "total_currency_iso": "BTC"
        },
        "message": null
    }