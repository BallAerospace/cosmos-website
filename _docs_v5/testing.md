---
layout: docs
title: Test Procedure
toc: true
---

## Cypress

###  Prerequesits

1. Be sure `NODE_EXTRA_CA_CERTS=/path/to/Globalsign.pem` is included in environment variables.

1. Rename (or delete) Cypress cache if it exists. (Usually located at `AppData\Local\Cypress\Cache`)

1. Install Yarn

    ```bash
    npm install --global yarn
    ```

1. Install Cypress

    ```bash
    COSMOS> npm install cypress
    ```

### Cypress Testing

1. Start COSMOS

    ```bash
    COSMOS> cosmos-control.bat start
    ```

1. Open COSMOS in your browser. At the login screen, set the password to "password".

1. Navigate to **cypress** folder, install testing dependencies with yarn.

    ```bash
    cypress> yarn
    ```

1. _[Optional]_ Fix istanbul/nyc coverage source lookups (use `fixlinux` if not on Windows).

    Tests will run successfully without this step and you will get coverage statistics, but line-by-line coverage won't work.
    ```bash
    cypress> yarn run fixwindows
    ```  

1. Open Cypress and run tests

    ```bash
    cypress> yarn run cypress open
    ```  	

Code coverage reports can be viewed at [cypress/coverage/lcov-report/index.html](./coverage/lcov-report/index.html)


## Unit Tests
1. Navigate to **cosmos** folder. Run the command:

    ```bash
    cosmos> bundle exec rspec
    ```

Code coverage reports can be found at [cosmos/coverage/index.html](./cosmos/coverage/index.html)
