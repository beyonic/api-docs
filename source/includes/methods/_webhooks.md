# Webhooks

## Introduction

Webhooks or callback URLs allow you to define URLs on your server that notifications should be sent to when specific events occur in your Beyonic account.

The webhooks api endpoint is:

    <aside class="notice">https://app.beyonic.com/api/webhooks</aside>

**Note:**

* Non-HTTPS URLs will be rejected. For testing with a self signed certificate, see the "Testing webhooks" section below.
* Data will be posted to the URL, and a 200 response is expected.
* A non 200 response will result in an error, and Beyonic will retry the request a total of 10 times, with an increasing wait period between each try.
* Beyonic implements a 5 second timeout on webhook requests. See **5 second timeout** below for more information.
* A 410 (gone) response will lead to deletion of the webhook
* The URL you submit isn’t specific to a specific object. Once submitted, it will receive notifications for all future events of the specific type made in your organization, whether they are made via the API or via the web-interface.
* Therefore, you are encouraged to use the one URL for each event type. Since URLs are stored at a per-organization level, using different URLs may result in duplicate notifications being sent to the different URLs.
* Previously submitted urls can be deleted via the web browser, or the Webhooks API methods described elsewhere in this reference.

**5 second timeout**

Beyonic implements a 5 second timeout. We wait 5 seconds for a response to each request, and if there isn't one, or there is an error, we will retry the request a total of 10 times, with an increasing wait period between each attempt.

Because of this timeout, we recommend that your webhook responds immediately with a 200 response, and then you continue processing the request. For example, you could store the event information, respond and then continue with your tasks.

Depending on the server and language you are using, there are various mechanisms for achieving this, including flushing() the output in PHP or using a message queue and background tasks in other languages.

## Supported Event types

See the [Events API](#events) for a list of supported event types.

## Managing Webhooks

You can create and manage webhooks in two ways:

* By logging into your Beyonic account via the web browser as described below
* By using the Webhooks API described below

**Managing Webhooks from the web browser**

To manage webhooks using the web browser:
1. Log into your account.
2. Go "Home" > Settings (Cogwheel) > "Organization Settings" > "Advanced Settings" > "Notification Endpoints".

**Managing Webhooks from the API**

You can use the Webhooks API described below to update or delete your callback URLs programatically.

**Required permissions**

To manage webhooks, either with the API or the web browser, you must have “Manage Users and Organization Settings” permissions for your organization.

## The Webhook object

> Sample Webhook Object (JSON):

```json
{
    "id": 53,
    "created": "2015-08-01T16:56:29Z",
    "updated": "2015-08-01T16:56:29Z",
    "event": "payment.status.changed",
    "target": "https://my.callback.url/",
    "user": 42
}
```

Field | Type | Description
----- | -----| ----
id | long integer | Unique object identifier
user | long integer | The ID of the user who created the webhook
event | string | The webhook event. See the supported events section
created | string | The date that the webhook was created, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
updated | string | The date that the webhook was last modified, in the UTC timezone. Format: "YYYY-MM-DDTHH:MM:SSZ"
target | string | The URL that triggered events will be sent to

## Notification body format

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "hook": {
			    "id": 53,
			    "created": "2015-08-01T16:56:29Z",
			    "updated": "2015-08-01T16:56:29Z",
			    "event": "payment.status.changed",
			    "target": "https://my.callback.url/",
			    "user": 42
			},
	"data": {
				"id": 2314,
			    "organization": 1,
			    "amount": "30",
			    "currency": "BXC",
			    "payment_type": "money",
			    "metadata": {"id": 1234, "name": "Lucy"},
			    "description": "Per diem payment",
			    "phone_nos": ["+80000000001"],
			    "state": "completed",
			    "last_error": null,
			    "rejected_reason": null,
			    "rejected_by": null,
			    "rejected_time": null,
			    "cancelled_reason": null,
			    "cancelled_by": null,
			    "cancelled_time": null,
			    "created": "2014-11-22T20:57:04.017Z",
			    "author": 15,
			    "modified": "2014-11-22T20:57:04.018Z",
			    "updated_by": null,
			    "start_date": "2014-11-22T20:57:04.018Z"
			}
}
```

When an event is triggered, a JSON post will be sent to the URL that subscribed to the webhook. The JSON post body will include the following information:

Parameter | Description
--------- | --------
hook | A JSON representation of a webhook object. See the see webhook section below.
data | A JSON representation of the object that triggered the event. This is different for each event. See the 'Supported events' section above.

## Creating a new webhook

> Sample Request:

```shell
curl http://app.beyonic.com/api/webhooks -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d "payment.status.changed" \
-d target="https://my.callback.url/"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = Beyonic::Webhook.create(
    event: "payment.status.changed",
    target: "https://my.callback.url/"
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$hook = Beyonic_Webhook::create(array(
  "event" => "payment.status.changed",
  "target" => "https://my.callback.url/"
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = beyonic.Webhook.create(event='payment.status.changed',
							  target='https://my.callback.url/'
							  )

```

```java
package com.beyonic.examples.webhooks;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;

public class CreateWebhookExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";
    private static final String EVENT = "payment.status.changed";
    private static final String TARGET = "https://my.callback.url/";

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

            JSONObject contact = createWebhookObject();

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
                    System.out.println("ID of created Webhook: " + beyID);
                }
            } finally {
                conn.disconnect();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static JSONObject createWebhookObject() throws JSONException {
        JSONObject webhook = new JSONObject();
        webhook.put("event",EVENT);
        webhook.put("target",TARGET);
        return webhook;
    }
}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 53,
    "created": "2015-08-01T16:56:29Z",
    "updated": "2015-08-01T16:56:29Z",
    "event": "payment.status.changed",
    "target": "https://my.callback.url/",
    "user": 42
}
```

To create a new webhook, make a POST to the endpoint above, with the attributes below.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
event | Yes | String | 'payment.status.changed' | Must be a supported event
target | Yes | String | 'https://my.callback.url/"' | Must be an https URL


**Responses**

* If successful, a JSON representation of the new webhook object will be returned.

## Retrieving a single webhook

> Sample Request:

```shell
curl https://app.beyonic.com/api/webhooks/23 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = Beyonic::Webhook.get(23)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$hook = Beyonic_Webhook::get(23);
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = beyonic.Webhook.get(23)

```

```java
package com.beyonic.examples.webhooks;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;


public class SingleWebhookExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/1");
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
    "id": 23,
    "created": "2015-08-01T16:56:29Z",
    "updated": "2015-08-01T16:56:29Z",
    "event": "payment.status.changed",
    "target": "https://my.callback.url/",
    "user": 42
}
```

To retrieve a single webhook object, provide the webhook id and a webhook object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 23 | The id of the webhook you want to retrieve

## Listing all webhooks

> Sample Request:

```shell
curl https://app.beyonic.com/api/webhooks -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hooks = Beyonic::Webhook.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$hooks = Beyonic_Webhook::getAll();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hooks = beyonic.Webhook.list()

```

```java
package com.beyonic.examples.webhooks;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class ListAllWebhooksExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
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
    "count": 4,
    "next": "http://localhost:8000/api/webhooks?offset=10",
    "previous": null,
    "results": [
        {
            "id": 50,
            "created": "2015-08-01T16:43:43Z",
            "updated": "2015-08-01T17:05:38Z",
            "event": "payment.status.changed",
            "target": "https://mysite.com/callbacks/payment/saved/1",
            "user": 42
        },
        {
            "id": 52,
            "created": "2015-08-01T16:43:43Z",
            "updated": "2015-08-01T17:05:38Z",
            "event": "payment.status.changed",
            "target": "https://mysite.com/callbacks/payment/saved",
            "user": 42
        },
        {
            "id": 53,
            "created": "2015-08-01T16:56:29Z",
            "updated": "2015-08-01T16:56:29Z",
            "event": "payment.status.changed",
            "target": "https://my.callback.url/",
            "user": 42
        },
        {
            "id": 55,
            "created": "2015-08-01T16:56:29Z",
            "updated": "2015-08-01T16:56:29Z",
            "event": "payment.status.changed",
            "target": "https://google.com",
            "user": 42
        }
    ]
}
```

To retrieve a list of all webhooks, make a GET request to the webhooks endpoint. This will return a list of webhooks objects.

## Updating a webhook

> Sample Request:

```shell
ccurl http://app.beyonic.com/api/webhooks/1 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" \
-d event="payment.status.changed" \
-d target="https://my.callback.url/" \
-X PUT
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = Beyonic::Webhook.get(1)
hook.target = 'https://my.callback.url/'
hook.save()
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$hook = Beyonic_Webhook::get(1);
$hook->target = 'https://my.callback.url/';
$hook->save();
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hook = beyonic.Webhook.get(1)
hook.target = 'https://my.callback.url/'
hook.save()

```

```java
package com.beyonic.examples.webhooks;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;


public class UpdateWebhookExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";
    private static final String EVENT = "payment.status.changed";
    private static final String TARGET = "https://my.callback.url/";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/503");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);
            conn.setRequestMethod("PUT");
            conn.setDoOutput(true);
            conn.setDoInput(true);

            BufferedWriter out = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
            out.write(webhookObject().toString());
            out.close();

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

    private static JSONObject webhookObject() throws JSONException {
        JSONObject webhook = new JSONObject();
        webhook.put("event",EVENT);
        webhook.put("target",TARGET);
        return webhook;
    }

}
```

> Sample Response (JSON) - if you use one of the development libraries, this is automatically converted into a native object for you:

```json
{
    "id": 1,
    "created": "2015-08-01T16:56:29Z",
    "updated": "2015-08-01T16:56:29Z",
    "event": "payment.status.changed",
    "target": "https://my.callback.url/",
    "user": 42
}
```

To update a webhook, make a PUT request to the specific webhook’s endpoint, identified by its Id.

## Deleting a webhook

> Sample Request:

```shell
curl http://app.beyonic.com/api/webhooks/1 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900" -X DELETE
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

Beyonic::Webhook.delete(1)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

Beyonic_Webhook::delete( 11 );
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

beyonic.Webhook.delete(11)

```

```java
package com.beyonic.examples.webhooks;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;


public class DeleteWebhookExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "/502");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("Accept", "application/json");
            conn.setRequestProperty("charset", CHARSET);
            conn.setRequestProperty("Authorization", "Token " + API_KEY);
            conn.setRequestMethod("DELETE");

            System.out.println(conn.getResponseCode() + " // " + conn.getResponseMessage());

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

To delete a webhook, make a DELETE request to the specific webhook’s endpoint, identified by its Id.

## Filtering webhooks

> Sample Request:

```shell
curl https://app.beyonic.com/api/webhooks?user=1 -H "Authorization: Token ab594c14986612f6167a975e1c369e71edab6900"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hooks = Beyonic::Webhook.list(
  user: 1
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiKey("ab594c14986612f6167a975e1c369e71edab6900");

$hooks = Beyonic_Webhook::getAll(array(
  "user" => 1
));
?>
```

```python
import beyonic
beyonic.api_key = 'ab594c14986612f6167a975e1c369e71edab6900'

hooks = beyonic.Webhook.list(user=1)

```

```java
package com.beyonic.examples.webhooks;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class FilterWebhooksExample {

    private static final String API_ENDPOINT = "https://app.beyonic.com/api/webhooks";
    private static final String API_KEY = "ab594c14986612f6167a975e1c369e71edab6900";
    private static final String CHARSET = "UTF-8";

    public static void main(String[] args){
        URL url = null;
        try {
            url = new URL(API_ENDPOINT + "?user=12");
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

You can search or filter webhooks on the following fields. Simply add them to your request as shown in the examples. You can combine multiple filters. Note that filters return exact matches only.

* user - the ID of the user who created the webhook. Note that only users within your organization will be matched.
* event - the webhook event


## Authorization of Webhooks

When web hooks are sent, there’s an option to send verification details in the web hook for your server or application to verify that the web hook actually originates from Beyonic.
Currently we support the HTTPBasicAuth scheme. 
You provide us with the username and password for the web hook in the Beyonic Web when creating a new web hook. 
We include these in `Authorization` Header in the requests to your server which will contain data such as `Basic abcdefghsijklm`. 
You can use this to authenticate the request.

If no Authentication details were provided during creation then no Authorization Header will be sent.
