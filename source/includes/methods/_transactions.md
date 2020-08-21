# Transactions

## Introduction
Transactions are funds movements that happened to your account. This includes Mobile Payments, Airtime Payments,
Collections, Service Charges, Account Balance Funds Update.The transactions API allows you to list all these
transactions.

The transactions api endpoint is:

    <aside class="notice">https://api.beyonic.com/api/transactions</aside>

## The Transaction object

> Sample Transaction Object (JSON):

```json
{
    "id": 218,
    "account": 12,
    "amount": "228.0000",
    "description": "Incoming payment from +401000000099",
    "created": "2017-03-28T12:50:59Z",
    "type": "mobile_collection"
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
account | long integer | The ID of the account which this transaction belongs to
amount | float | The transaction amount
description | string | The payment description
created | string | The date that the transaction was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
type | string | The transaction type. This can be admin_funds_update, mobile_funds_update, funds_deposit, funds_adjustment, mobile_collection, mobile_payment, airtime_topup, service_charge, mobile_withdrawal, bank_withdrawal

## Retrieving a Single Transaction

> Sample Request:

```shell
curl https://api.beyonic.com/api/transactions/12 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transaction = Beyonic::Transaction.get(12)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$transaction = Beyonic_Transaction::get(12);
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transaction = beyonic.Transaction.get(12)
```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    response = new Transaction().get(123);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
   "id": 12,
   "account": 123,
   "amount": "228.0000",
   "description": "Incoming payment from +401000000099",
   "created": "2017-03-28T12:50:59Z",
   "type": "mobile_collection"
}
```

To retrieve a single transaction, provide the transaction id and a transaction object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 12 | The id of the transaction you want to retrieve

## Listing all transactions

> Sample Request:

```shell
curl https://api.beyonic.com/api/transactions -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transactions = Beyonic::Transaction.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$transactions = Beyonic_Transaction::getAll();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transactions = beyonic.Transaction.list()

```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    response = new Transaction().list(null, null);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

> Sample Response (JSON)  - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
  "count": 2,
  "next": "https://api.beyonic.com/api/transactions?limit=10&offset=10",
  "previous": null,
  "results": [
    {
      "id": 137,
      "account": 123,
      "amount": "2000.0000",
      "description": "Complementary Test Credit.",
      "created": "2016-12-09T11:16:30Z",
      "type": "admin_funds_transfer"
    },
    {
      "id": 160,
      "account": 123,
      "amount": "-600.0000",
      "description": "test msente",
      "created": "2017-02-13T12:01:38Z",
      "type": "mobile_payment"
    }
  ]
}
```

To retrieve a list of all transactions, make a GET request to the transactions end point. This will return a list of transactions.

## Filtering Transactions

> Sample Request:

```shell
curl https://api.beyonic.com/api/transactions?type=mobile_payment -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transactions = Beyonic::Transaction.list(
  type: 'mobile_payment'
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$transactions = Beyonic_Transaction::getAll(array(
  "type" => "mobile_payment"
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

transactions = beyonic.Transaction.list(type='mobile_payment')

```

```java
package com.beyonic.samples;

import com.beyonic.exceptions.BeyonicException;
import com.beyonic.models.*;

Beyonic.API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";

String response = null;

try{
    HashMap<String, String> filterValues = new HashMap<>();
    filterValues.put("type", "mobile_payment");
    response = new Transaction().filter(filterValues, null);
    System.out.println(response);
}
catch (Exception e){
    e.printStackTrace();
}
```

You can search or filter transactions on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only.

* account - the account id
* type - the type of the transaction
* created_after - only return transactions created after this date (E.g. 2017-01-01 00:00)
* created_before - only return transactions created before this date (E.g. 2017-01-01 00:00)
* start_date - the earliest date you want to include transactions from, inclusive (E.g. 2017-01-01 00:00)
* end_date - the latest date you want to include transactions, inclusive (E.g. 2017-01-01 00:00)
