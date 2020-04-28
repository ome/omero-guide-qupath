Analyze OMERO data using QuPath
===============================

QuPath is a cross-platform software application designed for bioimage analysis - and specifically to meet the needs of whole slide image analysis and digital pathology.
See \ https://github.com/qupath/qupath/wiki/

#.  In OMERO.web, identify an image in the idr0018 project and the dataset Baz1a-14-100-gastrointestinal.

#.  Select the first image and double-click on it. This will open the image in OMERO.iviewer.

#.  In the OMERO.iviewer tab, select the whole URL in the address bar of your browser and copy it, for example using right-click and Copy.

#.  In the QuPath, select *File > Open URL...* and paste the link into the dialog.

  .. image:: images/qupath1.png

#.  Click *OK*. In the following dialog, enter your credentials.

  .. image:: images/qupath2.png

#.  Set image type to *Brightfield H&E* in the following dialog.

#.  Find a region with well-defined cells and nuclei in the image, zoom in.

#. Draw an ROI Annotation which denotes the region in which the cells will be detected using the *Wand* tool 

  .. image:: images/qupath3.png

#. Adjust your ROI Annotation using the *Brush* tool.

  .. image:: images/qupath4.png

#. Select *Analyze > Cell analysis > Cell* detection

#. You can adjust the parameters. Click *Run*. This will draw red ROIs around cells and nuclei inside your ROI Annotation.

  .. image:: images/qupath5.png

#. Select *Measure > Show detection measurements*

  .. image:: images/qupath6.png

#. Note: You can save the results locally by clicking *Save* in the bottom right of the *Detection results table*.

#. In following steps, we will show how to transfer the ROIs your just created in QuPath into OMERO ROIs and attach them to the image in OMERO.

#. First, use the ROI OME-XML export script to export your ROIs from QuPath into OME-XML file. https://github.com/glencoesoftware/ome-omero-roitool/blob/master/QuPath.scripts/OME_XML_export.groovy#L23

#. Note, this script works with QuPath version 0.2.0-m8, but not with versions 0.2.0-m9 and 0.2.0m-10.

#. In QuPath, go to *Automate > Show script editor*

#. Copy all the content of the scipt from https://raw.githubusercontent.com/glencoesoftware/ome-omero-roitool/master/QuPath.scripts/OME_XML_export.groovy and paste it into the script editor of QuPath. 

#. In QuPath, with Script editor window still highlighted, select Run > Run in the top menu. There will be some output in the console of the script editor (the window just under where you pasted your script).

#. Note: The large areas highlighted as yellow (called Annotations in QuPath), which denote the areas where you ran the *Cell detection* will not be recognized by the OME-XML export script properly, and will not be written into the OME-XML file correctly. Thus, they will later not appear as ROIs or annotations in OMERO. This is denoted by lines such as ``INFO: ROI type: class qupath.lib.roi.GeometryROI INFO: Unsupported ROI type: Geometry (117832, 22510, 562, 330)`` which appear in the console output. You can ignore them, the ROIs you just created from the *Cell detection* will be written correctly.

#. A new window will appear, in which you specify the name and location (on your local machine) of the output OME-XML file. Click Save.

#. Note: QuPath does not allow stroke and fill color to be specified separately, and thus the ROIs will be written into the OME-XML as filled with the same color as the color of the stroke (red by default in QuPath).

#. Import the OME-XML with the ROIs from QuPath into OMERO. These steps must be run on a command line. Open your terminal and install the ome-omero-roitool from https://github.com/glencoesoftware/ome-omero-roitool (note: in order to work with 5.6.x servers, https://github.com/glencoesoftware/ome-omero-roitool/pull/12 must be merged).

#. To install https://github.com/glencoesoftware/ome-omero-roitool, follow the https://github.com/glencoesoftware/ome-omero-roitool/blob/master/README.md - basically, you will have to 


Clone the repository

git clone git@github.com:glencoesoftware/ome-omero-roitool.git

cd ome-omero-roitool

Run the Gradle build and utilize the artifacts as required::

./gradlew installDist cd build/install ...

(Note: If you have gradle installed, ``gradle build -x test`` is easier option. After that, ``cd build/distributions/`` and ``unzip ome-omero-roitool-0.2.0-SNAPSHOT.zip``. Then ``cd ome-omero-roitool-0.2.0-SNAPSHOT``).

#. Find the built artifacts, unzip them and ``cd ome-omero-roitool-0.2.0-SNAPSHOT/bin``.

#. On Mac or Linux, run ``./ome-omero-roitool import -h``. On Windows, run ``ome-omero-roitool.bat import -h``. This will give you a helpful output about how ot costruct the import command.

#. For example, you can run ``./ome-omero-roitool import --password $PASSWORD --port 4064 --server $SERVER --username $USERNAME $IMAGE_ID $PATH/TO/OME-XML/FILE``. To associate the ROIs with the correct image in OMERO, replace the ``IMAGE_ID`` parameter with the ID of the image in OMERO. You can obtain this ID for example from OMERO.iviewer (see beginning of this workflow).

#. After you executed the command above, go to OMERO.iviewer in your browser and view the ROIs on the image (screenshot).

#. Note: Because of the impossibility to set a different fill and stroke color, the ROIs are appearing as filled in with the same color as the stroke color. You can rectify this in OMERO.iviewer if you first select all the ROIs in the table, then go to color picker on the top and click on the downward facing arrow. Then, set the opeacity slider in the bottom of the widget to the very left (= zero opacity). Click Save to save the changes.

[screenshot]




