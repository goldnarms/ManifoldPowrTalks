﻿<!---
 license: MIT License
-->

# Hosted Web Application
This plugin enables the creation of a hosted web application from a [W3C manifest](http://www.w3.org/2008/webapps/manifest/) that provides metadata associated with a web site. It uses properties in the manifest to update corresponding properties in the Cordova configuration file to enable using content hosted in the site inside a Cordova application.

**Typical manifest** 
<pre>
{
  "lang": "en",
  "name": "Super Racer 2000",
  "short_name": "Racer2K",
  "icons": [{
        "src": "icon/lowres",
        "sizes": "64x64",
        "type": "image/webp"
      }, {
        "src": "icon/hd_small",
        "sizes": "64x64"
      }, {
        "src": "icon/hd_hi",
        "sizes": "128x128",
        "density": 2
      }],
  "scope": "/",
  "start_url": "http://www.racer2k.net/start.html",
  "display": "fullscreen",
  "orientation": "landscape",
  "theme_color": "aliceblue"
}
</pre>

The W3C manifest enables the configuration of the application’s name, its starting URL, default orientation, and the icons it uses. In addition, it will update the application’s security policy to control access to external domains. 

When the application is launched, the plugin automatically handles navigation to the site’s starting URL.

Lastly, since network connectivity is essential to the operation of a hosted web application, the plugin implements a basic offline feature that will show an offline page whenever connectivity is lost and will prevent users from interacting with the application until the connection is restored.

## Installation
> **Note:** These are temporary installation steps until the plugin is published to the Cordova registry.

`cordova plugin add https://github.com/manifoldjs/ManifoldCordova.git`

> **IMPORTANT:** Before using the plugin, make sure to copy the W3C manifest file to the **root** folder of the Cordova application, alongside **config.xml**, and name it **manifest.json**.

## Design
The plugin behavior is mostly implemented at build time by mapping properties in the W3C manifest to standard Cordova settings defined in the **config.xml** file. 

This mapping process is handled by a hook that executes during the **before_prepare** stage of the Cordova build process. The hook updates the **config.xml** file with values obtained from the manifest. 

The plugin hook also handles downloading any icons that are specified in the manifest and copies them to the application’s directory, using their dimensions, and possibly their pixel density, to classify them as either an icon or a splash screen, as well as determining the platform for which they are suitable (e.g. iOS, Android, Windows, etc.). It uses this information to configure the corresponding icon and splash elements for each supported platform.

## Getting Started

The following tutorial requires you to install the [Cordova Command-Line Inteface](http://cordova.apache.org/docs/en/4.0.0/guide_cli_index.md.html#The%20Command-Line%20Interface).

### Hosting a Web Application
The plugin enables using content hosted in a web site inside a Cordova application by providing a manifest that describes the site.

1. Create a new Cordova application.  
	`cordova create sampleapp yourdomain.sampleapp SampleHostedApp`

1. Go to the **sampleapp** directory created by the previous command.

1. Download or create a [W3C manifest](http://www.w3.org/2008/webapps/manifest/) describing the website to be hosted by the Cordova application and copy this file to its **root** folder, alongside **config.xml**. If necessary, rename the file as **manifest.json**.

	> **Note:** You can find a sample manifest file at the start of this document. 
 
1. Add the **Hosted Web Application** plugin to the project.  
	`cordova plugin add https://github.com/manifoldjs/ManifoldCordova.git`

	> **Note:** These are temporary installation steps until the plugin is published to the Cordova registry.

1. Add one or more platforms, for example, to support Android.  
	`cordova platform add android`

1. Build the application.  
	`cordova build`

1. Launch the application in the emulator for one of the supported platforms. For example:  
	`cordova emulate android`

	> **Note:** The plugin updates the Cordova configuration file (config.xml) with the information in the W3C manifest. If the information in the manifest changes, you can reapply the updated manifest settings at any time by executing prepare. For example:  
	`cordova prepare`

### Offline Feature
The plugin implements a basic offline feature that will show an offline page whenever network connectivity is lost. By default, the page shows a suitable message alerting the user about the loss of connectivity. To customize the offline experience, a page named **offline.html** can be placed in the **www** folder of the application and it will be used instead.

1. To test the offline feature, interrupt the network connection to show the offline page and reconnect it to hide it. 

	> **Note:** The procedure for setting offline mode varies depending on whether you are testing on an actual device or an emulator. In devices, you can simply set the device to airplane mode. In the case of simulators there is no single method. For example, in [Ripple](http://ripple.incubator.apache.org/), you can simulate a network disconnection by setting the Connection Type to 'none' under Network Status. On the other hand, for the iOS Simulator, you may need to physically disconnect the network cable or turn off the WiFi connection of the host machine.

1. Optionally, replace the default offline UI by adding a new page with the content to be shown while in offline mode. Name the page **offline.html** and place it in the **www** folder of the project.

### Icons and Splash Screens
The plugin uses any icons specified in the W3C manifest to configure the Cordova application. However, specifying icons in the manifest is not mandatory. If the W3C manifest does not specify any, the application will continue to use the default Cordova icon set or you can enter icon and splash elements manually in the **config.xml** file and they will be used instead. However, be aware that the plugin does replace any such elements if it finds an icon in the manifest that matches its size. Typically, manifest entries reference icons hosted by the target site itself and should reference suitable icons for each platform supported by the application, as described in the [W3C spec](http://www.w3.org/2008/webapps/manifest/#icon-object-and-its-members). The plugin takes care of downloading the corresponding files and copies them to the correct locations in the project.

When you run **cordova prepare**, the plugin will download from the hosted site all image assets in the manifest, if they are available, and it will store them inside the Cordova project using their relative paths as specified in the manifest. You can add any icons missing from the site or replace any icons that were downloaded by simply copying them to the correct location inside the project always making sure that they match the relative path in the manifest. Once the images are in place, building the project will copy the icons to each platform specific folder at the correct locations.

For example, the following manifest references icons from the _/resources_ path of the site, for example, _/resources/android/icons/icon-36-ldpi.png_. The plugin expects the corresponding icon file to be stored in the same path relative to the root of the Cordova project.

<pre>
{
    "name": "Super Racer 2000",
    "short_name": "Racer2K",
    "icons": [
        {
            "src": "/resources/android/icons/icon-36-ldpi.png",
            "sizes": "36x36"
        },
        {
            "src": "/resources/android/icons/icon-48-mdpi.png",
            "sizes": "48x48"
        },
        ...
        {
            "src": "/resources/ios/icons/icon-40-2x.png",
            "sizes": "80x80"
        },
        ...
        {
            "src": "/resources/windows/icons/Square44x44Logo.scale-240.png",
            "sizes": "106x106"
        },
        ...
    ],
    "scope": "*",
    "start_url": "http://wat-docs.azurewebsites.net/",
    "display": "fullscreen",
    "orientation": "portrait"
}
</pre>

### URL Access Rules
For a hosted web application, the W3C manifest defines a scope that restricts the URLs to which the application can navigate. Additionally, the manifest can include a proprietary setting named **mjs_urlAccess** that defines an array of access rules, each one consisting of a _url_ attribute that identifies the target of the rule and a boolean attribute named _external_ that indicates whether URLs matching the rule should be navigated to by the application or launched in an external browser.

Typically, Cordova applications define access rules to implement a security policy that controls access to external domains. The access rules must not only allow access to the scope defined by the W3C manifest but also to external content used within the site, for example, to reference script files hosted by a  CDN origin. It must also handle any URLs that should be launched externally. 

To configure the security policy, the plugin hook maps the scope and URL access rules in the W3C manifest (**manifest.json**) to suitable access elements in the Cordova configuration file (**config.xml**). For example:

**Manifest.json**
<pre>
...
   "scope":  "http://www.xyz.com/", 
   "mjs_urlAccess":  [ 
     { "url": "http//googleapis.com/*" },
     { "url": "http//wat.codeplex.com/", "external": true }
   ]
...
</pre>

**Config.xml**
<pre>
...
&lt;access origin="http://www.xyz.com/*" /&gt;
&lt;access origin="http://googleapis.com/*" /&gt; 
&lt;access origin="http://wat.codeplex.com/" launch-external="yes" /&gt;
...
</pre>

## Methods
Even though the following methods are available, it should be pointed out that calling them is not required as the plugin will provide most of its functionality by simply embedding a W3C manifest in the application package.

### loadManifest
Loads the specified W3C manifest.
 
`hostedwebapp.loadManifest(successCallback, errorCallback, manifestFileName)`
  
|**Parameter**     |**Description**                                                            |
|:-----------------|:--------------------------------------------------------------------------|
|_successCallback_ |A callback that is passed a manifest object.                               |
|_errorCallback_   |A callback that executes if an error occurs when loading the manifest file.|
|_manifestFileName_|The name of the manifest file to load.                                     |

### getManifest
Returns the currently loaded manifest.

`hostedwebapp.getManifest(successCallback, errorCallback)`

|**Parameter**     |**Description**                                                            |
|:-----------------|:--------------------------------------------------------------------------|
|_successCallback_ |A callback that is passed a manifest object.                               |
|_errorCallback_   |A callback that executes if a manifest is not currently available.         |

### enableOfflinePage
Enables offline page support.

`hostedwebapp.enableOfflinePage()`

### disableOfflinePage
Disables offline page support.

`hostedwebapp.disableOfflinePage()`

## Supported Platforms
Windows 8.1  
Windows Phone 8.1  
iOS  
Android  
