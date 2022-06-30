---
layout: docs
title: Upgrading
---

### COSMOS 5 Upgrades

COSMOS is released as a zip or tar file containing all the source code to build the COSMOS Docker containers and run them. Since we're using Docker containers and volumes we can simply stop the existing COSMOS application, then build and start the new release. Keep your existing COSMOS 5 installation and [install]({{site.baseurl}}/docs/v5/installation) COSMOS according to the installation instructions to a new directory.

This example assumes an existing COSMOS 5 installation at C:\COSMOS_5.0.0 and a new COSMOS 5 installation at C:\COSMOS_5.1.0. Linux users can adjust paths and change from .bat to .sh to follow along.

1. Stop the current COSMOS application

    ```batch
    C:\COSMOS_5.0.0> cosmos-control.bat stop
    ```

1. Start the new COSMOS application (note this will also build the new containers)

    ```batch
    C:\COSMOS_5.1.0> cosmos-control.bat start
    ```


### Migrating From COSMOS 4 to COSMOS 5

COSMOS 5 is a new architecture and treats targets as independent [plugins]({{site.baseurl}}/docs/v5/plugins). Thus the primary effort in porting from 4 to 5 is converting targets to plugins. We recommend creating plugins for each independent target (with its own interface) but targets which share an interface will need to be part of the same plugin. The reason for independent plugins is it allows the plugin to be versioned separately and more easily shared outside your specific project. If you have very project specific targets (e.g. custom hardware) those can potentially be combined for ease of deployment.

COSMOS 5 includes a migration tool for converting an existing COSMOS 4 configuration into a COSMOS 5 plugin. This example assumes an existing COSMOS 4 configuration at C:\COSMOS4_config and a new COSMOS 5 installation at C:\COSMOS5. Linux users can adjust paths and change from .bat to .sh to follow along.

1. Change to the existing COSMOS configuration directory. You should see the config, lib, procedures, outputs directory. You can then run the migration tool by specifying the absolute path to the COSMOS 5 installation.

    ```batch
    C:\COSMOS4_config> C:\COSMOS5\cosmos-control.bat cosmos migrate -a demo
    ```

    This creates a new COSMOS 5 plugin called cosmos-demo with a target named DEMO containing the existing lib and procedures files as well as all the existing targets.

    ```batch
    C:\COSMOS4_config> C:\COSMOS5\cosmos-control.bat cosmos migrate demo-part INST SYSTEM
    ```

    This would create a new COSMOS 5 plugin called cosmos-demo-part with a target named DEMO_PART containing the existing lib and procedures files as well as the INST and SYSTEM targets (but no others).

1. Open the new COSMOS 5 plugin and ensure the [plugin.txt]({{site.baseurl}}/docs/v5/plugins#plugintxt-configuration-file) file is correctly configured. The migration tool doesn't create VARIABLEs or MICROSERVICEs or handle target substitution so those features will have to added manually.

1. Follow the [building your plugin]({{site.baseurl}}/docs/v5/gettingstarted#building-your-plugin) part of the Getting Started tutorial to build your new plugin and upload it to COSMOS 5.
