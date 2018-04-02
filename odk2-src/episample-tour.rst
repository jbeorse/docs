EpiSample Guided Tour
========================

.. _episample-tour:

This document guides you through each of the modules of the :doc:`episample-intro`, named EpiSample. They are presented in a logical order, so this guide can be read from top to bottom to mimic the flow of the application's use in the field. You can also skip to the module that most interests you. Each section provides both a brief description of the purpose and function of the module, as well as an overview of how that functionality is implemented.

.. note::

  All file paths in this document are inside of the Application Designer directory. Additionally, all user defined files in a Data Management Application are inside the :file:`app/config/` directory. For convenience this document omits these portions of the file paths.

  For example, let us assume I have stored the Application Designer directory on my computer in :file:`/home/bobsmith/workspace/app-designer`. If this guide were to reference a file as :`assets/index.html` that indicates the file located on my computer at :file:`/home/bobsmith/workspace/app-designer/app/config/assets/index.html`.

.. contents:: :local:

.. _episample-tour-config:

Configuration
---------------------

.. image:: /img/episample-tour/episample-config-blank.*
  :alt: Config Startup
  :class: device-screen-vertical

.. _episample-tour-config-function:

Function
~~~~~~~~~~~~~~~~~

The Configuration module is the first screen the user sees after launching the application. It allows the user to customize the behavior of the application via the Settings Screen. It also requires the user to specify the location that the surveys will take place through a series of drop down menus. Below you can see that the :guilabel:`Select` button is not revealed until the full location is specified.

  .. image:: /img/episample-tour/episample-config-filled.*
    :alt: Config Ready
    :class: device-screen-vertical

The location chosen on this screen is saved and all future modules will make use of that information.

The Settings screen is a submodule of Configuration. It allows you to customize the behavior of each module. This includes the acceptable GPS accuracy range, the number of data points to collect, and the Survey form to use for the :ref:`episample-tour-household-survey`. These settings are shared across all devices that share :doc:`cloud-endpoints-intro`.

  .. image:: /img/episample-tour/episample-settings.*
    :alt: Config Startup
    :class: device-screen-vertical

The settings can be protected behind a user defined administrative password. If a password is set, the settings cannot be viewed or modified until it is entered, as shown below.

  .. image:: /img/episample-tour/episample-settings-locked.*
    :alt: Config Startup
    :class: device-screen-vertical

.. _episample-tour-config-implementation:

Implementation
~~~~~~~~~~~~~~~~~

This is the home screen first shown when the application is launched, so its HTML file must be: :file:`assets/index.html`.

In the :code:`<head>` of :file:`index.html` notice that, among the standard ODK 2 javascript imports there are also :file:`libs/sha256.js` and :file:`js/epsConfigLib.js`. The :file:`sha256.js` file is used for encrypting the admin password. The :file:`epsConfigLib.js` file provides an interface for reading and writing the configuration to the *Config* table of the database. Since the configuration is stored in the ODK database, any time the application is synchronized, these settings will be synchronized with the server. In this way an administrator can remotely control the settings on all the field workers phones. This custom library is included across all the files in this application.

The library :file:`libs/bootstrap-3.3.7...` and the file :file:`js/eps_select_place_name.js` are also linked at the bottom of the :code:`<body>`. :program:`Bootstrap` is a third party library used for formatting and look-and-feel. :file:`eps_select_place_name.js` implements the dynamic portions of the user interface of the home screen including: the series of drop downs that specify the place name, the :guilabel:`Settings` button, and the login scren for password protected settings.

The place names dropdowns are populated dynamically by reading from the *Place Names* table. These are read via :code:`odkData.query` and :code:`odkData.arbitraryQeury` calls. When the place name is selected, it is stored for later use by the rest of the modules with :code:`odkCommon.setSessionVariable`. The :guilabel:`Select` button launches :file:`eps_main_menu.html`, which is covered in the next module.

When the :guilabel:`Settings` or :guilabel:`Login` buttons are pressed, they will launch :file:`assets/eps_config.html`. This file implements the Settins screen and all its inputs. It links to :file:`assets/js/eps_config.js` to handle its logic. This file handles reading the stored configuration from the database (via :file:`epsConfigLib.js`), populating that into the form fields, and saving the new configuration back to the database after the :guilabel:`Save` button is pressed.

To populate the :guilabel:`Choose Form` dropdown, the :code:`populateChooseFormControl()` function in :file:`eps_config.js` reads the list of available Survey forms via the :code:`odkData.getAllTableIds` function.

.. _episample-tour-config-implementation-files:

Files
"""""""""""""""""""""

  - :file:`assets/index.html`
  - :file:`assets/js/epsConfigLib.js`
  - :file:`assets/js/eps_select_place_name.js`
  - :file:`assets/eps_config.html`
  - :file:`assets/js/eps_config.js`

.. _episample-tour-config-implementation-forms:

Forms
"""""""""""""""""""""

None

.. _episample-tour-config-implementation-tables:

Database Tables
""""""""""""""""""""""

  - *Config*
  - *Place name*

.. _episample-tour-main-menu:

Main Menu
---------------------

.. image:: /img/episample-tour/episample-main-menu.*
  :alt: Main Menu
  :class: device-screen-vertical

.. _episample-tour-main-menu-function:

Function
~~~~~~~~~~~~~~~~~

The Main Menu module is the hub to launch the other modules. The currently selected place name is displayed along the top for convenience.

The buttons that launch other modules can be dynamically added and removed via the Settings screen in the :ref:`episample-tour-config` module.

.. _episample-tour-main-menu-implementation:

Implementation
~~~~~~~~~~~~~~~~~

The file :file:`assets/eps_main_menu.html` is launched by the :guilabel:`Select` button in the :ref:`episample-tour-config` module. It provides a basic skeleton for this UI, but most of this screen's elements are dynamic. They are defined in :file:`assets/js/eps_main_menu.js`.

The file :file:`eps_main_menu.js` creates the buttons to launch the various modules of this application. It selectively hides these buttons if the configuration dictates this (see the :code:`init()` function). It also handles setting up the state for launching each of these modules.

To launch the :ref:`Geographic Survey module <episample-tour-geo-survey>` (with the :guilabel:`Collect` button), no setup is required. A direct call to :code:`odkTables.launchHTML(...)` will suffice. The same is true of the :ref:`Task Generation module <episample-tour-task-gen>` (with the :guilabel:`Select` button).

To launch the :ref:`Navigation module <episample-tour-nav>`, a query must be passed that selects the points to which this particular user should navigate. The call happens with this function: :code:`odkTables.openTableToNavigateView(...)` which queries the *Census* table for these points (see the :ref:`Geographic Survey module <episample-tour-geo-survey>` for how this table is populated). The preceding code dynamically generates the SQL :code:`WHERE` clause and :code:`SELECT` arguments. This view is also opened with a :code:`dispatchStruct`, which triggers the Action-Callback workflow.

The launch of the :ref:`Household Survey module <episample-tour-nav>` is automatic, unlike the manual button pressing of the other modules. This occurs via the Action-Callback workflow triggered when the Navigation module is launched. See the :code:`actionCBFn()` function and the :code:`odkCommon.registerListener(...)`, :code:`odkCommon.viewFirstQueuedAction(...)`, and :code:`odkCommon.removFirstQueuedAction(...)` functions. When the Navigation module completes its action, the result is handled by this function. If the result indicates to do so, the Household Survey module will be launched via :code:`odkTables.editRowWithSurvey(...)` (using the configured Form ID).

The Househould Survey is also launched with the Action-Callback workflow. When it returns, the results are used to update the *Census* table to match its corresponding form's completion status.

The currently selected location is displayed at the top by reading the value from :code:`localStorage`.


.. _episample-tour-main-menu-implementation-files:

Files
"""""""""""""""""""""

  - :file:`assets/eps_main_menu.html`
  - :file:`assets/js/eps_main_menu.js`

.. _episample-tour-main-menu-implementation-forms:

Forms
"""""""""""""""""""""

None

.. _episample-tour-main-menu-implementation-tables:

Database Tables
""""""""""""""""""""""

  - *Config*
  - *Census*

.. _episample-tour-geo-survey:

Village Geographic Survey
--------------------------------

.. image:: /img/episample-tour/episample-collect-blank.*
  :alt: Collection Screen
  :class: device-screen-vertical

.. _episample-tour-geo-survey-function:

Function
~~~~~~~~~~~~~~~~~

The Village Geographic Survey module is used to gather census data about each household. It records very basic information: a house number, a head-of-household name, and the GPS coordinates of that household. The house number field is automatically increased with each saved record.

Recorded households are listed in the bottom portion of the screen. This list includes the name and house number, an :guilabel:`Edit` button that allows you to update the record, and nan icon indicating whether the record is :guilabel:`Valid` or not. The validity of a record is determined by the accuracy of its GPS coordinates. The thresholds are set in the :ref:`episample-tour-config` module.

The quality of the GPS signal is depicted by the color of the spinner and the specific rating listed.

  .. image:: /img/episample-tour/episample-collect-poor-gps.*
    :alt: Collection Screen with Poor GPS signal
    :class: device-screen-vertical side-by-side

  .. image:: /img/episample-tour/episample-collect-invalid.*
    :alt: Collection Screen with Invalid Record
    :class: device-screen-vertical side-by-side

The above screen on the left depicts a GPS signal that is not accurate enough, and is displayed in yellow. The screen on the right shows that the icon next to :guilabel:`Beth` is an :guilabel:`I` for *Invalid*, rather than the :guilabel:`V` for *Valid* next to Alex.

A running total of records is indicated in between the collection portion of the screen and the record list. It is separated into the *Valid*, *Invalid*, and *Excluded* categories. The difference between *Invalid* and *Excluded* is that an *Excluded* record is manually excluded via the :guilabel:`Exclude` checkbox.

In *Invalid* record can only be made valid by recapturing the GPS coordinates when the accuracy is sufficinent. The image below shows the record editing screen.

  .. image:: /img/episample-tour/episample-collect-update.*
    :alt: Collection Update Screen
    :class: device-screen-vertical

To replace the GPS, the :guilabel:`Replace GPS` checkmark should be checked, and the :guilabel:`Update` button should be pressed.

After all of the household data has been recorded, the user should synchronize their results to the server (:ref:`Syncing instructions <services-using-sync>`).

.. _episample-tour-geo-survey-implementation:

Implementation
~~~~~~~~~~~~~~~~~

The basic structure of the user interface is defined in :file:`assets/eps_collect.html`, including all the input fields and the container for the list. Dynamic adjustments to this user interface, as well as calls to the database and device hardware, are made in :file:`assets/js/eps_collect.js`. This file's makes heavy use of the third party :program:`Backbone` javascript library. Also notice that the third party library :program:`Underscore` is included, along with template HTML, for dynamically adding list items.

The file :file:`eps_collect.js` handles:

  1. Keeping track of the GPS coordinates and accuracy in real time. It also updates the user interface as necessary when these change. The thresholds for GPS accuracy are read from the settings with the :file:`epsConfigLib.js` file.
  2. Reading, Creating, and Updating records in the *Census* table. This data is also validated before being recorded. The records are read through a number of calls to :code:`odkData.query(...)` and :code:`odkData.ArbitraryQuery(...)`. They are recorded with calls to :code:`odkData.addRow(...)` and the are updated with calls to :code:`odkData.updateRow(...)`.
  3. Dynamically creating the visualization of the list of records from the *Census* and updating it as that list changes. This list is also paginated. The running totals of *Valid*, *Invalid*, and *Excluded* records are populated with :code:`odkData.arbitraryQuery` calls.

The file :file:`assets/js/util.js` is included to generate UUIDs (unique ids and primary keys in the database) for each new record as it is created.


.. _episample-tour-geo-survey-implementation-files:

Files
"""""""""""""""""""""

  - :file:`assets/eps_collect.html`
  - :file:`assets/js/eps_collect.js`
  - :file:`assets/js/util.js`

.. _episample-tour-geo-survey-implementation-forms:

Forms
"""""""""""""""""""""

None

.. _episample-tour-geo-survey-implementation-tables:

Database Tables
""""""""""""""""""""""

  - *Config*
  - *Census*


.. _episample-tour-task-gen:

Task List Generation
---------------------------------

.. image:: /img/episample-tour/episample-task-gen.*
  :alt: Task List Generation
  :class: device-screen-vertical

.. _episample-tour-task-gen-function:

Function
~~~~~~~~~~~~~~~~~

The Task List Generation module is used to create a randomized task list for each data collector to perform.

.. note::

  It is important that the Task List Generation module has access to all of the census data. Every data collector should synchronize their device to the server (:ref:`Syncing instructions <services-using-sync>`) so that all the records are available. After that, a synchronization can be performed to receive the full record set.

The :guilabel:`Main`, :guilabel:`Additional`, and :guilabel:`Alternate` points parameters are set in the :ref:`episample-tour-config` module.

.. tip::

  To allow these fields to be set in this module, set the parameters to zeros in the Config module.

If there are sufficient records available to meet the parameters, the user can press the :guilabel:`Select` button to generate the task list. A progress bar will appear while this process takes place, followed by a completion notification. This process can take some time if the data set is large. After the task list is generated it can be synchronized to the server, followed by each data collector synchronizing to receive the list.

This list of tasks is used to determine where to perform follow up surveys.


.. _episample-tour-task-gen-implementation:

Implementation
~~~~~~~~~~~~~~~~~

The file :file:`assets/eps_select.html` implements the look of this screen. This screen is not nearly as dynamic as the others, and as such most of the user interface is hard coded here. This includes the loading screen that appears while the list is being generated.

The file :file:`assets/js/eps_select.js` reads the *Main*, *Alternate*, and *Additional* point configuration and populates the user interface with it. If these are configured to zeros, it will read in the user defined values for these fields.

When the :guilabel:`Select` button is pressed, the configuration is used to determine the points to select for the task list. The third party :program:`Async` library is used to handle these long running calculations in the background without locking up the user interface. In the meantime, the :guilabel:`Please Wait` loading screen is shown. The records are read from the *Census* table with an :code:`odkData.arbitraryQuery` call, and then randomized. When this process is complete, the affected records in the *Census* table are updated with :code:`odkData.updateRow` calls.


.. _episample-tour-task-gen-implementation-files:

Files
"""""""""""""""""""""

  - :file:`assets/eps_select.html`
  - :file:`assets/js/eps_select.js`

.. _episample-tour-task-gen-implementation-forms:

Forms
"""""""""""""""""""""

None

.. _episample-tour-task-gen-implementation-tables:

Database Tables
""""""""""""""""""""""

  - *Config*
  - *Census*


.. _episample-tour-nav:

Geographic Navigation
---------------------------------

.. image:: /img/episample-tour/episample-navigation.*
  :alt: Main Menu
  :class: device-screen-vertical

.. _episample-tour-nav-function:

Function
~~~~~~~~~~~~~~~~~

The Navigation Module helps guide a data collector to the households specified in the task list. The compass, distance, and other navigational indicators will update in real time.

The map will show the points on the task list. The househoulds displayed can be configured on the :ref:`episample-tour-main-menu` module to show only *Main* points, or show *Main* and *Additional* and so on.

When the data collector arrives at the household, they can tap the :guilabel:`Arrive` button to launch the :ref:`episample-tour-household-survey` module. Or they can press :guilabel:`Cancel` at any time to return to the Main Menu.


.. _episample-tour-nav-implementation:

Implementation
~~~~~~~~~~~~~~~~~

This view is provided by the ODK 2 platform and is not customizable. The view is launched by a call to :code:`odkTables.openTableToNavigateView(...)` with query parameters to select the map markers. The query that selects the map markers is discussed in the :ref:`Main Menu section <episample-tour-main-menu-implementation>`. The handling of the results of the :guilabel:`Arrive` and :guilabel:`Cancel` button presses are also discussed in that section.

.. _episample-tour-nav-implementation-files:

Files
"""""""""""""""""""""

None

.. _episample-tour-nav-implementation-forms:

Forms
"""""""""""""""""""""

None

.. _episample-tour-nav-implementation-tables:

Database Tables
""""""""""""""""""""""

  - *Census*


.. _episample-tour-household-survey:

Household Data Collection
---------------------------

.. image:: /img/episample-tour/episample-household-survey.*
  :alt: Main Menu
  :class: device-screen-vertical

.. _episample-tour-househould-survey-function:

Function
~~~~~~~~~~~~~~~~~

The Household Data Collection module is an ODK Survey form that is configured in the :ref:`episample-tour-config` module. This is intended to be provided by the Deployment Architect for their particular use case, which makes this application flexible to different scenarios. For example, this could be used for follow up after a Malaria outbreak or it could be adapted to handle another disease by swapping this form.

The data collected in this form is available in the same database as the rest of the application and can be used by it.

.. _episample-tour-household-survey-implementation:

Implementation
~~~~~~~~~~~~~~~~~

The Household Data Collection form is user configured and not provided in this reference application. The provided form could be a simple survey or could include complex skip logic, data quality checks, and customizations to the look-and-feel. Instructions for creating these forms are available in Application Designer's :ref:`app-designer-common-tasks-designing-a-form` guide as well as the :doc:`xlsx-converter-intro` guide.

Prepopulated forms could also be included with CSV files. See the files in the :file:`assets/csv` directory  and the :file:`assets/tables.init` file for examples.

.. _episample-tour-household-survey-implementation-files:

Files
"""""""""""""""""""""

  - The files associated with the selected form.

.. _episample-tour-household-survey-implementation-forms:

Forms
"""""""""""""""""""""

  - The form configed in the :ref:`episample-tour-config` module.

.. _episample-tour-household-survey-implementation-tables:

Database Tables
""""""""""""""""""""""

  - The table corresponding to the selected form.


