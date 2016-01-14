Trackers
===================

The following compares the Multi-Target Trackers from **[KITTI benchmark site](http://www.cvlibs.net/datasets/kitti/eval_tracking.php)**.


Method   | Authors | Pub. Year | Code/Link | Deps
---------| ----    | --- | ----- | -----
DP_MCF  |H. Pirsiavash | 2011
TBD  |Andreas Geiger | ICCV'13, PAMI'14
DCO |  K. Schindler and S. Roth | CVPR'12 <sup id="d1">[1](#dco1)</sup> | dctracking-v1.0.zip, [dctracking](http://research.milanton.de/dctracking/)
DCO_X* | K. Schindler and S. Roth | CVPR'13<sup id="d2">[2](#dco2)</sup>, PAMI'16<sup id="d3">[3](#dco3)</sup> | bitbucket:dctracking, [dctracking](http://research.milanton.de/dctracking/) | compileMex, GCO, LightSpeed, MOT utils, OpenGM2
CEM |K. Schindler and S. Roth | CVPR'11 <sup id="c1">[4](#cem1)</sup>, ICCV'11 <sup id="c2">[5](#cem2)</sup>,PAMI'14 <sup id="c3">[6](#cem3)</sup> |  contracking-v1.0.zip,  [contracking](http://research.milanton.de/contracking/) | MOT utils

Publications
 - <a name="dco1">1</a>. CVPR2012_Discrete-Continuous Optimization for Multi-Target Tracking [↩](#d1)
 - <a name="dco2">2</a>. CVPR2013_Detection- and Trajectory-Level Exclusion in Multiple Object Tracking [↩](#d2)
 - <a name="dco3">3</a>. PAMI2016_Multi-Target Tracking by Discrete-Continuous Energy Minimization [↩](#d3)
 - <b name="cem1">4</b>. CVRP2011_Multi-target Tracking by Continuous Energy Minimization [↩](#c1)
 - <b name="cem2">5</b>. ICCV2011_An Analytical Formulation of Global Occlusion Reasoning for Multi-Target Tracking [↩](#c2)
 - <b name="cem3">6</b>. PAMI2014_Continuous Energy Minimization for Multi-Target Tracking [↩](#c3)


## Run DCO & DCO_X*
###Steps

1. 
> hg clone https://bitbucket.org/amilan/motutils
> hg clone https://bitbucket.org/amilan/dctracking

2. cd dctracking, run installDCTracker.m, which runs the following in sequence:
> - Installing MOT Utils
>   - didn't really compile MOT Utils in installDCTracker.m, simply check the existence of its folder `../motutils`
>   - mex files are compiled in `contracking`, ie. **CEM** in compileMex.m
> - Installing GCO
> - Installing Lightspeed
>   - compiled! with warning msg: lightspeed's matfile utility is not supported for this version of Matlab
>   - from: 
	```
	if atleast78
		disp('lightspeed''s matfile utility is not supported for this version of Matlab');
	elseif atleast65
	      % -V5 is required only for Matlab >=6.5
	      mex -f matopts.sh matfile.c -V5
	else
	      mex -f matopts.sh matfile.c
	end  
	```
	where
	```
	atleast65 = (v(1)>6 || (v(1)==6 && v(2)>=5));
	atleast73 = (v(1)>7 || (v(1)==7 && v(2)>=3));
	atleast75 = (v(1)>7 || (v(1)==7 && v(2)>=5));
	atleast76 = (v(1)>7 || (v(1)==7 && v(2)>=6));
	atleast78 = (v(1)>7 || (v(1)==7 && v(2)>=8)); % R2009a
	atleast82 = (v(1)>8 || (v(1)==8 && v(2)>=2)); % R2013b
	atleast83 = (v(1)>8 || (v(1)==8 && v(2)>=3)); % R2014a
	```
> - Installing OpenGM2

3. Regarding OpenGM2
> - after successful installation of OpenGM2, installDCTracker.m will call compileOGM.m from dctracking/opengm folder to mex-compile binaryInference.cxx in the same folder
> - OpenGM needs separate downloading & compilation with  QPBO and TRW-S support.
> - The original script **installDCTracker.m**will download OpenGM via _git_ then build the entire library under $dcdir/external/opengm/BUILD/src/external
> - I've downloaded the most recent OpenGM2 to opengm-master, then compiled in the usual CMAKE way
>   - patched QPBO-v1.3.src-patched & TRWS-v1.3.src-patched to have QPBO and TRW-S support
> - Modified include/library path in compileOGM.m so that binaryInference.cxx can be compiled properly

4. Ground truth

   If ground truth available, the code will automatically produce evaluated results using the same matrics as in their paper
> - resurrect the c++ visualisation program I've written over 3 years ago


4. Evaluation metrics
> - The Multi-Object Track- ing Accuracy (MOTA)
> - The Multi-Object Detection Accuracy (MODA)
> - The Multi-Object Tracking Precision (MOTP)
> - The Multi-Object Detection Precision (MODP)
> - False positive (FPR) and false negative rates (FNR), as well as the number of identity switches (ID Sw.)
> - The number of mostly tracked (MT) and mostly lost (ML) trajectories, track fragmentations (FM), and ID switches.

## Run CEM
### Steps
1. 
> hg clone https://bitbucket.org/amilan/contracking
> hg clone https://bitbucket.org/amilan/motutils

2. 
> cd contracking;
> Start MATLAB and run compileMex.m to build the utilities binaries
> produce mex file under motutils/mex/bin/

3. 
> run cemTrackerDemo.m

## Run TBD
### Steps
1. compile the DPM object detector
> enter the 'lsvm4' directory and run make.m.
> ```
mex -O resize.cc
mex -O dt.cc
mex -O features.cc
mex -O getdetections.cc
mex -O fconvsse.cc
> ```

2. compile the implementation of the Hungarian algorithm by running make.m in the tracking directory.
> ```
mex assignmentoptimal.c
> ```

3. run the object detector
> open  `run_detection.m` and modify the variables **base_dir** and **seq_dir** to point to one of the downloaded sequences. 
> run_detection.m
> NOTE that this part    takes approximately 5-10 seconds per image. The results for the left color    image (image_02) are stored in the 'object_02' subfolder of the sequence directory.
   
4. run the tracking stage
> - replace **fconv** in *lsvm4/gdetect.m* with **fconvsse**
> - open 'run_tracking.m' and modify the variables **base_dir** and **seq_dir** to point to one of the downloaded sequences for which the folder 'object_02' exists
> - The tracking results are stored in **_tracking_results.txt_**

5. visualize the tracking results
> - open 'run_visualization.m' and modify the variables base_dir and seq_dir to point to one of the downloaded    sequences for which the file ___tracking_results.txt___ exists
> - Images with colored bounding boxes are displayed and stored in the subfolder **_track_02_**




Introduction
-------------

StackEdit stores your documents in your browser, which means all your documents are automatically saved locally and are accessible **offline!**

> **Note:**

> - StackEdit is accessible offline after the application has been loaded for the first time.
> - Your local documents are not shared between different browsers or computers.
> - Clearing your browser's data may **delete all your local documents!** Make sure your documents are synchronized with **Google Drive** or **Dropbox** (check out the [<i class="icon-refresh"></i> Synchronization](#synchronization) section).

#### <i class="icon-file"></i> Create a document

The document panel is accessible using the <i class="icon-folder-open"></i> button in the navigation bar. You can create a new document by clicking <i class="icon-file"></i> **New document** in the document panel.

#### <i class="icon-folder-open"></i> Switch to another document

All your local documents are listed in the document panel. You can switch from one to another by clicking a document in the list or you can toggle documents using <kbd>Ctrl+[</kbd> and <kbd>Ctrl+]</kbd>.

#### <i class="icon-pencil"></i> Rename a document

You can rename the current document by clicking the document title in the navigation bar.

#### <i class="icon-trash"></i> Delete a document

You can delete the current document by clicking <i class="icon-trash"></i> **Delete document** in the document panel.

#### <i class="icon-hdd"></i> Export a document

You can save the current document to a file by clicking <i class="icon-hdd"></i> **Export to disk** from the <i class="icon-provider-stackedit"></i> menu panel.

> **Tip:** Check out the [<i class="icon-upload"></i> Publish a document](#publish-a-document) section for a description of the different output formats.


----------


Synchronization
-------------------

StackEdit can be combined with <i class="icon-provider-gdrive"></i> **Google Drive** and <i class="icon-provider-dropbox"></i> **Dropbox** to have your documents saved in the *Cloud*. The synchronization mechanism takes care of uploading your modifications or downloading the latest version of your documents.

> **Note:**

> - Full access to **Google Drive** or **Dropbox** is required to be able to import any document in StackEdit. Permission restrictions can be configured in the settings.
> - Imported documents are downloaded in your browser and are not transmitted to a server.
> - If you experience problems saving your documents on Google Drive, check and optionally disable browser extensions, such as Disconnect.

#### <i class="icon-refresh"></i> Open a document

You can open a document from <i class="icon-provider-gdrive"></i> **Google Drive** or the <i class="icon-provider-dropbox"></i> **Dropbox** by opening the <i class="icon-refresh"></i> **Synchronize** sub-menu and by clicking **Open from...**. Once opened, any modification in your document will be automatically synchronized with the file in your **Google Drive** / **Dropbox** account.

#### <i class="icon-refresh"></i> Save a document

You can save any document by opening the <i class="icon-refresh"></i> **Synchronize** sub-menu and by clicking **Save on...**. Even if your document is already synchronized with **Google Drive** or **Dropbox**, you can export it to a another location. StackEdit can synchronize one document with multiple locations and accounts.

#### <i class="icon-refresh"></i> Synchronize a document

Once your document is linked to a <i class="icon-provider-gdrive"></i> **Google Drive** or a <i class="icon-provider-dropbox"></i> **Dropbox** file, StackEdit will periodically (every 3 minutes) synchronize it by downloading/uploading any modification. A merge will be performed if necessary and conflicts will be detected.

If you just have modified your document and you want to force the synchronization, click the <i class="icon-refresh"></i> button in the navigation bar.

> **Note:** The <i class="icon-refresh"></i> button is disabled when you have no document to synchronize.

#### <i class="icon-refresh"></i> Manage document synchronization

Since one document can be synchronized with multiple locations, you can list and manage synchronized locations by clicking <i class="icon-refresh"></i> **Manage synchronization** in the <i class="icon-refresh"></i> **Synchronize** sub-menu. This will let you remove synchronization locations that are associated to your document.

> **Note:** If you delete the file from **Google Drive** or from **Dropbox**, the document will no longer be synchronized with that location.

----------


Publication
-------------

Once you are happy with your document, you can publish it on different websites directly from StackEdit. As for now, StackEdit can publish on **Blogger**, **Dropbox**, **Gist**, **GitHub**, **Google Drive**, **Tumblr**, **WordPress** and on any SSH server.

#### <i class="icon-upload"></i> Publish a document

You can publish your document by opening the <i class="icon-upload"></i> **Publish** sub-menu and by choosing a website. In the dialog box, you can choose the publication format:

- Markdown, to publish the Markdown text on a website that can interpret it (**GitHub** for instance),
- HTML, to publish the document converted into HTML (on a blog for example),
- Template, to have a full control of the output.

> **Note:** The default template is a simple webpage wrapping your document in HTML format. You can customize it in the **Advanced** tab of the <i class="icon-cog"></i> **Settings** dialog.

#### <i class="icon-upload"></i> Update a publication

After publishing, StackEdit will keep your document linked to that publication which makes it easy for you to update it. Once you have modified your document and you want to update your publication, click on the <i class="icon-upload"></i> button in the navigation bar.

> **Note:** The <i class="icon-upload"></i> button is disabled when your document has not been published yet.

#### <i class="icon-upload"></i> Manage document publication

Since one document can be published on multiple locations, you can list and manage publish locations by clicking <i class="icon-upload"></i> **Manage publication** in the <i class="icon-provider-stackedit"></i> menu panel. This will let you remove publication locations that are associated to your document.

> **Note:** If the file has been removed from the website or the blog, the document will no longer be published on that location.

----------


Markdown Extra
--------------------

StackEdit supports **Markdown Extra**, which extends **Markdown** syntax with some nice features.

> **Tip:** You can disable any **Markdown Extra** feature in the **Extensions** tab of the <i class="icon-cog"></i> **Settings** dialog.

> **Note:** You can find more information about **Markdown** syntax [here][2] and **Markdown Extra** extension [here][3].


### Tables

**Markdown Extra** has a special syntax for tables:

Item     | Value
-------- | ---
Computer | $1600
Phone    | $12
Pipe     | $1

You can specify column alignment with one or two colons:

| Item     | Value | Qty   |
| :------- | ----: | :---: |
| Computer | $1600 |  5    |
| Phone    | $12   |  12   |
| Pipe     | $1    |  234  |


### Definition Lists

**Markdown Extra** has a special syntax for definition lists too:

Term 1
Term 2
:   Definition A
:   Definition B

Term 3

:   Definition C

:   Definition D

	> part of definition D


### Fenced code blocks

GitHub's fenced code blocks are also supported with **Highlight.js** syntax highlighting:

```
// Foo
var bar = 0;
```

> **Tip:** To use **Prettify** instead of **Highlight.js**, just configure the **Markdown Extra** extension in the <i class="icon-cog"></i> **Settings** dialog.

> **Note:** You can find more information:

> - about **Prettify** syntax highlighting [here][5],
> - about **Highlight.js** syntax highlighting [here][6].


### Footnotes

You can create footnotes like this[^footnote].

  [^footnote]: Here is the *text* of the **footnote**.


### SmartyPants

SmartyPants converts ASCII punctuation characters into "smart" typographic punctuation HTML entities. For example:

|                  | ASCII                        | HTML              |
 ----------------- | ---------------------------- | ------------------
| Single backticks | `'Isn't this fun?'`            | 'Isn't this fun?' |
| Quotes           | `"Isn't this fun?"`            | "Isn't this fun?" |
| Dashes           | `-- is en-dash, --- is em-dash` | -- is en-dash, --- is em-dash |


### Table of contents

You can insert a table of contents using the marker `[TOC]`:

[TOC]


### MathJax

You can render *LaTeX* mathematical expressions using **MathJax**, as on [math.stackexchange.com][1]:

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> **Tip:** To make sure mathematical expressions are rendered properly on your website, include **MathJax** into your template:

```
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML"></script>
```

> **Note:** You can find more information about **LaTeX** mathematical expressions [here][4].


### UML diagrams

You can also render sequence diagrams like this:

```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

And flow charts like this:

```flow
st=>start: Start
e=>end
op=>operation: My Operation
cond=>condition: Yes or No?

st->op->cond
cond(yes)->e
cond(no)->op
```

> **Note:** You can find more information:

> - about **Sequence diagrams** syntax [here][7],
> - about **Flow charts** syntax [here][8].

### Support StackEdit

[![](https://cdn.monetizejs.com/resources/button-32.png)](https://monetizejs.com/authorize?client_id=ESTHdCYOi18iLhhO&summary=true)

  [^stackedit]: [StackEdit](https://stackedit.io/) is a full-featured, open-source Markdown editor based on PageDown, the Markdown library used by Stack Overflow and the other Stack Exchange sites.


  [1]: http://math.stackexchange.com/
  [2]: http://daringfireball.net/projects/markdown/syntax "Markdown"
  [3]: https://github.com/jmcmanus/pagedown-extra "Pagedown Extra"
  [4]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
  [5]: https://code.google.com/p/google-code-prettify/
  [6]: http://highlightjs.org/
  [7]: http://bramp.github.io/js-sequence-diagrams/
  [8]: http://adrai.github.io/flowchart.js/
