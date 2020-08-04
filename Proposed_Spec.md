# Proposed Specification

## Handling Unknown Data Structures

Thinking we could implement this like the same way that the Telegraf HTTP Input plugin does.

[Telegraf HTTP Input Plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/http)
[Telegraf JSON Parser](https://github.com/influxdata/telegraf/tree/master/plugins/parsers/json)
[GJSON Library used for parsing arbitrary JSON](https://github.com/tidwall/gjson)

### GJSON

[Path Syntax](https://github.com/tidwall/gjson/blob/master/SYNTAX.md)
[GJSON Path Creation Playground](https://gjson.dev/)

## Options

### Methods

Resources/Datasources will be created specifically for each supported method.

Example:

```hcl
data "http-get" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  ...
}
```

```hcl
resource "http-post" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  ...
}
```

HTTP Get requests will be made as data sources.

HTTP POST/PUT/DELETE requests will be resources.

### Headers

The requests headers should be specified as a `map[string]string` object.

Example:

```hcl
resource "http-post" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  request_headers = { Accept = "application/json" }
  ...
}
```

### Status Codes for Success

While status codes are fairly standardized, applications often do not fully follow spec or situations may arise where the desired status code is not one that would typically be seen as a success.

The user should be able to supply a list of status codes that should be taken as successful responses.

Example:

```hcl
resource "http-post" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  success_status_codes = [200, 201, 403]
  ...
}
```

### Return Data Keys

A list of GJSON Paths for keys that should be extracted from the response body.

Example:

```hcl
data "http-get" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  return_data_keys = ["data", "message"]
}
```

### Successful Response Values

Using a regular expression, test whether the value from the specified key matches. If a match is made, success, otherwise, failure.

Example:

```hcl
data "http-get" "example" {
  url    = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  success_response_key = "data"
  success_response_value = "Created thingy"
}
```

