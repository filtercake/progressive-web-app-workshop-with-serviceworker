Progressive Web App Workshop with ServiceWorker

# Welcome to the Progressive Web App Workshop!



# Overview

This document, along with today's instructors, will be your guide for today's workshop. The structure of the day is:

**  9:00am - 10:00am**:  Registration & Breakfast

**10:00am - 10:30am**: Introduction to PWA and Workshop Setup

**10:30am - 12:30am**: Deep dive into Service Workers and going Offline First

**12:30pm - 01:00pm**: Lunch Break

**1:30am - 2:00pm**: App Shell Architecture and using Service Worker Toolbox

**2:00pm - 2:30pm**: Add-to-Homescreen and Splash Screen

**            ** **2:30pm - 3:30pm**: Sending Push Notifications 

            ** 3:30pm - 4:00pm:** QnA - Cross Browser PWA, Solving for SEO etc

Post-workshop wind-down from 4:30pm; drinks at:

[Tied House ](https://goo.gl/maps/rSy2MKCXScB2)

[954 Villa St, Mountain View, CA 94041](https://goo.gl/maps/rSy2MKCXScB2)

## Required Tools

This workshop helps you build a largely static PWA, but some tooling is required, including:

* [Git](https://git-scm.com/downloads)

* [Node.js](https://nodejs.org/en/download/) >= v0.12.7 and working [NPM](https://www.npmjs.com/) (bundled with Node)

* [Chrome 47](https://www.google.com/chrome/browser/desktop/) or above

    * Suggested: a separate profile or side-by-side install (e.g. [Canary](https://www.google.com/chrome/browser/canary.html)) for debugging

**Optional but encouraged**: [Firefox Developer Edition (latest)](https://www.mozilla.org/en-US/firefox/developer/) and [Opera Beta](http://www.opera.com/computer/beta)

There will be time to verify your setup in the first session.

## Workshop Chat Room

The best way to learn is to teach! 

If you find yourself ahead in the workshop, you're encouraged to help your fellow attendees. We'll be using IRC for questions and help:

Join #pwahackday on irc.freenode.net (or  use [https://webchat.freenode.net/](https://webchat.freenode.net/) and join pwahackday room )

If you do not have an IRC client, [IRCCloud](https://www.irccloud.com/) is a free, web-based IRC client that works well.

# Why PWA?

To summarize:

It’s statistically much easier to get a user to visit your site than to try your app. On the web users can get to a new site with only a single tap, as opposed to a number of steps involved in installing a native app.

* ComScore recently published concrete numbers about this, citing how the top 1000 mobile web sites have on average 2.5X more monthly active users than the top 1000 native apps.

* ![image](https://cloud.githubusercontent.com/assets/170145/14200140/2b4ab23c-f7e9-11e5-87ce-5d48200fa1ff.png)


Having a strong web presence allows you to reach more people than native alone

* Flipboard launched on the web for the first time last year and reported their monthly actives increased by 75%

* Facebook, speaking at their @Scale conference announced that "Mobile first cannot mean native only"

Historically, the web has struggled to drive the same levels of user engagement as native apps, for four reasons

* Home screen icons

* Push notifications

* Performance

* Notably, survivorship bias: when we look at native users we often forget that if our app store listing has 10% conversion, that we’re only looking at the best 10% of users, and ignoring the 90% who drove no engagement at all.

In this workshop we hope to demonstrate how the problems of home screen icons, push notifications and performance are solved by a set of new technology that has together gained the name ‘progressive web apps’.

It’s also interesting that this technology is growing in usage insanely fast: at Chrome Dev Summit in November we announced Chrome is delivering 350M push messages a day and service worker is controlling 2B page loads a day and those stats are now both an order of magnitude higher,

Also note that you have full programmable control over your updating mechanisms, and can prevent users from getting stuck on old versions. Cool, huh?

Lets begin!

# Step 1: Introduction & Setup 

* Introduction to the workshop

* Machine setup and configuration

* What is a Progressive Web App and why should we build one?

* Get the code:

    * Clone git repo from:[https://github.com/owencm/ModernWebSummitWorkshop](https://github.com/owencm/ModernWebSummitWorkshop):
%> git clone [https://github.com/owencm/ModernWebSummitWorkshop.git](https://github.com/owencm/ModernWebSummitWorkshop.git) structure

* Install dependencies

    * %> cd ModernWebSummitWorkshop
%> npm install

* Running the app

    * %> node server

    * Verify that this works by visiting [http://localhost:8000/step1/](http://localhost:8000/step1/)

* Set up [local device debugging](https://developers.google.com/web/tools/chrome-devtools/debug/remote-debugging/remote-debugging) (optional, but encouraged)

    * If you have an Android phone, visit Setting and Enable USB Debugging

    * Open a new Chrome tab and visit
chrome://inspect

    * Plug your phone in over USB and verify that the device appears in the list

    * Once this is verified, use port-forwarding to map port 8000 to localhost:8000, which will allow you to access the PWA on your device. Or use `adb reverse tcp:8000 tcp:8000`.

# Step 2: Service Workers, Offline First, & App Shells

*Tip: Since service workers will cache your app.js file, you may find refreshing doesn’t cause the page to run your new code. To avoid this, always refresh using cmd+shift+r on OSX, or ctrl+shift+r on Windows and Ubuntu.** In Firefox you may need to close and re-open the tab.*

### Introduction to Service Workers

#### Intro to SW debugging tools, lifecycle, updates

### What is Offline First?

### Registering a Service Worker

Registering a Service Worker requires both a Service Worker script (preferably in the root of your project) and a corresponding call to navigator.serviceWorker.register()

To do this, ensure that you:

* Add an empty file for your Service Worker
%> cd step2
%> touch service-worker.js

* Add the following to the bottom of your scripts/app.js file:

if('serviceWorker' in navigator) {

  navigator.serviceWorker

           .register('./service-worker.js')

           .then(function(){ 

              console.log('Service Worker Registered'); 

            });

}

* Verify that this has worked by visiting Chrome Devtools (right-click > Inspect Element or F12)

    * In Chrome 48 and above, visit the Resources panel and look for "Service Workers" in the left-hand panel

    * In older versions of Chrome, visit chrome://serviceworker-internals to understand the current state of the system

    * In both cases, visit the index of your step2 project and observe that you now have a Service Worker in the "Active" state

* To verify this in Firefox Dev Tools, use Firefox Developer Edition

    * Open a new tab and visit about:debugging#workers and ensure that a worker appears for your URL

    * If it isn’t there, it may have been killed so try refreshing the source document

### Adding events (Install, Activate, Fetch)

To build your application, you’ll need to ensure that you’re handling the important events for your application. Let's start by logging out the various parts of the lifecycle. Ensure that your Service Worker contains:

var log = console.log.bind(console);

var err = console.error.bind(console);

self.addEventListener('install', (e)=>{ log('Service Worker: Installed'); });

self.addEventListener('activate',(e)=>{ log('Service Worker: Active'); });

self.addEventListener('fetch',   (e)=>{ log('Service Worker: Fetch'); });

In Chrome you can use the Resources > Service Workers panel in DevTools to remove previous versions of the Service Worker, or use Shift-Ctrl-R to refresh your application. In Firefox developer edition you can use the controls in about:serviceworkers to accomplish the same thing.

Once the new Service Worker become active, you should see logs in the Devtools console confirm that. Refreshing the app again should show logs for each request from the application.

Tip: during development, to ensure the SW gets updated with each page refresh, go to Sources -> Service Workers, and check "Force update on page load".

### Understanding HTML APP Shells + Content Architecture

The goal of a Progressive Web App is to ensure that our app *starts fast and stays fast*. 

To accomplish this we want all resources for our Application to boot to be cached by the Service Worker and ensure they’re sent to the page without hitting the network on subsequent visits.

Service Workers are very manual. They don’t provide any automation for accomplishing this goal, but they do provide a way for us to accomplish it ourselves.

Let's replace our dummy Service Worker with something more useful:

var version = '1';

var cacheName = 'weatherPWA-v' + version;

var appShellFilesToCache = [

  './',

  './index.html',

  './scripts/app.js',

  './styles/inline.css',

  './images/icons/icon-256x256.png',

  './images/ic_notifications_white_24px.svg'

];

self.addEventListener('install', (e) => {

  console.log('[ServiceWorker] Install');

  e.waitUntil(

    caches.open(cacheName).then((cache) => {

      console.log('[ServiceWorker] Caching App Shell');

      return cache.addAll(appShellFilesToCache);

    })

  );

});

This code is relatively self explanatory, and adds the files listed above to the local cache. If something is unclear about it, ask and we can talk through it.

Next, let’s make sure that each time a new service worker is activated that we clear the old cache. You can see here that we clear any caches which have a name that doesn’t match the current version set at the top of our service worker file:

self.addEventListener('activate', (e) => {

  console.log('[ServiceWorker] Activate');

  e.waitUntil(

    caches.keys().then((keyList) => {

      return Promise.all(keyList.map((key) => {

        console.log('[ServiceWorker] Removing old cache', key);

        if (key !== cacheName) {

          return caches.delete(key);

        }

      }));

    })

  );

});

Next, we’re going to ensure that we hand back resources we know are in the cache when we get an onfetch event inside the Service Worker. Add the following to your Service Worker:

self.addEventListener('fetch', (e) => {

  console.log('[ServiceWorker] Fetch', e.request.url);

  **e.respondWith**(

    **caches.match**(e.request).then((response) => {

      **return response || fetch(e.request);**

    })

  );

});

A few things to note about this code:

* First, we respondWith() a Promise, in this case the result of an operation that searches all the caches for a matching resource for the request we were sent. To search a specific cache, caches.match() takes an optional parameter.

* Next, when we don’t get a response from the cache, it still uses the success chain in the Promise. This is because the underlying storage didn’t have an error, it just didn’t have the result we were looking for.

    * To handle this case, we check to see if the response is a truthy object

* Lastly, if we don’t get a response from the cache, we use the fetch() method to check for one from the network

We’ve just witnessed something new: we’re always responding from the local cache for resources we know we have. This is the basis for **Application Shells**!

Specifically, if you visit devtools and watch the Network timeline, you’ll now see that resources for the top-level request and essential application content are now coming from the Service Worker:

![image alt text](image_1.png)

### Programming Caching Strategies

In addition to the shell, we also will want to cache data. To do this we can add a new cache name at the top of service-worker.js, and then whenever a fetch for data is made, we add the response into the cache.

    var dataCacheName = 'weatherData-v' + version;

Now, in the fetch handler, we’ll handle data separately (you can replace your old fetch handler with this):

self.addEventListener('fetch', (e) => {

  console.log('[ServiceWorker] Fetch', e.request.url);

**  // Match requests for data and handle them separately**

** **** if (e.request.url.****indexOf****('data/') != -1) {**

**    e.respondWith(**

**      fetch(e.request)**

**        .then((response) => {**

**          return caches.open(dataCacheName).then((cache) => {**

**            cache.put(e.request.url, response.clone());**

**            console.log('[ServiceWorker] Fetched & Cached', e.request.url);**

**            return response.clone();**

**          });**

**        })**

**    );**

**  } else {**

**    // The code we saw earlier**

    e.respondWith(

      caches.match(e.request).then((response) => {

        return response || fetch(e.request);

      })

    );

**  }**

});

Or we could use a read-through cache-first variant:

// The cache-first version

self.addEventListener('fetch', (e) => {

  console.log('[ServiceWorker] Fetch', e.request.url);

  // Match requests for data and handle them separately

  if (e.request.url.indexOf('data/') != -1) {

    e.respondWith(

      caches.match(e.request.clone()).then((response) => {

        return response || fetch(e.request.clone()).then((r2) => {

**          return caches.open(dataCacheName).then((cache) => {**

            console.log('[ServiceWorker] Fetched & Cached', e.request.url);

            cache.put(e.request.url, r2.clone());

            return  r2.clone();

          });

        });

      })

    );

  } else {

    // The code we saw earlier

    e.respondWith(

      caches.match(e.request).then((response) => {

        return response || fetch(e.request);

      })

    );

  }

});

We’re now seeing two separate caching strategies at work:

* For the App Shell, we’re using a **Cache First** strategy

* For data, we’re using a **Read-through cache** strategy

Because we can use promises to handle these scenarios, we could imagine an alternative version of the same logic that tried to use the network first:

    e.respondWith(

      fetch(e.request).catch((err) => {
	  return caches.match(e.request);

      })

    );

As in the previous example, we could add read-through caching to this as well. The sky’s the limit!

We’ll see many of these patterns return later as we investigate SW Toolbox and the strategies it implements.

### skipWaiting() and Clients.claim()

Normally when you modify your service worker code, the currently open tabs continue to be controlled by the old service worker. The browser will only swap to using the new service worker at the point where no tabs (which we call clients in this context) are still open.

To avoid this, you can use a set of features: [skipWaiting](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerGlobalScope/skipWaiting) and [Clients.claim()](https://developer.mozilla.org/en-US/docs/Web/API/Clients/claim).

This code will prevent the service worker from waiting until all of the currently open tabs on your site are closed before it becomes *active*:

self.addEventListener('install', function(event) {

  event.waitUntil(self.skipWaiting());

});

And this code will cause the new service worker to take over responsibility for the still open pages.

self.addEventListener('activate', function(event) {

  event.waitUntil(self.clients.claim());

});

# Step 3: SW Toolbox

In this step we’ll learn what SW Toolbox is, why you’ll want to use it, and how to use it.

You can read about SW Toolbox at: [https://github.com/GoogleChrome/sw-toolbox](https://github.com/GoogleChrome/sw-toolbox)

Normally, you would start by installing sw-toolbox from npm, but this was already done for you in step one when you called npm install.

First we need to import SW toolbox into our service worker. In this project we’ve set up the node serve to serve a copy of the library, so you can simply import it with 

importScripts('../node_modules/sw-toolbox/sw-toolbox.js')

In the future you may want to use a build-time module bundler like webpack or browserify to include the code directly in your service worker, or you can simply host the library somewhere on your site and then call importScripts.

You can think of importScripts as a way to include any library code directly in any worker, such as a service worker. To learn more about importScripts you can check out:

* [https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers#Importing_scripts_and_libraries](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers#Importing_scripts_and_libraries) 

* [https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope/importScripts](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope/importScripts) 

Note that if you run into errors, you may have to modify the default path of node_modules/sw-toolbox/sw-toolbox.js based on your machine.

Now, to get started, we’re going to set a default cache name that sw-toolbox will use. To do this, add the following line after where we define filesToCache.

toolbox.options.cache.name = cacheName;

**Files To Cache**

SW Toolbox provides function we can call which sets up pre-caching. To use it, simply call this at the top level scope.

toolbox.precache(filesToCache);

This function works by adding a new event listener for the install event, and setting up the cache within it.

SW toolbox will automatically manage the cache specified in the default option. For additional caches we will need to manage them ourselves

**//Activate Handler for managing other caches**

self.addEventListener('activate', function(e) {

  console.log('[ServiceWorker] Activate');

  e.waitUntil(

    caches.keys().then(function(keyList) {

      return Promise.all(keyList.map(function(key) {

        console.log('[ServiceWorker] Removing old cache', key);

        if (

             (key !== dataCacheName || key !== cacheName) && 

              key.indexOf("$$$inactive$$$") === -1

         ) {

          return caches.delete(key);

        }

      }));

    })

  );

  e.waitUntil(self.clients.claim());

});

Let’s now start intercepting fetch requests for the data, which provides the list of cities and their temperature. To do this we’d like to go to the network for the latest weather, and fall back to the cache if the network isn’t available. With sw toolbox is easy, we set up a router for the /data/ path, and apply a networkFirst strategy for our data cache:

toolbox.router.get('/data/(.*)', toolbox.networkFirst, {

	cache: { name: dataCacheName }

});

(this code is added in the global scope, you no longer need to manage the actual fetch events yourself)

The second fetch handler is for the shell and the strategy we follow there is

1. read from cache and return if response is found

2. if there is no response, fetch from network

This strategy is cacheFirst. Instead of setting up a router for our app shell explicitly, we can set sw toolbox to apply a default strategy for all requests using:

toolbox.router.default = toolbox.cacheFirst;

## Tips

**[maxEntrie**s](https://github.com/GoogleChrome/sw-toolbox#cachemaxentries-number)

sw-toolbox has options to specify the number of entries to cache and uses Least Recently Used cache expiration policy. This makes managing the cache of data in your app easy.

**[maxAgeSecond**s](https://github.com/GoogleChrome/sw-toolbox#cachemaxageseconds-number)

sw-toolbox has an option to imposes maximum age for cache entries - in seconds. 

toolbox.router.get('/data', toolbox.networkFirst, {

	cache: {

		name: dataCacheName,

		maxEntries: 3,

		maxAgeSeconds: 60

	}

});

You should now have replaced all of our old custom caching code with sw toolbox code. Hopefully you can see how much easier and clearer this is.

If you get stuck at any point, feel free to swap to using the ‘Step 4’ folder which has the above done for you.

# Step 4: Add to Home Screen (A2HS) & Splash Screens

In order to become an app, your site must provide a manifest of metadata about itself. This enables features such as add to home screen and splash screens.

First create a manifest.json file within your ‘stepX’ folder, containing the following:

{

  "**name**": "Weather",

  "**short_name**": "Weather",

  "**icons**": [{

    "src": "images/icons/icon-128x128.png",

      "sizes": "128x128",

      "type": "image/png"

    }, {

      "src": "images/icons/icon-144x144.png",

      "sizes": "144x144",

      "type": "image/png"

    }, {

      "src": "images/icons/icon-152x152.png",

      "sizes": "152x152",

      "type": "image/png"

    }, **{**

**      "src": "images/icons/icon-192x192.png",**

**      "sizes": "192x192",**

**      "type": "image/png"**

**    }**, {

      "src": "images/icons/icon-256x256.png",

      "sizes": "256x256",

      "type": "image/png"

    }],

  "**start_url**": "index.html?homescreen=1",

  "display": "standalone"

}

Next, inside your index.html, we'll need to link to the manifest. This is done by adding a reference to the manifest inside your <head>:

<link rel="manifest" href="manifest.json">

## Add to Home Screen tips

* Often subtle issues are caused by invalid JSON or missing fields in a manifest. To avoid this, it’s useful to paste your manifest contents into the handy Manifest Validator tool:
[https://manifest-validator.appspot.com/](https://manifest-validator.appspot.com/).

    * Required properties for Add-to-Homescreen prompt triggering include:

        * name

        * short_name

        * icons, one of which needs to be a PNG of at minimum 144x144px, and must specify its type as "image/png"

        * start_url

* Debugging Add-to-Homescreen prompting

    * Set the following flags in your chrome profile via chrome://flags:

chrome://flags/#bypass-app-banner-engagement-checks
chrome://flags/#enable-add-to-shelf

    * These will ensure that the A2HS banner appears immediately instead of waiting for 5 minutes, and enables the feature on desktop for testing.

    * Checking you've configured Chrome correctly:

        * Visit [airhorner.com](http://airhorner.com) and click ‘install’, checking that the A2HS prompt appears

## Controlling the Add to Home Screen banner

You are able to control the timing of the add to home screen banner by listening for the beforeinstallprompt events.

First, in app.js add the following:

/*****************************************************************************

*

* Listen for the add to home screen events

*

****************************************************************************/

window.addEventListener('beforeinstallprompt', function(e) {

  console.log('[App] Showing install prompt');

  e.userChoice.then(function(choiceResult) {

    console.log(choiceResult.outcome);

  });

});

This event is fired just before the browser shows the add to home screen banner. You can call e.preventDefault() to tell the browser you’re not ready yet, for example if you want to wait for the user to click a particular button. If you call e.preventDefault(), you can call e.prompt() at any point later to show the banner.

As you can see, the e.userChoice promise gives you the ability to observe the user’s response to the banner, for analytics or to update the UI appropriately.

## Adding a Splash Screen to your app

Splash screens are automatically generated from a combination of the short_name property, the icons you specify, and colors you may include in your manifest. The manifest you've added so far will already include short_name and icons. To control the colors, add something like this to your manifest file:

  "background_color": "#3E4EB8",

  "theme_color": "#2F3BA2",

Sadly the only way to verify this today is on-device, so if you’ve set-up on-device, use port forwarding to check the results of your work on localhost!

**Note:** to debug this on-device you’ll likely also need to set chrome://flags/#bypass-app-banner-engagement-checks on your Chrome for Android

**Note:** this does not work in Chrome for iOS or any other iOS browser today

If you get stuck at any point, feel free to swap to using the ‘Step 5’ folder which has the above done for you.

# Step 5: Push Notifications

To set up push notifications, first you need to provide your Google Cloud Messaging sender ID. For this project we will be providing one, but for production you should obtain one by following the instructions [here](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web?hl=en).

Note: there are good instructions for setting up push notifications on your site at:  [bit.ly/webpushguide](http://bit.ly/webpushguide). 

First, in your manifest.json add

  "gcm_sender_id": "70689946818"

## Setting up the UI and subscribing the client

We first want to setup push when the user clicks on some UI offering push notifications. In our UI we already have a notification icon, and a click event listener for it, you can find this under the ‘*Event listeners for the UI*’ section of app.js.

The flow here is to request permission to send notifications, then get a reference to the serviceWorker, which we can use to trigger the client to subscribe with the push server.

  // Request permission to send notifications

  Notification.requestPermission().then(() => {

    // Get a reference to the SW

    return navigator.serviceWorker.ready;

  }).then((sw) => {

    // Tell it to subscribe with the push server

    return sw.pushManager.subscribe({userVisibleOnly: true});

  }).then((subscription) => {

    // Now we have a subscription!

  });

Finally, once we have the subscription we simply pass it up to the server via a network request. On your site you may also send up a user ID etc, but for now we’re just going to post it to the /push endpoint on our server.

  // Send details about the subscription to the server

  return fetch('../push', {

    method: 'POST',

    body: JSON.stringify({

      action: 'subscribe',

      subscription: subscription

    }),

    headers: new Headers({

  	'Content-Type': 'application/json'

    })

  });

The node server has been configured to send a push message immediately when a new client subscribes and again 5 seconds later.

At this point you should check in the network inspection panel that what looks like a valid subscription is being sent to the server.

## Receiving the push message in your service worker

Whenever server decides to push an event to your device, a push event will be fired in your service worker, so let's start by adding an event listener in service-worker.js.

self.addEventListener('push', function(e) {

});

When that event fired, your service worker needs to decide what notification to show. To do this, we will send a fetch request to our server, which will provide the JSON for the notification we will show. Before we start, we must call e.waitUntil so the browser knows to keep our service worker alive until we’re done fetching data and showing a notification.

  e.waitUntil(

    fetch('/pushdata').then(function(response) {

      return response.json();

    }).then(function(data) {

      // Here we will show the notification returned in `data`

    }, function(err) {

      console.error(err);

    })

  );

Now we have the notification to show, we can actually show it. To do so, replace the comment above with:

  var title = 'Weather PWA';

  var body = data.msg;

  var icon = '/images/icons/icon-256x256.png';

  var tag = 'static-tag';

  return self.registration.showNotification(title, {

    body: body,

    icon: icon,

    tag: tag

  });

Now remember to increment your service worker version at the top of service-worker.js, then go back to your web browser, hard refresh the page (or clear the old SW to ensure you’re on the latest version) and then press the notification bell. If everything is working, after waiting 5 seconds you should see a notification appear.

If you’re interested in how we send the push message on the server side, feel free to check out server.js.

If you get stuck at any point, feel free to look at the step6-complete folder to see how your code should look.

# Step 6: Profiling with Devtools

Now we’re done building our site, and will talk a bit about ensuring you can measure your performance using devtools. For example, we’ll talk about:

* Need for Async JS and CSS

* GPU rasterization   

* Layers Panel

For more on this topic, check out this talk from [Chrome Dev Summit](https://www.youtube.com/watch?v=w0O2znkSBXA) and Google for talks and resource put together by Paul Irish.

To see real world examples of profiling, check out [this document](https://docs.google.com/document/d/1K-mKOqiUiSjgZTEscBLjtjd6E67oiK8H2ztOiq5tigk/pub) that shows an example profile of some major websites.

# What’s next?

For more resources on building progressive web apps, take a look at [https://developers.google.com/web/progressive-web-apps](https://developers.google.com/web/progressive-web-apps)

Service Worker cookbook:

[https://serviceworke.rs/](https://serviceworke.rs/)

Pokedex.org:

[http://www.pocketjavascript.com/blog/2015/11/23/introducing-pokedex-org](http://www.pocketjavascript.com/blog/2015/11/23/introducing-pokedex-org)

Offline Web Apps on GitHub Pages

[https://hacks.mozilla.org/2015/11/offline-web-apps-on-github-pages/](https://hacks.mozilla.org/2015/11/offline-web-apps-on-github-pages/)

Progressive Web Apps: Escaping Tabs Without Losing Our Soul

[https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/)

# Contact Info

Alex Russell <[slightlyoff@google.com](mailto:slightlyoff@google.com)>

Owen Campbell-Moore <[owencm@google.com](mailto:owencm@google.com)>

Aditya Punjani <[aditya.punjani@flipkart.com](mailto:aditya.punjani@flipkart.com)>

Myk Melez <[myk@mykzilla.org](mailto:myk@mykzilla.org)> (Mozilla)



---
// created 2016-04-01-08-28-49
