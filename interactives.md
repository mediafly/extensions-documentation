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

To learn more, read the [publish.py comments](https://bitbucket.org/mediafly/mediafly-interactivespublisher/src/1ca748f205eff3a8e8794acdcd64e9ddc1f7d338/publish.py?at=default).


## Testing on device
### iOS

There are two approaches that allow developers to interact with their Interactive when running on a device:

1. [jsconsole.com](jsconsole.com). This is a clever mechanism which allows you to interact with JavaScript on your device remotely. You:

	a. Start a remote debugging session with ```:listen [id]```.
	b. Add the script that is generated into your Interactive
	c. Load the Interactive into Airship and open it on your device
	d. Interact with the Interactive via jsconsole.com.
	
	
2. For trusted partners, we are building the capability to use the iOS Simulator to launch Whitebox and remotely debug with Safari's Web Inspector. Contact us for more information.


### Android

Please see [Remote Debugging Chrome on Android](https://developers.google.com/chrome-developer-tools/docs/remote-debugging). Note that this only applies to Android 4.4+.


----------


API and Howtos
===============

## Controlling the app
Controlling the app is done by opening a window to a special URL through JavaScript. The container app will handle this as an instruction to handle.  The list of available URLs, and their appropriate actions, are listed below.

***PLEASE NOTE***: every Interactive must allow the user to invoke either mfly://control/showControlBars or mfly://control/done in some way. Else, there is no way to exit the Interactive, as they run full-screen.

### Basic controls

#### Close the item

*URL:* mfly://control/done <br>
*Description:* Closes the item and returns the user to where they were before. <br>
*Availability:* iOS, Android, Windows 8

#### Show control bars

*URL:* mfly://control/showControlBars <br>
*Description:* Shows the control bars. <br>
*Availability:* iOS, Android


#### Hide control bars

*URL:* mfly://control/hideControlBars <br>
*Description:* Hides the control bars. <br>
*Availability:* iOS, Android

#### Browse
*URL:* mfly://control/browse <br>
*Description:* Multiple Interactives can layer on top of each other, to represent different layers of hierarchy. The developer may wish to allow the user to navigate the hierarchy using the core Mediafly app with the default grid/list view, e.g. if a “More” or “Browse” button is presented.  This URL closes all Interactives in the stack and takes the user to that hierarchy in the core app if called. <br>
*Availability:* iOS

#### Next
*URL:* mfly://control/next <br>
*Description:* Opens the next Item in the Folder or Collection <br>
*Availability:* iOS, Android

#### Previous
*URL:* mfly://control/previous <br>
*Description:* Opens the previous Item in the Folder or Collection <br>
*Availability:* iOS, Android

#### Get information about a folder or item

*URL:* mfly://data/item/[id] <br>
*Description:* Return a JSON representation of a folder or item. <br>
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
            “keywords”: [“keyword4”] 
        } 
        
        
    Example 2:
		GET mfly://data/item/slug2
		Result:
        {
            "id": "slug2", 
            “items”: [“slug3”, “slug4”],
            "url": "mfly://item/slug2", 
            "type": "folder", 
            "name": "Name", 
            "description": "Description", 
            "date": "2010-11-12T06:15:15:00-06:00", 
            "received": "2012-12-19T16:45:14:00-06:00", 
            "thumbnailUrl": "mfly://image/123", 
            "launched": "false",
            “keywords”: [“keyword1”, “keyword2”, “keyword3”],
            “new”: 1
        }
*Availability:* iOS. Android, Windows 8 coming soon.

        
#### Get contents of a folder
*URL:* mfly://data/folder/[id] <br>
*Description:* Return a JSON representation of the contents of a folder. To get the contents of the top-level folder, use id of \__root__.

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
            “keywords”: [“keyword4”] 
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
            "launched": "false" 
          } 
        ] 

*Availability:* iOS, Android, Windows 8


### App features

#### Show Settings
*URL:* mfly://control/showSettings?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the settings dialog at x-coord, y-coord coordinates with the specified width and height. <br>
*Availability:* iOS


#### Show User Management Dialog
*URL:* mfly://control/showUserManagement&x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the User Management dialog, which allows the user to login or manage users. <br>
*Availability:* iOS


#### Show Second Screen Options dialog
*URL:* mfly://control/secondScreenOptions <br>
*Description:* Shows the Second Screen Options dialog. <br>
*Availability:* iOS


#### Email
*URL:* mfly://control/email/[id] <br>
*Description:* If the item can be emailed, invoke email from within the app. <br>
*Availability:* iOS


#### Refresh the contents of the app
*URL:* mfly://control/refresh <br>
*Description:* Trigger the app to refresh its content. If items have changed, this should trigger mflySync calls appropriately. <br>
*Availability:* iOS

#### Show Annotations
*URL:* mfly://control/showAnnotations <br>
*Description:* If annotations are enabled, show the annotations control bar. <br>
*Availability:* iOS



### Links (Open and Goto)

Two types of links exist within Interactives: Open and Goto. When an Interactive "opens" an item, the item appears on top of the navigational stack, and the user is only given a single option to escape the item, by tapping Done/Close.  When an Interactive "gotos" an item, the app opens that item and throws away the navigational stack behind it. The user is given the same navigational capabilities as if they opened the destination item directly.

To create either link, simply create a typical `<a href>` link with the source pointing to an mfly URL as specified below.

#### Open an item
*URL:* mfly://item/[id] <br>
*Description:* Opens the specified item, where [id] is the ID of the item.  As described above, the historical navigational stack is maintained, and the user can return to it. The ID for an item can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android, Windows 8
	
#### Open a folder
*URL:* mfly://folder/[id] <br>
*Description:* Opens the specified folder, where [id] is the ID of the folder.  As described above, the historical navigational stack is maintained, and the user can return to it. The ID for a folder can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android, Windows 8

#### Goto an item or a folder
*URL:* mfly://control/goto/[id] <br>
*Description:* Gotos the specified item or folder, where [id] is the ID of the item or folder.  As described above, when the app goes to another item or folder, the navigational stack behind the destination is thrown away, and the user is given the same navigational capabilities as if they opened the destination item directly. The ID for a folder can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS (Android and Windows 8 coming soon)


### Search
Mediafly's apps provide real-time, native search as a core part of the functionality. There are two ways to work with Search:

1. Use the native app's core Search UI. In this case, you simply need to open the Search dialog and let the user use that dialog. See "Show Search dialog" below.
2. Implement your own UI. In this case, you use mfly:// calls to get search results given terms, and then render the UI as you wish. See "Search by keyword" below.

#### Show Search dialog
*Description:* Shows the search dialog at x-coord, y-coord coordinates with the specified width and height. <br>
*URL:* mfly://control/showSearch?x=[x-coord]&y=[y-coord]&w=[width]&h=[height]<br>
*Availability:* iOS

#### Search by keyword
*URL:* mfly://data/search?term=[alpha]<br>
*Description:* Conduct a search for the given keyword. Return value is a JSON-array of folders and items, similar in format to a call to mfly://folder/[id].<br>
*Availability:* iOS


### Collections
Collections allow users to create a "personal playlist" of items. Oftentimes this is used as a wa to organize items for, say, an upcoming meeting with a customer. Collections are pointers to items, not copies of items; updating the original item in Airship will update the item in the Collection as well.


#### Show "Collections"
*URL:* mfly://control/showCollections?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Collections dialog at x-coord, y-coord coordinates with specified width and height. <br>
*Availability:* iOS


#### Show "Add to Collections"
*URL:* mfly://control/showAddToCollection?id=[itemId]&x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Add To Collections dialog at x-coord, y-coord coordinates with specified width and height for the specified item. <br>
*Availability:* iOS


#### Get the list of Collections
*URL:* mfly://data/collections <br>
*Description:* Return a JSON representation of the list of Collections

	Example:
		GET mfly://data/collections
		Result:
		[ 
          { 
            "id": "collection1", 
            "name": "Collection 1", 
            "items": [
              id_1_if_item_1_in_collection,
              id_2_if_item_2_in_collection,
            ]
          }, 
          { 
            "id": "collection2", 
            "name": "Collection 2", 
            "items": [
              id_3_if_item_1_in_collection,
              id_4_if_item_1_in_collection,
            ]
          }
        ] 

*Availability:* iOS


#### Get the contents of a Collection
*URL:* mfly://data/collection/[collection unique ID] <br>
*Description:* Return a JSON representation of the contents of a collection, in a similar format to Get Folder.

	Example:
		GET mfly://data/collections/collection1
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
            “keywords”: [“keyword4”] 
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
            "launched": "false" 
          } 
        ] 
        
*Availability:* iOS


### Downloader
Mediafly's apps have been optimized for very advanced synchronization and download use cases. We offer a set of Interactives calls to obtain information about downloads and control the app.

#### Show Downloader
*URL:* mfly://control/showDownloader?x=[x-coord]&y=[y-coord]&w=[width]&h=[height] <br>
*Description:* Shows the Downloader dialog at x-coord, y-coord coordinates with specified width and height. <br>
*Availability:* iOS

#### Get total download progress
*URL:* mfly://data/download/status <br>
*Description:* Get total download progress, as a range between 0 (no downloads started) and 1 (all downloads complete), and number of failed downloads. <br>

	Example:
		GET mfly://data/download/status
		Result:
        {
          "progress":0.12, 
          "fails":1
        }

#### Get download progress for a single item
*URL:* mfly://data/download/status/[id] <br>
*Description:* Get download status for a single item. The attribute may be "progress" (download is progressing normally), "error" (download is in an error state), or "exceed" (download won't proceed because the cellular limit set for the app has been exceeded). <br>

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
        { "exceed": 0.34 }

*Availability:* iOS



#### Add to Downloader
*URL:* mfly://control/addToDownloader/[id]<br>
*Description:* Instruct the app to add an item to the Downloader. Response code 200 = item was added to the Downloader. Response code 404 if id is not found.<br>
*Availability:* iOS




#### mflyDownloadStatus
Interactives can listen for changes to total download status with mflyDownloadStatus. This function is called approximately once per second while a download is being conducted. The Interactive simply needs to implement this function and take some (short-running) action. The parameter is a JSON object that indicates progress percentage as a decimal from 0 to 1, and fail count as an integer.

	Example:
	    function mflyDownloadStatus(obj) {
			// obj = { 'progress': 0.12, 'fails': 1 }
			// Handle download status object
		}

*Availability:* iOS

### Notifications

When notifications are enabled for an app, users can subscribe or unsubscribe to/from emails on any folder. When new content appears in the folder, the system will email the user either instantly or at the end of the day in a digest of the availability of the new content. Interactives can control notifications with the following commands.

#### Get notification status for a folder
*URL:* mfly://data/getNotificationStatus/[id]<br>
*Description:* Get the notification status for a folder. If the folder is valid, response code is 200 and body is a JSON object similar to this:<br>

	Example:
		{ 
			"notification":"[status]" // [status] is email|none
		}

*Availability:* iOS

#### Add a folder to notifications
*URL:* mfly://data/addNotification/[id]<br>
*Description:* Instruct the app to subscribe the folder for notifications. Response code 200 if successful, 304 if folder was already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS

#### Remove a folder from notifications
*URL:* mfly://data/removeNotification/[id]<br>
*Description:* Instruct the app to unsubscribe the folder from notifications. Response code 200 if successful, 304 if folder was not already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS

#### Display the notifications manager
*URL:* mfly://control/showNotificationsManager<br>
*Description:* Display the notifications manager<br>
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

*URL:* mfly://data/gps<br>
*Description:* Interactives can request latitude and longitude from the app. This is useful for apps that connect to external services to report lat/lon, or use the Haversine (Big Earth) formula to calculate rough distances.<br>

	Example:
		GET mfly://data/gps
		Response:
		{ 
		  latitude: 41.851231, 
		  longitude: -87.652345 
		}
*Availability:* iOS, Android, Windows 8 (soon)


----------



## Being controlled by the app
The app sends many kinds of events into the Interactive, in case the Interactive is interested in reacting to those events. These are done by calling specific JavaScript functions. If the appropriately named function is defined, they will be called by the app at the appropriate time, and the Interactive can take action. If the function does not exist, nothing will happen.


### mflyDataInit
This function has two uses:

1. Receive configuration information from the app, and
2. Send configuration parameters to the app

#### Use 1: Receive configuration information from the app
Interactives can implement mflyDataInit and receive within the params parameter a JSON Object of initial configuration.  E.g. on iOS, params may be a JSON Object such as this:

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
		  “mflyInitVersion”: “3”,
		  “mflyWideScreenSupport”: true
		}

*Supported parameters:*<br>

* mflyInitVersion: indicates which version of mflyInit should be called by the app. Note that mflyInitVersion is only available on iOS. Android and Windows 8 support only 'version 3' of mflyInit.
	* "2": default version reflected in this documentation
	* "3": On iOS, sharing status is represented by canShare, canAccessAssetOffline, and canDownloadAsset. 	
* mflyWideScreenSupport: if true, this makes the app aware that the Interactive can handle widescreen second screens (HDMI, Apple TV).  This is specifically for the case where the Interactive has been designed to be a presentation-worthy UI for second screens. Only available on iOS.

*Example:*<br>

	function mflyDataInit(obj) {
		// Do initialization
	    return '{ "mflyInitVersion" : "3" }';
	}

*Availability:* iOS, Android, Windows 8. Not all parameters will be available on Android or Windows 8.



### mflyInit
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

*Availability:* iOS, Android

### mflySync
As the app synchronizes all folders within a user’s hierarchy, updated information (metadata, URLs, etc.) becomes available. If an Interactive is open while the new information appears, the app calls mflySync with this subset of updated information.  The parameter is an array of JSON objects that represent changed items.

Expect mflySync to be called many times.  Each time may contain a subset of changed information for the app.

The best use of mflySync is to listen for changes to “launched” status. For example, if the Interactive wishes to render “New!” banners on new items, and have those banners clear when the user launches the item, the Interactive would need to listen to mflySync and remove the “New!” banner when launched=true for a given item.

*Example:*<br>

	function mflySync(obj) {
		// Handle mflySync
	}

*Availability:* iOS


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


----------


## Saving and retrieving key/value data
Interactives can save data to and retrieve data from the app with AJAX. This is useful for persisting data within or between Interactives. After saving, you can be sure that your data will be saved if the user restarts the app or device (and, soon, uninstalls/reinstalls the app). You can use any key you wish, which provides a lot of flexibility, but please consider providing a namespace within your keys. Otherwise, two Interactives may find ways to clobber each others’ keys.

Key/value data is isolated by user, by app. So, two users cannot share keys, and two apps cannot share keys. Furthermore, currently all key/value data is stored on the device. So, uninstalling the app will remove all saved keys (though, there are plans to synchronize key/value data to servers).


### Save data
To save data to the app container, make an AJAX GET to ```mfly://data/info/[key]?method=PUT&value=[value]```, where [key] is the key you wish to use, and [value] is the value for that key.

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



*Availability:* iOS, Android, Windows 8

### Retrieve data

You can retrieve value information from keys in multiple ways. In all cases:

* Response of 200 OK indicates a successful get of an existing key. The body of the response will contain the values indicated below.
* Response of 404 Not Found indicates that the key does not exist


#### Single key ####

To get data for a specific key from the app container, make an AJAX GET to ```mfly://data/info/[key]```, where [key] is the key you wish to use.

On successful retrieval, body will contain the value of the key. 

#### All keys ####

To get data for all keys from the app container, make an AJAX GET to ```mfly://data/info``` .

On successful retrieval, body will contain a JSON object enumerating all keys and values, such as:

	{ "key1": "value1", "key2": "value2", ... } 
	
#### Key prefix ####

To get data for all keys that begin with X, make an AJAX GET to ```mfly://data/info?prefix=X```

On successful retrieval, body will contain a JSON object enumerating all keys and values where the keys begin with X. For example, if list of keys/values in app are: 

* key=snow, value=white 
* key=snowball, value=round 
* key=fire, value=red 

Then:
GET ```mfly://data/info?prefix=snow```
returns 

	{ "snow": "white", "snowball": "round" } 

GET ```mfly://data/info?prefix=abc```
returns 

	{} 



#### Example ####
This example retrieves value data for a given key, using jQuery.

	var key = $('#key).val();
	$.ajax({
		type: "GET",
		url: "mfly://data/info/" + key,
		success: function(result, status, xhr) {
			// Success!
			value = JSON.parse(result);
		},
		error: function(xhr) {
			// Error! Examine the response code to understand the cause of the error.
		}
	});

*Availability:* iOS, Android, Windows 8


----------

## Embedding an image or another Interactive

Interactives have the ability to embed items that exist in the app into it.  This is primarily used to embed images and other Interactives.  Documents, audio and video embeds will “stream” the media if they aren’t downloaded. HTML and YouTube links that are embedded may result in errors if the user is offline. The best user experience will be provided to the user only if embedding is limited to images and Interactives.


* To embed an Interactive, create an ```<iframe>``` and set the src to ```mfly://data/embed/[id]``` where [id] is the ID of the Interactive you wish to embed
* To embed an image, create an ```<img>``` and set the src to ```mfly://data/embed/[id]``` where [id] is the ID of the image you wish to embed
* Embed can also be used to pull data items from the app into the Interactive.

Here are the HTTP response codes by platform. Unfortunately, these are not all the same because of differing security limitations across platforms.

State | iOS | Android
------------ | ------------- | ------------
Item downloaded and ready | HTTP response code 301 (Redirect). Browser should automatically redirect to correct URL. Body contains retrieved data.  | HTTP response code 200. Body contains retrieved data.
Item not already downloaded | HTTP response code 202 (Accepted), with Retry-After header set to number of seconds the Interactive should wait before trying again. If not set, default to a reasonable value, like 5. | HTTP response code 200, with an empty body. Interactive should retry after a reasonable number of seconds, like 5.
Item not found | HTTP response code 404 (Not found) | HTTP response code 404 (Not found)


Please note that asking too many times may put the Downloader into a race condition, as each request will trigger the Downloader to poll and possibly start downloading again.  So, please be considerate and retry no more than once every few seconds.
  
As you can see, embedding requires care. You cannot assume that the embedded item is available yet, because it may be behind some longer items in the Downloader. Instead, you have to try to fetch the content and monitor response codes.

*Example:*<br>
We recommend using mflyCommands.js for this purpose. See our [open-source Interactives](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples/src/18033ec5a6c7ad4fb38d89b539eeb3c021b0716e/examples/Embed/Containing%20Interactive/?at=default) for a working example of Embed.


*Availability:* iOS, Android


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


3. (Optional) The app can be notified when the user flips second-screen mode on or off. When the user changes to/from second-screen mode, the app will call mflySecondScreenAvailable(bool), where bool is true or false. Apps can react to this call to render a button to invoke the Second Screen Options dialog.

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

We have created a detailed example within Mediafly Interactives Tools and Examples that demonstrates this, [here](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples/src/98dc1548d87fadeb15613896eb8335b2c772003e/examples/Swipe/app/?at=default).

### Smoother scrolling
By default, iOS scrolling in the UIWebView is exactly controlled by your finger. You can drag the web view down and up, but you cannot "throw" the page in a direction. Furthermore, if you have a lot of large images on the page, scrolling can become choppy.

To alleviate this, consider implementing ```-webkit-overflow-scrolling: touch``` on your DOM nodes that handle scrolling. When set to touch, UIWebView uses native-style scrolling on your node.

-----

## Examples and Blog posts

### Examples

Mediafly creates and maintains a detailed list of examples. These examples illustrate many of the features of Interactives and can serve as QA for client app developers.

Please see the [Mediafly Interactives Tools and Examples](https://bitbucket.org/mediafly/mediafly-interactives-tools-and-examples) BitBucket repository for more information.

To see the examples in action on your device:

1. Download Whitebox for [iOS](https://itunes.apple.com/us/app/whitebox/id399107683?mt=8), [Android](https://play.google.com/store/apps/details?id=com.mediafly.android.video.onmediafly&hl=en) or [Windows 8](http://apps.microsoft.com/windows/en-us/app/39a96812-56fa-4583-82eb-4d787fda0d4c)
2. When asked for Company Code or Company Login Name, enter "interactives"
3. Each example will be contained within a folder that describes the example


### Blog posts

Mediafly publishes blog posts illustrating interesting ways to use Interactives. More to come over time.

* [AngularJS and Interactives Create Awesome Workouts](http://www.mediafly.com/angularjs-and-interactives-create-awesome-workouts)

-----

## Other stuff

### Changelog
Please see the [repository](https://bitbucket.org/mediafly/mediafly-interactives-documentation/commits/all) of this document for a changelog.

### Feedback
See an area of confusion? Please [contact us](mailto:support@mediafly.com?Subject=Interactives%20Documentation%20Feedback).