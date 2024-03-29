![image](./images/ExtensionsAPI-logo.png)

Overview
========

Customers often wish to create rich, interactive content that include advanced UI effects, form entry, dynamic content, and more features that extend well beyond the limitations of PowerPoint or PDFs. With Mediafly Extensions and the Mediafly Platform, customers can create, release and control these packages.  This document serves as documentation for how a customer can create an Extension and what best practices exist in their creation and maintenance.


----------


## What are Extensions? ##

Extensions are all the components that make up a static website:

* HTML files
* JavaScript
* CSS
* Fonts
* Images

All of these files are packaged up in a specific way and uploaded to Mediafly Airship. These packages have the extension of `.mext`. Note: Packaging Extensions with the `.zip` is now obsolete and Viewer no longer supports this file extension for Extensions.

When the user opens the Extension, the app loads and renders the Extension much like it would a traditional web page.
![image](https://devdocs.mediafly.com/extensions/images/Extensions%20Overview.png)

To ensure that the apps work consistently and correctly, the Extension developer needs to follow a few rules:

* The starting page must be named index.html .  This is the common starting point from which any package can launch.  Sub-pages are acceptable, as long as they are navigable from index.html.
* Apps that reference third-party libraries (e.g. jQuery, jQuery Mobile, Reveal.js, Angular.JS) should relatively reference those files from the pages.  To be able to function while offline (for iOS and Android) no files should be absolutely referenced to a site on the web.  For example, an index.html file that contains the following is acceptable, because the files are relatively referenced:

		<script src="js/jquery.min.js"></script>
		<link rel="stylesheet" href="css/main.css">

    and an index.html file containing the following is not acceptable for offline use, because the files are absolutely referenced.

		<script src="http://code.jquery.com/jquery-1.8.2.min.js"></script>
		<link rel="stylesheet" href="http://mediafly.com/css/main.css">


----------


## Communication between the App and Extension ##

Extensions are single page applications. They allow for deep customization of the entire look and feel of the application. Because Extensions make use of the entire screen, Mediafly’s apps need to be told when to close the Extension as well as when to show other content.  The typical approach to this is:

1. The Extension will provide a Menu or X button in the app.
2. When the user taps/clicks on the Menu button, notify the app to close the Extension
3. The app will close the Extension and navigate to other content where the user can resume their workflow.

Extensions can link to other items within the hierarchy.  For example, if a user has access to one Extension and three other items (video, audio, documents, presentations, even other Extensions), the Extension can link directly to those items. 

----------

Building Extensions
=====================

## Creating an extension
A Mediafly extension is a `.mext` file. This file can be put together by zipping up the contents of a web application. Below are the steps to create a valid `.mext` file.

1. Create a folder and add an `index.html` file to it. This will be the entry point of the application.
2. Add `interactive-manifest.json` file with contents `{}` in the folder.
3. Add CSS and JavaScript files and add references to them in `index.html`.
4. Only needed for iOS. Add the following script tag to your `index.html`. `<script type="text/javascript" src="bootstrap_mflyCommands.js"></script>`. Be sure to add this script tag in an order such that mflyCommands script is loaded after it.
5. Zip up the contents of the folder. Make sure that `index.html` is at the root of the zip file.
6. Rename the zipped archive to have the `.mext` extension (e.g. `my-extension.mext`).
7. Upload the `.mext` file to Mediafly using Airship.

## Developing and testing an Extension
Mediafly provides a CLI (command line interface) utility called `extension-cli` to make it easier to test and develop an Extension. `extension-cli` allows an Extension developer to:

1. Rapidly develop Extensions by live-reloading changes from the developer's machine.
2. Generate the `.mext` package from static files.
3. Upload the Extension into Airship.

For more detailed instructions, refer to https://www.npmjs.com/package/@mediafly/extension-cli.

Note: You will need the [Node.js](https://nodejs.org/en/download/) runtime (v5 or higher) to use `extension-cli`. `extension-cli` is currently only supported on Viewer and iOS.

## Testing on device

Contact the Mediafly team to get assistance with debugging extensions on devices.

----------

## Examples and Resources

### Examples

Mediafly creates and maintains a detailed list of examples. These examples illustrate many of the features of Extensions and can serve as QA for client app developers.

Please see the [Mediafly Extensions Examples](https://github.com/mediafly/extensions-examples) repository for more information.

To see the examples in action on your device on a browser:

* Navigate to [https://viewer.mediafly.com/extensions](https://viewer.mediafly.com/extensions). Note: you will need a username and password; please contact [Mediafly Support](https://support.mediafly.com/support-center/contact/) to request one.

To see the examples on a device app:

* Download Whitebox for [iOS](https://itunes.apple.com/us/app/whitebox/id399107683?mt=8), [Android](https://play.google.com/store/apps/details?id=com.mediafly.android.video.onmediafly&hl=en), [Windows](http://downloads.mediafly.com.s3.amazonaws.com/desktopviewer/mediafly-desktop-viewer-prod-latest.exe) or [Mac](http://downloads.mediafly.com.s3.amazonaws.com/desktopviewer/mediafly-desktop-viewer-prod-latest.dmg)
* When asked for Company Code or Company Login Name, enter "extensions"
* Log in. You will need a username and password; please contact [Mediafly Support](https://support.mediafly.com/support-center/contact/) to request one.
* Each example will be contained within a folder that describes the example

### Google Group
Please [join our Google Group](https://groups.google.com/forum/?hl=en#!forum/mediafly-extensions) to keep up with the latest information, updates and news. As well, learn from and get help from other Extensions builders and customers.

### Feedback
See an area of confusion? Please [contact us](mailto:support@mediafly.com?Subject=Extensions%20Documentation%20Feedback).

----------

Configuring an extension with `interactive-manifest.json`
===============

This section describes properties available in the `interactive-manifest.json` that can be used to control how the Extension is launched.
----------
- `ios-use-fullscreen: true/false`. Controls if the extension is launched in fullscreen mode or with an exit button.
- `embed: true/false`. Controls if the extension is launched in the native player of each platform.

API and Examples
===============

This section describes all of the APIs available to Extensions developers.

*A note on 'Availability':* You will see many API calls have no version numbers within the Availability column. In these cases, the calls have been in existence for a sufficiently long period of time that Extensions developers no longer need to worry about whether their clients' apps support those calls anymore.

*Keeping mflyCommands up-to-date:* mflyCommands.js is available on the [npm](https://www.npmjs.com/package/mfly-commands). This is the recommended way of consuming it as a dependency in your Extension. The npm package can be found here: [https://www.npmjs.com/package/mfly-commands](https://www.npmjs.com/package/mfly-commands)

Communicating the app is done by calling functions on mflyCommands. The list of available functions, and their appropriate actions, are listed below.

***PLEASE NOTE***: Every Extension must allow the user to close the item. Else, the user will be stuck within the Extension, with no way to exit.

### Getting baseline information

#### getInteractiveInfo
*mflyCommands.js:* mflyCommands.getInteractiveInfo() <br>
*Description:* Obtains baseline information about this Extension. Extensions can call this function and receive an object of initial configuration. Response example:

		{
		  	"user": "jshah@mediafly.com",
			"displayName": "John Doe",
			"item": "Custom Dynamic Extension 1.2",
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
* item: title of this Extension
* id: the slug (unique id) of this Extension within Airship
* parentId: the slug (unique id) of the parent of this Extension in Airship
* osType: which OS (`iOS`, `Android`, `web`, `desktop` for Windows or Mac)
* osVersion: which version of the OS. Only works on iOS and Android.
* appVersion: which version of the app. Only works on iOS and Android.
* deviceId: the unique device ID for this device. Only works on iOS and Android.
* lastUpdated: the lasted updated time. This is good for debugging purposes, to help you identify which version of the Extension you are looking at on the device

Note that not every parameter is available on every platform.

*Availability:* iOS, Android, Web Viewer, Windows/Mac

#### getSystemInfo
*mflyCommands.js:* mflyCommands.getSystemInfo() <br>
*Description:* Obtains information about the particular platform that the extension is running on. This command is useful for an extension to determine what capabilities the platform does or deos not support and provide an appropriate experience to the user. Response example:

		{
			"osType": "web",
			"supportsComposeEmail": false,
			"supportsDownloadUrl": true,
			"supportsEmail": false,
			"supportsGetGpsCoordinates": false,
			"supportsGetOnlineStatus": false,
			"supportsGetSyncStatus": false,
			"supportsOpenWindow": true,
			"supportsRefresh": false,
			"supportsRelatedContent": true,
			"supportsSendEmail": true,
			"supportsShowAddToCollection": false,
			"supportsShowAnnotations": false,
			"supportsShowCollections": false,
			"supportsShowControlBars": false,
			"supportsDownloads": false,
			"supportsDownloader": false,
			"supportsShowNotificationManager": false,
			"supportsShowSearch": false,
			"supportsShowSecondScreenOptions": false,
			"supportsShowSettings": false,
			"supportsShowUserManagement": false,
			"supportsTakeAndEmailScreenshot": false
		}

*Availability:* iOS, Android, Web Viewer, Windows/Mac

#### getUserInfo
*mflyCommands.js:* mflyCommands.getUserInfo() <br>
*Description:* Obtains information about the logged in user. Response example:

		{
			"id": "1234",
			"username": "user@mediafly.com",
			"emailAddress": "user@mediafly.com",
			"displayName": "John Doe"
		}

*Availability:* iOS, Android, Web Viewer, Windows/Mac



### Navigation

#### Close the item
*mflyCommands.js:* mflyCommands.close() <br>
*Description:* Closes the item and returns the user to where they were before. <br>
*Availability:* iOS, Android, Web Viewer, Windows/Mac

#### Next
*mflyCommands.js:* mflyCommands.next() <br>
*Description:* Opens the next Item in the Folder or Collection <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Previous
*mflyCommands.js:* mflyCommands.previous() <br>
*Description:* Opens the previous Item in the Folder or Collection <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Get information about a folder or item
*mflyCommands.js:* mflyCommands.getItem(_id_) <br>
*Description:* Return a JSON representation of a folder or item. To get the contents of the folder, rely on the mflyCommands.getFolder(_id_) <br>
*Examples:*

	Example 1:
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
			"bookmark": 45,
			"length": 596,
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
*Availability:* iOS, Android, Web Viewer, Win/Mac


#### Get contents of a folder
*mflyCommands.js:* mflyCommands.getFolder(_id_) <br>
*Description:* Return a JSON representation of the _contents_ of a folder. To get the contents of the top-level folder, use id of \__root__.

	Example:
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
			"bookmark": 45,
			"length": 596,
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

*Availability:* iOS, Android, Web Viewer, Win/Mac

### App features

#### Show Second Screen Options dialog
*mflyCommands.js:* mflyCommands.showSecondScreenOptions() <br>
*Description:* Shows the Second Screen Options dialog. <br>
*Availability:* iOS


#### Email
*mflyCommands.js:* mflyCommands.email(_id_) <br>
*Description:* If the item can be emailed, invoke email from within the app. <br>
*Availability:* iOS, Android


#### Show Annotations
*mflyCommands.js:* mflyCommands.showAnnotations() <br>
*Description:* If annotations are enabled, show the annotations control bar. <br>
*Availability:* iOS


#### Take and email Screenshot
*mflyCommands.js:* mflyCommands.takeAndEmailScreenshot() <br>
*Description:* App will take a screenshot and populate the email with that screenshot. <br>
*Availability:* iOS

#### Open URL in a new Window
*mflyCommands.js:* mflyCommands.openWindow(url) <br>
*Description:* Web Viewer will use open the URL in a new browser tab. Win/Mac app will open the URL in a new native window. <br>
*Availability:* Web Viewer, Win/Mac. Requires mflyCommands.js 1.13.0+.

### Open an item
When an Extension opens an item, the item appears on top of the navigational stack, and the user is only given a single option to escape the item, by tapping Done/Close.

#### Open an item
*mflyCommands.js:* mflyCommands.openItem(_id_) <br>
*Description:* Opens the specified item, where [id] is the ID of the item.  As described above, the historical navigational stack is maintained, and the user can return to it. The ID for an item can be found in Airship. Expand the item, and the ID is displayed at the bottom. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Open another extension and pass some arbitrary parameters
*mflyCommands.js:* mflyCommands.openItem(_id_, { params: { test: 'test' } }) <br>
*Description:* Opens an extension with the specific id, where [id] is the ID of an extension. The extension will receive the params specified as query string parameters. The historical navigational stack is maintained. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac


#### Open an item from Search results, a Collection, or a Search Folder

An Extension can render a Collection, Search Results, or a Search Folder in its UI and leverage the app to open items from them. If the context is supplied, the app will allow the user to present all items from the given context from the playlist that is shown when the item is opened.

Examples:
Open an item from a collection:

*mflyCommands.js:* mflyCommands.openItem(_id_, { context: 'collection', collection: _collectionId_ }) <br>

Open an item from search results:

*mflyCommands.js:* mflyCommands.openItem(_id_, { context: 'search', search: _searchTerm_ }) <br>

Open an item from a search folder:

*mflyCommands.js:* mflyCommands.openItem(_id_, { context: 'searchFolder', parentSlug: _searchFolderId_ }) <br>

*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Open a folder
*mflyCommands.js:* mflyCommands.openFolder(_id_) <br>
*Description:* Opens the specified folder, where [id] is the ID of the folder. The historical navigational stack is maintained only on iOS and Android. Viewer and Win/Mac app does not provide a way to navigate back to the extension. When possible, the extension should render the folder contents itself inside the extension and provide a consistent navigation experience inside the extension. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac


### Search
Mediafly's apps provide real-time, native search as a core part of the functionality. There are two ways to work with Search:

An extension should implement its own UI to provide an optimal search experience for its use case. Use the mflyCommands.search() call to get search results for a given term.

#### Search by keyword
*mflyCommands.js:* mflyCommands.search(_term_, _offset_, _limit_) <br>
*Description:* Conduct a search for the given keyword. Return value is a JSON-array of folders and items. Parameters offset and limit are optional.<br>
*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Advanced Search
Advanced search syntax can help Extensions find folders and content more efficiently using mflyCommands.search(). You can find the advanced search syntax documentation at: [https://docs.google.com/document/d/1bQYRjKo_ctmv1OQQKo5qQKzRLaXnWgGBebdCdbs3-Mw](https://docs.google.com/document/d/1bQYRjKo_ctmv1OQQKo5qQKzRLaXnWgGBebdCdbs3-Mw)


### Get Recently Created Content
You can use this call to get a list of content that was recently created for the user.

*mflyCommands.js:* mflyCommands.getRecentlyCreatedContent() <br>
*Description:* Returns a JSON respresentation of the recently created content. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac <br>
*Note:* This call is only available when the device is online


### Get Recently Viewed Content
You can use this call to get a list of content that was recently viewed by the user. This list is limited to the last 50 items viewed by the user.

*mflyCommands.js:* mflyCommands.getLastViewedContent() <br>
*Description:* Returns a JSON respresentation of the last 50 items viewed by the user. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac <br>
*Note:* This call is only available when the device is online

### Get Content Related to Given Content
You can use this call to get a list of content that is related to the a given content.

*mflyCommands.js:* mflyCommands.getRelatedItems(id, offset, limit) <br>
*Description:* Returns a JSON respresentation of content related to content with the given id. <br>
*Availability:* Web Viewer <br>
*Note:* This call is only available when the device is online

### Favorties

You can use the following calls to build favorites functionality in an extension.

Get favorites

*mflyCommands.js:* mflyCommands.getFavorites() <br>
*Description:* Returns a JSON respresentation of the items favorited by the user. <br>
*Availability:* Web Viewer <br>

favorite

*mflyCommands.js:* mflyCommands.favorite(id) <br>
*Description:* Sets an item matching the id as a favorite for the user. <br>
*Availability:* Web Viewer <br>

unfavorite

*mflyCommands.js:* mflyCommands.getFavorites() <br>
*Description:* Removes an item as a favorite for the user. <br>
*Availability:* Web Viewer <br>


### Copy Item
You can use this call to copy an item to the desired folder in the hierarchy and optionally provide a new name for the copied item.

*mflyCommands.js:* mflyCommands.copy(folderId, id, newName) <br>
*Description:* Copies an item. <br>
*Availability:* iOS, Android, Web Viewer, Win/Mac <br>
*Note:* This call is only available when the device is online


### Update Item Metadata
You can use this call to update metadata fields of an item.

*mflyCommands.js:* mflyCommands.updateItemMetadata(id, metadataJson) <br>
*Description:* Update metadata fields of an item. <br>
*Example:*

	mflyCommands.updateItemMetadata('123', { field1: 'value1' })

*Availability:* iOS, Android, Web Viewer, Win/Mac <br>
*Note:* This call is only available when the device is online


### Collections
Collections allow users to create a "personal playlist" of items. Oftentimes this is used as a way to organize items for, say, an upcoming meeting with a customer. Collections are pointers to items, not copies of items; updating the original item in Airship will update the item in the Collection as well.


#### Get the list of Collections
*mflyCommands.js:* mflyCommands.getCollections() <br>
*Description:* Return a JSON representation of the list of Collections

	Example:
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

*Availability:* iOS, Android, Web Viewer, Win/Mac


#### Get the contents of a Collection
*mflyCommands.js:* mflyCommands.getCollection(_collection ID_) <br>
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
			"bookmark": 45,
			"length": 596,
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
			"bookmark": 4,
			"length": 38
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

*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Create a Collection
*mflyCommands.js:* mflyCommands.createCollection(_collection name_) <br>
*Description:* Creates the collection, and returns a success or failure error code.

	Example:
		mflyCommands.createCollection("Collection 2");
		Result:
		{
			"id": "collection2id",
			"name": "Collection 2"
		}

*Availability:* iOS, Android, Web Viewer, Win/Mac


#### Add items to a Collection
*mflyCommands.js:* mflyCommands.addItemsToCollection(_collection ID_, [_item ID_]) <br>
*Description:* Adds items to the Collection. If Collection exists and it can be created, response code is 200. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }. If item does not exist, response code is 404 with body "Item not found."

	Example:
		mflyCommands.addItemsToCollection("collection2id", ["item4id"]);

*Availability:* iOS, Android, Web Viewer, Win/Mac

*mflyCommands.js:* mflyCommands.addItemToCollection(_collection ID_, _item ID_) <br>
*Description:* Adds a single item to the Collection. If Collection exists and it can be created, response code is 200. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }. If item does not exist, response code is 404 with body "Item not found."

	Example:
		mflyCommands.addItemToCollection("collection2id", "item4id");

*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Remove item from a Collection
*mflyCommands.js:* mflyCommands.removeItemFromCollection(_collection ID_, _item ID_) <br>
*Description:* Removes the item from the Collection. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }. If item does not exist, response code is 404 with body "Item not found."

	Example:
		mflyCommands.removeItemFromCollection("collection2id", "item4id");

*Availability:*  iOS, Android, Web Viewer, Win/Mac

#### Delete a Collection
*mflyCommands.js:* mflyCommands.deleteCollection(_collection ID_, _shared_) <br>
*Description:* Deletes the Collection. If Collection does not exist, response code is 404 with body { "message": "Collection not found." }.

	Example:
		GET mflyCommands.deleteCollection("collection2id", false);

*Availability:*  iOS, Android, Web Viewer, Win/Mac

#### Reorder an item in a Collection
*mflyCommands.js:* mflyCommands.reorderItemInCollection(_collection ID_, _item ID_, _position_) <br>
*Description:* Changes the order of an item in the Collection. If Collection does not exist, response code is 404 with body { "message": "Collection not found." } is returned. If the Position parameter is not valid within the collection, response code 500 with body { "message": "Invalid position specified" } is returned.
(Note: The position parameter is a zero-based numerical value that indicates the desired position of the item in the collection. 

	Example:
		GET mflyCommands.reorderItemInCollection("collection2id", "item4id", 0);

*Availability:*  iOS, Android, Web Viewer, Win/Mac

#### Rename a Collection
*mflyCommands.js:* mflyCommands.renameCollection(_collection ID_, _name_) <br>
*Description:* Changes the name of the collection. If Collection does not exist, response code is 404 with body { "message": "Collection not found." } is returned. If the Position parameter is not valid within the collection, response code 500 with body { "message": "Invalid slug specified" } is returned.

	Example:
		GET mflyCommands.renameCollection("collection2id", "New Name");

*Availability:*  iOS, Android, Web Viewer, Win/Mac

### Share Links
An Extension can get a share link for an item.
*mflyCommands.js:* mflyCommands.getShare(_item ID_) <br>
*Description:* Returns an object that indicates whether the item is shareable or not. If the item is shareable, the share link is also provided.

	Example:
		GET mflyCommands.getShare("item4id");
		Result:
		{
			"shareable": true,
			"url": "https://assets.mediafly.com/shares/v?e=4hGCVQ2MYt7uO4%2fJbY67EC%2fcjC%2f1TZRiY2gKZV0HKdBON2%2fX8BL4qWJcNCAdwShBDf225NKtucVOuQ0OmMR4O4%2fZNe31JPtAI8OBBHV0kVk%3d"
		}
*Availability:*  iOS, Android, Web Viewer, Win/Mac

### Downloader
Mediafly's apps have been optimized for very advanced synchronization and download use cases. We offer a set of Extensions calls to obtain information about downloads and control the app. 

*Note*: Downloading capability does not exist on Web Viewer.

#### Get total download progress
*mflyCommands.js:* mflyCommands.getDownloadStatus() <br>
*Description:* Get total download progress, as a range between 0 (no downloads started) and 1 (all downloads complete), number of failed downloads, and number of downloads for whom storage has been exceeded. <br>

	Example:
		Result:
        {
          "progress":0.12,
          "fails":1,
          "storageExceeded": 3
        }

*Availability:* iOS, Android, Win/Mac

#### Get download progress for a single item
*mflyCommands.js:* mflyCommands.getDownloadStatus(_id_) <br>
*Description:* Get download status for a single item. The attribute may be "progress" (download is progressing normally), "error" (download is in an error state), "cellularExceeded" (download won't proceed because the cellular limit set for the app has been exceeded; iOS only), or "storageExceeded" (download won't proceed because there is no more available storage on this device). <br>

	Example:
		Result:
        { "progress": 0.34 }

	Example:
		Result:
        { "error": 0.34 }

	Example:
		Result:
        { "cellularExceeded": 0.34 }

	Example:
		Result:
        { "storageExceeded": true }

*Availability:* iOS, Android, Win/Mac


#### Add to Downloader
*mflyCommands.js:* mflyCommands.addToDownloader(_id_) <br>
*Description:* Instruct the app to add an item to the Downloader. Response code 200 = item was added to the Downloader. Response code 404 if id is not found.<br>
*Availability:* iOS, Android, Win/Mac


#### Remove from Downloader
*mflyCommands.js:* mflyCommands.removeFromDownloader(_id_) <br>
*Description:* Instruct the app to remove the item from the Downloader. Response code 200 = item was removed from the Downloader. Response code 404 if id is not found.<br>
*Availability:* iOS, Android, Win/Mac


### Notifications

When notifications are enabled for an app, users can subscribe or unsubscribe to/from emails on any folder. When new content appears in the folder, the system will email the user either instantly or at the end of the day in a digest of the availability of the new content. Extensions can control notifications with the following commands.

#### Get notification status for a folder
*mflyCommands.js:* mflyCommands.getNotificationStatus(_id_) <br>
*Description:* Get the notification status for a folder. If the folder is valid, response code is 200 and body is a JSON object similar to this:<br>

	Example:
		{
			"notification":"[status]" // [status] is email|none
		}

*Availability:* iOS, Android, Win/Mac

#### Add a folder to notifications
*mflyCommands.js:* mflyCommands.addNotification(_id_) <br>
*Description:* Instruct the app to subscribe the folder for notifications. Response code 200 if successful, 304 if folder was already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS, Android, Win/Mac

#### Remove a folder from notifications
*mflyCommands.js:* mflyCommands.removeNotification(_id_) <br>
*Description:* Instruct the app to unsubscribe the folder from notifications. Response code 200 if successful, 304 if folder was not already subscribed, and 500 if an error occurred.<br>
*Availability:* iOS, Android, Win/Mac

### GPS

#### Request latitude/longitude ####
*mflyCommands.js:* mflyCommands.getGpsCoordinates() <br>
*Description:* Extensions can request latitude and longitude from the app. This is useful for apps that connect to external services to report lat/lon, or use the Haversine (Big Earth) formula to calculate rough distances.<br>

	Example:
		Response:
		{
		  latitude: 41.851231,
		  longitude: -87.652345
		}
*Availability:* iOS, Android


### Get Sync Status

#### Get the current sync status ####
*mflyCommands.js:* mflyCommands.getSyncStatus() <br>
*event:* document.mflySyncStatus<br>
*Description:* Extensions can get the current sync status from the app. This information can be retrieved by calling the URL or listening to the event published by the app. Extensions can leverage information about content being snced and wait to load certain parts of the UI. Extensions can also display progress indicators and spinners based on the syncStatus.  <br>

	Example:
		Response:
		{
		  complete: 26,
		  total: 28,
		  isrunning: true
		}
*Availability:* iOS, Android, Win/Mac

### Log out

#### Instruct the app to log out ####
*mflyCommands.js:* mflyCommands.logout() <br>
*Description:* When an Extension makes this call, the app will log the current user out by de-authenticating them. The calling Extension will be closed and the app will leave the user bound (user can enter their PIN to log back in). The app will navigate to the log in screen. If the customer environment does not support PIN, you may receive a 500 error.<br>

*Availability:* iOS, Android, Web Viewer, Win/Mac


### Get Online Status

*mflyCommands.js:* mflyCommands.getOnlineStatus() <br>

*Description:* Check whether the device is online<br>
Example:

	Response:
	{
	  "status": "online"
	}
Respnose when the device is offline:

	{
	  "status": "offline"
	}

*Availability:* iOS, Android, Win/Mac

### Posting Arbitrary Events

Extensions can post arbitrary events that are recorded by the Mediafly backend for analytics purposes. An event is identified by a key which is a string. Optinally, you can submit additional information about the items as the properties parameter.

*mflyCommands.js:* mflyCommands.postEvent(key, properties) <br>

*Description:* Post an event to the app<br>
Example:
	
	mflyCommands.postEvent('myEvent', { eventInfo: 'user performed action' })
	Response: null

*Availability:* iOS, Android, Viewer, Win/Mac

### Opening external web links

In some cases Extensions need to open web links. mflyCommands provides the `openLink` command to handle this in a cross platform way. Following is the behavior of this command per platform:

- iOS: Opens the link in a modal with a WebView. The user can dismiss the modal and return back to the Extension.
- Viewer: Opens the link in a new browser tab
- Desktop: Opens the link in a new browser tab

*mflyCommands.js:* mflyCommands.openLinke(link) <br>

*Description:* Open an external web link<br>
Example:
	
	mflyCommands.openLink('https://www.mediafly.com')
	Response: null

*Availability:* iOS (v722), Viewer, Win/Mac

----------

## Saving and retrieving key/value data

Extensions can save data to and retrieve data from the app. This is useful for persisting data within or between Extensions. Data is synchronized with Mediafly servers and it is available across all platforms for an extension to use. Key/value data is isolated by user and by app. Two users cannot share keys, and two apps cannot share keys. After saving, you can be sure that your data will be saved if the user restarts the app or device. You can use any key you wish, which provides a lot of flexibility, but please consider providing a namespace within your keys. Otherwise, two Extensions may find ways to clobber each others’ keys.

Example: If a user saves key=value on Device A, then loads the Extension on Device B, Device B will pull down key=value onto Device B as the Extension loads.

### Save data
To save data to the app container, call mflyCommands.putSyncedValue(_key, value_), where _key_ is the key you wish to save, and _value_ is the value for that key.

*Example:*<br>
This example saves key/value data, using mflyCommands.js.

	var key = $('#key').val();
	var value = { name: ‘abc’, value: ‘def’ };

    mflyCommands.putSyncedValue(key, value)
        .done(function(data, status) {
            // Success! Do something.
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

HTTP response codes:

* Response of 200 OK indicates a successful save to an existing key
* Response of 201 Created indicates a successful save to a new key
* Response of 400 Bad Request is returned if the value has not been previously saved

*Availability:* iOS, Android, Web Viewer, Win/Mac

### Retrieve data

You can retrieve value information from keys in multiple ways using mflyCommands.js.

Alternatively, you can make the calls directly with AJAX, but we strongly recommend using mflyCommands instead. When making calls directly,

* Response of 200 OK indicates a successful get of an existing key. The body of the response will contain the values indicated below.
* Response of 404 Not Found indicates that the key does not exist


#### Single key

To get data for a specific key from the app container, use mflyCommands.getSyncedValue(_key_).

*Example:*

	// Assume key 'abc' has been set to value '123'.

    mflyCommands.getSyncedValue('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: 123

On successful retrieval, data will contain the value of the key.


#### All keys

To get data for all keys from the app container, use mflyCommands.getSyncedValues().

*Example:*

	// Assume key 'abc' has been set to value '123', and key 'def' has been set to value '456'.

    mflyCommands.getSyncedValues()
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: { "abc": "123", "def": "456" }


On successful retrieval, body will contain a JSON object enumerating all keys and values, as shown above.


#### Key prefix

To get data for all keys that begin with X, use mflyCommands.getSyncedValues(_prefix_). On successful retrieval, data will contain a JSON object enumerating all keys and values where the keys begin with X.

*Example:*

	// Assume:
	//   key 'snow' has been set to value 'white',
	//   key 'snowball' has been set to value 'round',
	//   key 'fire' has been set to value 'red'

    mflyCommands.getSyncedValues('snow')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: { "snow": "white", "snowball": "round" }

    mflyCommands.getSyncedValues('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log(data);
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: {}


*Availability:* iOS, Android, Web Viewer, Win/Mac


### Delete data

To delete a key/value pair by key from the app container, use mflyCommands.deleteSyncedKey(_key_).

*Example:*

	// Assume key 'abc' has been set to value '123'.

    mflyCommands.deleteSyncedKey('abc')
        .done(function(data, status) {
            // Success! Do something.
            console.log('abc has been deleted');
        })
        .fail(function(deferred, status) {
            // Error! Do something.
        });

    // Console output: 'abc has been deleted'

*Availability:* iOS, Android, Web Viewer, Win/Mac



----------

## Emailing content from Extensions

Extensions can either send emails using Mediafly's backend, or use a native email client if one available on the device running the extension.

### Sending emails using an email client
Extensions can invoke the native email client on the device.

*Example*

    mflyCommands.composeEmail({
    	subject: '[email subject]',
    	body: '[email body]',
    	attachments: [{
    		filename: "[name of the attachment. for example: Presentation.pdf]",
    		dataUrl: "[Base64 encoded URL]"
    	}]
    }).done(function(data, status) {
        // Success! Do something.
        console.log('email has been sent successfully');
    })
    .fail(function(deferred, status) {
        // Error! Do something.
        console.log('error sending email');
    });

*Availability*: iOS, Android

### Sending emails using Mediafly's backend
Extensions can have Mediafly's backend send emails with attachments. Attachments are Base64 encoded URLs.
Even though emails are sent from Mediafly's servers, they have "Reply-To" field populated with the curent logged in user's email address.

*Example*

    mflyCommands.sendEmail({
    	to: '[comma separated email addresses]',
    	cc: '[comma separated email addresses]',
    	bcc: '[comma separated email addresses]',
    	subject: '[email subject]',
    	body: '[email body]',
    	attachments: [{
    		filename: "[name of the attachment. for example: Presentation.pdf]",
    		dataUrl: "[Base64 encoded URL]"
    	}]
    }).done(function(data, status) {
        // Success! Do something.
        console.log('email has been sent successfully');
    })
    .fail(function(deferred, status) {
        // Error! Do something.
        console.log('error sending email');
    });

*Availability*: iOS (654), Android (2.29.07), Web Viewer, Win/Mac (1.0.92)

----------

## Embedding images, other Extensions, or pages of documents

### Overview
Extensions have the ability to embed items that exist in the app into it.  This is used for four primary purposes:

* Retrieve an item that contains structured data (JSON, XML, CSV)
* Embed an item that is an image and render it as an image in this Extension
* Embed another Extension into an iframe on this Extension
* Embed a page from a PowerPoint, PDF, or Word document

Embedding other types (audio, video, URLs) will have unexpected results. The best user experience will be provided to the user only if embedding is limited to data, images, documents and Extensions.

* To retrieve a data item, use ```mflyCommands.getData(id)```, where _id_ is the ID of the data item you wish to obtain.
* To embed an image, create an ```<img>``` that refers to a loading image. In JavaScript, call ```mflyCommands.embed($element, id)```, where _$element_ is a jQuery reference to the img element, and _id_ is the Airship id of the image item.
	* You can also use `mflyCommands.embedImage($element, id, options)` and supply dimensions for the image to be embedded (only supported on Web Viewer). This can be particularly helpful to scale down high resolution images to so browsers can load them quicker. _options_ are defined as:
		* _size_ (string):   format is WxH (e.g. "200x200")
		* _width_ (number): Width of the image
		* _height_ (number): Height of the image
		* _maxWidth_ (number): Maximum width of the image
		* _maxHeight_ (number): Maximum height of the image
		* _rotate_ (number): Degree of rotation to apply to the image. Values of 0, 90, 180, 270, or 360 are acceptable.
* To embed another Extension, construct an ```<iframe>```. In JavaScript, call ```mflyCommands.embed($element, id)```, where _$element_ is a jQuery reference to the iframe element, and _id_ is the Airship id of the other Extension.
* To embed the page from a document as an image, create an ```<img>``` that refers to a loading image. In JavaScript, call ```mflyCommands.embed($element, id, page)```, where _$element_ is a jQuery reference to the img element, _id_ is the Airship id of the image item, and _page_ is the page number that you wish to embed.
* Please note: if you are embedding an image asset for the web Viewer, please consider directly accessing the ```resourceUrl``` attribute instead of using ```mflyCommands.embed```. This will improve loading speed, caching, and reduce network requests. See the [optional changes](#optional_changes) section below.


### Technical details

Here are the HTTP response codes by platform. Unfortunately, these are not all the same because of differing security limitations across platforms.

State | iOS | Android
------------ | ------------- | ------------
Item downloaded and ready | HTTP response code 301 (Redirect). Browser should automatically redirect to correct URL. Body contains retrieved data.  | HTTP response code 200. Body contains retrieved data.
Item not already downloaded | HTTP response code 202 (Accepted), with Retry-After header set to number of seconds the Extension should wait before trying again. If not set, default to a reasonable value, like 5. | HTTP response code 200, with an empty body. Extension should retry after a reasonable number of seconds, like 5.
Item not found | HTTP response code 404 (Not found) | HTTP response code 404 (Not found)


Please note that asking too many times may put the Downloader into a race condition, as each request will trigger the Downloader to poll and possibly start downloading again.  So, please be considerate and retry no more than once every few seconds.

As you can see, embedding requires care. You cannot assume that the embedded item is available yet, because it may be behind some longer items in the Downloader. Instead, you have to try to fetch the content and monitor response codes. This is why we suggest using mflyCommands.js as shown in the example above.

*Availability:* iOS, Android, Web Viewer, Win/Mac

### Reporting when embedding pages

Our Reporting system automatically captures who viewed what, when, and where, when using the native app that sits underneath the Extension. However, when you embed pages, the app loses that information and is unable to tell our systems about view data.

To inform our Reporting system of a page view, post a page view after the page has been shown to the user:

*mflyCommands.js:* mflyCommands.postPageView(_item ID_, _page number_) <br>
*Description:* Posts the page view to the Mediafly Reporting system. Item ID is the id of the item, as seen in Airship. Page number is the page that was displayed (starting at page 1).
*Availability:* iOS

### Reporting

When building an Extension that implements its own navigation instead of relying on the, the Extension is responsible for notifying Mediafly's Reporting system.

To inform our Reporting system of various actions:

*mflyCommands.js:* mflyCommands.postAction(options) <br>
*Description:* Posts the action to the Mediafly Reporting system. Options object supplied contains:
  - type (required): Type can one of the following: "collection", "document", "navigation" or "search"
  - id (required when type is not "search"): Item ID is the id of the item, as seen in Airship
  - term (required when type is "search"): search term used to perform a search

*Availability:* iOS, Android, Web Viewer, Win/Mac


-----

## Take a picture on iOS

Extensions have the ability to take open iOS's native camera app and take a picture using the following command.

* To initiate taking a picture, use ```mflyCommands.takePicture().then(dataUrl => ...)```
* Upon successful completion returns a dataURL representation of the picture taken.

-----

## Support widescreen on iOS

### Control

When a user plugs into an HDMI dongle or connects via AirPlay, they can change the app to run in Second Screen mode.  When in this mode, the app can paint a canvas that uses the entire screen resolution (whereas, Mirroring mode restricts the TV/projector to a 4x3 output and adds black bars on the left and right).

To support this mode in an Extension, the Extension must do a few things.

1. On the app call to mflyDataInit, the object returned must have mflyWideScreenSupport set:

		{
		  “mflyWideScreenSupport”: true
		  ...
		}

	Other parameters may exist in this JSON object, of course, but mflyWideScreenSupport is the key to initiate proper app support for widescreen.

2. If the app has sent mflyWideScreenSupport=true in mflyDataInit, the Extension should expect to receive a call from the app to mflyWideScreen(useWideScreen), where useWidescreen is either true or false.


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

3. (Optional) Create a button that the user taps to bring up the Second Screen Options dialog. This button in the Extension would mimic the button in the native app that allows the user to switch between iOS, Mirroring, and Second Screen. When tapped, the Extension should invoke:

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

When the iPad is displaying an Extension only on its canvas, the WebKit container is 1024 x 568.  When the iPad is projecting to Second Screen, the WebKit container shrinks to 960 x 540. This allows for the browser to efficiently scale up when connected to 720p and 1080p TVs.

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

## Getting temporary credentials to communicate with Launchpad and Accounts APIs

Launchpad API allows abilities such as uploading files, and updating items in Airship. If an Extensions wishes to leverage these capabilities, it need to be authenticated. Extensions can call `mflyCommands.getCredentials()` function to retrive the necessary credentials to access these APIs. The response from `getCredentials` contains a short lived access token. It is the Extension's responsibility to get a new token when the existing token expires (If an API endpoint returns a `401` HTTP response code, a new token should be retrieved). In addition to the token, `environmentId` is also returned which is necessary to make calls to the Launchpad API.


*Example*

	mflyCommands.getCredentials();
	
	Result:
	{
		"accessToken": "token",
		"environmentId": "environmentId",
	}

*Availability*: iOS (678), Android (2.29.47), Web Viewer, Win/Mac (1.0.101)

-----

## Obsolete commands
The following commands are now obsolete and should not be used in new implementations. They are still supported in existing extensions.

#### Show control bars
*mflyCommands.js:* mflyCommands.showControlBars() <br>
*Description:* Shows the control bars. <br>
*Availability:* iOS, Android

#### Hide control bars
*mflyCommands.js:* mflyCommands.hideControlBars() <br>
*Description:* Hides the control bars. <br>
*Availability:* iOS, Android

#### Browse
*mflyCommands.js:* mflyCommands.browse() <br>
*Description:* Multiple Extensions can layer on top of each other, to represent different layers of hierarchy. The developer may wish to allow the user to navigate the hierarchy using the core Mediafly app with the default grid/list view, e.g. if a “More” or “Browse” button is presented.  This URL closes all Extensions in the stack and takes the user to that hierarchy in the core app if called. <br>
*Availability:* iOS

#### Show Settings
*mflyCommands.js:* mflyCommands.showSettings(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the settings dialog at x-coord, y-coord coordinates with the specified width and height. <br>
*Availability:* iOS, Android

#### Show User Management Dialog
*mflyCommands.js:* mflyCommands.showUserManagement(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the User Management dialog, which allows the user to login or manage users. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS.<br>
*Availability:* iOS, Android

#### Refresh the contents of the app
*mflyCommands.js:* mflyCommands.refresh() <br>
*Description:* Trigger the app to refresh its content. If items have changed, this should trigger mflySync calls appropriately. <br>
*Availability:* iOS, Android

#### Show "Collections"
*mflyCommands.js:* mflyCommands.showCollections(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the Collections dialog at x-coord, y-coord coordinates with specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS.<br>
*Availability:* iOS, Android

#### Show Downloader
*mflyCommands.js:* mflyCommands.showDownloader(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the Downloader dialog at x-coord, y-coord coordinates with specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS, Android, Win/Mac

#### Filter

Filter is deprecated in favor of mflyCommands.search() using the advanced query syntax.

Using mflyCommands.search() with metadata fields:

	mflyCommands.search('fieldName EQUALS "Field Value"')

We often find that developers need to identify folders and items that match a specific set of metadata. Often they want to use custom metadata fields as a way to drive hierarchy and categorization, and not rely on the actual hierarchy for that purpose. It makes sense in some use cases, because the people who will be managing content are often different from how the Extension should be rendered.

To support this, use Filter. Filter accepts up to three key=value pairs as JSON parameters, and returns a constrained list of folders and items that match ALL of the key=value pairs provided. The return value is a JSON Array of JSON Objects that match the various folders and items with the specified filter conditions, or an empty JSON Array if none match.

*mflyCommands.js:* mflyCommands.filter(_obj_) <br>
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

*Availability:* iOS, Android, Web Viewer, Win/Mac

#### Show Search dialog
*mflyCommands.js:* mflyCommands.showSearch(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the search dialog at x-coord, y-coord coordinates with the specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS

#### Show "Collections"
*mflyCommands.js:* mflyCommands.showCollections(_x-coord, y-coord, width, height_) <br>
*Description:* Shows the Collections dialog at x-coord, y-coord coordinates with specified width and height. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS.<br>
*Availability:* iOS, Android


#### Show "Add to Collection"
*mflyCommands.js:* mflyCommands.showAddToCollection(itemId, _x-coord, y-coord, width, height_) <br>
*Description:* Shows the Add To Collections dialog at x-coord, y-coord coordinates with specified width and height for the specified item. Parameters x-coord, y-coord, width, and height are all optional, and only work with iOS. <br>
*Availability:* iOS

#### Show the notifications manager
*mflyCommands.js:* mflyCommands.showNotificationsManager(_x-coord, y-coord, width, height_) <br>
*Description:* Display the notifications manager. Parameters x-coord, y-coord, width, and height are all optional. <br>
*Availability:* iOS

#### Local key/value storage

This method stores data locally and does not synchronize with our servers. If a user uninstalls the app (iOS, Android, Windows, Mac) clears their local storage (web), or uses a private/incognito window (web) the keys cannot be retrieved. It is recommended to use the synced key/value storage mechanism over local storage.

##### Save data (local)
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

*Availability:* iOS, Android, Web Viewer, Win/Mac

##### Retrieve data (local)

You can retrieve value information from keys in multiple ways using mflyCommands.js.

Alternatively, you can make the calls directly with AJAX, but we strongly recommend using mflyCommands instead. When making calls directly,

* Response of 200 OK indicates a successful get of an existing key. The body of the response will contain the values indicated below.
* Response of 404 Not Found indicates that the key does not exist


##### Single key

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


##### All keys

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


##### Key prefix

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


*Availability:* iOS, Android, Web Viewer, Win/Mac


##### Delete

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

*Availability:* iOS, Android, Web Viewer, Win/Mac


## Other useful information

### Stopping rubber band effects
Since Extensions are embedded web pages, on some (particularly iOS) devices they tend to have a “rubber band” effect.  E.g. if touch-and-hold a part of the Extension, then drag it, the entire screen will follow your finger, then "snap back" after you let go.  This does not feel appropriate for an Extension, as users want the look and feel to be more native.

To address this, we recommend using [Hammer.js](http://eightmedia.github.io/hammer.js/), a mobile-focused touch gesture library.

We have created a detailed example within Mediafly Extensions Examples that demonstrates this, [here](https://github.com/mediafly/extensions-examples/tree/master/examples/Swipe).

### Smoother scrolling
By default, iOS scrolling in the UIWebView is exactly controlled by your finger. You can drag the web view down and up, but you cannot "throw" the page in a direction. Furthermore, if you have a lot of large images on the page, scrolling can become choppy.

To alleviate this, consider implementing ```-webkit-overflow-scrolling: touch``` on your DOM nodes that handle scrolling. When set to touch, UIWebView uses native-style scrolling on your node.

### Deep Linking to an extension (iOS)

iOS supports invocation URLS. The iOS app will pass all supplied params to the Extension and the Extension can retrieve them using JavaScript window.location.search.

Mediafly (Next):
Example: [https://open.mediafly.com/testenv123/item/cbdbd823c1024f0f9d71d20f99788a11product456145?id=0010191012](https://open.mediafly.com/testenv123/item/cbdbd823c1024f0f9d71d20f99788a11product456145?id=0010191012)

This URL has the following format: https://open.mediafly.com/[companyCode]/item/[slug]?[params...]

Mediafly Classic:
Example: [mediafly://9cf282320e6340ee8b830e5376d54531product265958?param1=1&param2=2](mediafly://9cf282320e6340ee8b830e5376d54531product265958?param1=1&param2=2)
This URL has the following format: [companyCode]://[slug]?[params...]


### Share link invokation (iOS)
By default, share links are created with the format of [https://assets.mediafly.com/l/ABC123](https://assets.mediafly.com/l/ABC123), where ABC123 is a short code generated at the time the share link is created.

Custom domain support can be enabled on a per-environment basis to ensure that when a share link is tapped that it opens to a specific app. This can be helpful when a user has multiple derivative apps installed. In this case the share links for the environment will look like [https://{companyCode}.mediaf.ly/l/ABC123](https://{companyCode}.mediaf.ly/l/ABC123).

Supported Formats Reference:

Mediafly (Next):

- https://assets.mediafly.com/l/ABC123
- https://assets.mediafly.com/wl/ABC123 (/wl is used for Workspace items)
- (https://{*}.mediafly.com/l/ABC123 (A server-side update is required to support new domains)

Mediafly Classic:

- [https://assets.mediafly.com/l/ABC123](https://assets.mediafly.com/l/ABC123)
- A limited number of custom domains are supported on build 748 or higher. These follow the format of: https://{companyCode}.mediaf.ly/l/ABC123


### Running the extension on iOS Next
iOS Next bundles a copy of mflyCommands itself. Below are the steps necessary to support running an extension on iOS Next.

1. Update your extension with the latest version of mflyCommands.
2. Add the following `script` tag to your `index.html`. `<script type="text/javascript" src="bootstrap_mflyCommands.js"></script>`. This script is provided by the app and does not need to be bundled with the extension. Be sure to add this script tag in an order such that mflyCommands script is loaded after it.
3. If it is an Angular based extension it must whitelist the protocol `mextbinary://` since it is used to serve resources such as images to the extension.


----------


Appendix
===============

Additional information that doesn't fit very well elsewhere.

## Constants

The following constants can be used across customer environments or within an environment:

* ```__root__```: This is the ID of the top-level folder. It is consistent across all environments and all device platforms.
* ```[environment ID]productmyitems```: This is the ID of the "My Items" folder. "My Items" is a folder that, when enabled, gives users a personal space to upload and manage their own content within the customer environment. In this case, ```[environment ID]``` is a unique 32-character string that is specific to that environment. Please contact Mediafly to obtain the string for your customers; or, simply use Get Folder on the ```__root__``` and look for My Items as the last folder in the response. In order to access My Items, the user must be logged in.
