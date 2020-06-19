See the [original project here](https://github.com/Brucheion/Brucheion) and an [early intro video by developer Thomas Köntges here](https://drive.google.com/open?id=1TNYhsYH9sYPokfDpBjOcpm4JF_LPjwZ7).

In what follows, I will detail how I've used Brucheion as a supplementary visualization tool in combination with two other pieces of software: the well-known [Classical Text Editor](https://cte.oeaw.ac.at/) (CTE) and a custom-built, Python-based [CTE-to-CEX pipeline](https://github.com/tylergneill/cte2cex) (cte2cex).

Data Prep
--------

In theory, it's possible to start with nothing but images of one's textual object(s) (e.g., manuscript folia) and proceed to use Brucheion to create text transcriptions, then link those transcriptions to the images or even parts thereof. Here is assumed that transcripts have been or are being prepared elsewhere, and that all the links will be made automatically with the help of the cte2cex conversion tool.

For the transcripts, CTE encoding is assumed by the cte2cex conversion tool, but plain-text is also ok. These transcripts are also assumed to have very strict formatting, with milestones for both logical and physical transition points. Logical milestones anticipate CTS URNs (see [here](https://www.homermultitext.org/hmt-doc/cite/texts/ctsoverview.html) and [here](http://cite-architecture.org/cts/) for background info on the CITE architecture): e.g., "3.1.1". The format of physical milestones is familiar and not according to an official standard: e.g., "M2D_102r1".

Images must be preprocessed through "slicing" to produce dynamic zoom images (DZI). For this, I used VoidVolker's [MagickSlicer](https://github.com/VoidVolker/MagickSlicer) along with a [simple automation script](https://github.com/tylergneill/loop_magick_slicer). 

For more detail, see the [cte2cex](https://github.com/tylergneill/cte2cex) instructions.

Installation
--------

Install this version of Brucheion by simply downloading the repository. Executable (binary) files for macOS and Windows are provided in this fork, and these can hopefully be run directly. If these do not work, one can recompile one's own binary from the Golang file `./brucheion.go` using a command like the following:

~~~~
env GOOS=windows GOARCH=amd64 go build -v brucheion.go
~~~~

or just

~~~~
go build -v
~~~~

(Tips for recompiling: 1) Get the latest version of Golang [here](https://golang.org/doc/install). 2) Adjust the environment variables as needed. 3) Don't forget the `-v` flag.)

Log-in
------

> (Note: Brucheion has some preliminary online user authentication features meant to help both restrict proprietary data and facilitate team sharing. Since it was not yet possible to actively use these features, they are largely bypassed here. In fact, the entire "user" framework is instead repurposed for maintaining multiple project workspaces, as will be described below.)

With the executable secured above, start the program, but be careful to bypass the online user authentication by passing in the additional parameter `-noauth true`. In order to do this, it's easiest to launch from the command line, e.g., in macOS (and similarly in Linux):

~~~~
./Brucheion -noauth true
~~~~

> In Windows, this is equally possible with the command line. Otherwise, in order to start by double clicking on an icon while still also bypassing the authorization, first create a shortcut pointing to the .exe file, and under the shortcut's Properties menu, add `-noauth true` to its `Target` field (see [screenshot](...)), then use the shortcut to launch.

You should then see the command line provide a status update like:

~~~~
Listening at:7000...
~~~~

Open a browser of your choice and navigate to:

[localhost:7000/login/](localhost:7000/login/)

> Tip: Bookmark this URL in your browser.

The following screen should appear:

[image]

Choose a project here by typing in the name of the database, e.g., "01nbh3". If creating a new project, type the new name here. 

The next screen confirms the choice. Click `Forward to Mainpage`. 

[image]

The following landing page is very rough. In addition to a `Logout` link (with which one can go back and choose a different project), there are a few hard-coded links allowing access to particular points within particular projects. For now, there are one of each for the two major visualization modes developed so far: `Passage Overview` and `Multicompare`.

> Note: Once logged in, closing the command line process before without logging out currently results in a browser cookie problem. If this happens, find and delete the relevant cookie, then begin again like normal. Always log out before closing the command line process.

For now, click on `Passage Overview`.

Passage Overview Mode
---------

This is the image-to-text alignment mode.

Here the object image is front-and-center. The image viewer has buttons for zoom, and moving is possible with click-and-drag, or else one can also simply use the trackpad. There are also simple, temporary shape drawing features like lines and boxes.

> Tip: Simply refresh the page to clear such drawn features.

In this fork (which focuses on South Asian material in mostly horizontal or landscape layout), the corresponding text appears below the image (as opposed to on the right in the master branch; eventually it should be possible to toggle between the two). This text box has fixed dimensions with a scroll bar, to facilitate maximally close comparison of image and text. Text size can be adjusted with the provided buttons.

Above the image, arrow buttons are provided for moving between adjacent passages as defined by CTS URNs (again, see [here](https://www.homermultitext.org/hmt-doc/cite/texts/ctsoverview.html) and [here](http://cite-architecture.org/cts/) for background). To the right of the arrows is a dropdown box with which one can change to other witnesses also extant for the chosen passage. One can also simply modify the URL directly.

Below the text are a number of other features (e.g. metadata) and links to other program modes (e.g., `Transcribe`) not currently utilized in the present implementation.

At the very top of the page are two menus. The first, `Tools`, contains links to the other modes, most notably `Multicompare`. The second menu, named after the current project, contains a `Logout` link, which will bring one back to the Login screen where a different project can be chosen.

For now, under "Tools", choose "Multicompare" to go to the other of the main two modes.

> Tip: In addition to Brucheion's own navigation buttons and menus, the browser's native forward and back buttons and other browsing history features can be used as normal, provided that one remains logged in to the relevant "user" (or here, project).

Multicompare Mode
--------

This is the many-to-one text alignment mode.

Here, a base text appears on the lefthand side, and a blank space appears on the righthand side where other witnesses will be aligned. As in `Passage Overview` mode, the overall passage focus can be changed with the arrow buttons. The adjacent witness dropdown box, by contrast, has a slightly different function here: It specifically determines the base text on the *lefthand* side. Below the dropdown box appear several white buttons that can in turn be used to select (by simple siglum) a second witness for alignment on the *righthand* side.

Once selected, the second witness appears on the righthand side, complete with interactive alignment. Yellow color in the base text on the lefthand side  reflects *total, overall* variation in *all witnesses* relative to the base text. Green color in the aligned text on the righthand side indicates *specific* variation in *only that witness* relative to the base text.

For this alignment, lemmata selection — here: chunking by entire words or groups thereof — is currently only automatic (based on an implementation of the Needleman-Wunsch algorithm) and cannot be altered. Hovering with the mouse over text on either side results in tandem highlighting in bold of such corresponding groups on both sides. Clicking on an alignment group on either side results in (persistent bold highlighting at that spot and) a variants summary for that lemma at bottom left. The blue sigla links in this variant summary currently function just as the white buttons above do: to change the selection of the right alignment text.

There is also an option to instead align and view orthographically normalized transcription text, which can help direct attention to more significant variants. This feature, which works by way of regular expressions, is currently available by API call only. The command can be entered in the browser (e.g., another tab) or via the command line (e.g., with curl):

[localhost:7000/normalizeAndSave/all](localhost:7000/normalizeAndSave/all)

> Tip: Bookmark this URL.

> Note: This command (re)-normalizes the entire database at once. There also exists an alternative API endpoint [localhost:7000/normalizeTemporarily/](localhost:7000/normalizeTemporarily/), which requires a full CTS URN (e.g. [localhost:7000/normalizeTemporarily/urn:cts:sktlit:skt0001.nyaya002.C3D:3.1.1/](localhost:7000/normalizeTemporarily/urn:cts:sktlit:skt0001.nyaya002.C3D:3.1.1/)), which does not save and so can be used for testing. Options for toggling normalization and/or specifying a different set of (e.g., language- or dialect-specific) regular expressions can be managed in the file `config.json`.

Note also that the same top menus still apply: Under `Tools`, one can return to `Passage Overview`, maintaining focus on the selected base text, or under the second menu, one can `Logout` to end the session or switch to a different project.

> Note: Moving from `Multicompare` to `Passage Overview` while maintaining focus on the *aligned witness on the righthand* side is not yet supported. This functionality has been supplemented with the use of a small Python script, included here.

> Note: It's also not yet possible to use the arrow keys to move between passages in `Multicompare` while maintaining focus on the same aligned witness on the righthand. Either one must click again, or one can modify the URL instead.

Updating with cte2cex Conversion Pipeline
--------

In the course of using Brucheion to visualize transcript data, one may make new discoveries, and it may become necessary to make changes to that textual data in the primary files (e.g., CTE, txt). In order to update Brucheion with the new changes, first just be logged into the desired project. While logged in, go to the command line and run the cte2cex conversion script for the given project with the -u flag in order to update the database by overwriting it with the re-imported transcript data. The reloading of the new cex data into the database will produce a new tab in the browser confirming the operation. If the orthography normalization option in the cte2cex project config file is selected, this will also be redone at this time, again in its own new tab.





Data Prep Detail
========

The master branch of Brucheion is being designed for fully independent use as a digital research environment, capable of facilitating manual image-to-text alignment, manual transcription, semi-automatic editing, team collaboration (possibly git-based), and visualization of results.

In the meantime, the Nyāyabhāṣya DFG project has moved steadily forward with critical editing, not least to keep up with re-funding deadlines, using Brucheion as a supplementary visualization tool and creating new data for use with it outside of the developing environment. This data prep is namely done with ImageMagick and MagickSlicer, for images, and with Classical Text Editor (CTE) files (maintained in a team Dropbox folder), for text (here in the context of current Brucheion use meaning transcriptions first of all, to say nothing of collation and edition data with various apparatuses, not yet supported). This textual data is prepared according to strict structural and formatting standards (detailed here), partly in anticipation of combined use with images in Brucheion, and updates are then prepared for use with Brucheion via a Python CTE-to-CEX conversion pipeline (also included here). The resulting CEX file is then used to overwrite the relevant Brucheion project database. Details follow.

Images and slicing
----

Images must first be "sliced" in order to work with Brucheion's dynamic zoom function. With good filenames and a bit of scripting, all such slicing prep can be prepared from simple images with relatively little effort.

To this end, it's best to start out by ensuring that image files are named consistently, such as with the format OBJECT_LOCUS.EXTENSION (e.g. 'M2D_31r.jpg' for manuscript M2D, folio 31r). That way, the URNs referring to these images will also be consistent. This may also better facilitate time-saving batch-processing later on.

In order to perform the preparatory "slicing", so that images will work with Brucheion, you'll need two additional pieces of software: Image Magick and Magick Slicer. (NB: These are not required by Brucheion at run-time but rather only for data prep, so it's ok to prepare images on one system and simply copy them over to other machines for use).

Image Magick may or may not come pre-installed on your machine (e.g. it does on Ubuntu 16.04), and availability may depend on system. In any case, try at least to make sure it's up-to-date (e.g. with Homebrew, 'brew upgrade imagemagick'). On the other hand, Magick Slicer doesn't require installation; it's just a bash script that you download and run from the command line. Get it here:
https://github.com/VoidVolker/MagickSlicer/blob/master/magick-slicer.sh

Once you've obtained these two pieces of software, test Magick Slicer on a random image. Note how it produces a "xxx_files" folder and a .dzi file for each image. Conveniently, one never needs to look inside or otherwise interact manually with any of these files or folders, but rather just move them around in blocks.

[[INSERT HERE: test sliced image within Brucheion]]

With images named and the software installed, you're ready now to simply apply Magick Slicer to each individual image file. Essentially, each application requires a command-line call like:

~~~~ MagickSlicer-master/magick-slicer.sh path_to_imgs/img_filename path_to_output/img_filename

With a large number of such image files, this is obviously easiest to do with some sort of automation script. (I can provide here one such script written in Python.) Mac Automator (or similar) can also be used.

Ultimately, the output files must be organized in the following type of folder structure, according to the object, image versions, and individual object loci:

collection					(e.g. 'nyaya')
	object 1 				(e.g. 'M2D')
		version A			(e.g. 'positive')
			locus i_files	(e.g. '31r')
			locus i.dzi		(e.g. '31r')
			locus ii_files	(e.g. '31v')
			locus ii.dzi	(e.g. '31v')
			...
		version B			(e.g. 'negative')
			locus i_files	(e.g. '31r')
			locus i.dzi		(e.g. '31r')
			...
		...
	object 2 				(e.g. 'P4D')
		version A			(e.g. 'positive')
			locus i_files	(e.g. '051v')
			locus i.dzi		(e.g. '051v')
			locus ii_files	(e.g. '052r')
			locus ii.dzi	(e.g. '052r')
			...
		version B			(e.g. 'negative')
			locus i_files	(e.g. '051v')
			locus i.dzi		(e.g. '051v')
			...
		...
	...

(Note: The Python script also takes care of a particular naming convention particular to the Nyāya project — from the object name. E.g. M2D_31r becomes M2_31r, and TML_44v becomes T_44v — which need not remain the case, as it creates a bit of extra work, but it does currently demonstrate that the two different kinds of URN (cts for text portions and cite for image files) need not be the same. Additional naming conventions for the Nyāya project include: 1) leading zeroes are currently non-trivial, but they only occur where necessary; 2) there are also prefixes for parts of the work like '_3_' which are needed for foliation that resets between those parts.)

This whole structure, placed into a single folder (e.g. 'nyaya'), is then placed within the folder "Brucheion/static/image_archive".

(Note: Again, the name of the top image folder need not correspond to that of the .cex file, but here, they are allowed to do so.)

The images are now ready to be found by Brucheion.

CTE data standard
-----

...



Data Prep: Text
***************

All text material for a given collection to be imported into Brucheion should be placed within a single .cex file. This file is comprised of sections preceded with the following kinds of headers:
#!cexversion
#!citelibrary
#!ctscatalog
#!ctsdata
#!relations

(NB: So far, I have found #!ctsdata to be the most important, and so the focus below is on this. For more details on sections #!cexversion, #!citelibrary, #!ctscatalog, and more, see (CTS documentation...) at (...) The section #!relations can also be prepopulated with known mappings between text locus and images (or rather, parts of images), for which, see the below section 'Data Prep: Text and Image Relations'.)

The section #!ctsdata contains the actual textual data and is formatted as a simple two-column csv file, where the separator character is a hashtag #. To the left of this separator is an identifier in the form of a CTS URN, corresponding to an instance of a work (or source, e.g. manscript M2D of the Nyāyabhāṣya) and a given logical locus within that work (e.g. sūtra 3.1.1 within that manuscript M2D). To the right of the separator is the textual data relating to that identifier.

(NB: Each such line of identifier and content constitutes a node. Nodes are connected to each other via "previous" and "next" relationships, which are established upon import based on their ordering relative to each other. Thus, while identifiers may well constitute a logical numerical sequence, they need not do so. New nodes can also be added at run-time; currently, only adding them at the beginning or end of a sequence of a given work is supported.)

It may take considerable effort to transform one's textual data into this format. At a bare minimum, one must decide on identifiers for logical sections, and then one's transcription data (for example) must already clearly mark these logical sections, if not with CTS identifiers, then in a way that can easily be converted to this. Moreover, only plain-text is suitable for use with .cex; all other special formatting that renders the data non-human-readable (bold, links, etc.) should be removed.

(As a special case, Sanskrit works, whose logical sections tend to be unusually large, may necessitate retaining manuscript image information such as folio and line breaks in the transcription data itself even in the .cex. Even here, however, these markers must be reformatted to be only plain text.)

For an example of a workflow for transforming several dozen standardized Classical Text Editor (CTE) transcription files to valid #!ctsdata content (with markers for folio and line breaks) for use with a single .cex file, see the separate document (...).

To actually create the .cex file, first create an empty file with a name appropriate for the collection and the extension '.cex' (e.g. 'nyaya.cex'). Add initial metadata sections (e.g. #!cexversion, #!citelibrary, #!ctscatalog) as desired.

Next, under the header "#!ctsdata", enter the two-column text data, formatting locus information as valid CTS URN and the correspnding textual information WITHOUT newlines. Each line must contain one and only one node.

(Note: Newlines can be made to display within the program — for the sake of representating manuscript line breaks, for example — using a predefined tag defined within the Go file, currently: -NEWLINE-.)

Then, if desired, add the #!relations section (optional, see below section).

Finally, place this .cex file among the other .cex files in the "cex" folder at the top level of the Brucheion directory. It is now ready to be found and loaded into Brucheion.



Data Prep Detail: RELATIONS
***********


Optionally, rather than only establishing relationships between image and text individually using the "Image References Editor" within the Brucheion interface, one can also initialize the database with already-known such relationships by populating the "#!relations" .cex section on import.

Here, CTS textual and CITE object URNS are related to each other via CITE verb URNS (e.g. urn:cite2:dse:verbs.v1:appearsOn). Thus, a text-verb-image triplet looks for example like: urn:cts:sktlit:skt0001.nyaya002.msM2D:3.1.1#urn:cite2:dse:verbs.v1:appearsOn:#urn:cite2:nyaya:M2img.positive:M2_31r.

Assuming that (for example) one's transcript data consistently marks folio breaks with consistent formatting, it is then merely a mechanical matter to extract a linear sequence of tags for logical loci and tags for image loci. From this sequence, one can then further mechanically build table of pairings and then convert this table into a list of text-verb-image triplets which can simply be copied into the .cex file.

(It may even be possible to assign line numbers to each newline, based on distance from the top and/or bottom of a given folio, and to use the resulting line numbers, along with approximate information about the size of margins on a given folio, as a rough estimate for where a section appears on a given image. This would then allow further specification of the image portion of the triplet with the @top_left,top_right,bottom_left,bottom_right coordinate format, to guide where on the image one should begin looking for one's desired text.

For an example of a workflow for transforming several dozen standardized Classical Text Editor (CTE) transcription files to valid #!relations content for use with a single .cex file, see the separate document (...).



Python cte2cex conversion pipeline
----

...

Overwriting
----
The .cex library files are kept in the 'cex' folder. Databases can be initially created from such a collection file. Changes made within the program are saved in the database, but they can also be exported as a new .cex collection file at any time.

Bug: alphabetical, other features...

Step 3: Load A .cex file into Brucheion by entering the following URL in the browser address bar:

~~~~
http://localhost:7000/load/PROJECT
~~~~

If the load is successful, this converts the collection contents into a database file, named after the user/project ('.db'), and located at the top level of the Brucheion folder.



Loading Your Data
************

With the image and text files in place as described above, you are now ready to load them into Brucheion.

As with the test run, decide on a username. This cannot be the same username as used for, e.g., the test run, or else the data will end up all smushed together (e.g. Sanskrit with Jane Austen). If you wish to use the same string as before (e.g. 'elisabeth'), then simply delete the old database which appears in the top Brucheion folder (e.g. 'elisabeth.db'). (NB! All un-exported changes will be lost!)

Now, as before, load the new .cex file into a database by running Brucheion (if it is not already still running from before), opening a browser, and entering the 'load' URL, as above. E.g.:

~~~~ http://localhost:7000/tyler/load/nyaya

When just beginning with Brucheion, you will want to start small and try loading bigger and more comprehensive .cex files until you have finally ensured that your file is properly formatted and that there are correspondingly no more errors while loading. In between attempts, when loading is not successful, simply restart Brucheion (as necessary) and try loading again (that is, deleting the resulting .db file is not necessary, because nodes will be overwritten for any work mentioned in the .cex file). (An additional 'CTS/CITE' module may help with this validation process later on.)



Working with Your Data and Sharing It with Others
*************************************************

Again, to view your data, copy the steps for the test-run above, but now using your own username and node information. E.g.:

~~~~ http://localhost:7000/tyler/view/urn:cts:sktlit:skt0001.nyaya002.msM2D:3.1.1

And so on...

Use the 'CEX-Download' button at any time to export a .cex file incorporating all changes made within the program interface and stored in the internal database. This file can then be easily imported by another user running Brucheion on another system.

(NB: Proprietary text or image material should be shared more carefully, such as via links to private Dropbox or Gitlab accounts, or else through shared servers, or else by directly sharing via simple USB drives, for example. Assuming that another user has been given the folder containing all prepared images however, then it is simply a matter of placing them in the right place, as well as doing so with the .cex file, and the sytem will be ready to go.)






Unutilized features
===========

Download cex
------

Under the top-right menu (named for the user/project) there is

Image Reference Editor: for associating images or parts of images with text content

	Tip: To associate an image (or a part of an image) with some chunk of text, execute the following steps:
		0) Clear the ImageRef box on the right. (This will likely be changed later; if not cleared, since new additions come along with a # after themselves, the first addition will collide with the default contents of the box.)
		1) Choose the desired folio by entering its CITE URN into the Change Image box. Currently, this must be entered manually.
		2) Activate the image area selector box by clicking the far-right button in the accompanying image viewer (the one with a square icon). You can also activate this by pressing 'c'.
		3) Draw and adjust a selection box, then click on the checkmark to accept.
		4) After accepting, add the selected region to the list of associated images for the locus by clicking on the green '+' button next to the image-coordinate-containing URN that has been created above.
		5) Save this reference addition to the list by clicking 'Save'.

Transcription Comparison (and Alignment): view and modify alignment of two (perhaps later up to four) parallel versions of text

	Tip: To access, construct a URL as follows, taking as a reference point a "view" URL: replace "view" with "compare" and add after the text URN a plus sign + and then a second, different text URN where parallel text is expected.
	(E.g., http://localhost:7000/tyler/compare/urn:cts:sktlit:skt0001.nyaya002.msTML:3.1.36+urn:cts:sktlit:skt0001.nyaya002.msM3D:3.1.36)
	(I.e., currently being developed, can be accessed only by URL; no clickable links yet.)

	Tip: Hover over words to see current alignment pairings.

	Tip: Word-level alignment is initialized automatically using an implementation of the Needleman-Wunsch algorithm. Single click first on a word in one text and then on a corresponding word in another text to re-associate them as an alignment pair.

	Tip: Double click on a given token to declare it as not corresponding to anything in the other text(s).

	Tip: Changes cannot yet be saved.

Transcription Consolidation

	Tip: Access by constructing a URL similar to "compare" above, with "consolidate" in place of "compare".

	Tip: Click on a word from any version to add it to the end of the consolidation buffer.

	Tip: You can also simply edit the buffer manually by clicking in it and typing normally.

	Tip: Changes cannot yet be saved.
