#Payin and payout (xpayments) API

Funding of a wallet. Withdraws to external accounts and wallet. Deposits from external accounts and creditcards.

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
- *notification_url*, an URL

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

Request

Response

##Withdraw fee

Return the fee Mobbr charges for a withdraw. 

    POST /api_v1/xpayments/withdraw_fee

**Arguments**

- *type*, "BITCOIN" or "IBAN", "US", "GB", "CA", "OTHER"
- *currency*, ISO code
- *amount*, floating point amount
- *address*, see /api_v1/xpayments/withdraw
    
**Example**

Request arguments

Request

Response

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
_ *notification_url (=NULL)*, (not yet implemented)

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
- *address*, see /api_v1/xpayments/withdraw
    
**Example**

Request arguments

Request

Response

##List addresses

List all deposit addresses of a wallet

    GET /api_v1/xpayments/list_addresses

**Arguments**

**Example**

Request arguments

Request

Response

##New address

List all deposit addresses of a wallet

     /api_v1/xpayments/list_addresses

**Arguments**

**Example**

Request arguments

Request

Response

