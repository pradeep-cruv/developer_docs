# ONDC JSON Schema 

Go here to view a complete sample JSON Schema
ONDC JSON Schema is divided into 4 major components
```
{
"ONDC" : { //contains ONDC specific attributes },
"items" : [//array of item objects],
"restaurant" : {//contains restaurant specific details},
"offers" : [//array of offer objects at restaurant level],
}
```

## "ONDC" - Attribute
These attributes are present in the 'context' section of an on_search payload.
Here is an example of a structure
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

| Field          | Description                                                                                                                                                                              |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| domain         | Domain namespace of search, for a list of all available domains, see [here](https://docs.google.com/document/d/1brvcltG_DagZ3kGr1ZZQk4hG4tze3zvcxmGV4NMTzr8/edit#heading=h.w9zlp87xdha1) |
| city           | see [here](https://docs.google.com/spreadsheets/d/12A_B-nDtvxyFh_FWDfp85ss2qpb65kZ7/edit#gid=213574534) for city to Pincode mapping                                                      |
| action         | action being performed (Eg: search, on_search, etc.)                                                                                                                                     |
| bap_id         | Subscriber ID for Buyer App                                                                                                                                                              |
| bap_uri        | Subscriber URI of the buyer app, the domain of bap_uri must be similar to bap_id                                                                                                         |
| bpp_id         | Subscriber ID for Seller                                                                                                                                                                 |
| bpp_uri        | Subscriber URI for Seller, the domain of bpp_uri must be similar to bpp_id                                                                                                               |
| transaction ID | Unique ID for a transaction, can be similar for multiple /search, /init, and /confirm. During the lifespan of a complete transaction (from discovery to fulfillment), the transaction ID could remain the same even if the same API calls are made multiple times.          |
| message ID     | Unique ID for every message                                                                                                                                                              |
| timestamp      | Timestamp in RFC3339 format  

## "items" - Attribute

Contains item-specific information. with information regarding the supported categories and their customizations

### General Structure of item attribute
```
"items": [
    // item specific attributes
    "customization_categories": [
        // List of all the customization categories supported by this item.
        // Each object is a customization category which has all the supported customizations
        {
            // item-category-specific-attributes
            "customization_items": [
                // list of all the customizations supported by this customization_category.
                // Each object is an item which has its type = 'customization' and belongs to this customization group
            ]
        }
    ]
]
```
### Example format of item attribute
```
"items": [
    {
      "name": "Sample Item",
      "item_id": "item123",
      "symbol": "https://sellerNP.com/images/i1.png",
      "short_desc": "A sample item",
      "long_desc": "This is a description of the sample item.",
      "images": ["image_url1", "image_url2"],
      "is_item_enabled": true,
      "last_updated_time": "2023-08-14T12:34:56Z",
      "measured_unit": "unit",
      "measured_value": "1",
      "maximum_available_from_provider": "100",
      "maximum_items_per_order": "10",
      "price_currency": "INR",
      "price_value": "20000",
      "maximum_price_value": "250.00",
      "price_lower_bound": "150.00",
      "price_upper_bound": "300.00",
      "is_related_to_customer": true,
      "is_recommended_to_customer": true,
      "is_item_returnable": true,
      "is_item_cancellable": true,
      "item_return_window": "P7D",
      "will_seller_pickup_return": true,
      "time_taken_to_ship_item": "P2D",
      "is_available_on_cod": true,
      "day_available_from": "1",
      "day_available_to": "5",
      "time_available_from": "0900",
      "time_available_to": "1800",
      "is_veg": true,
      "customization_categories": [
        {
          "customization_id": "custom123",
          "category_name": "Size",
          "category_short_desc": "Size options",
          "category_long_desc": "Choose your preferred size",
          "category_images": ["image_url5", "image_url6"],
          "category_customization_type": "custom_menu",
          "category_day_available_from": "1",
          "category_day_available_to": "2",
          "category_time_available_from": "0900",
          "category_time_available_to": "2100",
          "category_min_value": "1",
          "category_max_value": "5",
          "is_text_engraving_available": true,
          "customization_items": [
            {
              "name": "Sample Item",
              "symbol": "SYM123",
              "short_desc": "A sample item",
              "long_desc": "This is a description of the sample item.",
              "images": ["image_url1", "image_url2"],
              "is_default_customization": true,
              "item_id": "item123",
              "is_item_enabled": true,
              "last_updated_time": "2023-08-14T12:34:56Z",
              "measured_unit": "pcs",
              "measured_value": "1",
              "maximum_available_from_provider": "100",
              "maximum_items_per_order": "10",
              "price_currency": "USD",
              "price_value": "200000",
              "maximum_price_value": "250000",
              "price_lower_bound": "15000",
              "price_upper_bound": "30000",
              "is_related_to_customer": true,
              "is_recommended_to_customer": true,
              "is_item_returnable": true,
              "is_item_cancellable": true,
              "item_return_window": "P7D",
              "will_seller_pickup_return": true,
              "time_taken_to_ship_item": "P2D",
              "is_available_on_cod": true,
              "day_available_from": "1",
              "day_available_to": "5",
              "time_available_from": "09:00",
              "time_available_to": "18:00",
              "is_veg": true
            }
          ]
        }
      ]
    }
  ]
```
## items

|                                          Items                                                                        |
| Attribute                    | Explanation                                                                           |
|------------------------------|---------------------------------------------------------------------------------------|
| name                         | Item name                                                                             |
| item_id                      | Unique item ID set by seller BP                                                     |
| symbol                       | URL of an item symbol as a picture                                                  |
| short_desc                   | Item short description                                                               |
| long_desc                    | Item long description                                                                |
| images                       | List of URLs of images of the item excluding the symbol                              |
| is_item_enabled              | Boolean value that specifies if the item is enabled or disabled in catalog refresh   |
| last_updated_time            | Item status last updated time                                                       |
| measure_unit                 | Unit to measure item                                                                 |
| measured_value               | Measured value by unit                                                               |
| price_currency               | Price currency unit                                                                  |
| price_value                  | Price currency value                                                                 |
| maximum_available_from_prov  | Maximum number of this item in stock                                                |
| maximum_items_per_order      | Maximum number per order                                                             |
| maximum_price_value          | MRP of item                                                                           |
| price_lower_bound           | Minimum price of item after applying customizations                                 |
| price_upper_bound           | Maximum price of item after applying customizations                                 |
| is_related_to_customer       | Boolean value, True if the item is relevant to customer's search                   |
| is_recommended_to_customer   | Boolean value, True if the item is recommended to the customer by the seller BP     |
| is_item_returnable           | Boolean value, True if the item can be returned                                     |
| is_item_cancellable          | Boolean value, True if the item can be cancelled                                    |
| item_return_window           | Timestamp value denoting the item return window                                     |
| will_seller_pickup_return    | Boolean value, True if seller picks up item return                                  |
| time_taken_to_ship_item      | Timestamp value denoting item shipment time                                         |
| is_available_on_cod          | Boolean value, True if Cash on Delivery is applicable for this item                |
| day_available_from           | Day when item is available from                                                     |
| day_available_to             | Day when item is available to                                                       |
| time_available_from          | Timestamp value denoting the time when item is available from                       |
| time_available_to            | Timestamp value denoting the time when item is available to                         |
| is_veg                       | Boolean value, True if the item is vegetarian                                       |




