# ODK Central Backend

![Platform](https://img.shields.io/badge/platform-Node.js-blue.svg)
[![License](https://img.shields.io/badge/license-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Build status](https://circleci.com/gh/opendatakit/central-backend.svg?style=shield)](https://circleci.com/gh/opendatakit/central-backend)

ODK Central Backend is a minimal [Open Data Kit](https://opendatakit.org/) server based on Node.js and Postgres. It is currently under development.

This repository contains only the code for the backend API server: [Central Frontend](https://github.com/opendatakit/central-frontend) holds frontend code, and [Central](https://github.com/opendatakit/central) contains both the Docker-based production deployment infrastructure for the combined frontend/backend, as well as project management and issue tickets.

## Contributing

We need your help to make this project as useful as possible! Please see the [Contribution Guide](https://github.com/opendatakit/central-backend/blob/master/CONTRIBUTING.md) for detailed information on discussion forums, project policies, code guidelines, and an overview of the software architecture.

## Using ODK Central Backend

For information on how to install and deploy ODK Central Backend for use as an ODK server, please see [these instructions](https://github.com/opendatakit/central) on the ODK Central repository. For information on how to set up a development environment for this server to help contribute to it, please skip to the next section.

### Command line scripts

A number of operational tasks (creating accounts, setting passwords, etc) may be accomplished directly via local command line. These may be accessed by running `node lib/bin/cli.js` from the project root. The full list of commands and arguments will be provided by the script.

### Accessing the API

ODK Central Backend is, first and foremost, a RESTful HTTP API server that manages Users, Forms, Submissions, and other objects necessary to run an ODK data collection campaign. This API is used by the bundled frontend web interface to form a complete user-installable server solution, but that API can also be used on its own with or without the frontend to programmatically manage a data collection project. We provide a full documentation of the API in the standard [API Blueprint](https://apiblueprint.org/) format: you can find a plain version of that documentation [here](https://github.com/opendatakit/central-backend/blob/master/docs/api.md) in the repository, or you can access the [Apiary version](https://odkcentral.docs.apiary.io/) for a friendlier version of the same material with neat features like an interactive query tool.

## Setting up a development environment

First, install Node.js 8 or higher.

Next, create a database and user in Postgres. Either use the same settings as the [default configuration file](config/default.json), or update your local configuration file to match the settings you choose. For example:

```sql
CREATE USER jubilant WITH PASSWORD 'jubilant';
CREATE DATABASE jubilant with owner=jubilant encoding=UTF8;
CREATE EXTENSION IF NOT EXISTS CITEXT;
```

Then, go to the repository root in a command line (where this README is) and run `make` with no arguments. This will install all npm dependencies and run all necessary migrations on the database; see the [makefile](Makefile) for details.

To run the server, run `make run` from the repository root. Once started, the server will be available on port `8383`. If you run into trouble with this step, the typical solution is to run `npm install` manually.

You can also run `make debug` to run the server with a standard node inspector port running (use your favorite tool, or visit [`about:inspect`](chrome://inspect) in Chrome to attach breakpoints and step through statements).

### Sending email

It isn't necessary to actually send email when working on this code. The default email configuration (see `config/default.json`) uses the `json` transport (as does the test configuration), in which case emails are printed to the local server log and delivery is not attempted.

If one wishes to send mail, the `sendmail` transport is relatively foolproof so long as `sendmail` is available locally. Postfix is the easiest way to ensure this. It may be necessary to configure Postfix to negotiate TLS with servers it connects to. If so, these commands ought to suffice:

```bash
sudo postfix tls enable-client
sudo postfix start
```

Even so, often mail messages will go at first to spam, so be sure to check that.

## Testing

Please see the [Contribution Guide](https://github.com/opendatakit/central-backend/blob/master/CONTRIBUTING.md) for complete information and guidelines on our tests.

This project is tested with both unit and integration tests. The unit tests (`/test/unit`) check, in isolation, the complicated parts of the core framework code. The integration tests (`/test/integration`) focus on verifying the correct behaviour of the API itself and the business logic that relies on the core framework code.

To run all tests (both unit and integration), run `make test` in the project root. [CircleCI](https://circleci.com/gh/opendatakit/central-backend) is configured to run all tests for verification.

Various other commands are available:

* To run only unit tests (which are much speedier than integration tests), run `make test-unit` in the project root.
* To run only API integration tests, run `make test-integration` instead.
* As provided by default by our testing framework Mocha, add `.only` after any `describe` or `it` call in the tests to run only the marked tests (eg: `it.only('should do something`,…`).
* To examine test coverage (runs both test suites), type `make test-coverage`.

## Style Guidelines

Please see the [Contribution Guide](https://github.com/opendatakit/central-backend/blob/master/CONTRIBUTING.md) for complete information on our coding style.

In general, follow the existing conventions in the project. We use linting as _a part of_ coding style verification. To run the linter, run `make lint` from the repository root. We use [rules](.eslintrc.json) based on the [Airbnb JavaScript style guide](https://github.com/airbnb/javascript). The linter is not perfect; we will make exceptions when we have a consensus to do so.

