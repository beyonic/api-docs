# Collections

Beyonic uses the term "Collections" for payments you receive (or collect) from mobile subscribers. See the [Beyonic collections guide](https://support.beyonic.com/beyonic-collections-receive-mobile-money-through-beyonic) for more information.

When the user sends in a payment, it will create a collection object that you can access via the Collections API using the methods shown below.

The collections api endpoint is:
  <aside class="notice">https://app.beyonic.com/api/collections</aside>

**NOTE: If you want to initiate new collections, use the [Collection Requests API](#collection-requests. The collections api only allows you to view funds that have already been sent to you.**

## The Collection object

> Sample Collection Object (JSON):

```json
{
   "remote_transaction_id" : "a9011942a8a9b669be6a05da411d9da2",
   "created" : "2017-07-26T09:28:25Z",
   "organization" : 4,
   "reference" : "",
   "transaction" : {
      "fee_transaction" : null,
      "account" : {
         "author" : 15,
         "low_balance_threshold" : null,
         "organization" : 4,
         "status" : "active",
         "created" : "2016-08-06T17:09:13Z",
         "updated_by" : 15,
         "label" : "Test currency",
         "description" : "Test currency",
         "low_balance_notification_last_send_time" : null,
         "id" : 2874303,
         "modified" : "2016-08-06T17:09:13Z",
         "currency" : 4
      },
      "created" : "2017-07-26T09:28:24.993743Z",
      "description" : "Incoming payment from +80000000011",
      "id" : 2797147,
      "amount" : "1000.0000",
      "type" : "mobile_collection",
      "currency" : "BXC"
   },
   "modified" : "2017-07-26T09:28:25Z",
   "payment_date" : "2017-07-26T09:28:25Z",
   "collection_request" : {
      "subscription_settings" : null,
      "instructions" : "Thank you for making a test credit purchase request. Please check your credit balance in 'Payment Options' menu on the app after completing your purchase.",
      "created" : "2017-07-26T09:28:24Z",
      "success_message" : "1000 test credit successfully purchased",
      "organization" : 4,
      "retry_interval_minutes" : 0,
      "send_instructions" : true,
      "modified" : "2017-07-26T09:28:25Z",
      "max_attempts" : null,
      "start_date" : "2019-06-11 21:11:13.702766+00:00",
      "error_details" : "",
      "phonenumber" : "+80000000011",
      "updated_by" : 134,
      "account" : null,
      "status" : "successful",
      "expiry_date" : "2017-10-03 06:15:55.722862+00:00",
      "collection" : 131820,
      "author" : 134,
      "currency" : "BXC",
      "error_message" : "",
      "reason" : "",
      "contact" : {
         "email" : null,
         "phone_number" : "+80000000011",
         "last_name" : "Doe",
         "modified" : "2019-08-26T08:21:15Z",
         "type" : "employee",
         "organization" : 4,
         "network_name" : "",
         "created" : "2017-06-17T14:43:57Z",
         "phone_is_mm_registered" : "yes",
         "metadata" : {
            "s_contact4partner_id" : null,
            "s_contact4gender" : null,
            "s_contact4age" : null
         },
         "id" : 219912,
         "phone_is_supported" : "yes",
         "first_name" : "John",
         "author" : 134,
         "name_on_network" : "John Doe",
         "status" : "active",
         "updated_by" : 134,
         "name_matches_network_score" : 100,
         "name_matches_network_status" : "checked"
      },
      "metadata" : {
         "amount" : "1000",
         "user_id" : "212407165802199720"
      },
      "id" : 86296,
      "amount" : "1000.0000"
   },
   "phonenumber" : "+80000000011",
   "updated_by" : null,
   "status" : "successful",
   "author" : null,
   "currency" : "BXC",
   "contact" : {
      "name_matches_network_status" : "checked",
      "status" : "active",
      "updated_by" : 134,
      "name_matches_network_score" : 100,
      "name_on_network" : "John Doe",
      "author" : 134,
      "first_name" : "John",
      "id" : 219912,
      "metadata" : {
         "s_contact4gender" : null,
         "s_contact4partner_id" : null,
         "s_contact4age" : null
      },
      "phone_is_supported" : "yes",
      "phone_is_mm_registered" : "yes",
      "network_name" : "",
      "created" : "2017-06-17T14:43:57Z",
      "organization" : 4,
      "type" : "employee",
      "modified" : "2019-08-26T08:21:15Z",
      "last_name" : "Doe",
      "email" : null,
      "phone_number" : "+80000000011"
   },
   "amount" : "1000.0000",
   "id" : 131820
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
remote_transaction_id | string | The unique transaction ID from the mobile network operator
organization | long integer | The ID of the organization that the collection was matched to
amount | decimal | The collection amount
currency | string | The 3 letter ISO currency code for the collection. **Note:**: BXC is the Beyonic Test Currency code. See the "Testing" section for more information. Supported currency codes are BXC (Testing), UGX (Uganda), KES (Kenya)
phonenumber | string | The phone number that the collection was initiated from, in international format, starting with a +
payment_date | string | The date that the collection was made, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
reference | string | The description or reference code that was included with the collection
status | string | A string showing the status of the collection. One of: successful, failed, pending or cashed_out
created | string | The date that the collection was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
author | long integer | The ID of the user who created the collection
modified | string | The date that the collection was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated_by | string | The ID of the user who last updated the collection
collection_request | JSON string| *New in V3.* A JSON representation of the collection request that this collection was matched to, if any. Before V3, this was simply the id of the collection request object. In V3. It's an expanded representation of the collection request object.
transaction | JSON string| *New in V4.* A JSON representation of the transaction that added funds to the organization account when this collection was received.

## Accept or reject Collections

By default, collections are matched to your account and automatically accepted. However, the following networks allow you to decide whether you want to accept or reject a collection.

* Safaricom (MPESA) Kenya
* MTN Uganda
* Airtel Tanzania
* Vodacom Tanzania
* All Rwanda Networks

### Enabling Collection verification

To enable this functionality, you need to add a verificaiton URL to your collection settings:

1. Log into your Beyonic Account
2. Click "Company Settings" on the home page
3. Click "Advanced Settings"
4. Click "Collection Settings"
5. Enter a Collection Verification URL.
6. You can also decide what action we should take if we cannot reach your verification URL for some reason. This is controlled by the "Accept Collections By Default" option. If this option is checked, we'll accept collections when your URL is unreachable. Otherwise, they will be rejected.

### Verification request parameters

```json
{
    "remote_transaction_id": "ASD12132",
    "phonenumber": "+80000000001",
    "reference": "Payment for A/C 13112313",
    "amount": "500",
    "currency_code": "BXC",
}
```

Once you have created a URL, we will send a JSON POST to the URL you have provided, whenever a new collection is received. The request body shall include the following parameters as a JSON object:

Field | Description
----- |----- | ----
phonenumber | The subscriber's phone number that was used to send collection in international format, e.g. +256782123123
amount | The amount that was sent, e.g. 500
currency_code | The 3 letter currency code, e.g. UGX
reference | The reference or description that the subscriber sent along with the collection. e.g A/C 12312
remote_transaction_id | The network transaction id that identifies this collection on the mobile network operator's system e.g. ASDASDW12321312

### ACCEPT response

> ACCEPT < remote_transaction_id >

```text
For example:

ACCEPT ASCD1234
``` 

In order to accept the collection, you must respond with a 200 response, and the following text in the body of the response:

ACCEPT < remote_transaction_id >

E.g. if the remote transaction id was ASCD1234, then you must respond with:

ACCEPT ASCD1234

### REJECT response

> REJECT < remote_transaction_id > < optional reason >

``` Text
For example:

REJECT ASCD1234 Wrong Account Number.
```

In order to reject the collection, you must respond with a 200 response, and the following text in the body of the response:

REJECT < remote_transaction_id > < optional reason >

E.g. if the remote transaction id was ASCD1234, then you must respond with:

NOTE: The reject reason is optional. If included, we shall attempt to return it to the network (and the network may send it on to the subscriber)

### Response timeout

You must respond within 60 seconds, otherwise the verification request will timeout and the default action shall be taken.


## Retrieving a single Collection

> Sample Request:

```shell
curl https://app.beyonic.com/api/collections/131820 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection = Beyonic::Collection.get(131820)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection = Beyonic_Collection::get(131820);
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection = beyonic.Collection.get(131820)

```

```java
package com.beyonic.examples.collections;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;


public class SingleCollectionExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/collections";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/131820");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            try {
                if (conn.getResponseCode() == 200) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    System.out.println(response);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
   "remote_transaction_id" : "a9011942a8a9b669be6a05da411d9da2",
   "created" : "2017-07-26T09:28:25Z",
   "organization" : 4,
   "reference" : "",
   "transaction" : {
      "fee_transaction" : null,
      "account" : {
         "author" : 15,
         "low_balance_threshold" : null,
         "organization" : 4,
         "status" : "active",
         "created" : "2016-08-06T17:09:13Z",
         "updated_by" : 15,
         "label" : "Test currency",
         "description" : "Test currency",
         "low_balance_notification_last_send_time" : null,
         "id" : 2874303,
         "modified" : "2016-08-06T17:09:13Z",
         "currency" : 4
      },
      "created" : "2017-07-26T09:28:24.993743Z",
      "description" : "Incoming payment from +80000000011",
      "id" : 2797147,
      "amount" : "1000.0000",
      "type" : "mobile_collection",
      "currency" : "BXC"
   },
   "modified" : "2017-07-26T09:28:25Z",
   "payment_date" : "2017-07-26T09:28:25Z",
   "collection_request" : {
      "subscription_settings" : null,
      "instructions" : "Thank you for making a test credit purchase request. Please check your credit balance in 'Payment Options' menu on the app after completing your purchase.",
      "created" : "2017-07-26T09:28:24Z",
      "success_message" : "1000 test credit successfully purchased",
      "organization" : 4,
      "retry_interval_minutes" : 0,
      "send_instructions" : true,
      "modified" : "2017-07-26T09:28:25Z",
      "max_attempts" : null,
      "start_date" : "2019-06-11 21:11:13.702766+00:00",
      "error_details" : "",
      "phonenumber" : "+80000000011",
      "updated_by" : 134,
      "account" : null,
      "status" : "successful",
      "expiry_date" : "2017-10-03 06:15:55.722862+00:00",
      "collection" : 131820,
      "author" : 134,
      "currency" : "BXC",
      "error_message" : "",
      "reason" : "",
      "contact" : {
         "email" : null,
         "phone_number" : "+80000000011",
         "last_name" : "Doe",
         "modified" : "2019-08-26T08:21:15Z",
         "type" : "employee",
         "organization" : 4,
         "network_name" : "",
         "created" : "2017-06-17T14:43:57Z",
         "phone_is_mm_registered" : "yes",
         "metadata" : {
            "s_contact4partner_id" : null,
            "s_contact4gender" : null,
            "s_contact4age" : null
         },
         "id" : 219912,
         "phone_is_supported" : "yes",
         "first_name" : "John",
         "author" : 134,
         "name_on_network" : "John Doe",
         "status" : "active",
         "updated_by" : 134,
         "name_matches_network_score" : 100,
         "name_matches_network_status" : "checked"
      },
      "metadata" : {
         "amount" : "1000",
         "user_id" : "212407165802199720"
      },
      "id" : 86296,
      "amount" : "1000.0000"
   },
   "phonenumber" : "+80000000011",
   "updated_by" : null,
   "status" : "successful",
   "author" : null,
   "currency" : "BXC",
   "contact" : {
      "name_matches_network_status" : "checked",
      "status" : "active",
      "updated_by" : 134,
      "name_matches_network_score" : 100,
      "name_on_network" : "John Doe",
      "author" : 134,
      "first_name" : "John",
      "id" : 219912,
      "metadata" : {
         "s_contact4gender" : null,
         "s_contact4partner_id" : null,
         "s_contact4age" : null
      },
      "phone_is_supported" : "yes",
      "phone_is_mm_registered" : "yes",
      "network_name" : "",
      "created" : "2017-06-17T14:43:57Z",
      "organization" : 4,
      "type" : "employee",
      "modified" : "2019-08-26T08:21:15Z",
      "last_name" : "Doe",
      "email" : null,
      "phone_number" : "+80000000011"
   },
   "amount" : "1000.0000",
   "id" : 131820
}
```

To retrieve a single collection object, provide the collection id and a collection object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 23 | The id of the collection you want to retrieve

## Listing all Collections

> Sample Request:

```shell
curl https://app.beyonic.com/api/collections -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection = Beyonic::Collection.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection = Beyonic_Collection::getAll();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection = beyonic.Collection.list()

```

```java
package com.beyonic.examples.collections;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class ListAllCollectionsExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/collections";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            try {
                if (conn.getResponseCode() == 200) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    System.out.println(response);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "count": 3,
    "next": "https://app.beyonic.com/api/collections?offset=10",
    "previous": null,
    "results": [
        {
            "id": 82,
            "remote_transaction_id": "1485758785",
            "organization": 1,
            "amount": "2000.0000",
            "currency": 2,
            "phonenumber": "+80000000001",
            "payment_date": "2015-07-14T09:57:44Z",
            "reference": "beyonic",
            "status": "successful",
            "created": "2015-07-14T15:19:05Z",
            "author": null,
            "modified": "2015-08-20T16:48:51Z",
            "updated_by": null
        },
        {
            "id": 81,
            "remote_transaction_id": "225718814",
            "organization": 1,
            "amount": "2000.0000",
            "currency": 2,
            "phonenumber": "+80000000001",
            "payment_date": "2015-07-14T10:26:32Z",
            "reference": "Payment Ian M",
            "status": "successful",
            "created": "2015-07-14T10:27:08Z",
            "author": null,
            "modified": "2015-07-14T10:27:11Z",
            "updated_by": null
        },
        {
            "id": 77,
            "remote_transaction_id": "215569420",
            "organization": 1,
            "amount": "500.0000",
            "currency": 2,
            "phonenumber": "+80000000001",
            "payment_date": "2015-06-25T08:16:26Z",
            "reference": "payment 10",
            "status": "successful",
            "created": "2015-06-25T08:17:07Z",
            "author": null,
            "modified": "2015-06-25T08:17:18Z",
            "updated_by": null
        },
	]
}
```

To retrieve a list of all collections, make a GET request to the collections endpoint. This will return a list of collection objects.

## Filtering Collections

> Sample Request:

```shell
curl https://app.beyonic.com/api/collections?phonenumber=%2B80000000001&remote_transaction_id=SS12312 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collections = Beyonic::Collection.list(
  phonenumber: "+80000000001",
  remote_transaction_id: "SS12312"
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collections = Beyonic_Collection::getAll(array(
  "phonenumber" => "+80000000001",
  "remote_transaction_id" => "SS12312"
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collections = beyonic.Collection.list(phonenumber='+80000000001',
                                     remote_transaction_id='SS12312')


```

```java
package com.beyonic.examples.collections;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class FilterCollectionsExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/collections";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "?remote_transaction_id=SS12312");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            try {
                if (conn.getResponseCode() == 200) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    System.out.println(response);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
[
       {
        "id": 134,
        "phonenumber": "+256XXXXXX",
        "remote_transaction_id": "ASDCECASF",
        "payment_date": "2014-11-22T20:57:04Z",
        "reference": "Test Payment",
        "amount": 3000.000,
        "currency": "BXC",
        "status": "successful"
       }
]
```

You can search or filter collections on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only (the phonenumber field is treated differently - see below).

* amount - the collection amount
* currency - the currency code
* phonenumber - the phonenumber that the collection request was intended for. Note that the phonenumber will be matched in international format, starting with a '+' sign. If the '+' sign isn't included in your request, it will be appended before attempting to match your request. This is only true for phone number searches with collections, to make claiming of transactions easier. Other
* reference - the reference code that the customer used when sending the collection
* remote_transaction_id - the transation id or transaction reference of the collection on the mobile network operator's side
* collection_request - the ID of the collection request that this collection was matched to
* created_after - only return collections created after this date (E.g. 2017-01-01 00:00)
* created_before - only return collections created before this date (E.g. 2017-01-01 00:00)

**Response**

Note that the response will be a list of collections, not a single collection.

## Claiming an ummatched Collection

> Sample Request:

```shell
curl https://app.beyonic.com/api/collections?phonenumber=%2B80000000001&remote_transaction_id=SS12312&claim=True&amount=200 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collections = Beyonic::Collection.list(
  phonenumber: "+80000000001",
  remote_transaction_id: "SS12312",
  claim: true,
  amount: 200
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collections = Beyonic_Collection::getAll(array(
  "phonenumber" => "+80000000001",
  "remote_transaction_id" => "SS12312",
  "claim" => "True",
  "amount" => "200",
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collections = beyonic.Collection.list(phonenumber='+80000000001',
                                     remote_transaction_id='SS12312',
                                     claim=True,
                                     amount='200')


```

```java
package com.beyonic.examples.collections;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class ClaimUnmatchedCollectionExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/collections";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "?phonenumber=%2B80000000001&remote_transaction_id=SS12312&amount=200&claim=True");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            try {
                if (conn.getResponseCode() == 200) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    System.out.println(response);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
[
       {
        "id": 134,
        "phonenumber": "+800XXXXXXXX",
        "remote_transaction_id": "ASDCECASF",
        "payment_date": "2014-11-22T20:57:04Z",
        "reference": "Test Payment",
        "amount": 3000.000,
        "currency": "BXC",
        "status": "successful"
       }
]
```

By default, when you search for a collection, only collections that have been successfully assigned to your organization are searched.

You can add the "claim" parameter to instruct Beyonic to also search unmatched collections, and if any of the unmatched collections match your input, they will be assigned to your organization.

Note that when you include the claim parameter, you **must** also include the amount, remote_transaction_id and phonenumber parameters. Failure to do so will result in an error.

Also note that the phonenumber will be matched in international format, starting with a '+' sign. Partial phone number matches will fail when you try to claim a transaction. If the '+' sign isn't included in your request, it will be appended before attempting to match your request.

Lastly, if your search parameters match more than one collection, the claim will fail.

Parameter | Type | Example | Notes
----------| ---- | ------- | -----
claim | Boolean or String | True | Instruct system to search unmatched transctions and claim them for your organization.

**Response**

Note that the response will be a list of collections, not a single collection.
