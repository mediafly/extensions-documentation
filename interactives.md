Overview
========

Customers often wish to create rich, engaging content that include advanced UI effects, form entry, dynamic content, and more features that extend well beyond the limitations of PowerPoint or PDFs. With Mediafly Interactives and the Mediafly Platform, customers can create, release and control these packages.  This document serves as documentation for how a customer can create an Interactive and what best practices exist in their creation and maintenance.


----------


## What are Interactives? ##

Interactives are all the components that make up a static website:

* HTML files
* JavaScript
* CSS
* Images

All of these files are zipped up in a specific way and uploaded to Mediafly Airship.

When the user opens the Interactive, the app loads and renders the Interactive much like it would a traditional web page.
![image](http://devdocs.mediafly.com/interactives/images/Interactives%20Overview.png)

To ensure that the apps work consistently and correctly, the Interactive builder needs to follow a few rules:

* The starting page must be named index.html .  This is the common starting point from which any package can launch.  Sub-pages are acceptable, as long as they are navigable from index.html.
* Apps that reference third-party libraries (e.g. jQuery, jQuery Mobile, Reveal.js, Angular.JS) should relatively reference those files from the pages.  To be able to function while offline (for iOS and Android) no files should be absolutely referenced to a site on the web.  For example, an index.html file that contains the following is acceptable, because the files are relatively referenced:

		<script src="js/jquery.min.js"></script>
		<link rel="stylesheet" href="css/main.css">

    and an index.html file containing the following is not acceptable for offline use, because the files are absolutely referenced.

		<script src="http://code.jquery.com/jquery-1.8.2.min.js"></script>
		<link rel="stylesheet" href="http://mediafly.com/css/main.css">


----------


## Communication between the App and Interactive ##

Interactives are full-screen, engaging documents. They allow for deep customization of the entire look and feel of the application. Because the apps make use of the entire screen, Mediafly’s apps need to be told how to render control bars and when to close.  The typical approach to this is:

1. The Interactive will provide a Menu or X button in the app.
	![Interactive showing Menu button](http://devdocs.mediafly.com/interactives/images/Interactives showing Menu button.png)

2. When the user taps/clicks on the Menu button, the app toggles the app’s control bars.  The control bars are a part of the app.
	![Interactive showing control bars](http://devdocs.mediafly.com/interactives/images/Interactives showing control bar.png)

3. When the user taps/clicks on the X button, the app closes the item.

Interactives can link to other items within the hierarchy.  For example, if a user has access to one Interactive and three other items (video, audio, documents, presentations, even other Interactives), the Interactive can link directly to those items.  This is useful for

* Providing a seamless transition between Interactives and other rich media such as video
* Delegating Interactive creation to multiple people. In this case, one creator may work on the wrapper while another works on a sub-page or presentation.

	![Interactives can link to other items](http://devdocs.mediafly.com/interactives/images/Interactives link to video.png)

Interactives can invoke Search and Collections as well.


----------

Building Interactives
=====================

## Developing on the web
Developing Interactives can be painful when you need to start integrating the mfly API, as each change requires you push to Airship and test on device. Mediafly built and maintains mflyTestRunner.js, a JavaScript file that emulates portions of what the device applications do. Specifically:

* It simulates the calls that the apps make to initialize the Interactive
* It can feed test hierarchy in via mflyInit, much like the app would
* It can be configured with different options, all laid out within the first dozen lines of the file

You can find mflyTestRunner.js within the [Mediafly Interactives Tools and Examples](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples) public repository.

To use:

* Include mflyTestRunner.js script, AFTER mflyCommands.js (if you are also using that):

		<script src="js/mflyTestRunner.js"></script>

* Change the file to test various features. We suggest you adjust, at least, mflyDataInit and mflyInit to pass in your startup information and hierarchy.
* Run a basic web server from within the root folder of your Interactive. If you have Python, SimpleHTTPServer is a good option:

		python -m SimpleHTTPServer

* Remove the ```<script>``` link to the file in production. Note: if you use the [Interactives Publisher](https://bitbucket.org/mediafly/mediafly-interactivespublisher), this will be taken care of automatically for you.


## Packaging and Deploying ##

There are three methods to package and deploy Interactives. From easiest+least efficient to "hardest"/most efficient:


<a name="packaging_interactives_1"></a>
### 1. Windows Explorer/Finder

A. Zip the contents of the containing folder, not the folder itself. For example:

![Do not zip from here](http://devdocs.mediafly.com/interactives/images/packaging/1. Do not zip from here.png) Do not zip from here...

![Zip from here](http://devdocs.mediafly.com/interactives/images/packaging/2. Zip from here.png) ... Zip from here

B. Upload the zip file you created into the Airship content management system:

![image](http://devdocs.mediafly.com/interactives/images/packaging/3. Drag into Airship.png)

C. (Or, if you are replacing an existing existing item, upload it into the Media panel)

![Replace media from the Media panel](http://devdocs.mediafly.com/interactives/images/packaging/3b. Replace item from Media panel.png)

D. Set your Interactive's item permissions

![image](http://devdocs.mediafly.com/interactives/images/packaging/4. Set permissions.png)

E. Load it up on the device (iOS, Android, Windows 8, etc.) and test



<a name="packaging_interactives_2"></a>
### 2. Shell command

This method is a slight improvement to [#1](#packaging_interactives_1) above, and is good if you are using a Mac or Linux
system and are familiar with using Terminal / Shell.

A. Open a Terminal window/Shell into the root of your Interactive
B. Run the following command:

        rm file.zip; zip -9 -r file.zip . -x *.png *.gif *.jpg; zip -0 -r file.zip . -i *.png *.gif *.jpg

C. Follow steps B through E from [#1](#packaging_interactives_1) above.


<a name="packaging_interactives_3"></a>
### 3. Mediafly Interactives Publisher

If you plan to perform significant development on an Interactive, and are running on a shell that
supports Python, this is the path for you.

Use the [Mediafly Interactives Publisher](https://bitbucket.org/mediafly/mediafly-interactivespublisher), a command-line, python-based script that will very quickly:

* Allow you to preconfigure needed variables to quickly publish revisions to your Interactive via a JSON document and environment variables
* Optimally package the Interactive for you
* Push the package up into the Airship content management system **very quickly**

To learn more, read the [publish.py comments](https://bitbucket.org/mediafly/mediafly-interactivespublisher/src/tip/publish.py?at=default).


## Testing on device

### weinre
We strongly recommend the use of [weinre](http://people.apache.org/~pmuellr/weinre-docs/latest/), the WEb INspector REmote. It is a debugger for remote web pages, and in particular, it <i>allows you to debug web pages on mobile devices with your laptop</i>.

#### How to use it
Please read the [installation](http://people.apache.org/~pmuellr/weinre-docs/latest/Installing.html) and [run](http://people.apache.org/~pmuellr/weinre-docs/latest/Running.html) instructions on the website for details. But here is a simple overview:

1. Install the tool via npm, (e.g. npm -g install weinre)
2. Run the debug server locally (e.g. weinre —boundHost 10.0.0.111)
3. Update the Interactive with a ```<script>``` tag to point to the debug server, and publish the new Interactive in Airship (e.g. ```<script src="http://10.0.0.111:8080/target/target-script-min.js"></script>```). Open the Interactive on your app
4. Point your PC/Mac browser to the debug client (e.g. http://10.0.0.111:8080)

You now have access to a relatively full featured web development tool for your Interactives!

#### Let's see it in action
We completed steps 1 and 2, and instrumented one of our apps, Kitchen Sink, with the ```<script>``` tag from 3. We open the Interactive, and see that it looks the same as without the ```<script>``` tag:

![Kitchen Sink on the device. Looks the same as without the new tag.](http://devdocs.mediafly.com/interactives/images/weinre/1. Kitchen Sink.png)

We now point our PC/Mac browser to http://10.0.0.111:8080/client, and click on the "debug client user interface: http://10.0.0.111:8080/client/#anonymous" link. We see a window that looks like this:

![Weinre debug client home](http://devdocs.mediafly.com/interactives/images/weinre/2. Debug Client Home.png)

We can tap on other tabs and see the tools familiar to web developers building and testing with major browsers. E.g. here is a view from the Elements tab.

![Weinre debug client Elements](http://devdocs.mediafly.com/interactives/images/weinre/3. Debug Client Elements.png)

The Elements, Resources, Network, Timeline, and Console tabs all perform as you would expect from their Chrome/Firefox/IE counterparts.

We've tested across all of our device platforms (iOS, Android, Windows 8), and each performs well.


----------

## Examples and Resources

### Examples

Mediafly creates and maintains a detailed list of examples. These examples illustrate many of the features of Interactives and can serve as QA for client app developers.

Please see the [Mediafly Interactives Tools and Examples](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples) BitBucket repository for more information.

To see the examples in action on your device:

1. Download Whitebox for [iOS](https://itunes.apple.com/us/app/whitebox/id399107683?mt=8), [Android](https://play.google.com/store/apps/details?id=com.mediafly.android.video.onmediafly&hl=en) or [Windows 8](http://apps.microsoft.com/windows/en-us/app/39a96812-56fa-4583-82eb-4d787fda0d4c)
2. When asked for Company Code or Company Login Name, enter "interactives"
3. Each example will be contained within a folder that describes the example

### Google Group
Please [join our Google Group](https://groups.google.com/forum/?hl=en#!forum/mediafly-interactives) to keep up with the latest information, udates and news. As well, learn from and get help from other Interactives builders and customers.

### Changelog
Please see the [repository](https://bitbucket.org/mediafly/mediafly-interactives-documentation/commits/all) of this document for a changelog.

### Feedback
See an area of confusion? Please [contact us](mailto:support@mediafly.com?Subject=Interactives%20Documentation%20Feedback).

----------


API and Howtos
===============

This section describes all of the APIs available to Interactives developers. For each call, we list the mflyCommands.js call as well as the base URL. Please use the mflyCommands.js call where possible.

*A note on 'Availability':* You will see many API calls have no version numbers within the Availability column. In these cases, the calls have been in existence for a sufficiently long period of time that Interactives developers no longer need to worry about whether their clients' apps support those calls anymore.

*Keeping mflyCommands up-to-date:* mflyCommands.js is available on the [bower package manager](http://bower.io/). This is the recommended way of consuming it as a dependency in your interactive. The bower package can be found here: [http://bower.io/search/?q=mfly-commands](http://bower.io/search/?q=mfly-commands)

## Controlling the app
Controlling the app is done by opening a window to a special URL through JavaScript. The container app will handle this as an instruction to handle.  The list of available URLs, and their appropriate actions, are listed below.

***PLEASE NOTE***: Every Interactive must allow the user to either close the item or show the control bars. Else, the user will be stuck within the Interactive, with no way to exit.

### Getting baseline information
*mflyCommands.js:* mflyCommands.getInteractiveInfo() <br>
*URL:* mfly://data/interactive <br>
*Description:* Obtains baseline information about this Interactive. Interactives can call this function and receive a JSON Object of initial configuration.  E.g. on iOS, the object params may look like this:

		{
		  	"user": "jshah@mediafly.com",
			"displayName": "John Doe",
			"item": "Custom Dynamic Interactive 1.2",
			“id”: “29342938241238product234234”,
			"osType": "iOS",
			"osVersion": "6.1.2",
			"appVersion": "6.1.4.405",
			"deviceId": "4639ufu115f0630a13a8744e8b5cc6309b46",
			"lastUpdated": "2014-02-03T07:04:07-06:00"
		}

*Parameters:*<br>

* user: The system's username, if any
* displayName: display name. Can be email, first+last, or something similar. May be blank in the case of content libraries that don't require logging in
* item: title of this Interactive
* id: the slug (unique id) of this Interactive within Airship
* osType: which OS (iOS, Android, Windows 8)
* osVersion: which version of the OS
* appVersion: which version of the app
* deviceId: the unique device ID for this device
* lastUpdated: the lasted updated time. This is good for debugging purposes, to help you identify which version of the Interactive you are looking at on the device

Note that not every parameter is available on every platform.

*Availability:* mflyCommands.js (1.4.4), iOS (588), Android (2.23.05), Windows 8 (soon), Web Viewer (1-Apr-2015)


### Basic controls

#### Close the item
*mflyCommands.js:* mflyCommands.close() <br>
*URL:* mfly://control/done <br>
*Description:* Closes the item and returns the user to where they were before. <br>
*Availability:* iOS, Android, Windows 8, Web Viewer

#### Show control bars
*mflyCommands.js:* mflyCommands.showControlBars() <br>
*URL:* mfly://control/showControlBars <br>
*Description:* Shows the control bars. <br>
*Availability:* iOS, Android


#### Hide control bars
*mflyCommands.js:* mflyCommands.hideControlBars() <br>
*URL:* mfly://control/hideControlBars <br>
*Description:* Hides the control bars. <br>
*Availability:* iOS, Android

#### Browse
*mflyCommands.js:* mflyCommands.browse() <br>
*URL:* mfly://control/browse <br>
*Description:* Multiple Interactives can layer on top of each other, to represent different layers of hierarchy. The developer may wish to allow the user to navigate the hierarchy using the core Mediafly app with the default grid/list view, e.g. if a “More” or “Browse” button is presented.  This URL closes all Interactives in the stack and takes the user to that hierarchy in the core app if called. <br>
*Availability:* iOS

#### Next
*mflyCommands.js:* mflyCommands.next() <br>
*URL:* mfly://control/next <br>
*Description:* Opens the next Item in the Folder or Collection <br>
*Availability:* iOS, Android, Windows 8

#### Previous
*mflyCommands.js:* mflyCommands.previous() <br>
*URL:* mfly://control/previous <br>
*Description:* Opens the previous Item in the Folder or Collection <br>
*Availability:* iOS, Android, Windows 8

#### Get information about a folder or item
*mflyCommands.js:* mflyCommands.getItem(_id_) <br>
*URL:* mfly://data/item/[id] <br>
*Description:* Return a JSON representation of a folder or item. To get the contents of the folder, rely on the mflyCommands.getFolder(_id_) <br>
*Examples:*

	Example 1:
		GET mfly://data/item/slug1
		Result:
        {
            "id": "slug1",
            "url": "mfly://item/slug1",
            "type": "video",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "true",
            “keywords”: [“keyword4”],
			"hierarchy": [
			{
				"id": "__root__",
				"name": "Root folder"
			},
			{
				"id": "9cf282320e6340ee8b830e5376d54531product91921",
				"name": "Parent Folder"
			}]
        }


    Example 2:
		GET mfly://data/item/slug2
		Result:
        {
            "id": "slug2",
            "url": "mfly://item/slug2",
            "type": "folder",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "false",
            “keywords”: [“keyword1”, “keyword2”, “keyword3”],
            “new”: 1,
			"hierarchy": [
			{
				"id": "__root__",
				"name": "Root folder"
			}]
        }
*Availability:* iOS, Android, Windows 8, Web Viewer


#### Get contents of a folder
*mflyCommands.js:* mflyCommands.getFolder(_id_) <br>
*URL:* mfly://data/folder/[id] <br>
*Description:* Return a JSON representation of the _contents_ of a folder. To get the contents of the top-level folder, use id of \__root__.

	Example:
		GET mfly://data/folder/slug2
		Result:
		[
          {
            "id": "slug3",
            "url": "mfly://item/slug3",
            "type": "video",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "true",
            “keywords”: [“keyword4”],
			"hierarchy": [
			{
				"id": "__root__",
				"name": "Root folder"
			},
			{
				"id": "9cf282320e6340ee8b830e5376d54531product91921",
				"name": "Parent Folder"
			}]
          },
          {
            "id": "slug4",
            “items”: [“slug5”],
            "url": "mfly://folder/slug4",
            "type": "folder",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "false",
			"hierarchy": [
			{
				"id": "__root__",
				"name": "Root folder"
			},
			{
				"id": "9cf282320e6340ee8b830e5376d54531product91921",
				"name": "Parent Folder"
			}]
          }
        ]

*Availability:* iOS, Android, Windows 8, Web Viewer


### App features

#### Show Settings
*mflyCommands.js:* mflyCommands.showSettings(_x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showSettings?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the settings dialog at x-coord, y-coord coordinates with the specified width and height. <br>
*Availability:* iOS, Android


#### Show User Management Dialog
*mflyCommands.js:* mflyCommands.showUserManagement(_x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showUserManagement&x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the User Management dialog, which allows the user to login or manage users. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS.<br>
*Availability:* iOS, Android


#### Show Second Screen Options dialog
*mflyCommands.js:* mflyCommands.showSecondScreenOptions() <br>
*URL:* mfly://control/secondScreenOptions <br>
*Description:* Shows the Second Screen Options dialog. <br>
*Availability:* iOS


#### Email
*mflyCommands.js:* mflyCommands.email(_id_) <br>
*URL:* mfly://control/email/[id] <br>
*Description:* If the item can be emailed, invoke email from within the app. <br>
*Availability:* iOS, Android


#### Refresh the contents of the app
*mflyCommands.js:* mflyCommands.refresh() <br>
*URL:* mfly://control/refresh <br>
*Description:* Trigger the app to refresh its content. If items have changed, this should trigger mflySync calls appropriately. <br>
*Availability:* iOS, Android

#### Show Annotations
*mflyCommands.js:* mflyCommands.showAnnotations() <br>
*URL:* mfly://control/showAnnotations <br>
*Description:* If annotations are enabled, show the annotations control bar. <br>
*Availability:* iOS


#### Take and email Screenshot
*mflyCommands.js:* mflyCommands.takeAndEmailScreenshot() <br>
*URL:* mfly://control/takeAndEmailScreenshot <br>
*Description:* App will take a screenshot and populate the email with that screenshot. <br>
*Availability:* iOS



### Links (Open and Goto)

Two types of links exist within Interactives: Open and Goto. When an Interactive "opens" an item, the item appears on top of the navigational stack, and the user is only given a single option to escape the item, by tapping Done/Close.  When an Interactive "gotos" an item, the app opens that item and throws away the navigational stack behind it. The user is given the same navigational capabilities as if they opened the destination item directly.

To create either link, simply create a typical `<a href>` link with the source pointing to an mfly URL as specified below.

#### Open an item
*mflyCommands.js:* mflyCommands.openItem(_id_) <br>
*URL:* mfly://item/[id] <br>
*Description:* Opens the specified item, where [id] is the ID of the item.  As described above, the historical navigational stack is maintained, and the user can return to it. The ID for an item can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android, Windows 8, Web Viewer

#### Open a folder
*mflyCommands.js:* mflyCommands.openFolder(_id_) <br>
*URL:* mfly://folder/[id] <br>
*Description:* Opens the specified folder, where [id] is the ID of the folder.  As described above, the historical navigational stack is maintained, and the user can return to it. The ID for a folder can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android, Windows 8, Web Viewer

#### Goto an item or a folder
*mflyCommands.js:* mflyCommands.goto(_id_) <br>
*URL:* mfly://control/goto/[id] <br>
*Description:* Gotos the specified item or folder, where [id] is the ID of the item or folder.  As described above, when the app goes to another item or folder, the navigational stack behind the destination is thrown away, and the user is given the same navigational capabilities as if they opened the destination item directly. The ID for a folder can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android


### Filter
We often find that developers need to identify folders and items that match a specific set of metadata. Often they want to use custom metadata fields as a way to drive hierarchy and categorization, and not rely on the actual hierarchy for that purpose. It makes sense in some use cases, because the people who will be managing content are often different from how the Interactive should be rendered.

To support this, use Filter. Filter accepts up to three key=value pairs as JSON parameters, and returns a constrained list of folders and items that match ALL of the key=value pairs provided. The return value is a JSON Array of JSON Objects that match the various folders and items with the specified filter conditions, or an empty JSON Array if none match.

*mflyCommands.js:* mflyCommands.filter(_obj_) <br>
*URL:* mfly://data/filter?[key1=value1][&keyN=valueN]<br>
Examples:

	Example 1:

		mflyCommands.filter({ "shouldShow": false }).done(function(results) {
			// results contains, e.g.
			// [ { "id": "123", "shouldShow": true, ... },
			//   { "id": "234", "shouldShow": false, ... } ]
		});

	Example 2:

		mflyCommands.filter({ "shouldShow": false, "key": "doesNotExist" }).done(function(results) {
			// results contains, e.g.
			// []
		});

*Availability:* iOS (590), Android (soon), Windows 8 (2.0.0.52), Web Viewer (15-Apr-2015). Requires mflyCommands.js 1.4.5+



### Search
Mediafly's apps provide real-time, native search as a core part of the functionality. There are two ways to work with Search:

1. Use the native app's core Search UI. In this case, you simply need to open the Search dialog and let the user use that dialog. See "Show Search dialog" below.
2. Implement your own UI. In this case, you use mflyCommands.search() or mfly:// calls to get search results given terms, and then render the UI as you wish. See "Search by keyword" below.

#### Show Search dialog
*mflyCommands.js:* mflyCommands.showSearch(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the search dialog at x-coord, y-coord coordinates with the specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*URL:* mfly://control/showSearch?x=[x-coord]&y=[y-coord]&w=[width]&h=[height]<br>
*Availability:* iOS

#### Search by keyword
*mflyCommands.js:* mflyCommands.search(_term_) <br>
*URL:* mfly://data/search?term=[term]<br>
*Description:* Conduct a search for the given keyword. Return value is a JSON-array of folders and items, similar in format to a call to mfly://folder/[id].<br>
*Availability:* iOS, Web Viewer


### Collections
Collections allow users to create a "personal playlist" of items. Oftentimes this is used as a way to organize items for, say, an upcoming meeting with a customer. Collections are pointers to items, not copies of items; updating the original item in Airship will update the item in the Collection as well.


#### Show "Collections"
*mflyCommands.js:* mflyCommands.showCollections(_x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showCollections?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Collections dialog at x-coord, y-coord coordinates with specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS.<br>
*Availability:* iOS, Android (2.22.91)


#### Show "Add to Collection"
*mflyCommands.js:* mflyCommands.showAddToCollection(itemId, _x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showAddToCollection?id=[itemId]&x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Add To Collections dialog at x-coord, y-coord coordinates with specified width and height for the specified item. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS


#### Get the list of Collections
*mflyCommands.js:* mflyCommands.getCollections() <br>
*URL:* mfly://data/collections <br>
*Description:* Return a JSON representation of the list of Collections

	Example:
		GET mfly://data/collections
		Result:
		[
          {
            "id": "collection1",
            "name": "Collection 1"
          },
          {
            "id": "collection2",
            "name": "Collection 2"
          }
        ]

*Availability:* iOS (483), Android (2.22.91). Requires mflyCommands.js 1.2+


#### Get the contents of a Collection
*mflyCommands.js:* mflyCommands.getCollection(_collection ID_) <br>
*URL:* mfly://data/collection/[collection ID] <br>
*Description:* Return a JSON representation of the contents of a collection, in a similar format to Get Folder.

	Example:
		mflyCommands.getCollection("collection1");
		Result:
		[
          {
            "id": "slug1",
            "url": "mfly://item/slug1",
            "type": "video",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "true",
            “keywords”: [“keyword4”],
			"hierarchy": [
	        {
	            "id": "__root__",
	            "name": "Root folder"
	        },
	        {
	            "id": "9cf282320e6340ee8b830e5376d54531product91921",
	            "name": "Parent Folder"
	        }]
          },
          {
            "id": "slug2",
            "url": "mfly://folder/slug3",
            "type": "pdf",
            "name": "Name",
            "description": "Description",
            "date": "2010-11-12T06:15:15:00-06:00",
            "received": "2012-12-19T16:45:14:00-06:00",
            "thumbnailUrl": "mfly://image/123",
            "launched": "false",
			"hierarchy": [
	        {
	            "id": "__root__",
	            "name": "Root folder"
	        },
	        {
	            "id": "9cf282320e6340ee8b830e5376d54531product91921",
	            "name": "Parent Folder"
	        }]
          }
        ]

*Availability:* iOS (483), Android (2.22.91), Requires mflyCommands.js 1.2+

#### Create a Collection
*mflyCommands.js:* mflyCommands.createCollection(_collection name_) <br>
*URL:* mfly://data/createCollection?name=[name] <br>
*Description:* Creates the collection, and returns a success or failure error code.

	Example:
		mflyCommands.createCollection("Collection 2");
		Result:
		{
			"id": "collection2id",
			"name": "Collection 2"
		}

*Availability:* iOS (570), Android (2.22.91), Requires mflyCommands.js 1.3.5+


#### Add item to a Collection
*mflyCommands.js:* mflyCommands.addItemToCollection(_collection ID_, _item ID_) <br>
*URL:* mfly://data/addItemToCollection?id=[collection ID]&item=[item ID] <br>
*Description:* Adds the item to the Collection. If Collection exists and it can be created, response code is 200. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }. If item does not exist, response code is 404 with body "Item not found."

	Example:
		mflyCommands.addItemToCollection("collection2id", "item4id");

*Availability:* iOS (570), Android (2.22.91), Requires mflyCommands.js 1.3.5+

#### Remove item from a Collection
*mflyCommands.js:* mflyCommands.removeItemFromCollection(_collection ID_, _item ID_) <br>
*URL:* mfly://data/removeItemFromCollection?id=[collection ID]&item=[item ID] <br>
*Description:* Removes the item from the Collection. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }. If item does not exist, response code is 404 with body "Item not found."

	Example:
		mflyCommands.removeItemFromCollection("collection2id", "item4id");

*Availability:* iOS (623), Android (2.25.15), web viewer (10/05/2015) Requires mflyCommands.js 1.9.0+


### Downloader
Mediafly's apps have been optimized for very advanced synchronization and download use cases. We offer a set of Interactives calls to obtain information about downloads and control the app.

#### Show Downloader
*mflyCommands.js:* mflyCommands.showDownloader(_x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showDownloader?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Downloader dialog at x-coord, y-coord coordinates with specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS, Android

#### Get total download progress
*mflyCommands.js:* mflyCommands.getDownloadStatus() <br>
*URL:* mfly://data/download/status <br>
*Description:* Get total download progress, as a range between 0 (no downloads started) and 1 (all downloads complete), number of failed downloads, and number of downloads for whom storage has been exceeded. <br>

	Example:
		GET mfly://data/download/status
		Result:
        {
          "progress":0.12,
          "fails":1,
          "storageExceeded": 3
        }

*Availability:* iOS (586+ for storageExceeded), Android (2.22.98+)

#### Get download progress for a single item
*mflyCommands.js:* mflyCommands.getDownloadStatus(_id_) <br>
*URL:* mfly://data/download/status/[id] <br>
*Description:* Get download status for a single item. The attribute may be "progress" (download is progressing normally), "error" (download is in an error state), "cellularExceeded" (download won't proceed because the cellular limit set for the app has been exceeded; iOS only), or "storageExceeded" (download won't proceed because there is no more available storage on this device). <br>

	Example:
		GET mfly://data/download/status/[id]
		Result:
        { "progress": 0.34 }

	Example:
		GET mfly://data/download/status/[id]
		Result:
        { "error": 0.34 }

	Example:
		GET mfly://data/download/status/[id]
		Result:
        { "cellularExceeded": 0.34 }

	Example:
		GET mfly://data/download/status/[id]
		Result:
        { "storageExceeded": true }

*Availability:* iOS (586+ for storageExceeded), Android (2.22.98+)



#### Add to Downloader
*mflyCommands.js:* mflyCommands.addToDownloader(_id_) <br>
*URL:* mfly://data/addToDownloader/[id]<br>
*Description:* Instruct the app to add an item to the Downloader. Response code 200 = item was added to the Downloader. Response code 404 if id is not found.<br>
*Availability:* iOS, Android, Windows 8


#### Remove from Downloader
*mflyCommands.js:* mflyCommands.removeFromDownloader(_id_) <br>
*URL:* mfly://data/removeFromDownloader/[id]<br>
*Description:* Instruct the app to remove the item from the Downloader. Response code 200 = item was removed from the Downloader. Response code 404 if id is not found.<br>
*Availability:* iOS (575), Android (2.22.76), Windows 8 (2.0.0.50)


#### mflyDownloadStatus
Interactives can listen for changes to total download status with mflyDownloadStatus. This function is called approximately once per second while a download is being conducted. The Interactive simply needs to implement this function and take some (short-running) action. The parameter is a JSON object that indicates progress percentage as a decimal from 0 to 1, fail count as an integer, and number of items for whom storage has been exceeded.

	Example:
	    function mflyDownloadStatus(obj) {
			// obj = { 'progress': 0.12, 'fails': 1, 'storageExceeded': 3 }
			// Handle download status object
		}

*Availability:* iOS (586+ for storageExceeded), Android (2.22.98+)


### Notifications

When notifications are enabled for an app, users can subscribe or unsubscribe to/from emails on any folder. When new content appears in the folder, the system will email the user either instantly or at the end of the day in a digest of the availability of the new content. Interactives can control notifications with the following commands.

#### Get notification status for a folder
*mflyCommands.js:* mflyCommands.getNotificationStatus(_id_) <br>
*URL:* mfly://data/getNotificationStatus/[id]<br>
*Description:* Get the notification status for a folder. If the folder is valid, response code is 200 and body is a JSON object similar to this:<br>

	Example:
		{
			"notification":"[status]" // [status] is email|none
		}

*Availability:* iOS

#### Add a folder to notifications
*mflyCommands.js:* mflyCommands.addNotification(_id_) <br>
*URL:* mfly://data/addNotification/[id]<br>
*Description:* Instruct the app to subscribe the folder for notifications. Response code 200 if successful, 304 if folder was already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS

#### Remove a folder from notifications
*mflyCommands.js:* mflyCommands.removeNotification(_id_) <br>
*URL:* mfly://data/removeNotification/[id]<br>
*Description:* Instruct the app to unsubscribe the folder from notifications. Response code 200 if successful, 304 if folder was not already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS

#### Show the notifications manager
*mflyCommands.js:* mflyCommands.showNotificationsManager(_x-coord, y-coord, width, height_) <br>
*URL:* mfly://control/showNotificationsManager<br>
*Description:* Display the notifications manager. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS

#### Listen for when notifications are changed
Interactives can listen for when notification status changes in the app by implementing mflyNotificationsChanged. The Interactive simply needs to implement this function and take some (short-running) action. The parameter is a JSON object that passes in the state of all notifications in the app.

	Example:
	    function mfyNotificationsChanged(notifications) {
		}

where notifications = JSON Object of all notifications, where key=id and value=JSON Object of new notification. E.g. notifications may be something like:


	notifications = {
    	"id1": { "notification": "email" },
	    "id2": { "notification": "none" }
	}

*Availability:* iOS



### GPS

#### Request latitude/longitude ####
*mflyCommands.js:* mflyCommands.getGpsCoordinates() <br>
*URL:* mfly://data/gps<br>
*Description:* Interactives can request latitude and longitude from the app. This is useful for apps that connect to external services to report lat/lon, or use the Haversine (Big Earth) formula to calculate rough distances.<br>

	Example:
		GET mfly://data/gps
		Response:
		{
		  latitude: 41.851231,
		  longitude: -87.652345
		}
*Availability:* iOS, Android, Windows 8


### Get Sync Status

#### Get the current sync status ####
*mflyCommands.js:* mflyCommands.getSyncStatus() <br>
*URL:* mfly://data/getSyncStatus<br>
*event:* document.mflySyncStatus<br>
*Description:* Interactives can get the current sync status from the app. This information can be retrieved by calling the URL or listening to the event published by the app. Interactives can leverage information about content being snced and wait to load certain parts of the UI. Interactives can also display progress indicators and spinners based on the syncStatus.  <br>

	Example:
		GET mfly://data/getSyncStatus
		Response:
		{
		  complete: 26,
		  total: 28,
		  isrunning: true
		}
*Availability:* iOS


### Log out

#### Instruct the app to log out ####
*mflyCommands.js:* mflyCommands.logout() <br>
*URL:* mfly://data/logout<br>
*Description:* When an Interactive makes this call, the app will log the current user out by de-authenticating them. The calling Interactive will be closed and the app will leave the user bound (user can enter their PIN to log back in). The app will navigate to the log in screen. If the customer environment does not support PIN, you may receive a 500 error.<br>

*Availability:* iOS (613), Android (2.24.15), web viewer (8/20/2015). Requires mflyCommands.js 1.6.0+


### Get Online Status

*mflyCommands.js:* mflyCommands.getOnlineStatus() <br>
*URL:* mfly://data/onlineStatus<br>

*Description:* Check whether the device is online<br>
Example:

	GET mfly://data/onlineStatus
	Response:
	{
	  "status": "online"
	}
Respnose when the device is offline:

	GET mfly://data/onlineStatus
	{
	  "status": "offline"
	}

*Availability:* iOS (540), Android (2.21.62).


----------


## Being controlled by the app
Mediafly's apps send many kinds of events into the Interactive. These events can be listened to by the Interactive reactively, and the Interactive can then take action. These capabilities are only availble to the native apps, not to web Viewer.

There are two methods in which this happens:

1. The old way. If an appropriately named JavaScript function is defined, they will be called by the app at the appropriate time, and the Interactive can take action. If the function does not exist, the console log will show a "function not defined" error and the Interactive will continue execution.
2. The new way. Recently created features will make use of JavaScript events in the DOM's document object. This better matches how other JavaScript frameworks similar to Interactives are structured.

Each feature listed below indicates which method it uses.



### mflyDataInit

<b>PLEASE NOTE:</b> We plan to deprecate mflyDataInit in favor of mflyCommands.getInteractiveInfo in the near future. Please migrate to using mflyCommands.getInteractiveInfo where possible.

This function has two uses:

1. Receive configuration information from the app, and
2. Send configuration parameters to the app

#### Use 1: Receive configuration information from the app
Interactives can implement mflyDataInit, using method 1.

		function mflyDataInit(obj) {
			// obj = JSON object that contains initial configuration
		}

mflyDataInit will be called with a JSON object that contains initial configuration.  E.g. on iOS, params may be a JSON Object such as this:

		{
			"mflyInitVersionMaximum": "2",
		  	"user": "jshah@mediafly.com",
			"displayName": "Jason Shah",
			"item": "Custom Dynamic Interactive 1.2",
			“id”: “29342938241238product234234”,
			"osType": "iOS",
			"osVersion": "6.1.2",
			"appVersion": "6.1.4.405",
			"deviceId": "4639ufu115f0630a13a8744e8b5cc6309b46",
			"lastUpdated": "2014-02-03T07:04:07-06:00"
		}

*Supported parameters:*<br>

* mflyInitVersionMaximum: indicates how many possible values mflyInitVersion can have in the return value (see below).
* user: user_context (essentially, system-known username)
* displayName: display name. Can be email, first+last, or something similar
* item: title of this Interactive
* id: the slug (unique id) of this Interactive within Airship
* osType: which OS (iOS, Android, Windows 8)
* osVersion: which version of the OS
* appVersion: which version of the app
* deviceId: the unique device ID for this device
* lastUpdated: the lasted updated time. This is good for debugging purposes, to help you identify which version of the Interactive you are looking at on the device


#### Use 2: Send configuration parameters to the app
Optionally, Interactives can then return a JSON Object with specifics on how the app should behave.  E.g. on iOS, return value may be a JSON Object such as this:

		{
		  “mflyInitVersion”: “4”,
		  “mflyWideScreenSupport”: true
		}

*Supported parameters:*<br>

* mflyInitVersion: indicates which version of mflyInit should be called by the app. Note that mflyInitVersion is only available on iOS. Android and Windows 8 support only version 3 and 4 of mflyInit. If ignored, defaults to "4".
	* "2": default version reflected in this documentation
	* "3": On iOS, sharing status is represented by canShare, canAccessAssetOffline, and canDownloadAsset
	* "4": No longer sends down mflyInit. mflyInit is deprecated and causes undo burden when content libraries are large, and we plan to deprecate it in the future.

* mflyWideScreenSupport: if true, this makes the app aware that the Interactive can handle widescreen second screens (HDMI, Apple TV).  This is specifically for the case where the Interactive has been designed to be a presentation-worthy UI for second screens. Only available on iOS.

*Example:*<br>

	function mflyDataInit(obj) {
		// Do initialization
	    return '{ "mflyInitVersion" : "4" }';
	}

*Availability:* iOS, Android, Windows 8. Not all parameters will be available on Android or Windows 8.


### mflyResume
The app calls this function when the app opens and shows the Interactive.  If you need to start animation or take other action when the Interactive shows, this is the place to do it.

*Example:*<br>

	function mflyResume() {
		// Handle mflyResume
	}

*Availability:* iOS, Android


### mflyPause
The app calls this function when the user hides the Interactive. If you need to stop animation, submit a form, or take other action when the Interactive hides, this is the place to do it.

*Example:*<br>

	function mflyPause() {
		// Handle mflyPause
	}

*Availability:* iOS, Android


### Sync Status
Our apps are built to automatically sync all folders on launch and at regular intervals. Sometimes, the Interactive needs to know about the status of sync to ensure that sufficient content is loaded before rendering the Interactive. To receive information about the status of sync, apps can listen to events injected into the DOM's document object, and react to those events.

*Example:*<br>

    document.addEventListener("mflySyncStatus", function(d) {
        if (d && d.detail) {
        	// Handle Sync Status
            $("#syncEventHandlerResult").html(JSON.stringify(d.detail, null, 2));
        }
    }, false);

The detail attribute of the event object will be structure as JSON with 2-3 fields, like so:

    {
        complete: 26,
        total: 28,
        isrunning: true
    }

where the JSON object's attributes have the following meaning:

* complete: the number of folders that have been synced
* total: the total number of folders known to the app. Note that this may not be defined immediately on application start, and may change if there are dramatic changes of what is available to the user.
* isrunning: true|false, indicating whether the sync is currently running.

*Availability:* iOS (604), Android (2.23.59)




### mflyInit (DEPRECATED)

<b>PLEASE NOTE</b>: mflyInit is deprecated. We have discovered that, as content libraries become incredibly large, mflyInit consumes an excessive amount of memory. As a result, we strongly suggest using mfly://data/folder and mfly://data/item to selectively construct the hierarchy that is needed at that time.

The app calls this function when the app launches an Interactive.  The parameter is a dictionary of JSON objects that represent a flattened version of the full hierarchy of folders and items available to the user. The key of each entry is the item/folder slug. Each entry for folders contains a JSON Array called “items”, which contains an array of slugs that are children of this folder.

For example, if a user has the following hierarchy:

	  folder [id=slug1]
	    item [id=slug2]
	    folder [id=slug3]
	      item [id=slug4]

then the object may look like this:

	{
	  “version”: 2,
	  “slug1” : {
	    "id": "slug1",
	    “items”: [“slug2”, “slug3”],
	    "url": "mfly://folder/slug1",
	    "type": "folder",
	    "name": "Name",
	    "description": "Description",
	    "date": "2010-11-12T06:15:15:00-06:00",
	    "received": "2012-12-19T16:45:14:00-06:00",
	    "thumbnailUrl": "mfly://image/123",
	    "launched": "false",
	    “keywords”: [“keyword1”, “keyword2”, “keyword3”],
	    “new”: 1
	  },
	  “slug2” : {
	    "id": "slug2",
	    "url": "mfly://item/slug2",
	    "type": "video",
	    "name": "Name",
	    "description": "Description",
	    "date": "2010-11-12T06:15:15:00-06:00",
	    "received": "2012-12-19T16:45:14:00-06:00",
	    "thumbnailUrl": "mfly://image/123",
        "launched": "true",
        “keywords”: [“keyword4”],
        “downloadModel”: “DownloadableShareable”
      },
      “slug3” : {
        "id": "slug3",
        “items”: [“slug4”],
        "url": "mfly://folder/slug3",
        "type": "folder",
        "name": "Name",
        "description": "Description",
        "date": "2010-11-12T06:15:15:00-06:00",
        "received": "2012-12-19T16:45:14:00-06:00",
        "thumbnailUrl": "mfly://image/123",
        "launched": "false"
      },
      “slug4” : {
        "id": "slug4",
        "url": "mfly://item/slug4",
        "type": "interactive",
        "name": "Name",
        "description": "Description",
        "date": "2010-11-12T06:15:15:00-06:00",
        "received": "2012-12-19T16:45:14:00-06:00",
        "thumbnailUrl": "mfly://image/123",
        "launched": "true",
        “new”: 1,
        “downloadModel”: “DownloadableProtected”
      }
    }


This function and mflyResume exist in harmony.  mflyInit is called on launch of the Interactive. mflyResume is called on both launch and resume of the Interactive.

Expect mflyInit to be called many times for large hierarchies, as often as every 2-3 seconds. Consider processing the results of the mflyInit object in parallel. Imagine in the worst case an mflyInit object with 10,000 items, called every 2-3 seconds. If processing that object occurred synchronously, the Interactive would grind to a halt or crash with out of memory errors.

Parameter definitions:

* version: mflyInit uses this version to indicate what the structure of mflyInit looks like. See mflyDataInit for possible versions and what they mean.
* items: a list of slugs of the children of this item (if the item is a folder and has any children). This is used to construct the hierarchy
* type: can be one of “Audio”, “Video”, “image”, “pdf”, “zip”, “ibooks”, “html”, “youtube”, “keynote”, “png”, “jpeg”, “jpg”, “gif”, “tif”, “tiff”
* received: the UTC datetime for when the app received this version of the item.
* launched: whether the user has launched this item in the past.
* new: for folders, number of items within that folder that are defined as “new” based on client’s business rules. For items, if 1, that item is considered “new”. [iOS 425+]
* keywords: any keywords the user set, as a JSON array.
* downloadModel: indicates whether the app will allow you to email this item. If downloadModel=DownloadableShareable, this item can be emailed. All other values indicate that the item cannot be emailed. This is visible if mflyDataInit.mflyInitVersion is set to 2.
* canShare: indicates whether the app will allow you to email this item. This is available if mflyDataInit.mflyInitVersion >= 3.
* canAccessAssetOffline: indicates whether the item is allowed to be downloaded and stored encrypted, per business rules. Applies to video, documents, audio and images. This is available if mflyDataInit.mflyInitVersion >= 3.
* canDownloadAsset: indicates whether the app is allowed to download the item and open it in an external application, unencrypted. Applies to e.g. iBooks, KeyNote, Excel. This is available if mflyDataInit.mflyInitVersion >= 3.

All dates are specified in ISO 8601 format.

*Example:*

	function mflyInit(obj) {
		// Handle mflyInit object
	}

*Availability:* iOS, Android, Windows 8


----------


## Saving and retrieving key/value data
Interactives can save data to and retrieve data from the app with AJAX. This is useful for persisting data within or between Interactives. After saving, you can be sure that your data will be saved if the user restarts the app or device (and, soon, uninstalls/reinstalls the app). You can use any key you wish, which provides a lot of flexibility, but please consider providing a namespace within your keys. Otherwise, two Interactives may find ways to clobber each others’ keys.

Key/value data is isolated by user, by app. So, two users cannot share keys, and two apps cannot share keys. Furthermore, currently all key/value data is stored on the device. So, uninstalling the app will remove all saved keys (though, there are plans to synchronize key/value data to servers).


### Save data
To save data to the app container, call mflyCommands.putValue(_key, value_), where _key_ is the key you wish to save, and _value_ is the value for that key.

*Example:*<br>
This example saves key/value data, using mflyCommands.js.

	var key = $('#key').val();
	var value = { name: ‘abc’, value: ‘def’ };

    mflyCommands.putValue(key, value)
        .done(function(data, status) {
            // Success! Do something.
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

Alternatively, you can make the calls directly with AJAX, but we strongly recommend using mflyCommands instead. To make an AJAX GET to ```mfly://data/info/[key]?method=PUT&value=[value]```, where [key] is the key you wish to use, and [value] is the value for that key.

HTTP response codes:

* Response of 200 OK indicates a successful save to an existing key
* Response of 201 Created indicates a successful save to a new key
* Response of 400 Bad Request is returned if the value has not been previously saved

(Note: attempts were made to use HTTP PUT verbs, but iOS seems to strip out the body of the PUT, so we migrated to use GET instead).

*Example:*<br>
This example saves key/value data, using jQuery.

	var key = $('#key').val();
	var value = { name: ‘abc’, value: ‘def’ };

	$.ajax({
		type: "GET",
		url: "mfly://data/info/" + key,
		contentType: "text/plain; charset=utf-8",
		data: "value=" + encodeURIComponent(JSON.stringify(value)) + "&method=PUT",
		dataType: "text",
		success: function(result, status, xhr) {
			// Success! Do something.
		},
		error: function(xhr) {
			// Error! Do something.
		}
	});

*Availability:* iOS, Android, Windows 8, Web Viewer

### Retrieve data

You can retrieve value information from keys in multiple ways using mflyCommands.js.

Alternatively, you can make the calls directly with AJAX, but we strongly recommend using mflyCommands instead. When making calls directly,

* Response of 200 OK indicates a successful get of an existing key. The body of the response will contain the values indicated below.
* Response of 404 Not Found indicates that the key does not exist


#### Single key ####

To get data for a specific key from the app container, use mflyCommands.getValue(_key_).

*Example:*

	// Assume key 'abc' has been set to value '123'.

    mflyCommands.getValue('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: 123

On successful retrieval, data will contain the value of the key.


#### All keys ####

To get data for all keys from the app container, use mflyCommands.getValues().

*Example:*

	// Assume key 'abc' has been set to value '123', and key 'def' has been set to value '456'.

    mflyCommands.getValues()
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: { "abc": "123", "def": "456" }


On successful retrieval, body will contain a JSON object enumerating all keys and values, as shown above.


#### Key prefix ####

To get data for all keys that begin with X, use mflyCommands.getValues(_prefix_). On successful retrieval, data will contain a JSON object enumerating all keys and values where the keys begin with X.

*Example:*

	// Assume:
	//   key 'snow' has been set to value 'white',
	//   key 'snowball' has been set to value 'round',
	//   key 'fire' has been set to value 'red'

    mflyCommands.getValues('snow')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: { "snow": "white", "snowball": "round" }

    mflyCommands.getValues('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: {}


*Availability:* iOS, Android, Windows 8, Web Viewer


### Delete data

To delete a key/value pair by key from the app container, use mflyCommands.deleteKey(_key_).

*Example:*

	// Assume key 'abc' has been set to value '123'.

    mflyCommands.deleteKey('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log('abc has been deleted');
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: 'abc has been deleted'

*Availability:* iOS (617+), Android (2.24.21), Windows 8 (soon), web viewer (8/20/2015). Requires mflyCommands.js 1.8.0+


----------

## Embedding, images, other Interactives, or pages

### Overview
Interactives have the ability to embed items that exist in the app into it.  This is used for four primary purposes:

* Retrieve an item that contains structured data (JSON, XML, CSV)
* Embed an item that is an image and render it as an image in this Interactive
* Embed another Interactive into an iframe on this Interactive
* Embed a page from a PowerPoint, PDF, or Word document

Embedding other types (audio, video, URLs) will have unexpected results. The best user experience will be provided to the user only if embedding is limited to data, images, documents and Interactives.

* To retrieve a data item, use ```mflyCommands.getData(id)```, where _id_ is the ID of the data item you wish to obtain.
* To embed an image, create an ```<img>``` that refers to a loading image. In JavaScript, call ```mflyCommands.embed($element, id)```, where _$element_ is a jQuery reference to the img element, and _id_ is the Airship id of the image item.
	* You can also use `mflyCommands.embedImage($element, id, options)` and supply dimensions for the image to be embedded (only supported on Web Viewer and requires mflyCommands.js version 1.4.10). This can be particularly helpful to scale down high resolution images to so browsers can load them quicker. _options_ are defined as:
		* _size_ (string):   format is WxH (e.g. "200x200")
		* _width_ (number): Width of the image
		* _height_ (number): Height of the image
		* _maxWidth_ (number): Maximum width of the image
		* _maxHeight_ (number): Maximum height of the image
		* _rotate_ (number): Degree of rotation to apply to the image. Values of 0, 90, 180, 270, or 360 are acceptable.
* To embed another Interactive, construct an ```<iframe>```. In JavaScript, call ```mflyCommands.embed($element, id)```, where _$element_ is a jQuery reference to the iframe element, and _id_ is the Airship id of the other Interactive.
* To embed the page from a document as an image, create an ```<img>``` that refers to a loading image. In JavaScript, call ```mflyCommands.embed($element, id, page)```, where _$element_ is a jQuery reference to the img element, _id_ is the Airship id of the image item, and _page_ is the page number that you wish to embed.
* Please note: if you are embedding an image asset for the web Viewer, please consider directly accessing the ```resourceUrl``` attribute instead of using ```mflyCommands.embed```. This will improve loading speed, caching, and reduce network requests. See the [optional changes](#optional_changes) section below.


### Example

See our [open-source 'Embed and Data Items' Interactive](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples/src/tip/examples/Embed%20and%20Data%20Items/Containing%20Interactive/?at=default) for an excellent working example of all four types of Embed described above.

### Technical details

Here are the HTTP response codes by platform. Unfortunately, these are not all the same because of differing security limitations across platforms.

State | iOS | Android
------------ | ------------- | ------------
Item downloaded and ready | HTTP response code 301 (Redirect). Browser should automatically redirect to correct URL. Body contains retrieved data.  | HTTP response code 200. Body contains retrieved data.
Item not already downloaded | HTTP response code 202 (Accepted), with Retry-After header set to number of seconds the Interactive should wait before trying again. If not set, default to a reasonable value, like 5. | HTTP response code 200, with an empty body. Interactive should retry after a reasonable number of seconds, like 5.
Item not found | HTTP response code 404 (Not found) | HTTP response code 404 (Not found)


Please note that asking too many times may put the Downloader into a race condition, as each request will trigger the Downloader to poll and possibly start downloading again.  So, please be considerate and retry no more than once every few seconds.

As you can see, embedding requires care. You cannot assume that the embedded item is available yet, because it may be behind some longer items in the Downloader. Instead, you have to try to fetch the content and monitor response codes. This is why we suggest using mflyCommands.js as shown in the example above.

*Availability:* iOS, Android, Windows 8, Web Viewer

### Reporting when embedding pages

Our Reporting system automatically captures who viewed what, when, and where, when using the native app that sits underneath the Interactive. However, when you embed pages, the app loses that information and is unable to tell our systems about view data.

To inform our Reporting system of a page view, post a page view after the page has been shown to the user:

*mflyCommands.js:* mflyCommands.postPageView(_item ID_, _page number_) <br>
*URL:* mfly://data/postaction/[item ID]?page=[page number] <br>
*Description:* Posts the page view to the Mediafly Reporting system. Item ID is the id of the item, as seen in Airship. Page number is the page that was displayed (starting at page 1).
*Availability:* iOS (575). Requires mflyCommands.js 1.3.6+




-----

## Support widescreen on iOS

### Control

When a user plugs into an HDMI dongle or connects via AirPlay, they can change the app to run in Second Screen mode.  When in this mode, the app can paint a canvas that uses the entire screen resolution (whereas, Mirroring mode restricts the TV/projector to a 4x3 output and adds black bars on the left and right).

TODO: Add screenshots of what this looks like

To support this mode in an Interactive, the Interactive must do a few things.

1. On the app call to mflyDataInit, the object returned must have mflyWideScreenSupport set:

		{
		  “mflyWideScreenSupport”: true
		  ...
		}

	Other parameters may exist in this JSON object, of course, but mflyWideScreenSupport is the key to initiate proper app support for widescreen.

2. If the app has sent mflyWideScreenSupport=true in mflyDataInit, the Interactive should expect to receive a call from the app to mflyWideScreen(useWideScreen), where useWidescreen is either true or false.


*Example:*<br>

        <script>
            function mflyWideScreen(useWideScreen) {
                if (useWideScreen) {
                    // Refresh the UI to show in 16x9 mode,
                    // probably by adjusting CSS. On the iPad, black bars will
                    // appear above and below the content.
                } else {
                    // Refresh the UI to show in 4x3 mode on iPad only
                }
            }
        </script>

3. (Optional) Create a button that the user taps to bring up the Second Screen Options dialog. This button in the Interactive would mimic the button in the native app that allows the user to switch between iOS, Mirroring, and Second Screen. When tapped, the Interactive should invoke:

		mflyCommands.showSecondScreenOptions();


4. (Optional) The app can be notified when the user flips second-screen mode on or off. When the user changes to/from second-screen mode, the app will call mflySecondScreenAvailable(bool), where bool is true or false. Apps can react to this call to render a button to invoke the Second Screen Options dialog.

*Example:*<br>

		function mflySecondScreenAvailable(isAvailable) {
			if (isAvailable) {
				// Second screen is now on/available.
				$("#secondScreenOptionsBtn").show();
			} else {
				// Second screen is no longer available.
				$("#secondScreenOptionsBtn").hide();
			}
		}


### UIWebKit Resolution

When the iPad is displaying an Interactive only on its canvas, the WebKit container is 1024 x 568.  When the iPad is projecting to Second Screen, the WebKit container shrinks to 960 x 540. This allows for the browser to efficiently scale up when connected to 720p and 1080p TVs.

To account for this, we suggest tagging the <body> element with a class that denotes either "ipad" or "widescreen", and adjusting your layout and images based on this.  

*Example:*<br>

1. You may tag specific images or image types based on whether they are in second screen or not:

		<style>
        .ipad .carousel_image       { width: 1024px; height: 378px;
        	background:url('image.png'); background-size: 1024px 378px; }
        .widescreen .carousel_image { width: 960px; height: 354px;
        	background:url(''image.png'); background-size: 960px 354px; }
		</style>

2. The body tag is initially decorated to be iPad-focused:

		<body class="ipad">
		...
		</body>

3. When the user switches between Second Screen and Disabled, the <body> tag changes its class:

		<script>
        function mflyWideScreen(isWidescreen) {
            if (isWidescreen) {
                $("body").removeClass("ipad").addClass("widescreen");
            } else {
                $("body").removeClass("widescreen").addClass("ipad");
            }
        }
        </script>

*Note*: You will likely have to fix the container to 960x540 when in Widescreen. We have seen cases where, if the container is left to auto width/height, and the user invokes the keyboard on the iPad (say, a search field), a giant black space where that keyboard was appears on the second screen. We have seen this on iOS 7.0.x; it may have been corrected in iOS 7.1+ and may no longer be relevant.

*Availability:* iOS

-----

## Other useful information

### Stopping rubber band effects
Since Interactives are embedded web pages, on some (particularly iOS) devices they tend to have a “rubber band” effect.  E.g. if touch-and-hold a part of the Interactive, then drag it, the entire screen will follow your finger, then "snap back" after you let go.  This does not feel appropriate for an Interactive, as users want the look and feel to be more native.

To address this, we recommend using [Hammer.js](http://eightmedia.github.io/hammer.js/), a mobile-focused touch gesture library.

We have created a detailed example within Mediafly Interactives Tools and Examples that demonstrates this, [here](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples/src/tip/examples/Swipe/app/?at=default).

### Smoother scrolling
By default, iOS scrolling in the UIWebView is exactly controlled by your finger. You can drag the web view down and up, but you cannot "throw" the page in a direction. Furthermore, if you have a lot of large images on the page, scrolling can become choppy.

To alleviate this, consider implementing ```-webkit-overflow-scrolling: touch``` on your DOM nodes that handle scrolling. When set to touch, UIWebView uses native-style scrolling on your node.


----------


Appendix
===============

Additional information that doesn't fit very well elsewhere.


## Supporting Interactives for the Web
We will release support for Interactives for the Web Viewer in Q2 2015. Like other platforms, this support will be rolled out slowly over time, as specific customers demand specific features.

With Web Interactives, developers can now also support all major browsers and all major mobile platforms easily and cleanly.

### How does it work?
Web Interactives will be able to make use of the same delivery mechanism and API as Interactives available on iOS, Android and Windows 8 today. When the user opens an Interactive in the Web Viewer, the web server will:

* Unzip the Interactive zip file
* Point to index.html
* Proxy requests to the Interactives API to the correct URL

Provided that you are using mflyCommands.js, the API will remain largely unchanged. Only a few minor issues need to be addressed for you to take full advantage of Interactives on the Web.


### Required changes
The following changes need to be made to your existing Interactives to support Interactives on the Web:

1. **Use mflyCommands.js, and keep it up to date**. This should be your primary way to access our API. Don't make mfly:// calls to the API directly, as those simply will not work in web browsers.

2. **Don't assume a specific browser or size**. The luxurious days of assuming latest Safari (iOS) or Google Chrome (Android 4.4+) are over. Build your Interactive like you would a standard website, because, it can be opened on any browser like a standard website.

3. **Don't rely on mflyDataInit, rely on mflyCommands.getInteractiveInfo() **. Web Interactives will not call "into" the Interactive. This means that mflyDataInit, mflySync, mflyResume, mflyPause, and mflyInit are not supported. Instead, make a new call once the document has been initialized: ```mflyCommands.getInteractiveInfo()```. This asynchronous function will obtain the same information that mflyDataInit used to be called with, like so:

		mflyCommands.getInteractiveInfo()
			.done(function(data) {
				// data contains a JSON object with information
				// about this app, user and Interactive
			}).fail(function() {
				// handle failure
			});

4. *** Don't include a file at the root of your Interactive called mflyManifest.json***. We doubt you would, but if you do, it is likely to be overwritten by one of your apps as a part of the initialization process.

5. **Adjust mflyCommands.getFolder(id) call signature change**. The call signature for mflyCommands.getFolder(id) (which maps to mfly://data/folder/[id]) has changed. Previously, if any of its items were also folders, it would return the items within the folders as an array of IDs. This has changed, and those subfolders no longer includes the items key at all. A separate mflyCommands.getFolder(id) call will have to be made on those children folders.

6. **Each URL should be able to establish its own state**. Don't pass around variables like you would in a traditional application. This breaks on websites, and it will break on web Interactives.

7. **All resources must be accessed in a relative way**. Don't try to construct absolute URLs to assets within your .zip file, as that path will undoutedly differ on other platforms.


### Optional changes
The following changes will help improve performance for your Interactives when opened in the Web Viewer.

1. **To render images, consider using the ```resourceUrl``` attribute and not mflyCommands.embed**. The resourceUrl attribute can be found on mflyCommands.[getFolder, getItem, filter], and other calls that return full item models. The benefits are many:

	* resourceUrl requests are more easily cached by browsers
	* resourceUrl requests return the original image in its original format (jpg, png, gif), with no transcoding
	* resourceUrl requests require one less redirect, which means faster load times

	There are downsides to using resourceUrl as well, however.

	* Because resourceUrl (currently) only exists on the web Viewer, you will need to separate your approach of laying in images between our web Viewer and our mobile platforms.
	* If you need to ask Mediafly's servers to resize images, you will need to use mflyCommands.embed to do so, as resourceUrl returns the image as-is.

## My Items

An interactive can retrieve the contents of the 'My Items' folder like any other folder. However, in order to do so, the following must be taken into consideration:

- My Items require that a user be logged in
- My Items are empty until content is put into a user's My Items folder
- The Id for My Items is always the same for an environment. You can determine the Id by querying the root folder.
