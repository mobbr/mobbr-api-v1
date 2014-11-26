#Payin and payout (xpayments) API

Funding of a wallet. Withdraws to external accounts and wallet. Deposits from external accounts and creditcards. Mobbr supports all major FIAT currencies plus Bitcoin.

The API sends email notifications for succeeded and failed payins and payouts.

- [Withdraw] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#withdraw)
- [Deposit] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#deposit)
- [Withdraw fee] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#withdraw-fee)
- [Deposit fee] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#deposit-fee)
- [List wallet addresses] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#list-addresses)
- [New wallet address] (https://github.com/mobbr/mobbr-api-v1/tree/master/xpayments#new-address)

##Withdraw

Withdraw money to external accounts. 

    POST /api_v1/xpayments/withdraw

**Arguments**

- *currency*, ISO code
- *amount*, floating point amount
- *address*, see below
- *note*, text
- *notification_url*, Not yet implemented

The following address formats are supported:

    {
        "type" : "BITCOIN",
        "address" : "..."
    }
    
    {
        "type" : "IBAN",
        "iban" : "..."
        "name" : "...",
        "address" : "..."
    }
    
    {
        "type" : "GB",
        "accountnumber" : "...",
        "sortcode" : "...",
        "name" : "...",
        "address" : "..."
    }
        
    {
        "type" : "US",
        "accountnumber" : "...",
        "aba" : "...",
        "name" : "...",
        "address" : "..."
    }
        
    {
        "type" : "CA",
        "accountnumber" : "...",
        "institutionumber" : "...",
        "branchcode" : "...",
        "brankname" : "...",
        "name" : "...",
        "address" : "..."
    }
        
    {
        "type" : "OTHER",
        "country" : "...",
        "accountnumber" : "...",
        "bic" : "...",
        "name" : "...",
        "address" : "..."
    }
    
**Example**, a withdraw to an IBAN number

Request arguments

    {
        "amount":25,
        "currency":"EUR",
        "address":
        {
            "type":"IBAN",
            "iban":"NL28INGB0004750773",
            "name":"SAVALLE",
            "address":"APENBERG, ZOETERWOUDE"
        }
    }

Request

    curl 
    -X POST 
    -H "Authorization: Basic UGF0cmljazptMGJicjIwMTE=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"amount":25,"currency":"EUR","address":{"type":"IBAN","iban":"NL28INGB0004750773","name":"SAVALLE","address":"APENBERG, ZOETERWOUDE"}}' 
    https://api.mobbr.com/api_v1/xpayments/withdrawResponse

##Withdraw fee

Return the fee that our payment provider charges for a withdraw.

    POST /api_v1/xpayments/withdraw_fee

**Arguments**

- *type*, "BITCOIN" or "IBAN", "US", "GB", "CA", "OTHER"
- *currency*, ISO code
- *amount*, floating point amount
- *address*, see /api_v1/xpayments/withdraw
    
**Example**

    {
        "amount":25,
        "currency":"EUR",
        "address":
        {
            "type":"IBAN",
            "iban":"NL28INGB0004750773",
            "name":"SAVALLE",
            "address":"APENBERG, ZOETERWOUDE"
        }
    }

Request

    curl 
    -X POST 
    -H "Authorization: Basic UGF0cmljazptMGJicjIwMTE=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"amount":250,"currency":"EUR","address":{"type":"IBAN","iban":"NL28INGB0004750773","name":"SAVALLE","address":"APENBERG, ZOETERWOUDE"}}' 
    https://api.mobbr.com/api_v1/xpayments/withdraw_fee

Response

    {
        "result":
        {
            "amount":1.00,
            "currency_iso":"EUR"
        },
        "message":null
    }

##Deposit

Fund a wallet from an external account/source. Depending on type and user profile, a redirect may be needed. The application should redirect to the URL returned by this method (if present in the response). 

The bankwire payin generates an one-time use address into the account of our payment provider, with a code that indicates to which user the amount should be assigned.

    POST /api_v1/xpayments/deposit

**Arguments**
    
- *type*, "creditcard", "bankwire"
- *currency*, ISO code
- *amount*, floating point amount
- *return_url (=NULL)*, if a redirect was done (creditcard), the called payment page will redirect back to this URL
- *note (=NULL)*, text
- *notification_url (=NULL)*, (not yet implemented)

**Example**, a creditcard payin

Request arguments

    {
        "type":"creditcard",
        "currency":"EUR",
        "amount":12, 
        "return_url":"https://mydomain.com/my_return_page"
    }

Request

    curl 
    -X POST 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -H "Authorization: Basic VXNlcjpwYXNzd29yZDEyMw==" 
    -d '{"type":"creditcard","currency":"EUR","amount":12, "return_url":"https://mydomain.com/my_return_page"}' 
    https://api.mobbr.com/api_v1/xpayments/deposit

Response

    {
        "result":
        {
            "type":"creditcard",
            "url":"https://webpayment.payline.com/webpayment/step2.do?reqCode=prepareStep2&token=165xU3SkIT9W0pHe86151999838282123"
        },
        "message":null
    }
    
##Deposit fee

Return the fee Mobbr charges for a deposit.

    POST /api_v1/xpayments/deposit_fee

**Arguments**

- *type*, "bitcoin" or "bankwire" or "creditcard"
- *currency*, ISO code
- *amount*, floating point amount
    
**Example**

Request arguments

    {
        "type":"creditcard",
        "currency":"EUR",
        "amount":12, 
        "return_url":"https://mydomain.com/my_return_page"
    }

Request

    curl 
    -X POST 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -H "Authorization: Basic VXNlcjpwYXNzd29yZDEyMw==" 
    -d '{"type":"creditcard","currency":"EUR","amount":12, "return_url":"https://mydomain.com/my_return_page"}' 
    https://api.mobbr.com/api_v1/xpayments/deposit_fee

Response

    {
        "result":
        {
            "amount":18.18,
            "currency_iso":"EUR"
        },
        "message":null
    }    

##New address

List all deposit addresses of a wallet

    PUT /api_v1/xpayments/new_address

**Arguments**

- *currency*, the currency for which a new receive address is requested, currently only 'BTC' supported

**Example**

Request arguments

    {
        "currency":"BTC"
    }
    
Request

    curl 
    -X PUT 
    -H "Authorization: Basic UGF0cmljazptMGJicjIwMTE=" 
    -H "Content-Type: application/json" 
    -H "Accept: application/json" 
    -d '{"currency":"BTC"}' 
    https://test-api.mobbr.com/api_v1/xpayments/new_account_address

Response

    {
        "result":
        {
            "address":"1AcUAVWd5w8iXvuJ95m5F8DDRAyhrbWZNn"
        },
        "message":
        {
            "text":"New external address for receiving BTC created",
            "type":"info"
        }
    }

##List addresses

List all deposit addresses of a wallet

     /api_v1/xpayments/list_addresses

**Example**

Request

    curl 
    -X GET 
    -H "Authorization: Basic UGF0cmljazptMGJicjIwMTE=" 
    -H "Accept: application/json" 
    https://test-api.mobbr.com/api_v1/xpayments/list_addresses
    
Response

    {
        "result": [
            {
                "message": "Limit address reuse as much as possible",
                "currency_iso": "BTC",
                "addresses": [
                    {
                        "bitcoin": "12NLcKZdn2P7GAWWimiSedGECW1mWgW5V3"
                    }
                ]
            }
        ],
        "message": null
    }