# /on_search Payload breakdown 

Refer ONDC's [API Contract for more details](https://docs.google.com/document/d/1brvcltG_DagZ3kGr1ZZQk4hG4tze3zvcxmGV4NMTzr8/edit)

For yaml schema of ONDC's APIs, refer [here](https://github.com/ONDC-Official/ONDC-Protocol-Specs/blob/master/protocol-specifications/core/v0/api/core.yaml)

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
| Field           | Description                                                                                                                              |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| domain          | Domain namespace of search, for a list of all available domains, see [here](https://docs.google.com/document/d/1brvcltG_DagZ3kGr1ZZQk4hG4tze3zvcxmGV4NMTzr8/edit#heading=h.w9zlp87xdha1) |
| city            | see [here](https://docs.google.com/spreadsheets/d/12A_B-nDtvxyFh_FWDfp85ss2qpb65kZ7/edit#gid=213574534) for city to Pincode mapping        |
| action          | action being performed (Eg: search, on_search, etc.)                                                                                    |
| bap_id          | Subscriber ID for Buyer App                                                                                                             |
| bap_uri         | Subscriber URI of the buyer app, the domain of bap_uri must be similar to bap_id                                                       |
| bpp_id          | Subscriber ID for Seller                                                                                                                |
| bpp_uri         | Subscriber URI for Seller, the domain of bpp_uri must be similar to bpp_id                                                              |
| transaction ID  | Unique ID for a transaction, can be similar for multiple /search, /init, and /confirm                                                  |
| message ID      | Unique ID for every message                                                                                                             |
| timestamp       | Timestamp in RFC3339 format                                                                                                             |


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
bpp/providers contain the actual search result, which is the structure of bpp/providers.

```
"bpp/providers" : [
  {
          "id"  : "P1"   //provider ID
          "time" : {
          "label" : enum["enable","disable"] //for this particular time, enable/disable this provider. 
            }
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

"ttl": "P1D",
fulfillments : [ // Array of supported fulfillment types for this particular]
locations : [//list of all the locations supported by the provider]
categories : [//list of Provider level definitions for item categories],
items : [//list of data about items offered by each provider],
offers : [//list of available offers available to add in cart],
tags : [//metaData about the providers (Eg: Details about fulfillment types)]
}
}
]
```
| Attribute                       | Description                                                                                               |
| ------------------------------- | --------------------------------------------------------------------------------------------------------- |
| bpp/providers                   | An array of provider objects, each containing details about a specific provider.                        |
| providers.id                   | The ID of the provider.                                                                                   |
| providers.time.label          | Time-related setting to enable or disable the provider for a specific time.used in incremental catalog refresh.                             |
| providers.descriptor.name     | Name of the provider store.                                                                              |
| providers.descriptor.symbol   | Symbol or logo URL of the provider store.                                                                |
| providers.descriptor.short_desc | Short description of the provider store.                                                                 |
| providers.descriptor.long_desc | Long description of the provider store.                                                                  |
| providers.descriptor.images  | Array of image URLs for the provider store.                                                              |
| providers.@ondc/org/fssai_license_no | FSSAI license number (mandatory for F&B providers).                                            |
| providers.ttl                  | Time-to-live (TTL) for message duration validity for this catalog (e.g., "P1D" for 1 day).           |


### provider.fulfillments
provider. fulfillments contains a list of all the fulfillment types supported by a provider along with the contact details of the merchant
##### provider.fulfillments JSON example
```
 "fulfillments": [
            //available fulfillment types for this particular provider
            {
              "id": "F1",
              "type": "Delivery",
              "contact": {
                //contact detail for merchant
                "phone": "9886098860",
                "email": "abc@xyz.com"
              }
            },
            {
              "id": "F2",
              "type": "Self-Pickup",
              "contact": {
                "phone": "9886098860",
                "email": "abc@xyz.com"
              }
            }
          ]

```
### provider.locations 
provider.locations contain a list of all the locations supported by the provider, each list item contains location information like 
address, availability of seller, delivery radius, etc...

##### provider.locations JSON example
```
"locations": [
            {
              "id": "L1",
              "time": {
                "label": "enable", 
                "timestamp": "2023-06-03T07:30:30.000Z",  
                "days": "1,2,3,4,5,6,7", 
                "schedule": {
                  "holidays": ["2023-08-15"],   
                  "frequency": "PT4H",
                  "times": ["1100", "1900"]   
                },
                "range": {
                  "start": "1100",
                  "end": "2100"
                }
              },
              "gps": "12.967555,77.749666", 
              "address": {
                "locality": "Jayanagar",
                "street": "Jayanagar 4th Block",
                "city": "Bengaluru",
                "area_code": "560076",
                "state": "KA"
              },
              "circle": {                   
                "gps": "12.967555,77.749666",
                "radius": {
                  "unit": "km",
                  "value": "3"
                }
              }
            }
          ]

```
| Attribute                                     | Description                                         |
| --------------------------------------------- | --------------------------------------------------- |
| locations.id                                  | The ID of the location.                            |
| locations.time.label                          | Time-related setting to enable or disable location.|
| locations.time.timestamp                      | Timestamp of the last search call.                 |
| locations.time.days                           | Days of the week when the location is available. 1-monday, 7-sunday  |
| locations.time.schedule.holidays              | List of future holidays.An empty array is returned of no holidays are present   |
| locations.gps                                | Latitude and longitude of the location.           |
| locations.circle.gps                         | Latitude and longitude of the provider
| locations.circle                              | Range of fulfillment supported by the provider by keeping circle.gps as center        |

### provider.categories

provider.categories contains a list of custom categories supported by each provider. a category can be of three types

* custom_menu : A menu of items that are provided by the BPP. If an item that belongs to a custom_menu is present in on_search result. Information about the custom_menu is returned. Every custom_menu has a rank attribute to show how it competes with other custom_menus from that same provider.
* custom_group : used for food item customizations. an item can have multiple customizations. and customizations may follow a hierarchical pattern.
* varient_group : used to specify some common attributes of many items. Eg: It could define wether a item can be measured using a givem unit and its measurement value, etc..

Refer [this](https://docs.google.com/document/d/1brvcltG_DagZ3kGr1ZZQk4hG4tze3zvcxmGV4NMTzr8/edit#heading=h.3jvvancz3alw) section of the ONDC-API Contract for Retail to understand how hierarcy works in customizations.

##### provider.categories JSON example
```
"categories" : [{
              "id": "CG1",
              "descriptor": {
                "name": "Crust (any 1 option)"
              },
              "tags": [
                {
                  "code": "type",
                  "list": [
                    {
                      "code": "type",
                      "value": "custom_group"
                    }
                  ]
                },
                {
                  "code": "config",
                  //customization group, has min, max, input, seq
                  "list": [
                    {
                      "code": "min",
                      "value": "1"
                    },
                    {
                      "code": "max",
                      "value": "1"
                    },
                    {
                      "code": "input",
                      "value": "select" //select : selection by buyer from available customizations based on min/max//////
                      //value can also have text : text entered by buyer eg : for engraving
                    },
                    {
                      "code": "seq",
                      "value": "1"
                    }
                  ]
                }
              ]
            }]
```
### providers.items 
contains all the items that are returned by the provider.Items have certain notable properties
* During every single incremental catalog refresh , every item must be explicitly enabled or disabled by the provider.(just like providers and provider locations)
* An item that is present in a custom_menu has a 'category_ids' attribute. that contains custom_menu rank and sequence of that item in that custom_menu.
* An item that inherits an item from provider.categories(it could be either custom_menu,custom_group or vairent_group) can modify its properties under tags.code = 'config'
* certain properties of items are defined at the item level rather than in provider.categories (Eg : tags.code = 'veg_nonveg')

##### provider.items JSON example
```
{
              "id": "I1",
              "time": {
                "label": "enable", // enable or disable
                //means that this item is available by this merchant at this point in time
                "timestamp": "2023-06-03T07:30:00.000Z"
              },
              "parent_item_id": "V1", //If varient group ID is defined above, this would match that ID. if not, this is considered as a normal stock item
              "descriptor": {
                "name": "Farmhouse Pizza",
                "symbol": "https://sellerNP.com/images/i1.png",
                "short_desc": "Farmhouse Pizza",
                "long_desc": "Farmhouse Pizza",
                "images": ["https://sellerNP.com/images/i1.png"]
              },
              "quantity": {
                "unitized": {
                  "measure": {
                    "unit": "unit", //unit,dozen,Kilogram,tonne,litre,millilitre
                    "value": "1"
                  }
                },
                "available": {
                  "count": "99" //Items at stock
                },
                "maximum": {
                  "count": "99" //cap per order
                }
              },
              "price": {
                "currency": "INR",
                "value": "269.0",
                "minimum_value": "269.075", //optional
                "maximum_value": "269.0"
              },
              "category_id": "Pizza",
              "category_ids": ["5:1"], //basically custom_menu:sequence
              //item assigned to custom menu. "custom menu item:sequence within custom menu item" or parent -> child menu item
              "fulfillment_id": "F1", //default fulfillment type.matches the fulfillment id at provider level definition
              "location_id": "L1", // provider location.matches the location from provider level definition
              "related": false,
              "recommended": true,
              "@ondc/org/returnable": false,
              "@ondc/org/cancellable": false,
              "@ondc/org/return_window": "PT1H",
              "@ondc/org/seller_pickup_return": false, //wether seller is responsible for pickup when return
              "@ondc/org/time_to_ship": "PT45M",
              "@ondc/org/available_on_cod": false,
              "@ondc/org/contact_details_consumer_care": "Ramesh,ramesh@abc.com,18004254444", // contact details exactly in this format
              "tags": [
                {
                  "code": "type",
                  "list": [
                    {
                      "code": "type", //only required for items that can be customized
                      "value": "item" // can be item, dynamic_item, customization
                    }
                  ]
                },
                {
                  "code": "custom_group", // all customization group must be listed here
                  //                   if any mandatory customization group, being used to configure the base item, is not defined (in
                  // provider.categories) or no customizations have been mapped to this customization group, the buyer NP may
                  // consider the base item as invalid & disable accordingly;
                  "list": [
                    {
                      "code": "id",
                      "value": "CG1"
                    },
                    {
                      "code": "id",
                      "value": "CG2"
                    },
                    {
                      "code": "id",
                      "value": "CG3"
                    }
                  ]
                },
                {
                  "code": "config",
                  //configurations are inherited from custom category definition for each provider.
                  "list": [
                    {
                      "code": "id",
                      "value": "CG1"
                    },
                    {
                      "code": "min",
                      "value": "1"
                    },
                    {
                      "code": "max",
                      "value": "1"
                    },
                    {
                      "code": "seq",
                      "value": "1"
                    }
                  ]
                },
                {
                  "code": "timing",
                  //availability timing for each item
                  "list": [
                    {
                      "code": "day_from",
                      "value": "1"
                    },
                    {
                      "code": "day_to",
                      "value": "5"
                    },
                    {
                      "code": "time_from",
                      "value": "1800"
                    },
                    {
                      "code": "time_to",
                      "value": "2200"
                    }
                  ]
                },
                {
                  "code": "veg_nonveg",
                  "list": [
                    {
                      "code": "veg", //veg, no_veg,egg
                      "value": "yes"
                    }
                  ]
                }
              ]
            }
```
## Errors
see [here](https://github.com/ONDC-Official/developer-docs/blob/main/protocol-network-extension/error-codes.md) for ONDC standard error codes
