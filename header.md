# Open Booking API Test Interface
This is the documentation and JSON-LD namespace for the test interface of the [Open Booking API](https://www.openactive.io/open-booking-api/EditorsDraft).

This interface must be implemented by the Booking System to make use of the "Controlled" test mode within the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/). For the "Random" test mode, opportunities that match the criteria specified in the TesttestOpportunityCriteria enumeration must exist in the Booking System.

This interface and associated namespace is defined as a convenience to aid testing of the Open Booking API. Booking Systems MUST NOT expose this interface in production environments.

The namespace MUST be referenced using the URL `"https://openactive.io/test-interface"` (which will return the [JSON-LD definition](https://openactive.io/test-interface/test-interface.jsonld) if the `Accept` header contains `application/ld+json`), and is designed to be used in conjunction with the `"https://openactive.io/"` namespace.

## Test Interface Endpoints

### Test Dataset Endpoints

To use the "Controlled" test mode within the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/), the `/test-dataset` endpoints must be implemented. They are not required for "Random" test mode.

A `testDatasetIdentifier` is set in the configuration of the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) instance, and is reused across multiple test runs of the same instance. This allows any existing test data to be cleaned up, while still allowing multiple test suite instances to execute against a shared booking system environment simultaneously.

#### DELETE /test-dataset/:testDatasetIdentifier

The endpoint is called just once before each test run, when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in "Controlled" test mode.

It must clean up test data from previous test runs for the given `testDatasetIdentifier`.

##### Example Request

This request would delete all opportunities within the test dataset "`uat-ci`".

```javascript
DELETE /test-dataset/uat-ci HTTP/1.1
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

#### POST /test-dataset/:testDatasetIdentifier/opportunities

The endpoint is called (potentially multiple times) before each individual test starts executing, when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in "Controlled" test mode.

The Booking System must create an opportunity of the type specified in `@type` matching the criteria specified by `test:testOpportunityCriteria`, within the specified notional "Test Dataset" defined by the `testDatasetIdentifier` within the path.

##### Example Request

This request would create a new `SessionSeries`, within the test dataset "`uat-ci`", that meets the criteria specified in `https://openactive.io/test-interface#TestOpportunityBookable`.

```javascript
POST /test-dataset/uat-ci/opportunities HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "SessionSeries",
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
  "@type": "SessionSeries",
  "@id": "https://id.booking-system.example.com/session-series/42"
}
```

### Test Simulation Endpoint

#### POST /test-simulations

The `/test-simulation` endpoint is called to simulate a Booking System instigated action for the specified opportunity. 

The endpoint is called when the [OpenActive Test Suite](https://github.com/openactive/openactive-test-suite/) is run in both "Controlled" and "Random" test modes, only for those tests that depend on test simulation functionality being available in the Booking System.

If this endpoint is not implemented, the features whose tests depend on this endpoint must be configured to "disabled-tests" mode, to allow the test suite to run successfully.

The Booking System must respond with a `204` status code and an empty body to indicate success.

##### Example Request

This request would execute the simulation specified by `https://openactive.io/test-interface#SimulateProviderCancellation` on the `SessionSeries` with `@id` of `https://id.booking-system.example.com/session-series/42`.

```javascript
POST /test-simulations HTTP/1.1
Host: example.com
Date: Mon, 8 Oct 2018 20:52:35 GMT
Accept: application/vnd.openactive.booking+json; version=1

{
  "@context": [
    "https://openactive.io/",
    "https://openactive.io/test-interface"
  ],
  "@type": "Action",
  "test:testOpportunitySimulation": "https://openactive.io/test-interface#SimulateProviderCancellation",
  "object": {
    "@type": "SessionSeries",
    "@id": "https://id.booking-system.example.com/session-series/42"
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
