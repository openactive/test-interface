# Open Booking API Test Interface
This is the repository of the specification and JSON-LD namespace for the test interface of the [Open Booking API](https://www.openactive.io/open-booking-api/EditorsDraft).

Please find documentation relating to this specification and namespace at https://openactive.io/test-interface.

## Contribution

Please create a pull request amending the file `test-interface.jsonld` with the relevant terms, which includes the rationale for such proposed amendments. Documentation is automatically generated from this file when the PR is merged into the `master` branch.

## Tooling dependencies

The OpenActive validator (https://validator.openactive.io) will pick up changes to the namespace immediately.

The OpenActive libraries ([.NET](https://github.com/openactive/OpenActive.NET), [Ruby](https://github.com/openactive/models-ruby) and [PHP](https://github.com/openactive/models-php)) require manual model regeneration and republishing in order to include any namespace updates.