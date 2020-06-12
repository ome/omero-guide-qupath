Analyze OMERO data using QuPath
===============================

Description
-----------

QuPath is a cross-platform software application designed for bioimage analysis - and specifically to meet the needs of whole slide image analysis and digital pathology.
See \ https://github.com/qupath/qupath/wiki/

We will show:

- How to open an image from OMERO server in QuPath

- How to draw annotations and perform simple Cell detection in QuPath

- How to export the ROIs from QuPath as OME-XML

- How to import the OME-XML to the OMERO.server and attach the QuPath ROIs to the original image in OMERO

Resources
---------

- Data: example images from

  - IDR project referenced as idr0018 http://idr.openmicroscopy.org/webclient/?show=project-101. Note that the data also have been imported into an OMERO.server where the possibility to write annotations exists (not the IDR server itself). See the Step by Step section for further details.

-  Plugin ``ome-omero-roitool`` for import and export of ROIs to or from OMERO using OME-XML format

   - https://github.com/glencoesoftware/ome-omero-roitool

-  Scripts for export and import of ROIs from/to QuPath in OME-XML format

   - https://github.com/glencoesoftware/ome-omero-roitool/tree/master/src/dist/QuPath.scripts


Setup
-----

Download QuPath v0.2.0 or later from https://qupath.github.io/.


Step-by-step
------------

#. You can go through this workflow directly with the image in the IDR, which is http://idr.openmicroscopy.org/webclient/?show=image-1920105. The QuPath will open that image without problems and not credentials are needed. Nevertheless, as you cannot write any data directly into IDR during your analysis, you will not be able to successfully import the resulting ROIs back into the OMERO in IDR. Thus, you might consider using another OMERO.server which you can write data to and upload this or another RGB large image into it.

#. In OMERO.web, identify an image in the idr0018 project and the dataset Baz1a-14-100-gastrointestinal comtained in that project.

#. Select the first image and double-click on it. This will open the image in OMERO.iviewer.

#. In the OMERO.iviewer tab, select the whole URL in the address bar of your browser and copy it, for example using right-click and Copy.

#. In QuPath, select *File > Open URL...* and paste the link into the dialog.

   |image0|

#. Click *OK*. In the following dialog, enter your credentials.

   |image1|

#. Set image type to *Brightfield H&E* in the following dialog.

#. Find a region with well-defined cells and nuclei in the image, zoom in.

#. Draw an ROI Annotation which denotes the region in which the cells will be detected using the *Wand* tool |image2|.

#. Adjust your ROI Annotation using the *Brush* tool |image3|.

#. Select *Analyze > Cell analysis > Cell* detection.

#. You can adjust the parameters. Click *Run*. This will draw red ROIs around cells and nuclei inside your ROI Annotation.

   |image4|

#. Select *Measure > Show detection measurements*.

   |image5|

#. Note: You can save the results locally by clicking *Save* in the bottom right of the *Detection results table*.

#. In the following steps, we will show how to convert the ROIs your just created in QuPath into OMERO ROIs and attach them to the image in OMERO.

#. First, use the ROI OME-XML export script to export your ROIs from QuPath into OME-XML file. Find the export script in https://github.com/glencoesoftware/ome-omero-roitool/tree/master/src/dist/QuPath.scripts

#. Copy the script text.

#. In QuPath, open *Automate > Show script editor*

#. Follow the instructions from step 3 onwards in https://github.com/glencoesoftware/ome-omero-roitool#export-ome-xml-rois to execute the export script in QuPath. This will produce a local OME-XML file.

#. Note: If you run a *Cell detection* in QuPath, the nuclei ROIs will be drawn as well as the ROIs around the cells. The ROI OME-XML export script will export both the ROIs around the cells as well as the nuclei ROIs. In OMERO, they will appear as two Shapes inside the ROI belonging to the particular cell.

#. Import the OME-XML with the ROIs from QuPath into OMERO. These steps must be run on a command line. Find the latest release of the ome-omero-roitool on https://github.com/glencoesoftware/ome-omero-roitool/releases. From there, download the ``ome-omero-roitool-xxx.zip``. Open your terminal window.

#. Unzip the downloaded file and go into the resulting folder as follows::

      unzip ome-omero-roitool-xxx.zip
      cd ome-omero-roitool-xxx
      cd bin

#. On Mac or Linux, run::

      ./ome-omero-roitool import -h

#. On Windows, run::

      ome-omero-roitool.bat import -h

#. The ``-h`` option will give you a helpful output about how to construct the import command.

#. To achieve the import of the ROIs to OMERO, you can run::

      ./ome-omero-roitool import --password $PASSWORD --port 4064 --server $SERVER --username $USERNAME $IMAGE_ID $PATH/TO/OME-XML/FILE

#. In the above command, replace the ``$IMAGE_ID`` parameter with the ID of the image in OMERO. You can obtain this ID for example from OMERO.iviewer (see beginning of this workflow).

#. After you executed the ``import`` command above, go to OMERO.iviewer in your browser and view the ROIs on the image. The "Annotation" from QuPath is displayed as a mask ROI in OMERO.iviewer (the yellow ROI in the screenshot below). Masks cannot be edited in OMERO.iviewer at the moment, but they can be viewed. The mask, when selected displays a blue bounding box around the "Annotation" on the image.

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
