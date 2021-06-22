# Open Booking API Test Interface

This is the specification and JSON-LD namespace for the test interface of the [Open Booking API](https://www.openactive.io/open-booking-api/EditorsDraft).

> Status: Draft | [Please Provide Feedback via GitHub](https://github.com/openactive/test-interface/issues)

## Status

This specification and namespace is in a draft state, however every effort will be made in subsequent revisions to maintain the IDs associated with the enumerations defined here.

## Overview

This interface must be implemented by the Booking System to make use of the "Controlled" test mode within the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/). For the "Random" test mode, opportunities that match the criteria specified in the `TestOpportunityCriteriaEnumeration` enumeration must exist in the Booking System.

This interface and associated namespace is defined as a convenience to aid testing of the Open Booking API. Booking Systems MUST NOT expose this interface in production environments.

## Namespace

The namespace MUST be referenced using the URL `"https://openactive.io/test-interface"` (which will return the [JSON-LD definition](https://openactive.io/test-interface/test-interface.jsonld) if the `Accept` header contains `application/ld+json`), and is designed to be used in conjunction with the `"https://openactive.io/"` namespace.

## Test Interface Endpoints

The Test Interface Endpoints are specified relative to the same [Base URI](https://openactive.io/open-booking-api/EditorsDraft/#dfn-base-uri) that is defined in the Open Booking API.

### Datasets Endpoints

To use the "Controlled" test mode within the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/), the `/test-interface/datasets` endpoints must be implemented. They are not required for "Random" test mode.

A `testDatasetIdentifier` is set in the configuration of the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) instance, and is reused across multiple test runs of the same instance. This allows any existing test data to be cleaned up, while still allowing multiple test suite instances to execute against a shared booking system environment simultaneously.

#### `DELETE /test-interface/datasets/:testDatasetIdentifier`

This endpoint deletes all opportunities within a given `testDatasetIdentifier`, and also deletes any `Order`s and `OrderItem`s associated with them.

The endpoint is called just once before each test run, when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in "Controlled" test mode.

It must clean up test data from previous test runs for the given `testDatasetIdentifier`.

##### Example Request

This request would delete all opportunities within the test dataset "`uat-ci`".

```javascript
DELETE /test-interface/datasets/uat-ci HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1
```

##### Example Response

```javascript
HTTP/1.1 204 No Content
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1
```

#### `POST /test-interface/datasets/:testDatasetIdentifier/opportunities`

This endpoint creates an opportunity in the Booking System, that matches the specified criteria.

The endpoint is called (potentially multiple times) before each individual test starts executing, when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in "Controlled" test mode.

The endpoint must accept a [bookable opportunity type](https://www.openactive.io/open-booking-api/EditorsDraft/#definition-of-a-bookable-opportunity-and-offer-pair), which includes a specific `test:TestOpportunityCriteriaEnumeration` to which the newly created opportunity just conform, and the appropriate `@type` of `superEvent` or `facilityUse` to disambiguate the type of opportunity to be created. It must also include an `organizer` or `provider` `@id` to specify the Seller within which the opportunity should be created.

The Booking System must create an opportunity of the type specified in `@type` (taking into account `@type` of `superEvent` or `facilityUse`) matching the criteria specified by `test:TestOpportunityCriteriaEnumeration`, within the specified notional "Test Dataset" defined by the `testDatasetIdentifier` within the path.

##### Example Request

This request would create a new `ScheduledSession`, within the test dataset "`uat-ci`", that meets the criteria specified in `https://openactive.io/test-interface#TestOpportunityBookable`, for the `organizer` with `@id` `https://id.booking-system.example.com/organizer/3`.

```javascript
POST /test-interface/datasets/uat-ci/opportunities HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "ScheduledSession",
  "superEvent": {
    "@type": "SessionSeries",
    "organizer": {
      "@type": "Organization",
      "@id": "https://id.booking-system.example.com/organizer/3"
    }
  },
  "test:testOpportunityCriteria": "https://openactive.io/test-interface#TestOpportunityBookable"
}
```


##### Example Response

```javascript
HTTP/1.1 201 Created
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1

{
  "@context": "https://openactive.io/",
  "@type": "ScheduledSession",
  "@id": "https://id.booking-system.example.com/scheduled-sessions/42"
}
```

### Actions Endpoint

#### `POST /test-interface/actions`

The `/test-interface/actions` endpoint is called to simulate a Booking System instigated action for the specified opportunity. 

The endpoint is called when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in both "Controlled" and "Random" test modes, only for those tests that depend on test action functionality being available in the Booking System.

If this endpoint is not implemented, the features whose tests depend on this endpoint must be configured to "disabled-tests" mode, to allow the test suite to run successfully.

The Booking System must respond with a `204` status code and an empty body to indicate success.

##### Example Request

This request would execute the simulation specified by `test:SellerRequestedCancellationSimulateAction` on the `ScheduledSession` with `@id` of `https://id.booking-system.example.com/session-series/42`.

```javascript
POST /test-interface/actions HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "test:SellerRequestedCancellationSimulateAction",
  "object": {
    "@type": "Order",
    "@id": "https://id.booking-system.example.com/orders/92e55c9f-ba86-471c-9cb4-5030188423b1"
  }
}
```

##### Example Response

```javascript
HTTP/1.1 204 No Content
Date: Mon, 8 Oct 2018 20:52:36 GMT
Content-Type: application/vnd.openactive.booking+json; version=1
```


# Namespace
