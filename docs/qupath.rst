Analyze OMERO data using QuPath
===============================

QuPath is a cross-platform software application designed for bioimage
analysis - and specifically to meet the needs of whole slide image
analysis and digital pathology.
See \ https://github.com/qupath/qupath/wiki/

#.  In OMERO.web, identify an image in the idr0018 project and the
    dataset Baz1a-14-100-gastrointestinal.

#.  Select the first image and double-click on it. This will open the
    image in OMERO.iviewer.

#.  In the OMERO.iviewer tab, select the whole URL in the address bar of
    your browser and copy it, for example using right-click and Copy.

#.  In the QuPath, select *File > Open URL...* and paste the link into the
    dialog.

  .. image:: images/qupath1.png

#.  Click *OK*. In the following dialog, enter your credentials.

  .. image:: images/qupath2.png

#.  Set image type to *Brightfield H&E* in the following dialog.

#.  Find a region with well-defined cells and nuclei in the image, zoom
    in.

#. Draw an ROI Annotation which denotes the region in which the cells
   will be detected using the *Wand* tool 

  .. image:: images/qupath3.png

#. Adjust your ROI Annotation using the *Brush* tool.

  .. image:: images/qupath4.png

#. Select *Analyze > Cell analysis > Cell* detection

#. You can adjust the parameters. Click *Run*. This will draw red ROIs
    around cells and nuclei inside your ROI Annotation.

  .. image:: images/qupath5.png

#. Select *Measure > Show detection measurements*

  .. image:: images/qupath6.png

#. Note: You can save the results locally by clicking *Save* in the
    bottom right of the *Detection results table*.

#. Select *Automate > Create* command history script.

#. This will automatically create a new script from the sequence of
    commands you just run and open it in the *Script editor*.

#. Note: This script does not capture the opening of images from OMERO.
    Also, we want to add parts where the ROIs from our *Detection* are
    saved on the image in OMERO. That is why we adjusted the script
    by adding the missing parts (opening of images from OMERO and
    saving the ROIs and result tables back to OMERO) and created a
    new script which we will run here.

#. Copy and paste a script from GitHub [Link To be added]. Run it.

#. Verify the results in OMERO.web.
