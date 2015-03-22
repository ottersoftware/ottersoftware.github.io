---
date: 2014-10-27 11:33
title: Xcode 6, Server 4 & Developer ID Signed Builds
permalink: /post/2014-10-27-xcode_6_server_6_developer_id_signed_builds
categories: cocoa
layout: post
share: true
---

Now that Xcode 6.1 and OS X Server 4 have been released I decided that it was time to see if I could get my Bots to code sign a client's Mac application with a Developer ID certificate (the application is a business to business one and distributed outside of the Mac App Store). Below are the steps I took. Please note that this is not a complete guide including screenshots and detailed explanations but rather a quick list of what I did to get things working.

1. Create a Developer ID certificate in the Mac Developer portal. This is pretty straightforward although I did choose to do it all manually rather than have Xcode help with any of it so that I had complete control and knew exactly what was going on.
2. I installed the downloaded certificate and added it to my login Keychain using Keychain Access on my development Mac. This meant that my Keychain now contained the private key used to generate the Developer ID certificate, the certificate itself and the public key. 
3. In Accounts in Xcode's preferences I checked that the Mac Developer account was set up. From that I could view the details of the account and check that the Developer ID Application signing identity was set up and valid.
4. Whilst checking the validity of the signing identity I clicked on the cog button and exported the Developer ID Application signing identity (which contains the private key and the certificate) so that they could be added to my build server later.
5. In the General settings for the application's target I changed Signing to 'Developer ID' and ensured that the right team was selected. I double-checked this in the Build Settings by ensuring that the Code Signing Identity was set to 'Developer ID'.
6. My client's project contains a third-party framework which also needs signing so after the Copy Files Build Phase I added a Run Script build phase which signed the framework. I used information from [Craig Hockenberry's blog](http://furbo.org/2013/10/17/code-signing-and-mavericks/) for this.
7. Similarly the project includes some plugins which are bundled with it so after their Copy Files Build Phase I added another Run Script build phase to handle them. This is essentially the same but remember to change the location from ${FRAMEWORKS_FOLDER_PATH} to ${PLUGINS_FOLDER_PATH}.
8. Having done all that test the setup by doing an Archive build and then validating it.

Assuming that validation is successful you can move onto the server.

1. Open Keychain Access on the build server Mac and then import the p12 file you created in step 4 above. If you didn't do that you can find the private key and the Developer ID certificate on your development Mac and export them from Keychain Access. You don't need to public key. When you import the key and certificate be sure to add them to the System keychain since the Bots use their own account which cannot access your own login keychain.
2. Still in Keychain Access find the private key you have just imported and double-click it to show its properties and then select Access Control. The private key needs to be accessed by kcproxy, codesign and Xcode.app. kcproxy should already be listed and you can find codesign in /usr/bin/ (more details about this in [Red Green Refactor blog](http://blog.redgreenrefactor.eu/post/70650375252/developer-diaries-fixing-provisioning-and-code-signing)) and Xcode should be in the /Applications folder.
3. Launch Xcode and check in Preferences that the Mac Developer account is set up and that the Developer ID Application signing identity is valid as you did in step 3 above.
4. In Server, select the Xcode service and make sure that the Mac Developer account is included in the Developer Teams.

And that should be it. It's worth manually doing an archive build on your server using Xcode to ensure that it is all working (and be prepared to authenticate access to the System keychain a lot!) and then create or run an existing Bot to check that everything is working properly.

As a final step, email the application you yourself which sets the quarantine flag and see if OS X allows you to open it or not.
