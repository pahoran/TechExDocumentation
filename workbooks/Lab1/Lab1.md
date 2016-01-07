- - -
# Lab 1 - Creating Cordova hybrid base application with MFP CLI

In this short lab (10min) we are going to create a new MFP/Cordova based application using the MFP CLI.  In a subsequent lab we will merge our Ionic app source into this MFP Cordova app container.

##Steps

1. Start a command line terminal (i.e. `cmd` on Windows or `terminal` on OS X and Linux).

2. Change context to the MobileRoadShow7.1 directory:

        cd MobileRoadShow7.1
        
4. In order to start from a known point for the first lab run the following command:

        git checkout -f step-0
        
     (this checkout will also add the **/snippets** and **/extras** folders for use in the editing steps later on)
        
3. Create a new MobileFirst Platform Cordova project with the following command:

        mfp cordova create
    
    You will be prompted for a name for your application.  Enter **`IBMTechEXEmployee`**.
    Accept the defaults for **package ID**, **app version** and **platforms**.

  ![mfp cordova create](images/Lab1-01-mfp-cordova-create.png)

  When prompted for plugins, don’t select anything and just press **Enter** twice.

  ![mfp cordova create](images/Lab1-02-mfp-cordova-path.png)

  Press **Enter** to select the default when prompted for a template path

  At the end you should get the following message

  ![mfp cordova create](images/Lab1-03a-mfp-cordova-complete.png)

  If you look at your directory you should see the following:
  
  <img src="images/Lab1-04a-folder-top.png" width="250"/>
  
   (For more information on MFP+Cordova integration, please see the Getting Started module at: <https://developer.ibm.com/mobilefirstplatform/documentation/getting-started-7-1/foundation/hello-world/integrating-mfpf-sdk-in-cordova-applications/>)

5. Lets explore the different folders.  The IBMTechEXEmployee folder contains an entire Cordova application.  The `www` folder contains the html, images, css and JavaScript files for the app.  The other folders and files contain the MobileFirst-specific artifacts.

  <img src="images/Lab1-05a-folder-expand.png" width="250"/>

##Summary

You used the MobileFirst Platform Command Line Interface (MFP CLI) to create a new Cordova-based MobileFirst project that will become your Employee Directory app.  The CLI generates the project structure including a template application.

If you were unable to complete this lab, you can catch up by running this command:

     git checkout -f step-1