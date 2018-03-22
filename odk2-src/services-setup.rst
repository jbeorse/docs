Setting Up ODK Services
==============================


.. _services-setup-servers:

Compatible Servers
-------------------------

The latest version of ODK Services is Version 2.0.2 (December 2017).

To synchronize data with the latest version of ODK Services (rev218) requires a rev 218, 216 or 214 :doc:`cloud-endpoints-intro`. ODK Aggregate v1.4.15 or Sync Endpoint 2.0.2 rev 218 will work. It will not work with earlier versions of ODK Aggregate. See :doc:`sync-endpoint` or :doc:`aggregate-tables-extension` for further information on how to configure the server to accept ODK 2.0 synchronization requests.

.. _services-setup-server-config:

Server Configuration
--------------------------------

Before you are able to synchronize data or application files to a device, you will need to configure your server settings within Services. This tells the device which server to contact and what user to authenticate. After these settings are configured, you can optionally lock them with an administrator password. See :ref:`services-setup-admin-settings`.

  1. Open Services. Press the Action Button (:guilabel:`⋮`)

    .. image:: /img/services-setup/services-options-settings.*
      :alt: Services Menu Options
      :class: device-screen-vertical

  2. Select :menuselection:`Settings --> Server Settings`

    .. image:: /img/services-setup/services-settings.*
      :alt: Services Settings Menu
      :class: device-screen-vertical

    .. image:: /img/services-setup/services-server-settings.*
      :alt: Services Server Settings Menu
      :class: device-screen-vertical

  3. Choose :guilabel:`Server URL` and specify your server URL

    .. note::

      If you are using SSL, be sure to specify :code:`https://...`

  4. Authenticate user credentials

    .. note::

      If your server is configured to allow anonymous access this step is optional.

    a. Change the :guilabel:`Server Sign-on Credential` to :menuselection:`Username` and enter the appropriate credentials in the :guilabel:`Username` and :guilabel:`Server Password` fields.
    b. Exit out of the :menuselection:`Server Settings` page, and then the :menuselection:`Settings` page, by using the back button.
    c. You will then be asked to :guilabel:`Authenticate Credentials`. Select the :guilabel:`Authenticate New User` option.

      .. image:: /img/services-setup/services-prompt-credentials.*
        :alt: Services Authenticate Credentials Prompt
        :class: device-screen-vertical

    d. On the next screen select :guilabel:`Verify User Permissions`.

      .. image:: /img/services-setup/services-verify-credentials.*
        :alt: Services Authenticate Credentials Verification
        :class: device-screen-vertical

    e. After the verification succeeds, you will see a :guilabel:`Verification Successful` popup, select :guilabel:`OK`.

.. _services-using-sync-detail:

Sync Details
~~~~~~~~~~~~~

Syncing has two phases. In the first phase, data tables are created on the device that correspond to the data tables on the server, and the form definitions and other files on your device are made to exactly match those available on the server (updating them as needed).

.. warning::

  If a data table on the device does not exist on the server, the configuration files and all associated forms for that table will be removed from the device. To prevent data loss, the table itself will not be deleted, but, by removing all of the configuration files for that table, the data will generally be unusable.

In the second phase, it synchronizes the contents of the local data tables with the contents on the server, including any row-level file attachments associated with individual records in the data table. Row-level file attachments are bundled and synced one row at a time.

Unlike ODK Collect, where individual forms can be added and removed at will, ODK Services and the ODK 2.0 tools are organized around coherent, complete, *data management applications* consisting of a set of interrelated data tables and forms. All the forms and tables on the server collectively define the *data management application* and ODK Services ensures that the device conforms to that *data management application* definition. You can operate multiple independent *data management applications* on a single device by placing their files and forms under different application folders within the :file:`/sdcard/opendatakit/` folder. Each such application will publish to a different ODK Cloud Endpoint. This is a significant and powerful change from the ODK 1.0 mindset.

.. _services-using-reset-app-server:

Reset App Server
-------------------------

Resetting your app server pushes the configuration and data on your tablet up to the server.

.. note::

  This option should only be used to initialize or update your Cloud Endpoint.

.. warning::

  If a data table on the server does not exist on the device, that table, all of its data, and all associated files (such as forms) will be deleted from the server.

If a data table on the server is identical to one on the device, the data in that table will be synced and the files on the server will be updated to be exactly those present on the device (deleting any files associated with this table that existed only on the server).

Before resetting, it is critical that you first ensure that your device contains all the tables, files, and data you want to preserve in your application (by :ref:`Syncing <services-using-sync>`).

Opening up the Sync application will take you to the Sync screen. You can also open up this application by pressing on the sync icon (two curved arrows) in the action bar of the Tables and Survey applications. On this screen:

  1. If you have not configured the application, from the menu, choose the :menuselection:`Settings --> Choose Server Settings`
  2. Choose :guilabel:`Server URL` and specify your server URL (if using :program:`Google App Engine`, be sure to specify :code:`https://...`).
  3. If your server is not configured to allow anonymous access:

    a. Change the :guilabel:`Server Sign-on Credential` to :menuselection:`Username` and enter the appropriate credentials in the other settings fields.
    b. Exit out of the :menuselection:`Server Settings` page, and then the :menuselection:`Settings` page, by using the back button.
    c. You will then be asked to :guilabel:`Authenticate Credentials`. Select the :guilabel:`Authenticate New User` option.
    d. On the next screen :guilabel:`Select Verify User Permissions`.
    e. After the verification succeeds, you will see a :guilabel:`Verification Successful` popup, select :guilabel:`OK`.
    f. Hit the back button until you see the sync screen with the options to :guilabel:`Change User` and :guilabel:`Sync Now`.

  4. The sync interaction has four options:

    - :menuselection:`Fully Sync Attachments` - *Default* - Synchronize all row-level data and file attachments with the server.
    - :menuselection:`Upload Attachments Only` - Only upload attachments from the device to the server
    - :menuselection:`Download Attachments Only` - Only download attachments from the server to the device
    - :menuselection:`Do Not Sync Attachments` -  Do not sync any attachments

    .. tip::
      When resetting the server, you typically want Fully Sync Attachments to be selected.

  6. Click on :guilabel:`Reset App Server`.
  7. A confirmation dialog will popup asking you to confirm resetting the App Server. Again, this can delete all data on this Cloud Endpoint! If you are sure you want to continue, click :guilabel:`Reset`.

Sync will contact the ODK Cloud Endpoint and attempt to push all configuration and data currently on the tablet up to the specified Cloud Endpoint. A progress dialog will be displayed and, alternatively, the status of resetting the app server can be obtained by looking at the notifications generated by Services in the notification area.

.. note::

  The sync will proceed whether or not you remain on this page and you can use the back button to back out of it and return to your work.

.. warning::

  Should you begin modifying data rows while syncing, the changes to those rows will not be synced until you save them as incomplete or finalize the row, and the act of editing will generally mark the sync as having ended with conflicts. This means that you must complete your edits and re-issue the sync to ensure that your changes are propagated up to the server.


.. _services-setup-admin-settings:

Administrator Settings
------------------------
