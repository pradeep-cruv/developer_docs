# /on_search Payload breakdown 

/on_search Payload consists of context, message and errors.

```
{
context : { //context data  },  
message : { //message data },
errors :  { //error data }
}
```


* Context contains metaData about the API call (like domain,bap_id,etc..)
* Message contains the actual data returned by the BPP
* If there are any errors while performing search (Eg : invalid search) it is present in the errors category

## Context 
Here is an example "context" section of on_search() payload


```
"context": {
"domain": "ONDC:RET11",
"country": "IND",
"city": "std:080",
"action": "on_search",
"core_version": "1.2.0",
"bap_id": "buyerNP.com",
"bap_uri": "https://buyerNP.com/ondc",
"bpp_id": "sellerNP.com",
"bpp_uri": "https://sellerNP.com/ondc",
"transaction_id": "T1",
"message_id": "M1",
"timestamp": "2023-06-03T08:00:30.000Z"
},
```
domain : Domain namespace of search, for a list of all available domains, see [here](https://docs.google.com/document/d/1brvcltG_DagZ3kGr1ZZQk4hG4tze3zvcxmGV4NMTzr8/edit#heading=h.w9zlp87xdha1)

city : see [here](https://docs.google.com/spreadsheets/d/12A_B-nDtvxyFh_FWDfp85ss2qpb65kZ7/edit#gid=213574534) for city to Pincode mapping

action : action beign performed (Eg: search,on_search,ect..)

bap_id : Subscriber ID for Buyer App

bap_uri : Subscriber URI of buyer app, domain of bap_uri must be similar to bap_id

bpp_id : Subscriber ID for Seller

bpp_uri : Subscriber URI for Seller, domain of bpp_uri must be similar to bpp_id

transaction ID : Unique ID for a transaction, can be similar for multiple /search,/init, and /confirm

message ID : Unique ID for every message.

timestamp : timestamp in RFC3339 format

## message

When we perform a /search, BPP returns a catalog in /on_search that contains search information.A is divided into 3 sections

* bpp/fulfillments - The fulfillment types supported by all of the providers in search results
* bpp/descriptor - Information about the seller NP
* bpp/providers - An array containing search result from each provider

  ##### Abstracted Format of message
  ```
  "message" : {
    "catalog" : {
     "bpp/fulfillments" : [
              //List of fulfillment types (Eg : Delivery,Self-Pickup,ect..) by all available providers
  ],
     "bpp/descriptor" : {
              //Information about seller NP
  },
     "bpp/providers" : [
              //List of search results provided by each provider
  ]
  }}
  ```
bpp/providers contains the actual search result and this is the structure of bpp/providers.

```
"bpp/providers" : [
  {
          "id"  : "P1"   //provider ID
          "time" : {
          "label" : enum["enable","disable"] //for this particular time,enable/disable this provider.used in incremental catalog refresh.
            }
fulfillments : [
 // Array of supported fulfillment types for this particular provider
{
          "id": "F1",
          "type": "Delivery",  //must be one of the types from bpp/fulfillments
          "contact": {
            //contact detail for merchant
            "phone": "phone",
            "email": "abc@xyz.com"
          }
        },
descriptor : {
          //Details about the provider store
           "name": "Store 1",
            "symbol": "https://sellerNP.com/images/store1.png",
            "short_desc": "Store 1",
            "long_desc": "Store 1",
            "images": ["https://sellerNP.com/images/store1.png"]
}

//fssai_license is mandatory for F&B
"@ondc/org/fssai_license_no": "12345678901234",

//ttl is for message furation validity for this catalog
"ttl": "P1D",
categories : [//list of Provider level definition for item categories],
items : [//list of data about items offered by each provider],
offers : [//list of available offers available to add in cart],
tags : [//metaData about the providers (Eg : Details about fulfillment types)]
}
}
]
```

## Errors
see [here](https://github.com/ONDC-Official/developer-docs/blob/main/protocol-network-extension/error-codes.md) for ONDC standard error codes
