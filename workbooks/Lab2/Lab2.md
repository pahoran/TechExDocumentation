- - -
# Lab 2 - Import your existing Ionic/Angular code to MFP App

In this lab, we will merge the existing Ionic application code from the IonicApp project into the IBMTechEXEmployee MobileFirst project.  These will be the typical steps you will need to go through in order to enable your existing Ionic/Angular Cordova based application to use the MobileFirst Platform.

General Steps:

1. Create a new MFP 7.1 Cordova App (You did this as part of Lab #1).
2. Create a new, temporary folder
3. Move the contents of www/ to the temporary folder.  This is really just a backup. Very little of the template files created by MobileFirst when you created the Cordova project will be used.
4. Copy the Ionic web app to www/
5. Adjust app as necessary
6. Use mfp plugin add to add any additional plugins
7. Deploy the app to the mfp development server

Reference: <http://www.raymondcamden.com/2015/08/19/developing-ionic-apps-with-mobilefirst-7-1>

##Steps

1. Change context into the MobileFirst project.

        cd IBMTechEXEmployee
        
2. Create a new ‘**tmp**' folder inside the **IBMTechEXEmployee** folder.

        mkdir tmp

2. **Move** the ‘www’ content to ‘tmp’ folder using

        mv www/* tmp

  <img src="images/Lab2-01a-folder-tmp.png" width="250"/>

3.  **Copy** the Ionic web app to the ‘www’ folder

        cp -r ../IonicApp/www/* www/

4.  Your **www** folder should look like this now :

  <img src="images/Lab2-02a-folder-www.png" width="250"/>

##Summary
In this lab, you enabled the MobileFirst Platform functionality for your existing Ionic application by moving the existing code into a blank MobileFirst Cordova application project.

If you were unable to complete this lab, you can catch up by running this command:

     git checkout -f step-2

   