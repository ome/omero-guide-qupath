Analyze OMERO data using QuPath
===============================

Description
-----------

QuPath is a cross-platform software application designed for bioimage analysis - and specifically to meet the needs of whole slide image analysis and digital pathology.
See https://github.com/qupath/qupath/wiki/

We will show:

- How to connect QuPath to OMERO.server and browse the data.

- How to open an image including ROIs from OMERO.server in QuPath.

- How to draw an annotation in QuPath on an image from OMERO and save the annotation on that image in OMERO as OMERO ROI.

- How to perform simple Cell detection in QuPath.

- How to save the Cell detection ROIs directly to OMERO using a script in QuPath.

- How to export the Cell detection ROIs from QuPath as OME-XML.

- How to import the OME-XML to the OMERO.server and attach the QuPath ROIs to the original image in OMERO.

Resources
---------

- Data: example images from

  - IDR project referenced as `idr0018 <https://idr.openmicroscopy.org/search/?query=Name:idr0018>`_. Note that the data also have been imported into an OMERO.server where the possibility to write annotations exists (not the IDR server itself). See the ``Step-by-step`` section for further details.

- Video showing the QuPath v0.3 OMERO extension https://www.youtube.com/watch?v=IffQ18ZQ3mI
- QuPath documentation describing the v0.3 QuPath v0.3 OMERO extension https://qupath.readthedocs.io/en/latest/docs/advanced/omero.html 

-  Plugin ``ome-omero-roitool`` **v0.2.4** for import and export of ROIs to or from OMERO using OME-XML format. The ``ome-omero-roitool-xxx.zip`` under Releases also contains the scripts for export and import of ROIs from/to QuPath in OME-XML format. For precise installation steps, see below the ``Step-by-step`` section.

   - https://github.com/glencoesoftware/ome-omero-roitool


Setup
-----

Download QuPath v0.3.0 from https://qupath.github.io/.
Download the v0.3.0 OMERO extension for QuPath from https://github.com/qupath/qupath-extension-omero/releases. Install the OMERO extension as described in https://github.com/qupath/qupath-extension-omero#qupath-omero-extension.

Step-by-step
------------

.. _OpeninginQuPath:

Opening images with ROIs from OMERO in QuPath
---------------------------------------------

#. You can go through this workflow directly using the Images from the IDR. Nevertheless, as you cannot write any data directly into IDR during your analysis, you will not be able to successfully import the resulting ROIs back into the OMERO in IDR. Thus, you might consider using another OMERO.server which you can write data to and upload this or another RGB large image into it.

#. In OMERO.web, identify an image in the `idr0018 <https://idr.openmicroscopy.org/search/?query=Name:idr0018>`_ project and the dataset ``Baz1a-14-100-gastrointestinal`` contained in that project.

#. Select the first image and double-click on it. This will open the image in OMERO.iviewer, in a new tab of your browser.

#. If on a read-write OMERO server (i.e. not IDR), you can draw and save some ROIs on that image in OMERO.iviewer to be able to open them in QuPath later below, see `OMERO.iviewer guide <https://omero-guides.readthedocs.io/en/latest/iviewer/docs/iviewer_rois.html>`_ for how to do it.

#. Start your locally installed QuPath. Create a new QuPath Project by creating a new empty folder on your machine and dropping this new folder into the main QuPath window. Answer ``Yes`` when prompted.

#. Create a connection to your OMERO server by clicking ``Extensions > OMERO > Browse server > New server`` and paste into the dialog a valid server url including the ``http`` or ``https`` motives, for example ``https://<server>.com``. Details are described in https://qupath.readthedocs.io/en/latest/docs/advanced/omero.html.

#. Once connection to the server is established, QuPath will pop up a new dialog. In this dialog, select the correct group in OMERO in top left corner and the correct user. Expand Projects and Datasets as necessary, selecting the image with ROIs which you worked on in previous steps.

#. Double click on the image in the tree. In the new window, select the image again in the ``Image paths`` list, check the ``Import Objects`` checkbox and click Import.

#. Click on the imported image in your QuPath project to open it in QuPath. Inspect the ROIs imported from OMERO.

#. To draw new ROIs or annotations in QuPath, find a region with well-defined cells and nuclei in the image, zoom in.

#. Draw an ``ROI Annotation`` which denotes the region in which the cells will be detected using the ``Wand`` tool |image2|. 

#. Select the ``Annotations`` tab, select the class from the list to the right (e.g. ``Stroma``) and click ``Set class`` . Click ``Extensions > OMERO > Send annotations to OMERO``. A dialog will inform you how many ROIs are to be saved. Click ``OK``.

#. Go to OMERO.iviewer, refresh the image and verify that the annotation was saved as an OMERO ROI (polygon).

#. Note that there is some loss of metadata when going through the ``Extensions > OMERO > Send annotations to OMERO`` step 

   - The Class of the Annotation in QuPath will be indicated only by a fill color of the ROI in OMERO. If you reopen the image in QuPath again from OMERO, the ROI fetched by QuPath from OMERO will have the correct name of the Annotation if you gave it one in QuPath, but both the Class as well as the Annotation color will be lost by the round trip to OMERO and back. 
   
   - All the holes in your Annotation will be ignored (filled in), as the Annotation is translated into a polygon ROI in OMERO. The ROI in OMERO will appear as a filled-in object, as shown in the cartoon in https://qupath.readthedocs.io/en/latest/docs/advanced/omero.html. 
   
   - The "derived" ROIs which were created for example by Cell detection algorithm in QuPath will be ignored when saving Annotations to OMERO. You can save them using a short script in QuPath scripting interface or using a Command Line Interface workflow, both described below. 

Saving of derived ROIs from QuPath to OMERO
-------------------------------------------
The QuPath plugin for OMERO described above allows saving of the "annotations" drawn in QuPath to OMERO, but it does not enable the saving of "derived" annotations, such as Cell detection ROIs. To save the "derived" ROIs either :ref:`Save detection ROIs using QuPath script<Saveroiscript>` or :ref:`ome-omero-roitool<Roitool>` workflows can be used.


.. _Saveroiscript:

Save detection ROIs using QuPath script
---------------------------------------
.. warning::
    The feature described in :ref:`Save detection ROIs using QuPath script<Saveroiscript>` was not really designed for saving large amounts of ROIs (thousands) back to OMERO. An attempt to save large amounts of ROIs might result in slow performance or other problems.    

#. Connect QuPath to OMERO, open an image from OMERO in QuPath and draw an Annotation on it as described in :ref:`Opening images with ROIs from OMERO in QuPath<OpeninginQuPath>`.

#. Adjust your ROI Annotation using the ``Brush`` tool |image3|.

#. Select ``Analyze > Cell detection > Cell detection``.

#. You can adjust the parameters. Click ``Run``. This will draw red ROIs around cells and nuclei inside your ``ROI Annotation``.

   |image4|

#. Click on ``Hierarchy`` tab in the left-hand pane of QuPath. Expand the Annotation you have just run the ``Cell detection`` on.

#. Select several detection ROIs.

#. Open the scripting dialog in QuPath ``Automate > Show script editor`` and paste into it the following code::

      import qupath.lib.images.servers.omero.OmeroTools
      OmeroTools.writePathObjects(getSelectedObjects(), getCurrentServer())

#. From the top menu, select ``Run > Run``. This saves the detection ROIs you selected in the ``Hierarchy`` tab into OMERO.

#. Go to OMERO.iviewer and refresh the image. Inspect the saved detection ROIs.

.. _Roitool:

Save detection ROIs using ome-omero-roitool
-------------------------------------------
This workflow necessritates the usage of the Command Line Interface. The limitation here are the Annotation ROIs, whcih are transformed into masks in OMERO. Although this preserves the holes in the Annotations, if the Annotation ROIs are too large, it might result in performance problems or even running out of resources on the machine where the export of the mask from QuPath is attempted.

#. Connect QuPath to OMERO, open an image from OMERO in QuPath and draw an Annotation on it as described in :ref:`Opening images with ROIs from OMERO in QuPath<OpeninginQuPath>`.

#. Adjust your ROI Annotation using the ``Brush`` tool |image3|.

#. Select ``Analyze > Cell detection > Cell detection``.

#. You can adjust the parameters. Click ``Run``. This will draw red ROIs around cells and nuclei inside your ``ROI Annotation``.

   |image4|

#. Use the ROI OME-XML export script to export your ROIs from QuPath into OME-XML file. Find the version of ``ome-omero-roitool`` mentioned in Resources on `ome-omero-roitool releases <https://github.com/glencoesoftware/ome-omero-roitool/releases>`_ and from there download the ``ome-omero-roitool-xxx.zip``. The downloaded zip contains both the plugin and the QuPath scripts needed for this workflow.

#. Unzip the downloaded artifact and drag and drop the ``OME_XML_export.groovy`` into your QuPath.

#. To run the script, select ``Run > Run``.

#. Note: If you run a ``Cell detection`` in QuPath, the nuclei ROIs will be drawn as well as the ROIs around the cells. The ROI OME-XML export script will export both the ROIs around the cells as well as the nuclei ROIs.

#. Import the OME-XML with the ROIs from QuPath into OMERO. These steps must be run on a command line. If you did not do so already, find the version of the ``ome-omero-roitool`` mentioned in Resources on `ome-omero-roitool releases <https://github.com/glencoesoftware/ome-omero-roitool/releases>`_. From there, download the ``ome-omero-roitool-xxx.zip``. Open your terminal window.

#. Unzip the downloaded file and go into the resulting folder as follows::

      unzip ome-omero-roitool-xxx.zip
      cd ome-omero-roitool-xxx
      cd bin

#. On Mac or Linux, run::

      ./ome-omero-roitool import --help

#. On Windows, run::

      ome-omero-roitool.bat import --help

#. The ``--help`` option will give you a helpful output about how to construct the import command.

#. In the command below, replace the ``$IMAGE_ID`` parameter with the ID of the image in OMERO. You can obtain this ID for example from OMERO.iviewer (see beginning of this workflow).

#. To achieve the import of the ROIs to OMERO, you can run::

      ./ome-omero-roitool import --password $PASSWORD --port 4064 --server $SERVER --username $USERNAME $IMAGE_ID $PATH/TO/OME-XML/FILE
    
      
   Note: if you are using websockets, set the port to ``443`` and the server with the protocol e.g. ``wss://outreach.openmicrocopy.org/omero-ws.``

#. After you executed the ``import`` command above, go to OMERO.iviewer in your browser and view the ROIs on the image. The ``Annotation`` from QuPath is displayed as a mask ROI in OMERO.iviewer (the yellow ROI in the screenshot below). Masks cannot be edited in OMERO.iviewer at the moment, but they can be viewed. The mask, when selected displays a blue bounding box around the ``Annotation`` on the image.

   |image6|

.. |image0| image:: images/qupath1.png
   :width: 4in
   :height: 1in

.. |image1| image:: images/qupath2.png
   :width: 4in
   :height: 2in

.. |image2| image:: images/qupath3.png
   :width: 0.3in
   :height: 0.3in

.. |image3| image:: images/qupath4.png
   :width: 0.3in
   :height: 0.3in

.. |image4| image:: images/qupath5.png
   :width: 8in
   :height: 4.4in

.. |image5| image:: images/qupath6.png
   :width: 5in
   :height: 2.5in

.. |image6| image:: images/qupath7.png
   :width: 8in
   :height: 6.5in
