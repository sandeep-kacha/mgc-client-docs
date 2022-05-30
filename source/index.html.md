---
title: Merit Gitfcards Platform API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

search: true

code_clipboard: true

meta:
  - name: Merit Giftcards API
    content: Documentation for  the Merit Giftcards API
---

# Introduction

Welcome to the Merit API! You can use our API to access Merit Giftcard platform. 

This API has been created for partners to connect to the Gift Card Platform to create gift cards and send it to customers

- Current version of the API is V1 and the endpoints will be prefixed by <code>v1/</code>.


# Authentication

> To authorize, use this code:



```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: Bearer secret_key"
```


> Make sure to replace `secret_key` with your API key.

Merit uses token based authentication. API keys to allow access to the API. 

The client needs to send HTTP requests with the Authorization header that contains the word ‘Bearer’ followed by a space and the Key Secret.

Merit expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer secret_token`

<aside class="notice">You must replace <code>secret_token</code> with your API key.</aside>


Info on Vendor ID 

<aside class="notice">All partners who require access to apis will be allocated a vendor ID. Some of the requests require a vendor ID to be passed to get meaningful results. A vendor ID will be allocated by the Merit IT department</aside>


<aside class="warning">The IP of the client's test and production environment needs to be shared to Merit Team to whitelist and share the Test credentials</aside>




## Base URL

Base URL for Merit API endpoints

Description | URL 
----------- | ---
Staging | https://testapi.meritincentives.com/api/v1  
Host | https://testapi.meritincentives.com/ 
Content-Type | application/json 
Authorization | Bearer secret_token 

# Giftcards

## Create a Giftcard

This request is for creating gift cards for a particular brand. The corresponding campaign ID for the brand needs to be known. This will be provided by Merit.

### HTTP Request

`POST https://testapi.meritincentives.com/api/v1/currency/giftcards`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
quantity | Yes* | Gift Card quantity
denomination | Yes* | Gift Card value
campaign_id | Yes* | Product ID provided by Merit
vendor_id | Yes* | Vendor ID provided by Merit
request_id | Optional | Your transaction reference number. Querying the same transaction reference again, will provide the original response. This is prevent duplicate gift card creation and help at the reconciliation process
partner_name | Optional | Name of the client's program name or client or partner. This can be used if client has multiple programs getting fulfilled via same VID and secret key
card_supplier | Optional | Card Supplier name
card_expiry | optional | Expiry date of card will be changed as per the date you send otherwise it will be generated from MGC


```shell
curl --location --request POST 'https://testapi.meritincentives.com/api/v1/currency/giftcards' \
--header 'Content-Type: application/json' \
--data-raw '{
    "giftcard": {
        "quantity": 1,
        "denomination": "1",
        "campaign_id": "product_18443216549891f6a",
        "vendor_id": "V_321654",
        "request_id": "OD3FF76852BVB",
        "partner_name": "random-Test37",
        "card_supplier": "Microsite",
        "card_expiry": "28-02-2033" //(dd-mm-yyyy)
    }
}'
```
> The above command returns JSON structured like this:

```json
{
   "data":{
      "giftcard":{
         "batch_id":"batch_72acf03a03212",
         "created_at":"2021-06-16T05:55:21.021Z"
      }
   },
   "status":"OK",
   "code":200,
   "message":"Giftcards created successfully",
   "errors":[
      
   ]
}
```


<aside class="notice">
Batch ID returned in response contains the information of all gift cards. This can be passed in the "Get gift card request" to return details about the gift card.
</aside>


## Fetch Giftcards

### HTTP Request

`GET https://testapi.meritincentives.com/api/v1/currency/giftcards`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
vendor_id | Yes* | Vendor ID provided by Merit
batch_id | Yes* | This is obtained from the create gift card response


```shell
curl --location --request GET 'https://testapi.meritincentives.com/api/v1/currency/giftcards?vendor_id=Vendor ID provided by Merit&batch_id=This is obtained from the create gift card response' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token'
```


> The above command returns JSON structured like this:

```json
{
   "data":{
      "total_giftcards":1,
      "giftcards":[
         {
            "active":false,
            "batch_id":"batch_592e129e59b3216545b6",
            "batch_created_at":"2021-04-08T04:48:09.000Z",
            "request_id":null,
            "giftcard_number":"54ABCD339569",
            "card_id":"203216548-3185275",
            "original_value":1,
            "remaining_value":1,
            "campaign_id":"product_1844f32165491f6a",
            "expiration_date":"2022-04-08",
            "currency":"US Dollar",
            "currency_code":"USD",
            "convert_rate":1,
            "style_image":"https://development-mylist- assets.s3.amazonaws.com/in/giftcards/styles/2e9a89c1ccf51eadc32c13cf5f8ebd6776fbe3671/test- style-shanti-7cc9c0c9c410b20310.png",
            "giftcard_url":"http://localhost:8080/giftcards/preview/key_73b0s321d6597c32167c00b2413",
            "display_custom_url":"False"
         }
      ]
   },
   "status":"OK",
   "code":200,
   "message":null,
   "errors":[
      
   ]
}
```

- The response contains the gift card URL and also provides a style image which points to the location where the gift card image template is stored.

<aside class="notice">
We would recommend using Merit's dynamic <code>giftcard_url</code> in the reponse which has the Giftcard image, barcode and T&c, exam0le of a dynmaic <code>giftcard_url</code>  <code>https://testapi.meritincentives.com/giftcards/preview/key_73b0321dsf65432fsd5462413</code>
</aside>

- This <code>giftcard_url</code> can be embedded in client’s email templates to avoid issues in the redemption process as the merchants store locations . The URL template can be branded with client logo upon request to Merit Operations team.

- We return all records based on details, we don’t apply any pagination based on response parameters like, <code>current_page</code>, <code>next_page</code>, <code>prev_page</code> etc

- These are the data <code>total_pages, current_page, next_page, prev_page, giftcard_key, transaction_type, order_refe_no,challenge_token </code> we have removed recently from the response



## Balance Check 


```shell
curl --location --request GET 'https://testapi.meritincentives.com/api/v1/currency/giftcards/balance_check?card_number=This is obtained from the get gift card response as giftcard_number&card_id=This is obtained from the get gift card response&card_key=This is obtained from the get gift card response as giftcard_key' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token'
```


> The above command returns JSON structured like this:

```json
{
   "data":{
      "giftcard_number":"5432114755339569",
      "giftcard_currency":"USD",
      "original_value":"1.0",
      "remaining_value":"1.0",
      "expiry_date":"08-04-2022",
      "user_first_name":"Bhomit",
      "user_last_name":"Tagline",
      "user_email":"tagline.davara@gmail.com",
      "user_phone":"8460757259",
      "giftcard_image":"https://development-mylist-assets.s3.amazonaws.com/in/giftcards/styles/4cbe3d6227abe57a801103c2e9a89c1ccf51eadc32c13cf5f8 ebd6776fbe3671/test-style-shanti-7cc9c0c9c410b20310.png",
      "mylist_card":true
   },
   "status":"OK",
   "code":200,
   "message":null,
   "errors":[
      
   ]
}
```

This endpoint retrieves the balance of any gift cards.
### HTTP Request

`GET https://testapi.meritincentives.com/api/v1/currency/giftcards/balance_check`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
card_number | Optional | This is obtained from the get gift card response as giftcard_number
card_id | Optional | This is obtained from the get gift card response
card_key | Optional | This is obtained from the get gift card response as giftcard_key

<aside class="warning">One parameter is required among of above three</aside>

## Fetch Batch ID


```shell
curl --location --request GET 'https://testapi.meritincentives.com/api/v1/currency/giftcards/get_batch?vendor_id=enter vendor id&request_id=request id of card' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token'
```


> The above command returns JSON structured like this:

```json
{
   "data":{
      "distributor_giftcard_batch":[
         {
            "batch_id":"batch_7bdf1265ab8esdf213880",
            "created_at":"2022-01-18"
         }
      ]
   },
   "status":"OK",
   "code":200,
   "message":null,
   "errors":[
      
   ],
   "error_message":[
      
   ]
}
```

This endpoint retrieves a Batch ID
### HTTP Request

`GET https://testapi.meritincentives.com/api/v1/currency/giftcards/get_batch`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
vendor_id | Yes* | Vendor ID provided by Merit
request_id | Yes* | Request ID of giftcard

## Redeem a Giftcard

```shell
  curl --location --request POST 'https://testapi.meritincentives.com/api/v1/currency/giftcards/spend' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token' \
--data-raw '{
    "giftcard": {
        "card_number": "46544843246545401",
        "email": "milan.tagline@gmail.com",
        "store_id": "21003",
        "first_name": "NA",
        "last_name": "NA",
        "phone": "NA",
        "redemption_amount": "1"
    }
}'
```

> The above command returns JSON structured like this:

```json
{
   "data":{
      "transaction":{
         "id":432222,
         "reference_no":"transaction_1622451681_840325",
         "transaction_type":"REDEMPTION",
         "transaction_amount":"1.0",
         "transaction_date":"2021-05-31",
         "transaction_timestamp":"2021-05-31T09:01:22.900Z",
         "transaction_description":null,
         "giftcard_url":"http://localhost:8080/giftcards/preview/key_413254867a42426560",
         "giftcard_number":"46544843246545401"
      },
      "data":{
         "spend":{
            "remaining_value":"39.7",
            "card_status":"REDEMPTION"
         },
         "owner":{
            "first_name":"milan",
            "last_name":"tagline",
            "email":"milan.tagline@gmail.com",
            "phone":"8141666204"
         }
      }
   },
   "status":"OK",
   "code":200,
   "message":"Giftcard redeemed successfully",
   "errors":""
}
```
This API request is to redeem a giftcard.

### HTTP Request
`https://testapi.meritincentives.com/api/v1/currency/giftcards/spend`

### URL parameters

Parameter | Required | Description
--------- | ------- | -----------
card_number | Yes* | Giftcard Number
store_id | Yes* | Store Reference number.For multiple stores, each store will have unique Store ID which will be used for tracking the redemption.Store_id should be preferably shared by Merchant for configuring in Merit System
card_pin | Optional | Giftcard pin
redemption_amount | Yes* | Redemption amount
transaction_description_id | Optional | Transaction description
note | Optional | Note about transaction
email | Yes* | Owner email (required only for first Redemption).For scenarios where email ID is not available, pass the Store email ID
first_name | Optional | Giftcard Owner first name
last_name | Optional | Giftcard Owner Last name
phone | Optional | Giftcard Owner phone number

## Cancel Redemption

```shell
  curl --location --request POST 'https://testapi.meritincentives.com/api/v1/currency/giftcards/cancel_transaction' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token' \
--data-raw '{
    "transaction_id": "transaction_id",
    "store_id":"store_id"
}'
```

> The above command returns JSON structured like this:

```json
{
   "data":{
      "transaction":{
         "id":432227,
         "reference_no":"transaction_1622453185_866131",
         "transaction_type":"CANCEL_REDEMPTION",
         "transaction_date":"2021-05-31",
         "transaction_timestamp":"2021-05-31T09:26:25.571Z",
         "transaction_amount":"1.0"
      }
   },
   "status":"OK",
   "code":200,
   "message":"Transaction successfully Cancelled",
   "errors":""
}
```
This API request is to cancel transaction of redemption of a giftcard.

### HTTP Request
`https://testapi.meritincentives.com/api/v1/currency/giftcards/cancel_transaction`

### URL parameters

Parameter | Required | Description
--------- | ------- | -----------
transaction_id | Yes* | Redeemed transaction ID
store_id | Yes* | same Store id from that transaction was created

<aside class="notice">Cancel API is only a system check and balance that a redeemed transaction can be canceled only within 30 minutes of the actual transaction and to kindly ensure that this error message is handled at your end. This is to avoid any fraud or incorrect reconciliation</aside>

## Sell a Gift Card

```shell
  curl --location --request POST 'https://testapi.meritincentives.com/api/v1/currency/giftcards/sell' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token' \
--data-raw '{
    "sell": {
        "sender_first_name": "Sender",
        "sender_last_name": "User",
        "sender_email": "sender@meritincentives.com",
        "sender_phone": "9876543210",
        "receiver_first_name": "Receiver",
        "receiver_last_name": "User",
        "receiver_email": "receiver@gmail.com",
        "receiver_phone": "9876543210",
        "campaign_id": "product_1844fe956239d91f6a",
        "card_denomination": "1",
        "request_id": "TestDubaiPoints",
        "vendor_id": "v_v457sd",
    }
}'
```

> The above command returns JSON structured like this:

```json
{
   "data":{
      "giftcard":{
         "batch_id":"batch_3ca485a698425c62fb",
         "created_at":"2020-04-16T07:43:07.000Z"
      }
   },
   "status":"OK",
   "code":200,
   "message":"Giftcard sold successfully",
   "errors":[
      
   ]
}
```
This API request is to sell a giftcard.



<aside class="warning">The sell gift cards API is applicable only if Merit will be fulfilling the Giftcard on behalf of the clients to their end customers</aside>

<aside class="notice">The sold giftcard generated BATCH ID in the response can check from this api
Get Batch id</aside>


### HTTP Request
`https://testapi.meritincentives.com/api/v1/currency/giftcards/sell`

### URL parameters

Parameter | Required | Description
--------- | ------- | -----------
campaign_id | Yes* | Product ID (Required id)
vendor_id | Yes* | Vendor id given by MGC
card_denomination | Yes* | Value of Card
request_id | Yes* | Request Id of Giftcard
sender_first_name | Optional | Sender’s first name
sender_last_name | Optional | Sender’s last name
sender_email | Optional | Sender’s email
sender_phone | Optional | Sender's phone
receiver_first_name | Yes* | Receiver’s first name
receiver_last_name | Yes* | Receiver’s last name
receiver_email | Yes* | Receiver’s email
receiver_phone | Yes* | Receiver’s Phone
note | Optional | Note about transaction
email | Yes* | Owner email (required only for first Redemption).For scenarios where email ID is not available, pass the Store email ID
first_name | Optional | Giftcard Owner first name
last_name | Optional | Giftcard Owner Last name
phone | Optional | Giftcard Owner phone number


## Fetch Vendor Balance


```shell
curl --location --request GET 'https://testapi.meritincentives.com/api/v1/currency/giftcards/vendor_balance?vendor_id=enter vendor id given by Merit' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token'
```


> The above command returns JSON structured like this:

```json
{
   "data":{
      "vendor":"currency alce_Live",
      "vendor_id":"VID_asbLIVE",
      "active":true,
      "amount":49982465.870000035,
      "total_transaction_amount":"238913.14099",
      "float_amount":253121.48,
      "balance":14208.339009999996
   },
   "status":"OK",
   "code":200,
   "message":null,
   "errors":[
      
   ],
   "error_message":[
      
   ]
}
```

This API will return the total of transaction amount and float amount of distributor if available.
### HTTP Request

`GET https://testapi.meritincentives.com/api/v1/currency/giftcards/vendor_balance`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
vendor_id | Yes* | Vendor ID provided by Merit



## Check Inventory


```shell
curl --location --request GET 'https://testapi.meritincentives.comapi/v1/currency/giftcards/check_inventory?campaign_id=Product ID provided by Merit' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer secret_token'
```


> The above command returns JSON structured like this:

```json
{
   "data":{
      "campaign_id":"product_1844fe956239d91f6a",
      "campaign_name":"TestVariety Shanti",
      "denominations":[
         {
            "value":10,
            "inventory":2
         }
      ]
   },
   "status":"OK",
   "code":200,
   "message":null,
   "errors":[
      
   ],
   "error_message":[
      
   ]
}
```

API will return the available manual inventory with available denominations.
### HTTP Request

`GET https://testapi.meritincentives.com/api/v1/currency/giftcards/check_inventory`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
campaign_id | Yes* | Product ID provided by Merit




# Errors


Merit API uses the following error codes:


Type |  Code |  Message | Descrption
---------- | ------- | ------------- | -------------
Bad Request| 400 | Store not valid or anything else |Bad Request -- Your request is invalid.
Processable Entity | 422 | Server error | Unauthorized -- Your API key is wrong.
Internal server error | 500 | Internal server error | Internal server error
Not Found | 404 | Resource not found | Resource not found
Request Error | 600 | Request issue | Request issue 
Unauthorized | 401 | You need to login to continue | Wrong secret token
Unauthorized | 401 | Your IP is not whitelisted | Your IP is not whitelisted


### Sample Error responses 

> When store not found

```json
    {
      "status": "Bad Request",
      "code": 400,
      "message": "store not available", "errors": "store not available"
    }
```

> When card not found

```json
    {
      "status": "Bad Request",
      "code": 400,
      "message": "Invalid Card Number!", "errors": "Invalid Card Number!"
    }

```
> when card redemption amount is invalid

```json

    {
      "status": "Bad Request",
      "code": 400,
      "message": "Please Enter valid Amount", "errors": "Please Enter valid Amount"
    }

```
> when card currency and store currency does not match

```json
    {
      "status": "Bad Request",
      "code": 400,
      "message": "The card of given currency cannot be redeemed from selected store!", "errors": "The card of given currency cannot be redeemed from selected store!"
    }
```

>  unauthorized (wrong or missing secret)

```json
  {
   "status":"Unauthorized",
   "code":401,
   "errors":{
      "login":[
         "Invalid token"
      ]
   },
   "message":"You need to login to continue."
}
```

> Invalid IP

```json
    {
   "status":"Unauthorized",
   "code":401,
   "errors":{
      "login":[
         "Invalid Ip"
      ]
   },
   "message":"Your Ip is Not Whitelisted."
}
```

# General Errors

Error | Meaning
----- | ------- 
200 | Success
400 | Bad request
401 | Unauthorized - Invalid Key
403 | Forbidden - Incorrect Authorization
404 | Not Found
409 | Conflict
422 | Unprocessable Entity
500 | Internal Server Error
503 | Service Unavailable
600 | request Error


