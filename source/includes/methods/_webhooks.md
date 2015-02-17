## Wehbooks

Webhooks or callback URLs allow you to define URLs on your server that notifications should be sent to. Notifications are sent for specific events, as defined in the “Supported Event” section below.

You can be managed in two ways:

* By logging into your account via the web browser
* By using the Webhooks API

*Required permisions*

To manage webhooks, either with the API or the web browser, you must have “Manage Users and Organization Settings” permissions for your organization

*Supported events*

The following events are supported:

* payment.status.changed – Receive notification whenever the status of any payments in your organization changes. See the Payments API for more information.
* collection.received - Receive notifications whenever you receive a collection (an incoming payment) from a customer)

*Managing Webhooks from the web browser*

To manage webhooks using the web browser:
1. Log into your account.
2. Go to your organizations profile page. You can access this either from the organizations menu, or by clicking your organization name at the top left of the page.
3. Click the Settings button (Cog wheel with an arrow pointing down)
4. Click “Manage Webhooks” from the drop down menu that appears

*Managing Webhooks from the API*

You can use the Webhooks API described below to update or delete your callback URLs programatically.

The webhooks api endpoint is https://app.beyonic.com/api/webhooks

### Creating a new webhook

> Example Request:

```shell
curl http://app.beyonic.com/api/webhooks -H "Authorization: Token my-api-key" \
-d "payment.status.changed" \
-d target="https://my.callback.url/"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'my-api-key'

hook = Beyonic::Webhook.create(
    event: "payment.status.changed",
    target: "https://my.callback.url/"
)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiVersion("v1");

Beyonic_Webhook::create(array(
  "event" => "payment.status.changed",
  "target" => "https://my.callback.url/"
));
?>
```

```python
import beyonic
beyonic.api_version = 'v1'

...
# Please check back soon for python examples.
```

To create a new webhook, make a POST to the endpoint above, with the attributes below.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
event | Yes | String | 'payment.status.changed' | Must be a supported event
target | Yes | String | 'https://my.callback.url/"' | Must be an https URL


**Responses**

* If successful, a JSON representation of the new webhook object will be returned.

### Retrieving a single webhook

> Retrieve Individual Webhooks:

```shell
curl https://app.beyonic.com/api/webhooks/23 -H "Authorization: Token my-authorization-token"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'my-api-key'

hook = Beyonic::Webhook.get(23)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiVersion("v1");

$hook = Beyonic_Webhook::get(23);
?>
```

```python
import beyonic
beyonic.api_version = 'v1'

...
# Please check back soon for python examples.
```

To retrieve a single webhook object, provide the webhook id and a webhook object will be returned.

Parameter | Required | Type | Example | Notes
--------- | -------- | ---- | ------- | -----
id | Yes | Integer | 23 | The id of the webhook you want to retrieve

### Listing all webhooks

> List Webhooks:

```shell
curl https://app.beyonic.com/api/webhooks -H "Authorization: Token my-authorization-token"
```

```ruby
require 'beyonic'
Beyonic.api_key = 'my-api-key'

hooks = Beyonic::Webhook.list
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiVersion("v1");

$hooks = Beyonic_Webhook::getAll();
?>
```

```python
import beyonic
beyonic.api_version = 'v1'

...
# Please check back soon for python examples.
```

To retrieve a list of all webhooks, make a GET request to the webhooks endpoint. This will return a list of webhooks objects.

### Updating a webhook

> Updating a Webhook:

```shell
ccurl http://app.beyonic.com/api/webhooks/1 -H "Authorization: Token my-api-key" \
-d event="payment.status.changed" \
-d target="https://my.callback.url/" \
-X PUT
```

```ruby
require 'beyonic'
Beyonic.api_key = 'my-api-key'

hook = Beyonic::Webhook.get(1)
hook.target = 'https://my.callback.url/'
hook.save()
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiVersion("v1");

$hook = Beyonic_Webhook::get(1);
$hook->target = 'https://my.callback.url/';
$hook->save();
?>
```

```python
import beyonic
beyonic.api_version = 'v1'

...
# Please check back soon for python examples.
```

To update a webhook, make a PUT request to the specific webhook’s endpoint, identified by its Id.

### Deleting a webhook

> Deleting a Webhook:

```shell
curl http://app.beyonic.com/api/webhooks/1 -H "Authorization: Token my-api-key" -X DELETE
```

```ruby
require 'beyonic'
Beyonic.api_key = 'my-api-key'

Beyonic::Webhook.delete(1)
```

```php
<?php
require_once('./lib/Beyonic.php');
Beyonic::setApiVersion("v1");

Beyonic_Webhook::delete( 11 );
?>
```

```python
import beyonic
beyonic.api_version = 'v1'

...
# Please check back soon for python examples.
```

To delete a webhook, make a DELETE request to the specific webhook’s endpoint, identified by its Id.