# Events

## Introduction

Events track certain changes that happen to your Beyonic account. Events are created automatically when certain changes occur, and you can use them to stay informed about the changes. For example, when a new contact is created, we will create a "contact.created" event.

You can use the events API to retrieve information about recent events. Events are saved for at least 30 days. After that, they may be discarded.

The events api endpoint is:
        <aside class="notice">https://api.beyonic.com/api/events</aside>

You can also set up your account to automatically notify you as event occur. See the [Webhooks API](#webhooks) for more information about setting up automatic notification for events.

## Supported Events types

The following events are supported

Event | Description | Object returned in data
--------- | -------- | ----------------------
payment.status.changed | Triggered any time a payment changes state. See the "Payments" section for possible state values. | Payment object
collection.received | Triggered any time a collection (an incoming payment) is received from a customer | Collection object
collection.credited | Triggered any time a collection (an incoming payment) is actually credited to a wallet | Collection object
contact.created | Triggered any time a contact is created in your account | Contact object
collectionrequest.status.changed | Triggered any time a collection request status changes. See the "Collection Requests" section for possible status values. | CollectionRequest object

## The Event object

> Sample Event Object (JSON)

```json
{
    "id": 1146,
    "organization":1,
    "type": "contact.created",
    "data": {
        "id": 26,
        "organization": 1,
        "first_name": "Suzanne",
        "last_name": "Kwagala",
        "email": "suzanne@beyonic.com",
        "phone_number": "+80000000001",
        "type": "employee",
        "status": "active",
        "metadata": null,
        "created": "2013-09-19T21:26:10Z",
        "author": 1,
        "modified": "2015-04-14T18:21:47Z",
        "updated_by": 42
    },
    "created": "2013-09-19T21:26:10Z",
    "author": 1,
    "modified": "2015-04-14T18:21:47Z",
    "updated_by": 42
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
organization | long integer | The ID of the organization that the event belongs to. (This is usually your organization ID)
type | string | The event type. See Supported Event Types above
data | object | The details about the event. This is a representation of the object that triggered the event. For example, for the "contact.created" event, this will repesent a "Contact" object
created | string | The date that the event was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
author | long integer | The ID of the user who created the event
modified | string | The date that the event was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated_by | string | The ID of the user who last modified the event

## Retrieving a Single Account

> Sample Request:

```shell
curl https://api.beyonic.com/api/events/12 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

event = Beyonic::Event.get(12)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$event = Beyonic_Event::get(12);
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

event = beyonic.Event.get(12)

```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    response = new Event().get(123);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 1146,
    "organization":1,
    "type": "contact.created",
    "data": {
        "id": 26,
        "organization": 1,
        "first_name": "Suzanne",
        "last_name": "Kwagala",
        "email": "suzanne@beyonic.com",
        "phone_number": "+80000000001",
        "type": "employee",
        "status": "active",
        "metadata": null,
        "created": "2013-09-19T21:26:10Z",
        "author": 1,
        "modified": "2015-04-14T18:21:47Z",
        "updated_by": 42
    },
    "created": "2013-09-19T21:26:10Z",
    "author": 1,
    "modified": "2015-04-14T18:21:47Z",
    "updated_by": 42
}
```

To retrieve a single event, provide the event id and an event object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 12 | The id of the event you want to retrieve

## Listing all Events

> Sample Request:

```shell
curl https://api.beyonic.com/api/events -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

events = Beyonic::Event.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$events = Beyonic_Event::getAll();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

events = beyonic.Event.list()

```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    response = new Event().list(null, null);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "count": 1,
    "next": "https://api.beyonic.com/api/events?offset=10",
    "previous": null,
    "results": [
        {
            "id": 1146,
            "organization":1,
            "type": "contact.created",
            "data": {
                    "id": 26,
                    "organization": 1,
                    "first_name": "Suzanne",
                    "last_name": "Kwagala",
                    "email": "suzanne@beyonic.com",
                    "phone_number": "+80000000001",
                    "type": "employee",
                    "status": "active",
                    "metadata": null,
                    "created": "2013-09-19T21:26:10Z",
                    "author": 1,
                    "modified": "2015-04-14T18:21:47Z",
                    "updated_by": 42
                }
        },
    ]
}
```

To retrieve a list of all events, make a GET request to the events end point. This will return a list of events.

## Filtering Events

> Sample Request:

```shell
curl https://api.beyonic.com/api/events?type=contact.created -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

events = Beyonic::Event.list(
  type: "contact.created"
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$events = Beyonic_Event::getAll(array(
  "type" => "contact.created"
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

events = beyonic.Event.list(type='contact.created')

```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    HashMap<String, String> filterValues = new HashMap<>();
    filterValues.put("type", "contact.created");
    response = new Event().filter(filterValues, null);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

You can search or filter events on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only.

* type - the event type
