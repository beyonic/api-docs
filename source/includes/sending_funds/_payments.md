# Payments

To send payments (mobile money or prepaid airtime credit) to a mobile subscriber, you create a new payment object using the payments API. You can also use the payments API to retrieve individual payments or list all payments, as shown in the sections below.

The payments api endpoint is:
    <aside class="notice">https://app.beyonic.com/api/payments</aside>

## The Payment object

> Sample Payment Object (JSON):

```json
{
   "id" : 22744,
   "cancelled_time" : null,
   "start_date" : "2016-03-31T08:08:01Z",
   "modified" : "2018-02-13T06:02:20Z",
   "phone_nos" : [
      {
         "metadata" : null,
         "amount" : "1200.0000",
         "last_error" : "Recipient mobile money readiness checks failed.+256773712831",
         "state" : "error",
         "contact" : "2978",
         "batch_id" : 437751,
         "description" : "Per diem",
         "phonenumber" : "+256773712831",
         "payment_id" : 123209
      }
   ],
   "organization" : 4,
   "author" : 134,
   "created" : "2016-03-31T08:08:01Z",
   "send_sms_message" : false,
   "updated_by" : 134,
   "rejected_time" : null,
   "metadata" : {
      "s_batchpaymentschedule4123" : null
   },
   "remote_transaction_id" : null,
   "cancelled_by" : null,
   "charged_fee" : 0,
   "account" : 48,
   "cancelled_reason" : null,
   "transactions" : [],
   "rejected_by" : null,
   "description" : "Per diem",
   "currency" : "UGX",
   "amount" : "1200.00",
   "rejected_reason" : null,
   "payment_type" : "money",
   "last_error" : "Last Error: System error. - Recipient mobile money readiness checks failed.+256773712831",
   "state" : "processed_with_errors"
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
organization | long integer | The ID of the organization that the payment belongs to. (This is usually your organization ID)
payment_type | String | The payment type. Either "money" for mobile money or "airtime" for airtime
amount | decimal | The payment amount
currency | string | The 3 letter ISO currency code for the payment. **Note:**: BXC is the Beyonic Test Currency code. See the "Testing" section for more information. Supported currency codes are BXC (Testing), UGX (Uganda), KES (Kenya)
account | long integer | The ID of the account from which the payment is made
description | string | The payment description
phone_nos | dictionary | A dictionary of phone numbers that this payment was sent to and the amounts sent to each number **New in v5** Before v5, this was a list of phone numbers only
start_date | string | The date that the payment is scheduled to be delivered, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
metadata | hash | Any custom metadata that was added to the contact object at creation time
state | string | The payment state. One of: new, validated, approval_needed, approval_requested, approved, rejected, scheduled, processed, processed_with_errors or cancelled
last_error | string | A string description of the latest error that occurred, if any
rejected_reason | string | Describes why the payment was rejected, if it is in the rejected state
rejected_time | string | The date that the payment was rejected, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
rejected_by | long integer | The ID of the user who rejected the payment
cancelled_reason | string | Describes why the payment was cancelled, if it is in the cancelled state
cancelled_time | string | The date that the payment was cancelled, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
cancelled_by long integer | The ID of the user who cancelled the payment
created | string | The date that the payment was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
author | long integer | The ID of the user who created the payment
modified | string | The date that the payment was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated_by | string | The ID of the user who last updated the payment
remote_transaction_id | string | The transaction id provided by the mobile network operator. This will only be available once the payment is completed.
send_sms_message | boolean | Defaults to False. Indicates whether we should send the description as a separate sms message to the consumer. Additional SMS fees apply. See https://beyonic.com/pricing for fees information. When SMS is enabled, can use the following placeholders in the description for personalization: {firstname}, {fullname}, {phone}, {amount}
charged_fee | decimal | The fee that was charged. It is only available for completed payments.

## Creating a new Payment

> Sample Request:

```shell
curl https://app.beyonic.com/api/payments -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d phonenumber=+80000000001 \
-d first_name=Kennedy\
-d last_name=Amani\
-d currency=BXC \
-d amount=30 \
-d description="Per diem payment" \
-d callback_url="https://my.website/payments/callback" \
-d payment_type=money
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payment = Beyonic::Payment.create(
    phonenumber: "+80000000001",
    first_name: "Kennedy",
    last_name: "Amani",
    amount: "100.2",
    currency: "BXC",
    description: "Per diem payment",
    payment_type: "money",
    callback_url: "https://my.website/payments/callback",
    metadata: {id: "1234", name: "Lucy"}
)

p payment  # Examine the returned object
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$payment = Beyonic_Payment::create(array(
  "phonenumber" => "+80000000001",
  "first_name" => "Kennedy",
  "last_name" => "Kennedy",
  "amount" => "100.2",
  "currency" => "BXC",
  "description" => "Per diem payment",
  "payment_type" => "money",
  "callback_url" => "https://my.website/payments/callback",
  "metadata" => array("id"=>"1234", "name"=>"Lucy")
));

print_r($payment);  // Examine the returned object
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payment = beyonic.Payment.create(phonenumber='+80000000001',
                       first_name='Kennedy',
                       last_name='Amani',
                       amount='1200',
                       currency='BXC',
                       description='Per diem',
                       callback_url='https://my.website/payments/callback',
                       metadata={'id': '1234', 'name': 'Lucy'}
                       )

print payment  # Examine the returned object
```

```java
package com.beyonic.examples.payments;

import org.json.JSONException;
import org.json.JSONObject;
import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;

public class CreatePayment {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/payments";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";
    private static final String PHONE_NUMBER = "+80000000001";
    private static final String CURRENCY = "BXC";
    private static final String DESCRIPTION = "Per Diem";
    private static final String AMOUNT = "1200";
    private static final String CALLBACK_URL = "https://my.website/payments/callback";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);
            conn.setRequestMethod("POST");
            conn.setDoOutput(true);
            conn.setDoInput(true);

            JSONObject paymentObject = createPaymentObject();

            BufferedWriter out = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
            out.write(paymentObject.toString());
            out.close();

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            try {
                if (conn.getResponseCode() == 201) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    JSONObject obj = new JSONObject(response);
                    int paymentID = obj.getInt("id");
                    System.out.println("ID of created Payment: " + paymentID);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static JSONObject createPaymentObject() throws JSONException {
        JSONObject payment = new JSONObject();
        payment.put("phonenumber", PHONE_NUMBER);
        payment.put("amount", AMOUNT);
        payment.put("currency", CURRENCY);
        payment.put("description", DESCRIPTION);
        payment.put("callback_url", CALLBACK_URL);
        return payment;
    }
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
   "id" : 22744,
   "cancelled_time" : null,
   "start_date" : "2016-03-31T08:08:01Z",
   "modified" : "2018-02-13T06:02:20Z",
   "phone_nos" : [
      {
         "metadata" : null,
         "amount" : "1200.0000",
         "last_error" : "Recipient mobile money readiness checks failed.+256773712831",
         "state" : "error",
         "contact" : "2978",
         "batch_id" : 437751,
         "description" : "Per diem",
         "phonenumber" : "+256773712831",
         "payment_id" : 123209
      }
   ],
   "organization" : 4,
   "author" : 134,
   "created" : "2016-03-31T08:08:01Z",
   "send_sms_message" : false,
   "updated_by" : 134,
   "rejected_time" : null,
   "metadata" : {
      "s_batchpaymentschedule4123" : null
   },
   "remote_transaction_id" : null,
   "cancelled_by" : null,
   "charged_fee" : 0,
   "account" : 48,
   "cancelled_reason" : null,
   "transactions" : [],
   "rejected_by" : null,
   "description" : "Per diem",
   "currency" : "UGX",
   "amount" : "1200.00",
   "rejected_reason" : null,
   "payment_type" : "money",
   "last_error" : "Last Error: System error. - Recipient mobile money readiness checks failed.+256773712831",
   "state" : "processed_with_errors"
}
```

To create a new payment, make a POST to the end point above, with the attributes below.

**Note**:

* A contact is created for each new phone number you send payments to. This gives you the benefits of the pre-verification checks that are done prior to payment processing.
* If the contact isn't registered for mobile money, then the payment will not be sent.
* **Important**: One of the pre-verification checks does a name match check to make sure that the contact name is right. This check can cause your payments to fail if the names that you are sending are not the same as the names in the telecom database. If you want to by-pass this check, go into your settings in your account and uncheck the checkbox that says "Name match affects payment".
* **Important**: The account and currency parameters are both optional but you must provide one of them. If you provide both them then the account's currency must match the currency parameter.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
phonenumber | Yes* | String | +80000000001 | Must be in international format. * Only required if recipient_data is not set.
payment_type | No | String | money | Options: money (default), airtime - use "airtime" to send an airtime payment instead of a mobile money payment
amount | Yes | String, Integer or Decimal | 3000 | Do not include thousands separators
currency | No | String | BXC | 3 letter ISO currency code. No currency conversion is done, so the currency must be valid for the selected phonenumber. You must have a funded Beyonic account in this currency. If your account for this currency has zero balance, your payment will fail. If you also provide an account parameter then the account's currency must match the currency parameter. **Note:**: BXC is the Beyonic Test Currency code. See the "Testing" section for more information. Supported currency codes are BXC (Testing), UGX (Uganda), KES (Kenya)
account | No | Integer | 1 | The ID of the account from which you are making the payment. The account must be active and funded. If the account has zero balance, your payment will fail. This parameter is optional if a currency is provided. If you have more than one account with the same currency, and you leave this parameter out, the earliest created account (oldest account) that is still active will be used.
description | Yes | String | Per diem payment | This description will be sent to the recipient along with the payment, so it should be limited to about 140 characters.
callback_url | No | String | https://my.website/payments/callback | Callback URLs are used to send notifications of changes in payment status. Not all payments will be completed immediately, especially if it is a payment to a new number that hasn’t been verified, or if your account has approval rules that require other users to approve payments before they are processed. Note that the URL you submit isn’t specific to a specific payment you have created. Once submitted, it will receive notifications for all future payments made via your organization, whether they are made via the API or via the web-interface. Therefore, you are encouraged to use the same URL for all payments. Since URLs are stored at a per-organization level, using different URLs may result in duplicate notifications being sent to the different URLs. See "Webhooks" below for more info.
metadata | No | JSON-formatted string or dictionary | "{'id':'1234','name':'Lucy'}" | Metadata allows you to add custom attributes to your payments. E.g. You can include a unique ID to identify each payment. Attributes must be key-value pairs. Both the keys and values must be strings. You can add up to 10 attributes. This data will be returned when you retrieve a payment.
first_name | No | String | John | If this payment is to a new contact, you can include their first name. This name will only be used if the phone number is new.
last_name | No | String | Doe | If this payment is to a new contact, you can include their last name. This name will only be used if the phone number is new.
recipient_data | No | JSON-formatted list of dictionaries | [{'phonenumber': '+256712321232', 'amount': 500},...] | Used to send multiple payments in one request. See the section: ["Creating multiple Payments"](#create-multiple-payments) Below for more details

**Responses**

* If successful, a JSON representation of the new payment object will be returned. The state field will be "new"
* The payment object can be identified using it’s id field, or the metadata that you supplied.
* On subsequent notification callbacks to your callback URL, the same payment object (with the same id field) will be returned. Check the state field for the latest payment status.
* The state field values can be:
    * new – for new payments
    * processed – for successfully completed payments
    * processed_with_errors – for payments that didn’t complete successfully. The last_error field with have more information.
    * rejected – for payments that were rejected during the approval process. The following fields will have more information: rejected_reason, rejected_by and rejected_time
    * cancelled – for payments that were cancelled. The following fields will have more information: cancelled_reason, cancelled_by and cancelled_time

## Creating multiple Payments
```shell
curl -d '{"currency": "BXC", "description": "Test response on mutiples", "callback_url": "https://my.url.com/callback", "payment_type": "money", "account": 1, "recipient_data": "[\\n {\\n  \\"amount\\": 1,\\n \\"phonenumber\\": \\"+80000000001\\",\\n \\"first_name\\": \\"Jerry\\",\\n  \\"last_name\\": \\"Airtel\\",\\n \\"description\\": \\"This is a payment to {first_name}. This desc is in child obj.\\"\\n\\n  },\\n {\\n  \\"phonenumber\\": \\"+80000000002\\",\\n \\"description\\": \\"What the response?\\",\\n  \\"amount\\":5\\n }\\n ]\\n "}' \
-H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-H "Content-type : application/json" \
https://app.beyonic.com/api/payments
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

recipient_data = [
                   {phonenumber: "+254727447101",first_name: "Jerry", last_name: "Shikanga", amount: 500, description: "Per diem payment2"},
                   {phonenumber: "+254739936708", amount: 30000, description:"Salary for January2"}
                 ]
                 
payment = Beyonic::Payment.create(
    currency: "BXC",
    description: "Per diem payment",
    payment_type: "money",
    callback_url: "https://my.website/payments/callback",
    metadata: {id: "1234", name: "Lucy"},
    recipient_data: recipient_data.to_json
)

p payment  # Examine the returned object
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$recipient_data = [
	array("phonenumber" => "+254727447101", "first_name" => "Jerry", "last_name" => "Shikanga", "amount" => 500, "description" => "Per diem payment"),
	array("phonenumber" => "+254739936708", "amount" => 30000, "description" => "Salary for January")

];

$payment = Beyonic_Payment::create(array(
  "currency" => "BXC",
  "description" => "Per diem payment",
  "payment_type" => "money",
  "callback_url" => "https://my.website/payments/callback",
  "metadata" => array("id"=>"1234", "name"=>"Lucy"),
  "recipient_data" => json_encode($recipient_data)
));

print_r($payment);  // Examine the returned object
?>
```

```python
import json
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

recipients = [
    {"amount":54, "phonenumber":"+80000000001"},
    {"amount":23, "phonenumber":"+80000000023"},
]
payment = beyonic.Payment.create(
                       currency='BXC',
                       description='Per diem',
                       callback_url='https://my.website/payments/callback',
                       metadata={'id': '1234', 'name': 'Lucy'},
                       recipient_data=json.dumps(recipients)
                       )

print payment  # Examine the returned object
```

```java
package com.beyonic.samples;

import com.beyonic.models;
import com.beyonic.exceptions.BeyonicException;
import org.json.JSONArray;
import org.json.JSONObject;

// Multiple payments
JSONObject recipient1 = new JSONObject();
recipient1.put("amount", 15);
recipient1.put("phonenumber", "+80000000001");
JSONObject recipient2 = new JSONObject();
recipient2.put("amount", 25);
recipient2.put("phonenumber", "80000000005");
recipient2.put("description", "Cool guy benefits");
JSONArray recipientsArray = new JSONArray();
recipientsArray.put(recipient1);
recipientsArray.put(recipient2);

HashMap<String, Object> mutiplePayments = new HashMap<>();
mutiplePayments.put("recipient_data", recipientsArray.toString());
mutiplePayments.put("currency", "BXC");
mutiplePayments.put("description", "Per diem payment");
mutiplePayments.put("payment_type", "airtime");
response = new Payment().create(mutiplePayments, null);
System.out.println(response);
```

Beyonic now supports creation of multiple payments via API. When multiple payments are created via one API call, they will all be part of the same payment schedule, making it easier for you to perform bulk operations such as approval. 

To create a bulk payment, you have to include an extra field called "recipient_data" in the request payload. 

recipient_data should be a list of dictionaries, each containing the following fields :
    * first_name, 
    * last_name, 
    * phonenumber, 
    * amount 
    * description
    
Phonenumber is a mandatory field. 
Amount can be nullable, but if missing then it must be set in the main payload. In this case each user will be paid the same amount in the payload. If provided in the recipient_data dictionaries then each user will be paid the amounts specified in the recipient_data.
The rest of the fields are optional.

> The following is a sample response

```json
{
   "id" : 22744,
   "cancelled_time" : null,
   "start_date" : "2016-03-31T08:08:01Z",
   "modified" : "2018-02-13T06:02:20Z",
   "phone_nos" : [
      {
         "metadata" : null,
         "amount" : "1200.0000",
         "last_error" : "Recipient mobile money readiness checks failed.+256773712831",
         "state" : "error",
         "contact" : "2978",
         "batch_id" : 437751,
         "description" : "Per diem",
         "phonenumber" : "+254727447101",
         "payment_id" : 123209
      },
      {
         "metadata" : null,
         "amount" : "1200.0000",
         "last_error" : "Recipient mobile money readiness checks failed.+256773712831",
         "state" : "error",
         "contact" : "2978",
         "batch_id" : 437751,
         "description" : "Per diem",
         "phonenumber" : "+254739936708",
         "payment_id" : 123209
      }
   ],
   "organization" : 4,
   "author" : 134,
   "created" : "2016-03-31T08:08:01Z",
   "send_sms_message" : false,
   "updated_by" : 134,
   "rejected_time" : null,
   "metadata" : {
      "s_batchpaymentschedule4123" : null
   },
   "remote_transaction_id" : null,
   "cancelled_by" : null,
   "charged_fee" : 0,
   "account" : 48,
   "cancelled_reason" : null,
   "transactions" : [],
   "rejected_by" : null,
   "description" : "Per diem",
   "currency" : "UGX",
   "amount" : "1200.00",
   "rejected_reason" : null,
   "payment_type" : "money",
   "last_error" : "Last Error: System error. - Recipient mobile money readiness checks failed.+256773712831",
   "state" : "processed_with_errors"
}
```


## Retrieving a single Payment

> Sample Request:

```shell
curl https://app.beyonic.com/api/payments/22744 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payment = Beyonic::Payment.get(22744)

p payment  # Examine the returned object
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$payment = Beyonic_Payment::get(22744);

print_r($payment);  // Examine the returned object
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payment = beyonic.Payment.get(22744)

print payment  # Examine the returned object
```

```java
package com.beyonic.examples.payments;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;


public class SinglePaymentExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/payments";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/22744");
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
   "id" : 22744,
   "cancelled_time" : null,
   "start_date" : "2016-03-31T08:08:01Z",
   "modified" : "2018-02-13T06:02:20Z",
   "phone_nos" : [
      {
         "metadata" : null,
         "amount" : "1200.0000",
         "last_error" : "Recipient mobile money readiness checks failed.+256773712831",
         "state" : "error",
         "contact" : "2978",
         "batch_id" : 437751,
         "description" : "Per diem",
         "phonenumber" : "+256773712831",
         "payment_id" : 123209
      }
   ],
   "organization" : 4,
   "author" : 134,
   "created" : "2016-03-31T08:08:01Z",
   "send_sms_message" : false,
   "updated_by" : 134,
   "rejected_time" : null,
   "metadata" : {
      "s_batchpaymentschedule4123" : null
   },
   "remote_transaction_id" : null,
   "cancelled_by" : null,
   "charged_fee" : 0,
   "account" : 48,
   "cancelled_reason" : null,
   "transactions" : [],
   "rejected_by" : null,
   "description" : "Per diem",
   "currency" : "UGX",
   "amount" : "1200.00",
   "rejected_reason" : null,
   "payment_type" : "money",
   "last_error" : "Last Error: System error. - Recipient mobile money readiness checks failed.+256773712831",
   "state" : "processed_with_errors"
}
```

To retrieve a single payment object, provide the payment id and a payment object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 2314 | The id of the payment you want to retrieve

## Listing all Payments

> Sample Request:

```shell
curl https://app.beyonic.com/api/payments -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payments = Beyonic::Payment.list

p payments  # Examine the returned objects
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$payments = Beyonic_Payment::getAll();

print_r($payments);  // Examine the returned objects
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payments = beyonic.Payment.list()

print payments  # Examine the returned objects
```

```java
package com.beyonic.examples.payments;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class ListAllPaymentsExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/payments";
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
    "count": 2,
    "next": "http://app.beyonic.com/api/payments?offset=10",
    "previous": null,
    "results": [
        {
            "id": 1,
            "organization": "Beyonic",
            "amount": "0.0000",
            "currency": "BXC",
            "account": "1",
            "payment_type": "money",
            "metadata": null,
            "description": "Test",
            "phone_nos": [
                "+80000000001"
            ],
            "state": "approved",
            "last_error": null,
            "rejected_reason": null,
            "rejected_by": null,
            "rejected_time": null,
            "cancelled_reason": null,
            "cancelled_by": null,
            "cancelled_time": null,
            "created": "2013-08-20T17:21:39Z",
            "author": 1,
            "modified": "2013-08-20T17:21:39Z",
            "updated_by": null,
            "start_date": "2013-08-20T00:00:00Z",
            "charged_fee": 0
        },
        {
            "id": 2,
            "organization": "Beyonic",
            "amount": "200.0000",
            "currency": "BXC",
            "account": "2",
            "payment_type": "money",
            "metadata": null,
            "description": "Test2",
            "phone_nos": [
                "+80000000001"
            ],
            "state": "scheduled",
            "last_error": null,
            "rejected_reason": null,
            "rejected_by": null,
            "rejected_time": null,
            "cancelled_reason": null,
            "cancelled_by": null,
            "cancelled_time": null,
            "created": "2013-08-22T01:53:35Z",
            "author": 1,
            "modified": "2013-08-22T01:53:36Z",
            "updated_by": 1,
            "start_date": "2013-08-21T00:00:00Z",
            "charged_fee": 0
        },
   ]
}
```

To return a list of all payments, make a GET request to the payments endpoint. This will return a list of payment objects.

## Filtering Payments

> Sample Request:

```shell
curl https://app.beyonic.com/api/payments?amount=500 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payments = Beyonic::Payment.list(
  amount: 500
)

p payments  # Examine the returned objects
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$payments = Beyonic_Payment::getAll(array(
  "amount" => 500
));

print_r($payments);  // Examine the returned objects
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

payments = beyonic.Payment.list(amount=500)

print payments  # Examine the returned objects
```

```java
package com.beyonic.examples.payments;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class FilterPaymentsExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/payments";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "?amount=500");
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

You can search or filter payments on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only.

* amount - the payment amount
* currency - the payment currency code
* payment_type - the payment type
* created_after - only return payments created after this date (E.g. 2017-01-01 00:00)
* created_before - only return payments created before this date (E.g. 2017-01-01 00:00)
