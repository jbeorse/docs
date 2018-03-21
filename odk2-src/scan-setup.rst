ODK Scan Setup
==================

.. _scan-setup:

.. contents:: :local:

.. _scan-architect-prereqs:

Prerequisites
---------------------

Before setting up ODK Scan, you will need:

  - :doc:`services-intro`
  - :doc:`survey-intro`
  - :doc:`tables-intro`
  - :doc:`app-designer-intro`

If you have not installed Scan already, follow our guide for :ref:`scan-installing`

.. _scan-setup-transferring-template:

Transferring a Form Template to the App
------------------------------------------

ODK Scan works with machine readable forms created using the :doc:`scan-form-designer-intro`. Refer to the :doc:`scan-form-designer-using` for instructions on how to create these forms.

After creating a form with Form Designer, you'll have generated the machine readable files. To push them to your device, you will use the same mechanism that is used to push Survey and Tables files to the device.

  #. Create a form using the ODK Scan Form Designer. Save that form with the :guilabel:`Save to File System` option.
  #. Follow the instructions in the :ref:`Application Designer Guide <app-designer-common-tasks-move-to-device>` to push updates to the device. These describe pushing Survey files, but they will push Scan files to the device too with the same procedure.
  #. To confirm that the *[your_form]* template has been successfully been transferred, open the ODK Scan app on your device and go to :guilabel:`Settings` (the wheel icon) and select :menuselection:`Templates to Use`. The folder name should appear in the list of templates.

.. image:: /img/scan-setup/scan-template-list.*
  :alt: Example list of Scan templates
  :class: device-screen-vertical

