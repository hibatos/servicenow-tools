# Simple Table Navigation

This project aims to solve the problem of the one table name you need but do not remember completely.
It makes them searchable within the filter navigator.

## How does it work?

To understand how this app works, you first need to understand how the menu structore works that the filter navigator uses.
First there are the so called application **menus** (sys_app_application) that act as categories.
These menus contain **modules** (sys_app_module), that are basically links to all sorts of conent, such as lists with certain filters, URLs and many more.

We also need an application menu the modules are assigned to. This per default is the application menu with the sys_id 7b7a185e2f751110dc91c886f699b6cc.
It is recommended not to change this default behaviour but possible nontheless by modifying the system property _simple_table_nav.target_menu_.

Then we need to know which tables to include in the search index since indexing all is certainly possible,
but will slow down your filter navigator a bit and make the search results overcrowded.
This list of tables is stored in the system property _simple_table_nav.tables_ as a comma separated string.
I selected three tables per default (core_company,sys_user,sys_user_group) for demo purposes.

As soon as this list is updated the contents of the list are checked against the system table definitions or **Tables** (sys_db_object).
This check is implemented in the before business rule _Check Simple Table Navigation Table List_ on the **system properties** (sys_properties) table.
The update is aborted if invalid table names are detected and an error is echoed to the user.

Then for each list entry a new module is created if there is no existing one in the modules already.
Before that all modules that exist, but are not included in the list of tables specified in the system property are then deleted.
This update step is implemented in the after business rule _Reload Simple Table Navigation_.

### Sidenote
It would have been easier to delete all and create all from scratch every tome the list is updated.
But this would impact performance considerably so I decided against it.

## Why is this application in the global scope?

This application needs to be able to insert and delete into the sys_app_application and sys_app_module table, which both are protected from writes within scoped applications.
Also since we do not need new tables, roles or anything that would make a scope recommended I spared some time and made it global.

## Limitations

### Only on Dev!

This application modifies the menu structure, so the use is only recommended in development

### Only admin 

Only admin users (users having the **admin** role) are allowed to use this application

### Custom and scoped tables

Custom and scoped tables might prevent the application to reference them because of cross scope privileges.

## Uninstall

Uninstalling the application is as easy as removing it from the installed applications with one exception.
If the system property _simple_table_nav.target_menu_ is modified and set to a menu outside the application then the modules are not removed.
they can also be removed by first clearing the table list, waiting for the reload business rule to run and the removing the application.


## Bugs
### Only V1
#### update of module list failing
If the table list property is updated it might not reload the modules, it might require updating it again.
You can also run the following code as a background script
```
new SimpleTableNavigationUtils().reloadTables()
```
### Long runtime on configuration
If too many tables are added to the table list, the user session will freeze for up to 5 minutes for 4000 tables, caused by the reload business rule.
