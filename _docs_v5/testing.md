---
layout: docs
title: Test Procedure
toc: true
---

## Cypress

###  Prerequesits

- Be sure `NODE_EXTRA_CA_CERTS=/path/to/Globalsign.pem` is included in environment variables.

- Rename (or delete) Cypress cache if it exists. 
(Usually located at `AppData\Local\Cypress\Cache`.)

- Install Yarn 

	`npm install --global yarn`

- Install Cypress

	`COSMOS> npm install cypress`

### Cypress Testing

- Start COSMOS

	`COSMOS> cosmos-control.bat start`

- Open COSMOS in your browser. At the login screen, set the password to "password".

- Navigate to **cypress** folder, install testing dependencies with yarn.

	`cypress> yarn`

- _[Optional]_ Fix istanbul/nyc coverage source lookups (use `fixlinux` if not on Windows).

    Tests will run successfully without this step and you will get coverage statistics, but line-by-line coverage won't work.

	`cypress> yarn run fixwindows`

- Open Cypress and run tests

	`cypress> yarn run cypress open`

Code coverage reports can be viewed at [cypress/coverage/lcov-report/index.html](./coverage/lcov-report/index.html)


## Unit Tests
- Navigate to **cosmos** folder. Run the command:
 
    `cosmos> bundle exec rspec`

Code coverage reports can be found at [cosmos/coverage/index.html](./cosmos/coverage/index.html)