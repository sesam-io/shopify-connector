# shopify-connector
Sesam Talk connector against Shopify's API

Shopify doesn't use any refresh tokens, so you have to use the new cli argument `--ignore-refresh-token` (available from 2.5.35) when running `sesam upload/authenticate` in this repository.

When authenticating on dev-nodes through sesam-py you need to add your store name to the place-keeper for site_id in the manifest file -> oauth2 section and add site_id in your test-env file
