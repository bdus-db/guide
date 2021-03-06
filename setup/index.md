---
title: Setting up the application
---

Up to now we have downloaded the main software and created the new application,
setting up the database connection, and BraDypUS took care of the rest.

We can now login in the new application, which is almost entirely disabled, because no data tables have been defined yet.

![screenshot](./../images/setup/empty_app.png "Login after a fresh installation") 
*Login after a fresh installation*

We need to enter in the **System configuration** module, where all the configuration
and table structure can be edited.

{: .callout-block .callout-block-warning }
System configuration is a highly dangerous functionality since data tables can be added or removed,
deleting, with no possibility for recover entire datasets.

This is why the super-admin password is required each time the module is opened.

![screenshot](./../images/setup/sys_config_pwd.png "System configuration requires super admin password confirmation") 
*System configuration requires super admin password confirmation*

On the left side are located the main functions, i.e:
- Validate application
- Main app configuration
- Single tables configuration

![screenshot](./../images/setup/sys_config.png "System configuration module") 
*System configuration module*

A special mention requires the **Validate application** utility that performs a very deep
analysis on the database structure and configuration files, and checks for perfect match.

In case of discrepancies, some suggestions will be provided.

![screenshot](./../images/setup/vaildate_app.png "System configuration validation") 
*System configuration validation*
