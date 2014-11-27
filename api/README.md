#General purpose API

Helper methods. All public. 

- [List currencies *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-currencies)
- [List languages *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-languages-countries-timezones)
- [List countries *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-languages-countries-timezones)
- [List timezones *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-languages-countries-timezones)
- [List KYC income ranges *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#kyc-income-ranges)
- [List OAUTH provider *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-oauth-providers)
- [List ID providers *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-id-providers)
- [List event types *public*] (https://github.com/mobbr/mobbr-api-v1/tree/master/api#list-event-types)

##List translations

Return the API languages, currently only 'EN' available.

    GET	/api_v1/api/translations	
    
Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/translations

Response

    {
        "result":
        [
            {
                "code":"EN",
                "name":"English"
            }
        ],
        "message":null
    }

##List currencies

Return all currencies. Indicates which currencies also have wallet support. The other justs have a FOREX rate and can be used by the client to match a locale. 

    GET	/api_v1/api/currencies	
    
Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/currencies

Response

    {
        "result": [
            {
                "exchange_rate": "3.444999933242798",
                "currency_iso": "ZMK",
                "description": "Kwacha",
                "base_currency_iso": "EUR",
                "exchange_rate_datetime": "2014-11-26 12:00:21",
                "exchange_rate_source_url": "http://www.federalreserve.gov",
                "wallet_support": false
            }
        ],
        "message": null
    }

##List languages, countries, timezones

Return the ISO codes of languages and countries and all supported timezones, can be used to allow the user to customize his/her profile.

Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/languages
    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/countries
    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/timezones

##KYC income ranges

For the KYC support (legally accepted identification of a user) it is needed the user sets an incomerange in his/her profile.

	GET	/api_v1/api/kyc_incomeranges
		
Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/kyc_incomeranges

Response

    {
        "result":
        {
            "1":"< EUR 18k",
            "2":"EUR 18k..30k",
            "3":"EUR 30k..50k",
            "4":"EUR 50k..80K",
            "5":"EUR 80k..120k",
            "6":"> EUR 120k"
        },
        "message":null
    }
	
##List OAUTH providers
	
	GET	/api_v1/api/oauth_providers	
	
Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/oauth_providers
    
Response

    {
        "result":
        [
            {
                "host":"bitbucket.com",
                "provider":"bitbucket.com",
                "favicon":"https:\/\/bitbucket.org\/favicon.ico",
                "icon":""
            }
        ],
        "message":null
    }
	
##List ID providers
	
List all ID-providers. If one of the confirmed profile's / id's in the result is a confirmed ID in the user's profile, all other profiles are added as ID's to the user's profile too. ID's can be used to receive payments.
			
	GET	/api_v1/api/id_providers

Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/id_providers
    
Response

    {
        "result":
        [
            {
                "host":"gravatar.com",
                "provider":"gravatar",
                "favicon":"https:\/\/gravatar.com\/favicon.ico",
                "icon":"https:\/\/gravatar.com\/favicon.ico"
            },
            {
                "host":"onename.io",
                "provider":"onename",
                "favicon":"https:\/\/onename.io\/favicon.ico",
                "icon":null
            }
        ],
        "message":null
    }
		
##List event types

All eventtypes that can be returned by the NOTIFICATION API. There are 'user', 'uri', 'domain' notifications, depending on scope or origin of the event. Most events have an associated email notification.
		
	GET	/api_v1/api/event_types

Request

    curl -X GET -H "Accept: application/json" https://api.mobbr.com/api_v1/api/event_types
    
Response

    {
        "result": [
            {
                "type": "user",
                "event": "account_activation",
                "description": "Account activated"
            },
            {
                "type": "user",
                "event": "pre_registration",
                "description": "Account registered"
            },
            {
                "type": "user",
                "event": "invoice_download",
                "description": "Invoice ready for download"
            },
            {
                "type": "user",
                "event": "kyc_request_submitted",
                "description": "Identity verification requested"
            },
            {
                "type": "user",
                "event": "kyc_acceptance",
                "description": "Identity verified"
            },
            {
                "type": "user",
                "event": "kyc_failure",
                "description": "Identity rejected"
            },
            {
                "type": "user",
                "event": "email_address_confirmation",
                "description": "Email change requested"
            },
            {
                "type": "user",
                "event": "email_address_update",
                "description": "New email address set"
            },
            {
                "type": "user",
                "event": "password_update",
                "description": "New password set"
            },
            {
                "type": "user",
                "event": "payment_pledged",
                "description": "Task received a pledge"
            },
            {
                "type": "user",
                "event": "payment_claimed",
                "description": "Unclaimed share(s) claimed"
            },
            {
                "type": "user",
                "event": "payment_sent",
                "description": "Payment sent"
            },
            {
                "type": "user",
                "event": "payment_received",
                "description": "Payment received"
            },
            {
                "type": "user",
                "event": "payment_revocation",
                "description": "Pledge revoked"
            },
            {
                "type": "user",
                "event": "script_mention",
                "description": "Mentioned as participant"
            },
            {
                "type": "user",
                "event": "task_invitation",
                "description": "Invited for a task"
            },
            {
                "type": "user",
                "event": "xpayment_created",
                "description": "Pay-in pending"
            },
            {
                "type": "user",
                "event": "xpayment_failure",
                "description": "Pay-in failed"
            },
            {
                "type": "user",
                "event": "xpayment_received",
                "description": "Pay-in received"
            },
            {
                "type": "user",
                "event": "login_key_generation",
                "description": "Login key requested"
            },
            {
                "type": "user",
                "event": "account_login",
                "description": "Account logged in"
            },
            {
                "type": "user",
                "event": "profile_update",
                "description": "Profile updated"
            },
            {
                "type": "user",
                "event": "login",
                "description": "Logged in"
            },
            {
                "type": "user",
                "event": "logout",
                "description": "Logged out"
            },
            {
                "type": "user",
                "event": "auto_logout",
                "description": "Logged out by system for security reasons"
            },
            {
                "type": "user",
                "event": "profile_update",
                "description": "Profile updated"
            }
        ],
        "message": null
    }	