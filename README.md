Evernote SDK for iOS version 0.1.1
=========================================

What this is
------------
A pleasant iOS-wrapper around the Evernote Cloud API (v1.21), using OAuth for authentication. 

Required reading
----------------
Please check out the [Evernote Developers portal page](http://dev.evernote.com/documentation/cloud/).

Installing 
----------

### Register for an Evernote API key (and secret)

You can do this on the [Evernote Developers portal page](http://dev.evernote.com/documentation/cloud/).

### Include the code

You have a few options:

- Copy the evernote-sdk-ios source code into your Xcode project.
- Add the evernote-sdk-ios xcodeproj to your project/workspace.
- Build the evernote-sdk-ios as a static library and include the .h's and .a.
- Use [cocoapods](http://cocoapods.org), a nice Objective-C dependency manager. Our pod name is "Evernote-SDK-iOS".

### Link with frameworks

evernote-sdk-ios depends on Security.framework, so you'll need to add that to any target's "Link Binary With Libraries" Build Phase.

### Modify your AppDelegate

First you set up the shared EvernoteSession, configuring it with your consumer key and secret. Do something like this in your AppDelegate's application:didFinishLaunchingWithOptions: method.

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        // Initial development is done on the sandbox service
        // Change this to @"www.evernote.com" to use the production Evernote service
        NSString *EVERNOTE_HOST = @"sandbox.evernote.com";
    
        // Fill in the consumer key and secret with the values that you received from Evernote
        // To get an API key, visit http://dev.evernote.com/documentation/cloud/
        NSString *CONSUMER_KEY = @"your key";
        NSString *CONSUMER_SECRET = @"your secret";
    
        // set up Evernote session singleton
        [EvernoteSession setSharedSessionHost:EVERNOTE_HOST 
                                  consumerKey:CONSUMER_KEY 
                               consumerSecret:CONSUMER_SECRET];    
        }
    
Then, let the EvernoteSession handle incoming URLs, which is part of the OAuth authentication process.  Modify your AppDelegate's application:handleOpenURL: method like so:

    - (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url {
        // delegate to the Evernote session singleton
        if ([[EvernoteSession sharedSession] handleOpenURL:url]) {
            return YES;
        } 
        return NO;
    }

### Modify your Info.plist

As part of the OAuth process, your app needs to know to handle certain URLs.  Add the following stanza to your app's info.plist file, replacing "your key" with your particular Evernote consumer key:

    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>en-your key</string>
            </array>
        </dict>
    </array>

Now you're good to go.

Using the Evernote SDK from your code
-------------------------------------

### Authenticate

Somewhere in your code, you'll need to authenticate the EvernoteSession.  A normal place for this would be a "link to Evernote" button action in some view controller.

    EvernoteSession *session = [EvernoteSession sharedSession];
    [session authenticateWithCompletionHandler:^(NSError *error) {
        if (error || !session.isAuthenticated) {
            // authentication failed :(
            // show an alert, etc
            // ...
        } else {
            // authentication succeeded :)
            // do something now that we're authenticated
            // ... 
        } 
    }];

Calling authenticateWithCompletion:Handler will start the OAuth process. Your app will be backgrounded while Safari is opened to an Evernote authorization page. When the user authorizes your app, it will be foregrounded again.

### Use EvernoteNoteStore and EvernoteUserStore for asynchronous calls to the Evernote API

Both EvernoteNoteStore and EvernoteUserStore have a convenience constructor that uses the shared EvernoteSession.  
All API calls are asynchronous, occurring on a background GCD queue. You provide the success and failure callback blocks.
E.g.,

    EvernoteNoteStore *noteStore = [EvernoteNoteStore noteStore];
    [noteStore listNotebooksWithSuccess:^(NSArray *notebooks) {
                                    // success... so do something with the returned objects
                                    NSLog(@"notebooks: %@", notebooks);
                                }
                                failure:^(NSError *error) {
                                    // failure... show error notification, etc
                                    NSLog(@"error %@", error);                                            
                                }];
                                
Full information on the Evernote NoteStore and UserStore API is available on the [Evernote Developers portal page](http://dev.evernote.com/documentation/cloud/).

FAQ
---

### Does the Evernote SDK support ARC?

Not yet.

### What if I want to do my own Evernote Thrift coding?

EvernoteNoteStore and EvernoteUserStore are an abstraction layer on top of Thrift, and try to keep some of that nastiness out of your hair.
You can still get access to the underlying Thrift client objects, though: check out EvernoteSession's userStore and noteStore properties.


