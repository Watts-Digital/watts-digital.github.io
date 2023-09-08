
# Watts Digital REST API Style Guide <!-- omit in toc -->

## Purpose <!-- omit in toc -->

Great RESTful APIs look like they were designed by a single team. This promotes API adoption, reduces friction, increases velocity, and enables clients to use them properly. To build APIs that meet this standard, and to answer many common questions encountered along the way of RESTful API development. For these reasons we have created a RESTful style guide. We have shared it with you to inspire additional discussion and refinement within and among your teams, and contribute our learnings and suggestions to the tech community at large.

## How to read the Style Guide <!-- omit in toc -->

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

The words "REST" and "RESTful" MUST be written as presented here, representing the acronym as all upper-case letters. This is also true of "JSON," "XML," and other acronyms.

## Usage <!-- omit in toc -->

The style guide is work in progress. We’d love your feedback – whether you agree, disagree, or have some additional practices and tips to add.

We encourage you to improve the style guide with modifications and extensions via Pull Requests.

## Table of Contents <!-- omit in toc -->

- [Naming](#naming)
  - [URI Component Names](#uri-component-names)
  - [URI Naming conventions](#uri-naming-conventions)
    - [Examples](#examples)
    - [Formal Definition](#formal-definition)
  - [Resource names](#resource-names)
  - [Query Parameter Names](#query-parameter-names)
  - [Resource Path](#resource-path)
  - [Sub-Resources](#sub-resources)
  - [Resource Identifiers](#resource-identifiers)
  - [Field names](#field-names)
  - [Enum Names](#enum-names)
- [Versioning](#versioning)
  - [Principles](#principles)
  - [Practices](#practices)
- [HTTP Methods, Headers and Statuses](#http-methods-headers-and-statuses)
  - [HTTP Methods](#http-methods)
    - [Data Model](#data-model)
    - [Serialization](#serialization)

## Naming

The naming conventions as described are to provide consistency for Watts Digital APIs. We know that naming is where art meets science and there MAY be deviations but any deviations MUST be kept to a minimum. An evolving linter will be published which conforms to these standards. Teams developing new APIs MUST conform to that linter in build pipelines. The linter will be written to also apply and help older APIs find differences that need to conform in future versions. This section focuses on URIs, resources, field names, and enums.

### URI Component Names

Please reference the [RFC 3986](https://www.ietf.org/rfc/rfc3986.txt) specification for REST API development and consumption.

### URI Naming conventions

Following is a brief description of the URI specific naming convention guidelines for APIs. This specification uses parentheses "( )" to group, an asterisk " * " to specify zero or more occurrences, and brackets "[ ]" for optional fields.

```markdown
[scheme"://"][host[':'port]]"[/api/v" major-version '][/'namespace ']/'resource ('/'resource)* '?' query
```

- URIs MUST start with a letter and use only lower-case letters.
- Literals/expressions in URI paths SHOULD be separated using a hyphen ( - ).
- Literals/expressions in query strings SHOULD use camel case.
- URI paths and query strings MUST percent encode data into [UTF-8 octets](https://en.wikipedia.org/wiki/Percent-encoding).
- Plural nouns SHOULD be used in the URI where appropriate to identify collections of data resources.
  - `/locations`
  - `/devices`
  - `/device-pins`  
- An individual resource in a collection of resources MAY exist directly beneath the collection URI.
  - `/locations/{locationId}`
  - `/devices/{id}`
  - `/device-pins/{id}`
- Sub-resource collections MAY exist directly beneath an individual resource. This should convey a relationship to another collection of resources.
  - `/devices/{id}/device-pins`
- Sub-resource individual resources MAY exist, but should be avoided in favor of top-level resources.
  - `/devices/{id}/device-pins/{devicepin-id}`
  - Better: `/device-pins/{devicepin-id}`
- Resource identifiers SHOULD follow [recommendations](#resource-identifiers) described in subsequent section.

#### Examples

- `https://apis-mtls.wattswater.com/admin-service/locations`
- `https://apis-mtls.wattswater.com/admin-service/locations/123456789`
- `https://marlin.wattswater.com/device-pins/987654321`

#### Formal Definition

  |Term|Defiition|
  |---|---|
  |URI|[end-point] '**/**' resource-path ['**?**'query]|
  |end-point|[scheme "**://**"][ host ['**:**' port]]|
  |scheme|"**http**" or "**https**"|
  |resource-path|"**/api/v**" version  '**/**' namespace-name '**/**' resource ('**/**' resource)|
  |resource|resource-name ['**/**' resource-id]|
  |resource-name|Alpha (Alpha \| Digit \| '-')* |
  |resource-id|value|
  |query|name '**=**' value ('**&**' name = value)* |
  |name|Alpha (Alpha \| Digit \| '_')* |
  |value|URI Percent encoded value|

### Resource names

When modeling a service as a set of resources, developers MUST follow these principles:

- Nouns MUST be used, not verbs.
- Resource names MUST be singular for singletons; collections' names MUST be plural.
  - A description of the devices saved pins
    - `GET /device-pins` returns the full representation
  - A collection of hypothetical device-pins:
    - `GET /device-pins` returns a list of charges device pins
    - `POST /device-pins` creates a new charge resource, `/device-pins/1234`
    - `GET /device-pins/1234` returns a full representation of a single device-pin

- Resource names MUST be lower-case and use only alphanumeric characters and hyphens.
  - The hyphen character, ( - ), MUST be used as a word separator in URI path literals. **Note** that this is the only place where hyphens are used as a word separator.

### Query Parameter Names

- Literals/expressions in query strings MUST use camel case where there are multiple words.
- Query parameters values MUST be percent-encoded.
- Query parameters MUST start with a letter and only alpha characters and digits SHALL be used.
- Query parameters SHOULD be optional.

### Resource Path

An API's [resource path](#uri-component-names) consists of URI's path, query and fragment components. It would include API's major version followed by namespace, resource name and optionally one or more sub-resources. For example, consider the following URI.

Following table lists various pieces of the above URI's resource path.

- `https://marlin.wattswater.com/v1/admin-service/users/1e0d5d33-3b83-4353-bd2b-5a8b9bdf38bc`

|Path Piece|Description|Definition|
|---|---|---|
|`v1`|Specifies major version 1 of the API|The API major version is used to distinguish between two backward-incompatible versions of the same API. The API major version is an integer value which MAY be included as part of the URI. API versions MUST apply to all resources after its in the URI Path and cannot be versioned separately in a single API specification. |
|`admin-service`|The namespace|Namespace identifiers are used to provide a context and scope for resources. They are determined by logical boundaries in the business capability model implemented by the API platform.|
|`users`|The resource name|If the resource name represents a collection of resources, then the `GET` method on the resource should retrieve the list of resources. Query parameters should be used to specify the search criteria.|
|`1e0d5d33-3b83-4353-bd2b-5a8b9bdf38bc`|The resource ID|To retrieve a particular resource out of the collection, a resource ID MUST be specified as part of the URI. Sub-resources are discussed below.|

### Sub-Resources

Sub-resources represent a relationship from one resource to another. The sub-resource name provides a meaning for the relationship. If cardinality is 1:1, then no additional information is required. Otherwise, the sub-resource SHOULD provide a sub-resource ID for unique identification. If cardinality is 1:many, then all the sub-resources will be returned. No more than two levels of sub-resources SHOULD be supported.

|Example|Description|
|---|---|
|`GET /v1/admin-service/users/123456789/accounts`|This call should return all the accounts associated with an original payment.|
|`GET /v1/admin-service/users/123456789/accounts/102030`|This call should return only the details for a particular account associated with this user. Keep in mind that this is only an illustrative example  to show how to use sub-resource IDs. In practice, two step invocations SHOULD be avoided. If second identifier is unique, top-level resource (e.g. `/v1/admin-service/users/123456789`) is preferred.|

### Resource Identifiers

Resource identifiers identify a resource or a sub-resource. These **MUST** conform to the following guidelines.

- The lifecycle of a resource identifier MUST be owned by the resource's domain model, where they can be guaranteed to uniquely identify a single resource.
- APIs MUST NOT use the database sequence number as the resource identifier.
- A [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier), Hashed Id ([HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) based) is preferred as a resource identifier.
- For security and data integrity reasons all sub-resource IDs MUST be scoped within the parent resource only.
  - *Example:* `/devices/1234/device-pins/ABCD` Even if device pin "ABCD" exists, it MUST NOT be returned unless it is linked to device: 1234.
- Enumeration values can be used as sub-resource IDs. String representation of the enumeration value SHOULD be used.
- There MUST NOT be two resource identifiers one after the other.
  - *Example:* `/v1/devices/device-pins/12345/102030`
- Resource IDs SHOULD try to use either Resource Identifier Characters or ASCII characters. There **SHOULD NOT** be any ID using UTF-8 characters.
- Resource IDs and query parameter values MUST perform URI percent-encoding for any character other than URI unreserved characters. Query parameter values using UTF-8 characters MUST be encoded.

### Field names

The data model for the representation MUST conform to JSON. The values may themselves be objects, strings, numbers, booleans, or arrays of objects.

- Key names MUST be lower-case words or camel case.
  - `foo`
  - `barFoo`
- Prefix such as `is` or `has` SHOULD NOT be used for keys of type boolean.
- Fields that represent arrays SHOULD be named using plural nouns (e.g. refunds-contains one or more refunds, co-buyers-contains one or more co-buyers).  

### Enum Names

Entries (values) of an enum SHOULD be composed of only upper-case alphanumeric characters and the underscore character, ( _ ).

- `FIELD_10`
- `NOT_EQUAL`

If there is an industry standard that requires us to do otherwise, enums MAY contain other characters.

Enum names should be as expressive as possible, avoiding opqaque codes (e.g., "AMERICAN_EXPRESS" vs. "AX").

## Versioning

### Principles

- Evolve your APIs in a non-breaking way if at all possible instead of versioning
  - Adding a field may not cause a versioning event. Most languages will handle this without breaking but not all client languages
- Versioning SHOULD be explicit and obvious to the consumer. The consumer should know which version of a resource returns
- Versioning SHOULD strive for simplicity
- Any changes to an API MAY cause versioning event and SHOULD be discussed:
  - Adding or removing fields or sub-resources
  - Changing field types MAY impact versioning
  - Adding a value in an enumeration MAY impact versioning
  - Discuss business logic and validation changes with a view of how they impact versioning
- Consider existing resources that may exist in a database and test returning in all versions of the API
- Reach out to clients in advance to work through a versioning event and timeline to move
- New resources or routes in an API SHOULD NOT be created to avoid versioning
- If you need to break your API contract, use the versioning practices below

### Practices

- The versioning format MUST be v{major-version-identifier}, where the {major-version-identifier} follows semver
  - Use one of these strategies MUST be followed for versioning:
    - Preferred: Include the major version as the first routing token after /api
      - Example: service.wattswater.com/api/v1/customers ➡️ service.wattswater.com/api/v2/customers
    - Alternative: If you want to avoid mingling versions of resources in your codebase and aren't using a reverse proxy in front of your resources, you can version at the subdomain level:
      - Example: service-v1.wattswater.com ➡️ service-v2.wattswater.com
      - This increases your options for deciding how you manage underlying versions (e.g., separate codebases behind a traffic manager), but requires more up-front investment and infrastructure costs in the transition
    - For API proxies or API management (e.g., Apigee, APIM), put the major version in the proxy name itself
      - Example: apis.wattswater.com/customer-service-v1/ ➡️ apis.wattswater.com/customer-service-v2/
      - API proxy changes cannot version with DNS as they share a common DNS
      - It is not recommended to utilize URL segments as this is complex and requires deep dives and maintenance for proxy and target URL flows which might limit changes in the future
      - API proxies might need to version due to the following
        - Security changes for client
        - Security changes for target
        - Format changes. Ex: SOAP to json
        - Javascript callout logic or URL changes
        - Any other proxy code breaking change
  - versioning MUST NOT utilize HTTP headers and query string parameters
  - Versioning MUST be done at the API level, and not at the individual resource level    
  - Only maintain one major version at a time. If you introduce a new major version of your API, communicate an end-of-life date for the oldest version at least six months out for all of your clients, and then retire it after that data

## HTTP Methods, Headers and Statuses

### HTTP Methods

Using HTTP Methods will help an API easily communicate what actions can be performed on a domain resource. Most domain resources follow a basic CRUD (Create, Read, Update, Delete) model which map perfectly to HTTP verbs.

|HTTP Method|Description|
|---|---|
| `GET`| To *retrieve* a resource or collection of resources. |
| `POST`| To *create* a resource, or to *execute* a complex operation on a resource. |
| `PUT`| To *completely replace* a resource. |
| `DELETE`| To *delete* a resource. |
| `PATCH`| To perform a *partial update* to a resource. |

The actual operation invoked MUST match the HTTP method semantics as defined in the table above.

- The **`GET`** method MUST NOT have side effects. It MUST NOT change the state of an underlying resource.
- **`POST`**: method SHOULD be used to create a new resource in a collection.
  - **Example:** To add a credit card on file to a wallet, `POST https://api.foo.com/api/v1/wallet/credit-cards`
  - Idempotency semantics: If this is a subsequent execution of the same invocation and the resource was already created, then the request SHOULD be idempotent.
- The **`POST`** method SHOULD be used to create a new sub-resource and establish its relationship with the main resource.
  - **Example:** To activate a device with ID 12345: `POST https://api.foo.com/api/v1/admin-service/devices/12345/activate`
- A **`POST`** method which does not create a resource MAY only sparingly exist as an exception to the RESTful model.
- The **`PUT`** method MUST update the entire resource.  For partial updates PATCH MUST be used
- The **`PUT`** method SHOULD be used to update resource attributes or to establish a relationship from a resource to an existing sub-resource; it updates the main resource with a reference to the sub-resource.
- The **`PATCH`** method MUST contain only the fields that need to be updated within a resource.
 
The data model for representation MUST conform to the JSON Data Interchange Format as described in [RFC 7159](https://tools.ietf.org/html/rfc7159).

#### Serialization

- Resource endpoints MUST support `application/json` as content type.
- If an `Accept` header is sent and `application/json` is not an acceptable response, a `406 Not Acceptable` error MUST be returned.
