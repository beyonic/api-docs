# Collection Requests

Collection Requests allow you to notify a customer to send funds to you. When you create a collection request, we send a notification to the customer, with insructions on how to fulfill the request. When they send the funds, a collection object shall be created, and matched with the collection request.

Creating a collection request prior to receiving an expected collection greatly improves the user experience for your subscribers by automatically matching transactions to your organization, even if the subscriber misses out some information or gets some of the information wrong, for example if they forget to include a reference code. Payments matching the amount, number and currency in the collection requests will be correctly assigned to you.

On some supported networks, collection requests actually "pull" the funds from the recipient's mobile money wallet, and all they have to do is enter their PIN code to approve the transaction. This greatly improves the customer experience.

The collection requests api endpoint is:
    <aside class="notice">https://app.beyonic.com/api/collectionrequests</aside>

## The Collection Request object

> Sample Collection Request Object (JSON):

```json
{
    "id": 427737,
    "organization": 4,
    "amount": "3000.0000",
    "currency": "BXC",
    "phonenumber": "+80000000001",
    "contact": {
        "id": 127694,
        "organization": 4,
        "first_name": "Kennedy",
        "last_name": "Amani",
        "email": null,
        "phone_number": "+80000000001",
        "type": "employee",
        "status": "active",
        "metadata": {},
        "phone_is_supported": "yes",
        "phone_is_mm_registered": "yes",
        "name_on_network": "Insufficient Data Or Too Low Score",
        "name_matches_network_status": "checked",
        "name_matches_network_score": 0.0,
        "network_name": "",
        "created": "2016-12-30T21:14:27Z",
        "author": 134,
        "modified": "2018-04-24T08:16:27Z",
        "updated_by": 134
    },
    "reason": "Test Org",
    "metadata": {
        "my_id": "123ASDAsd123"
    },
    "created": "2018-05-25T19:06:51Z",
    "author": 134,
    "modified": "2018-05-25T19:06:51Z",
    "updated_by": 134,
    "collection": null,
    "account": null,
    "success_message": "",
    "instructions": null,
    "send_instructions": false,
    "status": "pending",
    "error_message": null,
    "expiry_date": "2018-05-26 19:06:51.300273+00:00"
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
organization | long integer | The ID of the organization that owns this collection request (This is usually your organization ID)
amount | decimal | The collection request amount
currency | string | The 3 letter ISO currency code for the collection request. **Note:**: BXC is the Beyonic Test Currency code. See the "Testing" section for more information. Supported currency codes are BXC (Testing), UGX (Uganda), KES (Kenya)
phonenumber | string | The phone number that the collection request is intended for, in international format, starting with a +
contact | object | The contact that has been matched to this request. See the "Contacts" section for more information.
reason | string or null | Internal description or reason for this collection request
metadata | hash | Any custom metadata that was added to the collection request when it was created
created | string | The date that the collection request was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
author | long integer | The ID of the user who created the collection request
modified | string | The date that the collection request was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated_by | string | The ID of the user who last updated the collection request
collection | long integer or null | The ID of the collection that fulfilled the collection request, if any
success_message | string or null | The confirmation message delivered to the customer upon successful completion of this payment request
send_instructions | boolean | Defaults to False (but you probably want to set this to True). Indicates whether we should send payment instructions to the subscriber via SMS. Note that this field defaults to False, so if you want the collection request to actually notify the customer (with a USSD popup and an SMS), you must set this field to True. Omitting the field will prevent collection requests from being sent out to the customer.
instructions | string or null | Allows overriding of the default instructions sent to the subscriber. If omitted, default network-specific instructions will be sent to the subscriber via SMS. If you want to skip sending ANY sms instructions and turn off even the default instructions, set this parameter to "skip" (instructions = "skip")
status | string | This is the status of the collection request. Possible values are: pending, successful, failed, expired or reversed
error_message | string | This will contain an error description in case an error occurs
expiry_date | string or null | Defaults to "24 hours". Specifies the date and time when this collection request will be marked as expired. Examples of valid values for this field include strings such as "tomorrow", "24 hours", "2 minutes", or %d/%m/%Y format e.g 24/05/2019 or %d/%m/%Y %H:%M:%S format e.g 24/05/2019 13:24:12


## Creating a new Collection Request

> Sample Request:

```shell
curl https://app.beyonic.com/api/collectionrequests -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d phonenumber=+80000000001 \
-d currency=BXC \
-d amount=3000 \
-d metadata.my_id='123ASDAsd123'
-d send_instructions=True
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_request = Beyonic::CollectionRequest.create(
    phonenumber: "+80000000001",
    amount: "100.2",
    currency: "BXC",
    metadata: {"my_id": "123ASDAsd123"},
    send_instructions: true
)

p collection_request  # Examine the returned object
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection_request = Beyonic_Collection_Request::create(array(
  "phonenumber" => "+80000000001",
  "amount" => "100.2",
  "currency" => "BXC",
  "metadata" => array("my_id"=>"123ASDAsd123"),
  "send_instructions" => True
));

print_r($collection_request);  // Examine the returned object
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'


collection_request = beyonic.CollectionRequest.create(phonenumber='+80000000001',
                       amount='1200',
                       currency='BXC',
                       description='Per diem',
                       callback_url='https://my.website/payments/callback',
                       metadata={'my_id': '123ASDAsd123'},
                       send_instructions = True
                       )

print collection_request  # Examine the returned object
```

```java
package com.beyonic.examples;

import com.beyonic.models.CollectionRequest;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    HashMap<String, Object> crCreateData = new HashMap<>();
    crCreateData.put("amount", "1200");
    crCreateData.put("currency", "KES");
    crCreateData.put("description", "Test  Java Client");
    crCreateData.put("phonenumber", "+254727447101");
    response = new CollectionRequest().create(crCreateData, null);

    System.out.println(response);
}
catch (BeyonicException e){
    e.printStackTrace();
}


```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 427737,
    "organization": 4,
    "amount": "3000.0000",
    "currency": "BXC",
    "phonenumber": "+80000000001",
    "contact": {
        "id": 127694,
        "organization": 4,
        "first_name": "Kennedy",
        "last_name": "Amani",
        "email": null,
        "phone_number": "+80000000001",
        "type": "employee",
        "status": "active",
        "metadata": {},
        "phone_is_supported": "yes",
        "phone_is_mm_registered": "yes",
        "name_on_network": "Insufficient Data Or Too Low Score",
        "name_matches_network_status": "checked",
        "name_matches_network_score": 0.0,
        "network_name": "",
        "created": "2016-12-30T21:14:27Z",
        "author": 134,
        "modified": "2018-04-24T08:16:27Z",
        "updated_by": 134
    },
    "reason": "Test Org",
    "metadata": {
        "my_id": "123ASDAsd123"
    },
    "created": "2018-05-25T19:06:51Z",
    "author": 134,
    "modified": "2018-05-25T19:06:51Z",
    "updated_by": 134,
    "collection": null,
    "account": null,
    "success_message": "",
    "instructions": null,
    "send_instructions": false,
    "status": "pending",
    "error_message": null,
    "expiry_date": "2018-05-26 19:06:51.300273+00:00"
}
```

To create a new collection request, make a POST to the endpoint above, with the attributes below.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
phonenumber | Yes | String | +80000000001 | Must be in international format
first_name | No | String | Luke | Optional subscriber first name - if omitted, the first name will be set to 'Anonymous'
last_name | No | String | Woods | Optional subscriber last name - if omitted, the last name will be set to 'Contact'
amount | Yes | String, Integer or Decimal | 3000 | Do not include thousands separators
currency | Yes | String | BXC | 3 letter ISO currency code. No currency conversion is done, so the currency must be valid for the selected phonenumber. You must have funded Beyonic an account in this currency. If your account for this currency has zero balance, your payment will fail. **Note:**: BXC is the Beyonic Test Currency code. See the "Testing" section for more information. Supported currency codes are BXC (Testing), UGX (Uganda), KES (Kenya)
reason | No | String | Transaction Fees | The reason for this collection request. This is used when sending automated instructions to the customer. If you omit this field, it will be set to your company name. Please keep this field below 20 characters because some networks may truncate it. You are also advised to include your company name so that it's clear who is requesting the funds.
metadata | No | JSON String | "{'my_id': '123ASDAsd123'}" | Custom attributes to store with this object. See the Metadata section for more information.
success_message | No | String (Max 140 characters) | "Thank you for your payment!" | *New in V2.* This message will be sent via SMS to the subscriber when they make a payment for this collection request. '-Powered by Beyonic' shall be appended to this message. If you leave this message out, a default message shall be sent by Beyonic. You can include {amount} and {customer} placeholders - these will be replaced with the amount and customer name or number details before the message is sent.
send_instructions | No | Boolean | False | *New in V2.* Defaults to False (but you probably want to set this to True). Indicates whether we should send payment instructions to the subscriber via SMS. Note that this field defaults to False, so if you want the collection request to actually notify the customer (with a USSD popup and an SMS), you must set this field to True. Omitting the field will prevent collection requests from being sent out to the customer.
instructions | No | String (Max 140 characters) | "Use #1234 as the reference" | *New in V2.* Allows overriding of the default instructions sent to the subscriber. If omitted, default network-specific instructions will be sent to the subscriber via SMS. If you want to skip sending ANY sms instructions and turn off even the default instructions, set this parameter to "skip" (instructions = "skip")
expiry_date | No | Date String | 24 hours | Defaults to "24 hours". Specifies the date and time when this collection request will be marked as expired. Examples of valid values for this field include strings such as "tomorrow", "24 hours", "2 minutes", or %d/%m/%Y format e.g 24/05/2019 or %d/%m/%Y %H:%M:%S format e.g 24/05/2019 13:24:12

## Retrieving a single Collection Request

> Sample Request:

```shell
curl https://app.beyonic.com/api/collectionrequests/427737 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_request = Beyonic::CollectionRequest.get(427737)

p collection_request  # Examine the returned object
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection_request = Beyonic_Collection_Request::get(427737);

print_r($collection_request);  // Examine the returned object
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_request = beyonic.CollectionRequest.get(427737)

print collection_request  # Examine the returned object
```

```java
package com.beyonic.examples;

import com.beyonic.models.CollectionRequest;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    response = new CollectionRequest().get(123);
    System.out.println(response);
}
catch (BeyonicException e){
    e.printStackTrace();
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 427737,
    "organization": 4,
    "amount": "3000.0000",
    "currency": "BXC",
    "phonenumber": "+80000000001",
    "contact": {
        "id": 127694,
        "organization": 4,
        "first_name": "Kennedy",
        "last_name": "Amani",
        "email": null,
        "phone_number": "+80000000001",
        "type": "employee",
        "status": "active",
        "metadata": {},
        "phone_is_supported": "yes",
        "phone_is_mm_registered": "yes",
        "name_on_network": "Insufficient Data Or Too Low Score",
        "name_matches_network_status": "checked",
        "name_matches_network_score": 0.0,
        "network_name": "",
        "created": "2016-12-30T21:14:27Z",
        "author": 134,
        "modified": "2018-04-24T08:16:27Z",
        "updated_by": 134
    },
    "reason": "Test Org",
    "metadata": {
        "my_id": "123ASDAsd123"
    },
    "created": "2018-05-25T19:06:51Z",
    "author": 134,
    "modified": "2018-05-25T19:06:51Z",
    "updated_by": 134,
    "collection": null,
    "account": null,
    "success_message": "",
    "instructions": null,
    "send_instructions": false,
    "status": "pending",
    "error_message": null,
    "expiry_date": "2018-05-26 19:06:51.300273+00:00"
}
```

To retrieve a single collection request, provide the collection request id and a collection request object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 427737 | The id of the collection you want to retrieve

## Listing all Collection Requests

> Sample Request:

```shell
curl https://app.beyonic.com/api/collectionrequests -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_requests = Beyonic::CollectionRequest.list

p collection_requests  # Examine the returned objects
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection_requests = Beyonic_Collection_Request::getAll();

print_r($collection_requests);  // Examine the returned objects
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_requests = beyonic.CollectionRequest.list()

print collection_requests  # Examine the returned objects
```

```java
package com.beyonic.examples;

import com.beyonic.models.CollectionRequest;
import com.beyonic.exceptions.BeyonicException;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    // Pass any extra filter options and headers
    response = new CollectionRequest().list(null, null);
    System.out.println(response);
}
catch (BeyonicException e){
    e.printStackTrace();
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "count": 31271,
    "next": "https://app.beyonic.com/api/collectionrequests?limit=10&offset=10",
    "previous": null,
    "results": [{
        "id": 427737,
        "organization": 4,
        "amount": "3000.0000",
        "currency": "BXC",
        "phonenumber": "+80000000001",
        "contact": {
            "id": 127694,
            "organization": 4,
            "first_name": "John",
            "last_name": "Doe",
            "email": null,
            "phone_number": "+80000000001",
            "type": "employee",
            "status": "active",
            "metadata": {},
            "phone_is_supported": "yes",
            "phone_is_mm_registered": "yes",
            "name_on_network": "Insufficient Data Or Too Low Score",
            "name_matches_network_status": "checked",
            "name_matches_network_score": 0.0,
            "network_name": "",
            "created": "2016-12-30T21:14:27Z",
            "author": 134,
            "modified": "2018-04-24T08:16:27Z",
            "updated_by": 134
        },
        "reason": "Test Org",
        "metadata": {
            "my_id": "123ASDAsd123"
        },
        "created": "2018-05-25T19:06:51Z",
        "author": 134,
        "modified": "2018-05-25T19:06:51Z",
        "updated_by": 134,
        "collection": null,
        "account": null,
        "success_message": "",
        "instructions": null,
        "send_instructions": false,
        "status": "pending",
        "error_message": null,
        "expiry_date": "2018-05-26 19:06:51.300273+00:00"
    }, {
        "id": 427028,
        "organization": 4,
        "amount": "50000.0000",
        "currency": "UGX",
        "phonenumber": "+256XXXXXXXXX",
        "contact": {
            "id": 188525,
            "organization": 4,
            "first_name": "John",
            "last_name": "Doe",
            "email": null,
            "phone_number": "+256XXXXXXXXX",
            "type": "employee",
            "status": "active",
            "metadata": {},
            "phone_is_supported": "yes",
            "phone_is_mm_registered": "yes",
            "name_on_network": "John Doe",
            "name_matches_network_status": "checked",
            "name_matches_network_score": 100.0,
            "network_name": "",
            "created": "2017-05-11T19:04:40Z",
            "author": 134,
            "modified": "2018-02-22T21:00:15Z",
            "updated_by": 432
        },
        "reason": "Test Org",
        "metadata": {
            "my_id": "123ASDAsd123"
        },
        "created": "2018-05-25T14:56:56Z",
        "author": 134,
        "modified": "2018-05-25T14:58:01Z",
        "updated_by": 134,
        "collection": null,
        "account": null,
        "success_message": "",
        "instructions": null,
        "send_instructions": true,
        "status": "pending",
        "error_message": null,
        "expiry_date": "2018-05-26 14:56:55.917817+00:00"
    }, ... ]
}
```

To retrieve a list of all collections, make a GET request to the collections endpoint. This will return a list of collection objects.

## Filtering Collection Requests

> Sample Request:

```shell
curl "https://app.beyonic.com/api/collectionrequests?phonenumber=%2B80000000001&amount=500" -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_requests = Beyonic::CollectionRequest.list(
  phonenumber: '+80000000001',
  amount: 500
)

p collection_requests  # Examine the returned objects
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$collection_requests = Beyonic_Collection_Request::getAll(array(
  "phonenumber" => "+80000000001",
  "amount" => 500
));

print_r($collection_requests);  // Examine the returned objects
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

collection_requests = beyonic.CollectionRequest.list(phonenumber='+80000000001', amount=500)

print collection_requests  # Examine the returned objects
```

```java
package com.beyonic.examples;

import com.beyonic.models.CollectionRequest;
import com.beyonic.exceptions.BeyonicException;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    HashMap<String, String> crFilter = new HashMap<>();
    crFilter.put("amount", "155");
    response = new CollectionRequest().filter(crFilter, null);
    System.out.println(response);
}
catch (BeyonicException e){
    e.printStackTrace();
}
```

You can search or filter collection requests on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only. (The phonenumber field is treated differently - see below).

* amount - the collection request amount
* currency - the currency code
* collection - the ID of the collection that fulfilled or matched this collection request
* phonenumber - the phonenumber that the collection request was intended for. Note that the phonenumber will be matched in international format, starting with a '+' sign. If the '+' sign isn't included in your request, it will be appended before attempting to match your request.
* remote_transaction_id - the transaction id or transaction reference of the collection on the mobile network operator's side
* created_after - only return collection requests created after this date (E.g. 2017-01-01 00:00)
* created_before - only return collection requests created before this date (E.g. 2017-01-01 00:00)