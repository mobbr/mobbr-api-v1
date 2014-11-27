#User API

Register users, add id's, login, handle oauth, maintain user profile.

- [Create user] (https://github.com/mobbr/mobbr-api-v1/tree/master/user#create-user)
- [Update user] (https://github.com/mobbr/mobbr-api-v1/tree/master/user#update-user)
- [Password login] (https://github.com/mobbr/mobbr-api-v1/tree/master/user#password-login)
- Logout
- Send login link
- Update primary email
- Confirm primary email 
- Add extra email ID
- Login with link
- Get OAUTH ID
- Confirm OAUTH ID
- Delete ID

##The complete user profile

Most USER API methods return the (updated) user data. Some of the returned fields are read-only. For example:

    {
        "result": {
            "username": "Patrick",
            "email": "patrick@mobbr.com",
            "status": "activated",
            "currency_iso": "EUR",
            "registerdatetime": "2013-09-24 02:19:05",
            "lastlogindatetime": "2014-11-27 10:29:48",
            "lastupdateddatetime": "2014-11-27 10:29:48",
            "kyclightdatetime": null,
            "kycregulardatetime": null,
            "updatesdatetime": null,
            "kyc_level": "regular",
            "language_iso": "EN",
            "timezone": "Europe/Amsterdam",
            "bitcoin_address": null,
            "ripple_address": null,
            "firstname": "Patrick",
            "lastname": "Savalle",
            "birthday": "1967-08-16",
            "address": "Flappentap 16, 2736 XS Zoetermeer",
            "country_of_residence": "NL",
            "occupation": "Webdeveloper",
            "income_range": "4",
            "nationality": "NL",
            "mangopay_identity_proof": "VALIDATED",
            "mangopay_address_proof": "PROOF_REQUIRED",
            "companyname": "patricksavalle.com",
            "vat_number": "NL807665915B01",
            "vat_rate": "21",
            "bio": null,
            "invoice_numbering_prefix": "MOBBR-",
            "invoice_numbering_postfix": "-2014",
            "id": [
                "http://profiles.google.com/116413503186570946277",
                "http://www.youtube.com/user/patricksavalle",
                "https://bitcoin.stackexchange.com/users/3361/patrick-savalle",
                "https://facebook.com/patricksavalle",
                "https://github.com/patricksavalle",
                "https://gravatar.com/patricksavalle",
                "https://mobbr.com/#/person/patrick",
                "https://onename.io/patricksavalle",
                "https://serverfault.com/users/185726/patrick-savalle",
                "https://stackoverflow.com/users/1199612/patrick-savalle",
                "https://twitter.com/patricksavalle",
                "mailto:patrick@mobbr.com",
                "mailto:patrick@patricksavalle.com"
            ],
            "thumbnail": "https://secure.gravatar.com/avatar/e6032c3bbb3ece98d2782862594b08c2?size=30&d=https://mobbr.com/img/default-gravatar2.png",
            "setting": {
                "hide_my_outgoing_payments": "0",
                "hide_my_incoming_payments": "0",
                "hide_my_items": "0",
                "send_invoice_download_notification": "1",
                "send_task_invitation_notification": "1",
                "hide_my_email_from_donators": "1",
                "hide_my_email_from_public": "1",
                "send_monthly_reports": "1",
                "send_newsletter": "1",
                "send_json_mention_notification": "1",
                "send_payment_received_notification": "1",
                "send_payment_expired_notification": "1"
            },
            "msg": []
        },
        "message": null
    }
    
##Create user

Create a new user. The API will send an email to have the user confirm the new account.

    PUT	/api_v1/user/register_user_send_login_link	    

**Arguments**

- *username*
- *email* 
- *password*
    
**Example**

Request


##Update user

Create a new user. The API will send an email to have the user confirm the new account. Not all user profiles fields can be changed through this method, other USER API methods will set those.

    POST /api_v1/user/update_user

**Arguments**

- *user*, array of fields, only fields that are changed need to be sent.

    {
        "currency_iso": "EUR",
        "language_iso": "EN",
        "timezone": "Europe/Amsterdam",
        "bitcoin_address": null,
        "ripple_address": null,
        "firstname": "Patrick",
        "lastname": "Savalle",
        "birthday": "1967-08-16",
        "address": "Rampenberg 12, 2715 TD Zoetermeer",
        "country_of_residence": "NL",
        "occupation": "Webdeveloper",
        "income_range": "4",
        "nationality": "NL",
        "companyname": "patricksavalle.com",
        "vat_number": "NL807665915B01",
        "vat_rate": "21",
        "bio": null,
        "invoice_numbering_prefix": "MOBBR-",
        "invoice_numbering_postfix": "-2014",
        "setting": {
            "hide_my_outgoing_payments": "0",
            "hide_my_incoming_payments": "0",
            "hide_my_items": "0",
            "send_invoice_download_notification": "1",
            "send_task_invitation_notification": "1",
            "hide_my_email_from_donators": "1",
            "hide_my_email_from_public": "1",
            "send_monthly_reports": "1",
            "send_newsletter": "1",
            "send_json_mention_notification": "1",
            "send_payment_received_notification": "1",
            "send_payment_expired_notification": "1"
        }
    }
    
**Example**

Request arguments

    {   
        "user":
        {
            "currency_iso" : "BTC",
            "nationality" : "NL"
        }
    }

Request

    curl 
    -X POST 
    -H "Authorization: Basic UGF0cmljazefftMGJicjIsdMTE=" 
    -H "Accept: application/json" 
    -H "Content-Type: application/json" 
    -d '{"user":{"currency_iso":"BTC","nationality":"NL"}}' 
    https://test-api.mobbr.com/api_v1/user/update_user

Response

The complete user profile, see at top

##Password login

Creates a temporary token that can be used by clients instead of the username:password combination that is needed for HTTP BASIC AUTH (which is for other API's calling our API).

This token can be used to authenticate using HTTP BASIC AUTH, leave the username blank and put the token in the password-field. See https://github.com/mobbr/mobbr-api-v1#authentication

    PUT	/api_v1/user/password_login	
    
**Arguments**

- *username*
- *password*
    
**Example**

Request arguments

    {
        "username":"Patrick",
        "password":"flappie"
    }

Request

    curl 
    -X PUT 
    -H "Authorization: Basic UGF0cmljazptMGJicjIwMTE=" 
    -H "Accept: application/json" 
    -H "Content-Type: application/json" 
    -d '{"username":"Patrick","password":"flappie"}' 
    https://test-api.mobbr.com/api_v1/user/password_login

Response

The full user profile PLUS a login token. 

The token expires after a certain time of inactivity.

    {
        "result": {
            "username": "Patrick",
            ...
            "token": "137a08a448f2e148975d4ae0b31cc15c",
            ...
            },
            "msg": []
        },
        "message": {
            "text": "Welcome back, previous login: 2014-11-26 13:47:01",
            "type": "info"
        }
    }
