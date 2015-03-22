---
date: 2013-05-20 10:09
title: Concurrent Debug, Beta and App Store Builds 
permalink: /post/2013-05-20-concurrent-debug-beta-app-store-builds
categories: cocoa
layout: post
share: true
---

Whilst I was developing [avTag](http://avtag.it) I started using [HockeyApp](http://www.hockeyapp.net) to distribute builds to beta testers. As part of that process I decided to follow their advice and configure my project so that I could have debug, beta and App Store builds all installed on devices concurrently. You then have access to the three states of your application quickly and easily. However I found some of the configuration notes slightly confusing and although [Andreas Linde](https://twitter.com/therealkerni) gave me some [very handy notes](http://support.hockeyapp.net/discussions/questions/1803-optimal-project-setup) I've decided to write things up in a more agnostic way. I wholeheartedly recommend HockeyApp but realise that some of you are either tied in to things like [TestFlight](https://testflightapp.com/dashboard/) or simply don't use a distribution service.

### The Theory

The theory behind what we are doing is very simple. Each application on your device needs to have a unique bundle identifier. If two apps share the same bundle identifier then one will over-write the other. All we are going to do to create three versions of our application is give each its own bundle identifier.

### Application Identifiers & Provisioning Profiles

If your application isn't doing anything with iCloud, Game Center or any of the other services which require unique bundle identifiers then you could use a wildcard application identifier:

	com.mycompany.ios.myapp*

If however you are using a service which requires unique bundle identifiers then your must create three explicit application identifiers such as:

    com.mycompany.ios.myapp
    
    com.mycompany.ios.myapp.debug
    
    com.mycompany.ios.myapp.adhoc

Not all of you will add the 'ios' element but I tend to so that I can also create Mac equivalents if I need to.

'debug' and 'adhoc'  can also be anything you want. Just make sure you can remember what type of build they refer to.

The 'clean' identifier is the one that will be used for the App Store version.

Whilst the wildcard identifier is okay, since you still need to create three provisioning profiles (one for development, one for ad hoc releases and one for App Store releases) I tend to create three unique application identifiers. It also means that if you do then retrospectively add a service which needs unique bundle identifiers it's easy to do.

After setting up your application identifiers you need to create the provisioning profiles. As mentioned above, you can either create development, ad hoc and release ones for your wildcard application identifier or create one development one for com.mycompany.ios.myapp.debug, an ad hoc distribution one for com.mycompany.ios.myapp.adhoc and an App Store one for com.mycompany.ios.myapp.

Add those provisioning profiles to Xcode and you're ready to start configuring your project.

### Project Configuration

Select the Project Navigator and then click on your project at the top to show the project and target details. Select the project and then the Info tab. You need to add a new configuration so click on the plus button and duplicate the Release configuration. Call it something like 'Ad Hoc' or something else which will help you identify its purpose.

<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-1.png" alt="Project Configurations" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-1.png" target="_blank">Full Sized Version</a>]

Next select your target in Xcode and then switch to the Info tab. You need to modify the bundle identifier and the bundle display name.

Your bundle identifier should be something like 'com.mycompany.ios.myapp' or 'com.mycompany.ios.${PRODUCT_NAME:rfc1034identifier}'. This is fine for the App Store provisioning profile but it won't work for your other two provisioning profiles.

Edit the bundle identifier so that it reads:

    com.mycompany.ios.myapp${BUNDLE_ID_SUFFIX}

or

    com.mycompany.ios.${PRODUCT_NAME:rfc1034identifier}${BUNDLE_ID_SUFFIX}

Next change the bundle display name. It's hopefully currently ${PRODUCT_NAME}. This simply means that the product name is gleaned from the build settings. Change it to:

    ${PRODUCT_NAME}${BUNDLE_DISPLAY_NAME_SUFFIX}

<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-2.png" alt="Target Info Settings" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-2.png" target="_blank">Full Sized Version</a>]

Switch to the Build Settings tab and click on the Add Build Settings button in the bottom right and select 'Add User-Defined Setting'. Call the new setting 'BUNDLE_ID_SUFFIX'. Add a second user-defined setting and call it 'BUNDLE_DISPLAY_NAME_SUFFIX'. These are the two values you entered in the Info tab.

Expand both and you will see that you can enter values for each of the three configurations. You're not going to add anything to the Release configurations but you do need to change the Debug and Ad Hoc ones. 

The BUNDLE_ID_SUFFIX needs to match the bundle identifiers you set up in the Apple Developer centre so enter '.debug' against the Debug configuration and '.adhoc' against the Ad Hoc one (note the leading periods). This means that when you build your application these values are appended to the bundle identifier so you end up with:

* com.mycompany.ios.myapp.debug for the Debug configuration.
* com.mycompany.ios.myapp.adhoc for the Ad Hoc configuration.
* com.mycompany.ios.myapp for the Release configuration.

The BUNDLE_DISPLAY_NAME_SUFFIX is totally subjective. I would suggest adding something short. I use alpha and beta symbols for debug and ah hoc releases respectively. Remember to add a space before the suffix too.

<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-3.png" alt="User-Defined Build Settings" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-3.png" target="_blank">Full Sized Version</a>]

At this point you should configure the Code Signing Entitlements too and match the Debug, Ad Hoc and Release configurations to their respective provisioning profiles. This is the only place where using a wildcard application identifier comes in handy since you can leave Xcode to automatically pick the correct provisioning profile.

### Schemes

You need to do some tinkering with the schemes next so select the 'Product > Scheme > Manage Schemes...' menu item. The idea is that you have two schemes; one for ad hoc builds and one for App Store builds. Your debug configuration is shared across them both.

The schemes list probably only shows one item so duplicate it and call the duplicate something like 'MyApp Beta'. Run and Test should use the Debug configuration, whilst Profile and Archive should use the Ad Hoc configuration.

Then edit the original scheme. Select it in the Manage Schemes sheet and then click it again so that you can change the name to something like 'MyApp App Store'. Then edit it and check that Run and Test use the Debug configuration and that Profile and Archive use the Release configuration.

You can now select either scheme in Xcode which means that you can build debug versions of your application, build a beta version for distribution by doing an archive build of the Ad Hoc scheme or build an App Store version by doing an archive build of the App Store scheme.

### Build Numbers

Version and build numbers tend to be a bit subjective. The approach I use is this...

For pre-release development before the application is in the App Store I increment the version number ('Bundle versions string, short' in the Info.plist file) whenever I release a new build to the beta testers. This is something like 0.1 or 0.2.3. The build number ('Bundle version' in the Info.plist file) is also incremented each time I release a new build to the beta testers.

The first version to be submitted to the App Store will be version 1 and might have a build number of 27.

Once the application is in the App Store the version number only increased when a new version is released through the store. Here the build number becomes more important since this still increases each time I release a build to the beta testers.

So, imagine that version 1 of the application is in the store with a build number of 27. A feature is added and the beta testers get a new build to test. This is version 1.1, build 28. Two more beta versions are released to testers so the version which is submitted to the App Store is 1.1 build 30.

The next beta build would be 1.2 build 31 if it added a feature or, if it is a minor bug release then it would be 1.1.1 build 31.

The important thing is that the version number increments for App Store releases and the build number increments for beta releases.

On this basis it would be good if the build number auto-incremented each time you did a beta build. For that I use a slightly modified Build Phase [script from Stack Overflow](http://stackoverflow.com/questions/9855955/xcode-increment-build-number-only-during-archive):

    if [ $CONFIGURATION == "Ad Hoc" ]; then
        echo "Bumping build number..."
        plist=${PROJECT_DIR}/${INFOPLIST_FILE}

        # increment the build number
        buildnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${plist}")
        if [[ "${buildnum}" == "" ]]; then
            echo "No build number in $plist"
            exit 2
        fi

        buildnum=$(expr $buildnum + 1)
        /usr/libexec/Plistbuddy -c "Set CFBundleVersion $buildnum" "${plist}"
        echo "Bumped build number to $buildnum"

    else
        echo $CONFIGURATION " build - Not bumping build number."
    fi

The only things you have to remember are:

1. Increase the version number manually. I tend to do this immediately after releasing a version so that I don't forget.
2. Manually increase the build number if you didn't do any ad hoc builds for a release.

You could negate number two by running the script for Ad Hoc and Release configurations. As I say, it's all subjective.

There is a gotcha however. The scripts is run after Xcode has read the values from the plist file if your current build number is 27 and you do a beta build then the beta application will have a build number of 27. Xcode then ends up with a build number of 28 ready and waiting. Basically, consider the shown build number to be the build number of the version you are next going to release. Thanks to [Spencer MacDonald](https://twitter.com/ObjColumnist) for reminding me to add this.

### Badging The Icon

Another way you can improve this process is to badge the actual application icons with build numbers. [Richard Stelling](https://twitter.com/rjstelling) kindly emailed me some sample code for this.

The first thing to note is that the method Richard suggests uses a private API so you don't want the code included in your App Store build. You can use conditional compilation to achieve this.

The first step is to add some values to the three configurations for the Preprocessor Macros in the target's Build Settings. The three values are:

    CONFIGURATION_DEBUG
    CONFIGURATION_ADHOC
    CONFIGURATION_RELEASE

These can be seen in-situ in the following screenshot:

<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-4.png" alt="Preprocessor Macros" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-4.png" target="_blank">Full Sized Version</a>]

The 'DEBUG=1' item is probably already there, inserted by Xcode.

What this means is that in your code you can now use conditional compilation to include or exclude code from builds based on their configuration.

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
    #if defined (CONFIGURATION_DEBUG)
	    [[UIApplication sharedApplication] performSelector:@selector(setApplicationBadgeString:) withObject:@"α"];
    #elif defined (CONFIGURATION_ADHOC)
	    [[UIApplication sharedApplication] performSelector:@selector(setApplicationBadgeString:) withObject:@"β"];
    #elif defined (CONFIGURATION_RELEASE)
	    // Don't do the badging. That'll get you rejected!
    #endif

    ...
    
<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-5.png" alt="Badging the App Icon" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-5.png" target="_blank">Full Sized Version</a>]

The end result of all of this is that your device will be able to show something like this:

<img src="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-6.png" alt="Badged App Icons" width="540" />

[<a href="http://images.swwritings.com/2013-05-20-concurrent-debug-beta-app-store-builds-6.png" target="_blank">Full Sized Version</a>]

[Evan Doll](https://twitter.com/edog1203) talked about something similar in his great talk about iOS Tools at Flipboard which he gave at NSConference 5 this year. You can get a video of Evan's talk from [the NSConference web site](http://nsconference.com). Evan's approach is to actually change the icon's image and add version information to it. This is a good idea if you don't have version information available somewhere within the application itself.

### URL Schemes

[Simon Harris](http://www.harukizaemon.com) pointed out that if you implement custom URL schemes then you need to make sure that you include some way of differentiating between the app types.

### Bootstrapping with KZBootstrap

[Krzysztof Zabłocki](https://twitter.com/merowing_) has created an open source project called [KZBootstrap](https://github.com/krzysztofzablocki/KZBootstrap) which automates an awful lot of what I've talked about above and it is definitely worth a look.

---

### Acknowledgements

This article was based on two main sources:

* [Andreas Linde's reply](http://support.hockeyapp.net/discussions/questions/1803-optimal-project-setup) to a HockeyApp support ticket.
* Chris Miles's blog post [iOS Dev, Beta & Production builds installed side-by-side](http://blog.chrismiles.info/2011/04/ios-dev-beta-production-builds.html)

Additional thanks to:

* [Inafziger](http://stackoverflow.com/users/937822/lnafziger) for the build script.
