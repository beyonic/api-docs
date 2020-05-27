# Contacts

## Introduction

Contacts represent people whom you can send payments to, or receive payments from. The contacts api method allows you to add, retrieve, list and update contacts in your Beyonic account. Contacts are also added automatically whenever you send a payment to a new phone number.

The contacts api endpoint is:
    <aside class="notice">https://app.beyonic.com/api/contacts</aside>

## The Contact object

> Sample Contact Object (JSON)

```json
{
    "id": 26,
    "organization": "Beyonic",
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
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
organization | long integer | The ID of the organization that the contact belongs to. (This is usually your organization ID)
first_name | string | The contact's first name
last_name | string | The contact's last name
email | string | The contact's email address
phone_number | string | The contact's phone number, in international format, starting with a +
type | string | The contact's type, as set in the system. One of: employee, beneficiary, vendor, other OR a custom string
status | string | The contact's status. One of: active or inactive
metadata | hash | Any custom metadata that was added to the contact object at creation time
created | string | The date that the contact was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
author | long integer | The ID of the user who created the contact
modified | string | The date that the contact was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated_by | string | The ID of the user who last updated the contact


## Creating a new Contact

> Sample Request:

```shell
curl https://app.beyonic.com/api/contacts -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d first_name='John' \
-d last_name='Doe' \
-d phone_number='+80000000001' \
-d email='john.doe@beyonic.com' \
-d metadata.my_id='123ASDAsd123'
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contact = Beyonic::Contact.create(
    phone_number: "+80000000001",
    first_name: "John",
    last_name: "Doe",
    email: "john.doe@beyonic.com",
    metadata: {"my_id": "123ASDAsd123"}
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$contact = Beyonic_Contact::create(array(
  "phone_number" => "+80000000001",
  "first_name" => "John",
  "last_name" => "Doe",
  "email" => "john.doe@beyonic.com",
  "metadata" => array("my_id"=>"123ASDAsd123")
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contact = beyonic.Contact.create(phone_number='+80000000001',
                       first_name='John',
                       last_name='Doe',
                       email='john.doe@beyonic.com',
                       metadata={'my_id': '123ASDAsd123'}
                       )
```

```java
package com.beyonic.examples.contacts

import org.json.JSONException;
import org.json.JSONObject;
import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
import static org.junit.Assert.assertTrue;

public class CreateContactExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/contacts";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";
    private static final String FIRST_NAME = "John";
    private static final String LAST_NAME = "Doe";
    private static final String PHONE_NUMBER = "+80000000001";
    private static final String EMAIL = "john.doe@beyonic.com";

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

            JSONObject contact = createContactObject();

            BufferedWriter out = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
            out.write(contact.toString());
            out.close();

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

            int beyID = 0;
            try {
                if (conn.getResponseCode() == 201) {
                    InputStream inputStream = conn.getInputStream();
                    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
                    String response = reader.readLine();
                    reader.close();

                    JSONObject obj = new JSONObject(response);
                    beyID = obj.getInt("id");
                    System.out.println("ID of created Contact: " + beyID);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static JSONObject createContactObject() throws JSONException {
        JSONObject contact = new JSONObject();
        contact.put("phone_number",PHONE_NUMBER);
        contact.put("first_name",FIRST_NAME);
        contact.put("last_name",LAST_NAME);
        contact.put("email",EMAIL);
        return contact;
    }
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 26,
    "organization": "Beyonic",
    "first_name": "Suzanne",
    "last_name": "Kwagala",
    "email": "suzanne@beyonic.com",
    "phone_number": "+80000000001",
    "type": "employee",
    "status": "active",
    "metadata": {
        "my_id": "123ASDAsd123"
    },
    "created": "2013-09-19T21:26:10Z",
    "author": 1,
    "modified": "2015-04-14T18:21:47Z",
    "updated_by": 42
}
```

To create a new contact, make a POST to the endpoint above, with the attributes below.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
phone_number | Yes | String | +80000000001 | Must be in international format
first_name | Yes | String | John | The contact's first name
last_name | Yes | String | Doe | The contact's last name
email | No | String | john.doe@beyonic.com | The contact's email address
metadata | No | JSON String | "{'my_id': '123ASDAsd123'}" | Custom attributes to store with this object. See the Metadata section for more information.

## Retrieving a single contact

> Sample Request:

```shell
curl https://app.beyonic.com/api/contacts/44702 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contact = Beyonic::Contact.get(44702)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$contact = Beyonic_Contact::get(44702);
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contact = beyonic.Contact.get(44702)

```

```java
package com.beyonic.examples.contact;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;


public class SingleContactExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/contacts";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/44702");
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
    "id":44702,
    "organization":4,
    "first_name":"John",
    "last_name":"Doe",
    "email":null,
    "phone_number":"+80000000001",
    "type":"employee",
    "status":"active",
    "metadata":{},
    "created":"2016-03-30T17:44:30Z",
    "author":134,
    "modified":"2016-03-30T17:45:22Z",
    "updated_by":134
}
```

To retrieve a single contact, provide the contact id and a contact object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 23 | The id of the contact you want to retrieve

## Listing all Contacts

> Sample Request:

```shell
curl https://app.beyonic.com/api/contacts -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contacts = Beyonic::Contact.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$contacts = Beyonic_Contact::getAll();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contacts = beyonic.Contact.list()

```

```java
package com.beyonic.examples.contacts

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;

public class ListAllContactsExample{

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/contacts";
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
    "next": "https://app.beyonic.com/api/contacts?offset=10",
    "previous": null,
    "results": [
        {
            "id": 1146,
            "organization": "Demo Org",
            "first_name": "Eliana",
            "last_name": "Kalema",
            "email": null,
            "phone_number": "+25677XXXXXX",
            "type": "other",
            "status": "active",
            "metadata": null,
            "created": "2015-02-13T04:22:31Z",
            "author": 7,
            "modified": "2015-02-26T13:00:35Z",
            "updated_by": 1
        },
        {
            "id": 1175,
            "organization": "Demo Org",
            "first_name": "Trina",
            "last_name": "Faith",
            "email": null,
            "phone_number": "+25677XXXXXX",
            "type": "employee",
            "status": "active",
            "metadata": null,
            "created": "2015-02-18T11:52:43Z",
            "author": 137,
            "modified": "2015-02-26T13:00:35Z",
            "updated_by": 1
        },
    ]
}
```

To retrieve a list of all contacts, make a GET request to the contact end point. This will return a list of contacts.

## Filtering Contacts

> Sample Request:

```shell
curl https://app.beyonic.com/api/contacts?first_name=luke -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contacts = Beyonic::Contact.list(
  first_name: "luke"
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$contacts = Beyonic_Contact::getAll(array(
  "first_name" => "luke"
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

contacts = beyonic.Contact.list(first_name='luke')

```

```java
package com.beyonic.examples.contact;

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;

public class FilterContactsExample{

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/contacts";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "?first_name=John");
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

You can search or filter contacts on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only. (The phone_number field is treated differently - see below).

* first_name - the contact's first name
* last_name - the contact's last name
* email - the contact's email
* phone_number - the contact's phone number. Note that the phonenumber will be matched in international format, starting with a '+' sign. If the '+' sign isn't included in your request, it will be appended before attempting to match your request.
* created_after - only return contacts created after this date (E.g. 2017-01-01 00:00)
* created_before - only return contacts created before this date (E.g. 2017-01-01 00:00)

## Instant contact verification 
> Sample Request:

```shell
curl https://app.beyonic.com/api/contacts -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d first_name='John' \
-d last_name='Doe' \
-d phone_number='+80000000001' \
-d email='john.doe@beyonic.com' \
-d metadata.sync='1'
```

Sample Response (JSON)  

```json
{
    "id": 5523949,
    "organization": 4,
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@beyonic.com",
    "phone_number": "+80000000001",
    "type": "employee",
    "status": "active",
    "metadata": {
        "sync": "1",
        "s_contact4gender": null,
        "s_contact4age": null,
        "s_contact4partner_id": null,
        "s_contact4tester": null,
        "s_contact4another_one": null,
        "s_contact4tested": null
    },
    "phone_is_supported": "yes",
    "phone_is_mm_registered": "yes",
    "name_on_network": "James Doe",
    "name_matches_network_status": "checked",
    "name_matches_network_score": 34.0,
    "network_name": "",
    "created": "2020-05-21T09:46:07Z",
    "author": 4926,
    "modified": "2020-05-21T09:46:10Z",
    "updated_by": 4926,
    "national_id": null,
    "national_id_type": "national"
}
```

Instant contact verification API can be used to verify the names of a contact from our third party connections.

**Note**: 
* The API carries a charge, please contact support@beyonic.com for more info.
* The API is only supported in Uganda for the following networks: Airtel, Mtn and Msente.
* The sample requests provided won't work because +800 aren't real phone numbers. Please test with real phone numbers.

To use the API:
1) Add the keyword "sync" with a value of 1 or "1" to your contact metadata.
2) Use the contact read API if a contact already exists and  the contact creation API if it's a new contact.
3) Verification is done instantly, so when the API call returns, you'll have the validation data.
4) Verification data will include the full names + scoring information even if there is no match with the one you provided.


