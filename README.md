See the [original project here](https://github.com/Brucheion/Brucheion) and an [early intro video by developer Thomas Köntges here](https://drive.google.com/file/d/1bV4vTyOK5PtzxIspzLYOFOp_6PVizlZd/view?usp=sharing).

In what follows, I detail how I've used Brucheion as a supplementary visualization tool in combination with two other pieces of software: the well-known [Classical Text Editor](https://cte.oeaw.ac.at/) (CTE) and a custom-built, Python-based [CTE-to-CEX pipeline](https://github.com/tylergneill/cte2cex) (cte2cex).

Data Prep
--------

In theory, it's possible to start with nothing but images of one's textual object(s) (e.g., manuscript folia) and proceed to use Brucheion to create text transcriptions, then link those transcriptions to the images or even parts thereof with the program's graphical user interface (GUI). Here, by contrast, is assumed that all transcripts are being prepared elsewhere, and that all image-text relations will be established automatically with the help of the cte2cex conversion tool operating upon the very carefully prepared transcripts.

For the transcripts, CTE encoding is assumed by the cte2cex conversion tool, but plain-text is also ok. These transcripts are also assumed to have very strict formatting, with milestones for both logical and physical transition points. Logical milestones (a.k.a. "chapter identifiers") anticipate CTS URNs (see [here](https://www.homermultitext.org/hmt-doc/cite/texts/ctsoverview.html) and [here](http://cite-architecture.org/cts/) for background info on the CITE architecture): for example, "3.1.1" for book 1, section 1, verse 1. The format of physical milestones (a.k.a. "object identifiers") is not according to an official standard, but philological practices tend to be fairly similar in this regard: for example, "J1D_102r1" for witness J1D, folio 102, side r, line 1. The exact specifications can be modified as needed.

Images stored locally must be preprocessed through "slicing" to produce Dynamic Zoom Images (DZI) in order for Brucheon to use them. For this, one can use VoidVolker's [MagickSlicer](https://github.com/VoidVolker/MagickSlicer) along with some sort of automation workflow, such as the [Python script here](https://github.com/tylergneill/loop_magick_slicer). See `Image Archive Setup` below for more on how to make the sliced images locally available to Brucheion.

For more detail on data prep, see the [cte2cex](https://github.com/tylergneill/cte2cex) instructions.

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

> Tips for recompiling: 1) Get the latest version of Golang [here](https://golang.org/doc/install). 2) Adjust the environment variables as needed. 3) Don't forget the `-v` flag.

Log-in
------

> Note: Brucheion has some preliminary online user authentication features meant to help both restrict proprietary data and facilitate team sharing once the software is hosted online. Since this point was not yet reached, these features are bypassed here. In fact, the entire "user" framework is instead repurposed for maintaining multiple project workspaces, as described below.

With the executable secured above, start the program, but be careful to bypass the online user authentication by passing in the additional parameter `-noauth true`. In order to do this, it's easiest to launch from the command line, e.g., in macOS (and similarly in Linux):

~~~~
./Brucheion -noauth true
~~~~

> In Windows, this is equally possible with the command line. Otherwise, in order to start by double clicking on an icon while still also bypassing the authorization, it is necessary to first have a shortcut pointing to the .exe file. Then, under this shortcut's Properties menu, in the `Target` field, adding `-noauth true` at the end will cause this parameter to be passed in every time the shortcut is used.

![screenshot](...)

You should then see the command line provide a status update like:

~~~~
Listening at:7000...
~~~~

Open a browser of your choice and navigate to:

`localhost:7000/login/`

> Tip: Bookmark this URL in your browser.

The following screen should appear:

![screenshot](...)

Choose a project here by typing in the name of the desired database, located at the top level of the Brucheion folder, e.g., "01nbh3". If creating a new project, then simply type the new name here. 

The next screen confirms the choice. 

![screenshot](...)

Now click `Forward to Mainpage`. 

The following landing page is very rough. 

![screenshot](...)

In addition to a `Logout` link (with which one can go back and choose a different project), there are a few hard-coded links here allowing access to particular points within particular projects. For now is provided just one of each for the two major visualization modes developed so far: `Passage Overview` and `Multicompare`, for J1D 3.1.1 and D1E 3.1.1, respectively.

> Note: Once logged in, closing the command line process prematurely, i.e. without logging out in the browser, currently results in a browser cookie problem. When this happens, find and delete the relevant cookie (e.g., by searching for "localhost" in `chrome://settings/siteData`, then begin again like normal. Always log out before terminating the command line process.

For the sake of this tutorial, now click on `Passage Overview`.

Passage Overview Mode
---------

This is the image-to-text alignment mode.

Here the object image is front-and-center. The image viewer has buttons for zoom, and moving is possible with click-and-drag, or else one can also simply use the trackpad. There are also simple, temporary shape drawing features like lines and boxes.

> Tip: Simply refresh the page to clear such drawn features.

In this fork (which focuses on South Asian material in mostly horizontal or landscape layout), the corresponding text appears below the image (as opposed to on the right in the master branch; eventually it should be possible to toggle between the two). This text box has fixed dimensions with a scroll bar, to facilitate maximally close comparison of image and text. Text size can be adjusted with the provided buttons.

Above the image, arrow buttons are provided for moving between adjacent passages as defined by CTS URNs (see above for links to background on this citation architecture). To the right of the arrows is a dropdown box with which one can change to other witnesses also extant for the chosen passage. One can also simply modify the URL directly.

Below the text are a number of other features (e.g. metadata) and links to other program modes (e.g., `Transcribe`) not needed for the visualization functionality being described here.

At the very top of the page are two menus. The first, `Tools`, contains links to the other modes, most notably `Multicompare`. The second menu, named after the current project, contains a `Logout` link, which will bring one back to the Login screen where a different project can be chosen.

For now, under "Tools", choose "Multicompare" to go to the other of the main two modes.

> Tip: In addition to Brucheion's own navigation buttons and menus, the browser's native forward and back buttons and other browsing history features can be used as normal, provided that one remains logged into the relevant "user" (or here, project).

Multicompare Mode
--------

This is the many-to-one text alignment mode.

Here, a base text appears on the lefthand side, and a blank space appears on the righthand side where other witnesses will be aligned. As in `Passage Overview` mode, the overall passage focus can be changed with the arrow buttons. The adjacent witness dropdown box, by contrast, has a slightly different function here: It specifically determines the base text on the *lefthand* side. Below the dropdown box appear several white buttons that can in turn be used to select (by simple siglum) a second witness for alignment on the *righthand* side.

Once selected, the second witness appears on the righthand side, complete with interactive alignment. Yellow color in the base text on the lefthand side  reflects *total, overall* variation in *all witnesses* relative to the base text. Green color in the aligned text on the righthand side indicates *specific* variation in *only that witness* relative to the base text.

For this alignment, lemmata selection — here: chunking by entire words or groups thereof — is currently only automatic (based on an implementation of the Needleman-Wunsch algorithm) and cannot be altered. Hovering with the mouse over text on either side results in tandem highlighting in bold of such corresponding groups on both sides. Clicking on an alignment group on either side results in (persistent bold highlighting at that spot and) a variants summary for that lemma at bottom left. The blue sigla links in this variant summary currently function just as the white buttons above do: to change the selection of the right alignment text.

There is also an option to instead align and view orthographically normalized transcription text, which can help direct attention to more significant variants. This feature, which works by way of regular expressions, is currently available by API call only. The command can be entered in the browser (e.g., another tab) or via the command line (e.g., with curl):

`localhost:7000/normalizeAndSave/all/`

> Tip: Bookmark this URL.

> Note: This command (re)-normalizes the entire database at once. There also exists an alternative API endpoint `localhost:7000/normalizeTemporarily/`, which requires a full CTS URN (e.g. `localhost:7000/normalizeTemporarily/urn:cts:sktlit:skt0001.nyaya002.C3D:3.1.1/`), which does not save and so can be used for testing. Options for toggling normalization and/or specifying a different set of (e.g., language- or dialect-specific) regular expressions can be managed in the file `config.json`.

Once orthographic normalization has been performed, whether this or the original text is displayed in Multicompare is controlled by a toggle in the Brucheion `config.json` file.

> Note: Changes to this config file only take effect upon starting Brucheion. To refresh with new options, log out of Brucheion, terminate the process, then start the process and log back in again, navigating back to the desired page.

Note also that the same top menus still apply: Under `Tools`, one can return to `Passage Overview`, maintaining focus on the selected base text, or under the second menu, one can `Logout` to end the session and/or switch to a different project.

> Note: Moving from `Multicompare` to `Passage Overview` while maintaining focus on the *aligned witness on the righthand* side is not yet supported. This functionality has been supplemented with the use of a small Python script, included here.

> Note: It's also not yet possible to use the arrow keys to move between passages in `Multicompare` while maintaining focus on the same aligned witness on the righthand side; one must either choose it again by clicking, or one can move between passages by modifying the URL directly (without changing the aligned witness parameter after the hashtag).

Updating with cte2cex Conversion Pipeline
--------

In the course of using Brucheion to visualize transcript data, one may make new discoveries, and it may become necessary to make changes to that textual data in the primary files (e.g., CTE, txt). In order to update Brucheion with the new changes, first one should be logged into the desired project. While logged in, go to the command line and run the cte2cex conversion script for the given project (i.e., with the project JSON file) along with the `-u` flag in order to update the database by overwriting it with the re-imported transcript data. The reloading of the new cex data into the database will produce a new tab in the browser confirming the operation. If the orthography normalization option in the cte2cex project config file is selected, this will also be redone at this time, again in its own new tab.

> Note: All nodes in the database for any work mentioned in the CEX file being loaded will be deleted. Then, only those nodes for that work which are mentioned in the CEX file will be reinstated in the database according to the corresponding content in the newly loaded CEX file.

Image Archive Setup
----

Before using Brucheion, Dynamic Zoom Image (DZI) files are to be placed within the folder `Brucheion/static/image_archive` in a folder sub-structure corresponding to the CITE URN protocol. For example, assuming a project titled "nyaya", with multiple witnesses among which is a "J1", with two versions "positive" and "negative", and with individual folio "37r" and "37v", the following CITE URNs

~~~~
urn:cite2:nyaya:J1img.positive:J1_37r
urn:cite2:nyaya:J1img.negative:J1_37v
~~~~

would correspond to the following structure within `Brucheion/static/image_archive`

![screenshot](...)

> Note: In the Sanskrit projects behind the present description, some pains have been taken to maintain a distinction between CTS URNs and CITE URNs as regards witness sigla. Namely, manuscript sigla, which contain a letter or letters designating the script (D - Devanāgarī, S - Śāradā, ML - Malayalam, etc.), drop this element in the CITE URN. Thus "J1D" in the logically-oriented CTS URNs corresponds to "J1" in the physically-oriented CITE URNs. Thus, an example RDF triple relating the two types of URN reads:

~~~~ 
urn:cts:sktlit:skt0001.nyaya002.J1D:3.1.1#urn:cite2:dse:verbs.v1:appearsOn:#urn:cite2:nbh:J1img.positive:J1_37r
~~~~

> The point was to maintain the conceptual distinction between the two types of URNs, but such a distinction is by no means technically necessary here. Note also the difference in the workspace protocol ("skt0001.nyaya002") and the image archive subfolder ("nbh").

The images are now ready to be found by Brucheion.

Understanding CEX
-----

All text material for a given project to be visualized in Brucheion is consolidated into a single .cex file, which becomes the basis for the .db Bolt database file. A CEX file, as used here (further detail can be found [here](...)), is comprised of five blocks of data:

* `#!ctscatalog`: defines which witnesses are considered in a given project.

* `#!ctsdata`: the actual textual data, formatted in two columns with hashtag (#) as a  separator

* `#!relations`: here, just relationships between image and text, expressed as RDF triples

If building a new project from scratch for use with such a protocol, it takes considerable effort to transform one's textual data into this format. Firstly, one must already have segmented one's text into reasonably sized portions, each with its own CTS identifier, and this must be marked clearly in the transcription data itself. Next, all non-plain-text content (font formatting, XML tags, etc.) must be filtered out somehow; here, the cte2cex pipeline is used. 

> Note: A special provision is made for line breaks ("-NEWLINE-") and folio breaks (e.g., "J1D_37r1"). The former tag is provided for within the Golang code-base itself.

Finally, one must actually compose the CEX file itself, with proper formatting (i.e., proper use of blocks and separators, etc.) For developing a new project, therefore, it's best to simply copy an existing project and make changes as necessary.

> Note: In keeping with use of Brucheion for visulization only, the CEX format here serves only to consolidate data from elsewhere and to use it to populate an internal Bolt database. Conversion in the opposite direction, from Bolt database to CEX file, is also possible (currently with some alphabetization bugs) by way of the CEX export ("Download CEX") feature, in the top-right burger menu, but that is not used here.

The `#!ctsdata` block is most essential. Each line of identifier and content constitutes a node. Nodes are connected to each other via "previous" and "next" relationships within the corresponding Bolt database; these are established upon import based on their ordering relative to each other in the CEX file. Thus, while identifiers may well constitute a logical numerical sequence, they need not do so. It is also possible to create and populate nodes totally within the GUI, at run-time, but currently it is only possible to do so at the beginning or end of a sequence of a given work; in any case, no run-time modification functionality is used in the visualization implementation described here.

The `#!relations` block is used here to prepopulate the database with relations worked out in advance, with the help of the cte2cex conversion tool, based on the transcription files.

Namely, once one's transcript data consistently marks both chapter and object identifiers with consistent formatting, it is then merely a mechanical matter to extract a linear sequence of such identifiers, on the one hand, and textual content, on the other, and then from this sequence, one can further mechanically build a table of paired identifiers, which then simply needs to be formatted as RDF triplets (here: chapter-verb-object) which can then be incorporated directly into the CEX file. It's also possible to use the `Image References Editor` within the GUI to specify individual relations, even to the level of parts of images, but that is not used here.

Finally, it is only if it is placed in the `Brucheion/cex` folder that a CEX file is able to be found by the Brucheion `load` call.

Quick Run-Down of Features Not Utilized
----------

* Image Reference Editor: for associating images or parts of images with text content.

	> Tip: To associate an image (or a part of an image) with some chunk of text, execute the following steps:
	1. Clear the `ImageRef` box on the right. (This will likely be changed later; if not cleared, since new additions come along with a `#` after themselves, the first addition will collide with the default contents of the box.)
	2. Choose the desired folio by entering its CITE URN into the `Change Image` box. Currently, this must be entered manually.
	3. Activate the image area selector box by clicking the far-right button in the accompanying image viewer (the one with a square icon). You can also activate and/or deactivate this by pressing `c` on the keyboard.
	4. Draw and adjust a selection box, then click on the checkmark to accept.
	5. After accepting, add the selected region to the list of associated images for the locus by clicking on the green `+` button next to the image-coordinate-containing URN that has been created above.
	6. Save this reference addition to the list by clicking `Save`.

* Transcription Comparison (and Alignment): view and modify alignment of two (perhaps later up to four) parallel versions of text

	> Tip: To access this mode, construct a URL as follows, taking as a reference point a "view" URL: replace "view" with "compare" and add after the text URN a plus sign + and then a second, different text URN where parallel text is expected. E.g.: 

	~~~~
	http://localhost:7000/tyler/compare/urn:cts:sktlit:skt0001.nyaya002.msTML:3.1.36+urn:cts:sktlit:skt0001.nyaya002.msM3D:3.1.36
	~~~~

	> More Tips:
	* Hover over words to see current alignment pairings.
	* Word-level alignment is initialized automatically using an implementation of the Needleman-Wunsch algorithm. Single click first on a word in one text and then on a corresponding word in another text to re-associate them as an alignment pair.
	* Double click on a given token to declare it as not corresponding to anything in the other text(s).
	* Changes cannot yet be saved.

* Transcription Consolidation: similar to the above, but meant for choosing between competing transcriptions.

	> Tip: This mode is probably not being further developed. To access it anyway, construct a URL similar to "compare" above, with "consolidate" in place of "compare".

	> More Tips:

	* Click on a word from any version to add it to the end of the consolidation buffer.
	* You can also simply edit the buffer manually by clicking in it and typing normally.
	* Changes cannot yet be saved.
