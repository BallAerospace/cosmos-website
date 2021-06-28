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
    {% highlight bash %}
    npm install --global yarn
    {% endhighlight %}

- Install Cypress
    {% highlight bash %}
    COSMOS> npm install cypress
    {% endhighlight %}

### Cypress Testing

- Start COSMOS
    {% highlight bash %}
    COSMOS> cosmos-control.bat start
    {% endhighlight %}

- Open COSMOS in your browser. At the login screen, set the password to "password".

- Navigate to **cypress** folder, install testing dependencies with yarn.
    {% highlight bash %}
    cypress> yarn
    {% endhighlight %}

- _[Optional]_ Fix istanbul/nyc coverage source lookups (use `fixlinux` if not on Windows).

    Tests will run successfully without this step and you will get coverage statistics, but line-by-line coverage won't work.
    {% highlight bash %}
    cypress> yarn run fixwindows
    {% endhighlight %}  

- Open Cypress and run tests
    {% highlight bash %}
    cypress> yarn run cypress open
    {% endhighlight %}  	

Code coverage reports can be viewed at [cypress/coverage/lcov-report/index.html](./coverage/lcov-report/index.html)


## Unit Tests
- Navigate to **cosmos** folder. Run the command:
    {% highlight bash %}
    cosmos> bundle exec rspec
    {% endhighlight %}
Code coverage reports can be found at [cosmos/coverage/index.html](./cosmos/coverage/index.html)