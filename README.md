# shopify-connector
Sesam Talk connector against Shopify's API

Shopify doesn't use any refresh tokens, so you have to use the new cli argument `--ignore-refresh-token` (available from 2.5.35) when running `sesam upload/authenticate` in this repository.

When authenticating on dev-nodes through sesam-py you need to add your store name to the place-keeper for site_id in the manifest file -> oauth2 section and add site_id in your test-env file

## Setting up Webhooks
To enable webhooks you must ensure that the `service_url` environment variable is set in your Datahub Environment Variables of your subscription. The `service_url` should look like so:
```html
https://<your_datahub_id>.sesam.cloud/api
```

To register your webhooks make sure to run the webhook register pipes for each of your defined datatypes that support webhooks.

Verify in the execution log for each of your webhooks pipes that the pipe succeeded in registering the webhooks.

In order to consume the webhook events, verify that the manifest includes `"use_webhook_secrets": true`.
This will create webhook secret and set `group:Anonymous` and `write_data` permissions to all event pipes.
For Shopify we don't need the webhook secret but rather use `client_secret` to validate the webhook events (Shopify signs the webhook payload with `client_secret` instead of our custom webhook secret, like in Superoffice).
The event pipes must have the correct validation expression that uses `client_secret`.
Note that when testing the webhooks with `Sesampy` on dev nodes, this is not automatically handled and you may need to manually put the `client_secret` value into the validation expression.

In talk environments, the validation expression looks like:
```json
"validation_expression": "{% if request_headers['X-Shopify-Hmac-SHA256'] == b64encode(hmacsha256digest(secret('oauth_client_secret'), request_body)) %}{% else %}FAIL!{% endif %}"
```
and the `oauth_client_secret` is automatically set during onboarding.

In case you test on your dev node you have to use:
```json
"validation_expression": "{% if request_headers['X-Shopify-Hmac-SHA256'] == b64encode(hmacsha256digest('<oauth_client_secret_value>', request_body)) %}{% else %}FAIL!{% endif %}"
```
