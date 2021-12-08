# Box Salesforce Communities demo with UI Elements
This sample is designed to give developers a head start in setting up a Salesforce Community that can use Box UI Elements to integrate with Box Content Services.


## Pre-Requisites

1. Clone this github repo.
2. Setup your Salesforce DX environment: https://trailhead.salesforce.com/en/content/learn/projects/quick-start-salesforce-dx/set-up-your-salesforce-dx-environment
3. Setup VS Code: https://trailhead.salesforce.com/content/learn/projects/quick-start-lightning-web-components/set-up-visual-studio-code
4. Enable Dev Hub in Salesforce.
5. Install and Configure the Box for Salesforce Managed Package: https://community.box.com/t5/How-to-Guides-for-Integrations/Installing-and-Configuring-Box-For-Salesforce/ta-p/180
    > Note: Dont forget to add the Box VisualForce components to each of the record type layouts.

6. Install the Box Salesforce SDK Unmanaged Package:
    
    * Production/Developer Org: https://cloud.box.com/Box-Apex-SDK
    * Sandbox Org: https://cloud.box.com/Box-Apex-SDK-Sandbox

7. Open the source from this repo in VS Code.
8. In VS Code, use the cmd+shift+p shortcut and select SFDX: Authorize Org
9. Confirm you've successfully authorized your org by listing orgs and their associated status:
```
sfdx force:org:list
```
10. List the installed packaged for your org:
```
sfdx force:package:installed:list -u <username@domain.com>
```
11. Locate the Box for Salesforce package and copy the PACKAGE ID and VERSION into a new dependencies json element of the sfdx-project.json located at the root project directory.

It should looks something like this:
```
{
  "packageDirectories": [
    {
      "path": "force-app",
      "default": true
    }
  ],
  "namespace": "",
  "sfdcLoginUrl": "https://login.salesforce.com",
  "sourceApiVersion": "45.0",
  "dependencies": [
    { 
      "package" : "033700000004yvWAAQ",
      "versionNumber": "3.63.0.1"
    },
    { 
      "package" : "0334P000000kKiUQAU",
      "versionNumber": "2.12.0.1"
    }
  ]
}
```
13. Deploy you project source to either you scratch org or developer org in the next section.

## Deploy to your Org
Push to Scratch Org:
```
sfdx force:source:push
```


Deploy to Developer/Production Org:
```
sfdx force:source:deploy -p force-app -u username@company.com
```

## Create a Box JWT App
https://developer.box.com/guides/authentication/jwt/jwt-setup/

Once you have downloaded the json config file with your private key run the below command from the scripts directory
./parse_box_config.py /path/to/your/config/json/file

This should output a correctly formatted private key in a file 'sfdc_box_config.json' in the scripts directory

Next, you need to copy the values from the json file into the BoxConnection class
```
String publicKeyId = '';
String privateKey = '';
String enterpriseId = '';
String clientId = '';
String clientSecret = '';
```

## Whats in the Box
The force app you have installed has a number of components that can be used to integrate Box and Salesforce Communities.
- Ligtning Components
    These are Aura components and each represent a Custom Component that can be used in the Salesforce Digital Experience builder. Each component has a corresponding Apex controller class that manages the token generation.

    ![Components](/images/17-aura.png)


- Apex Classes
    The Apex classes are the controller classes for the above components and classes for the apex triggers to handle account and portal user creation.

    ![APEX](/images/18-apex.png)
 
- CSP sites
    The Content Security Policy defines how Salesforce manages web site security and prevent XSS. The CSP definitions allows the community site to communcate with Box API and other services

    ![CSP](/images/19-csp.png)

- Static Resources
    The static resources are the javascript and stylesheet files that are required for UI elements. These must be added here as static resources as the Salesforce Community security posture doesn't allow loading of external libraries. These resources are referenced in the Component resource files (.cmp)

    ![STATIC](/images/20-static.png)

    ..in the CMP File

    ![CMP](/images/22-cmp.png)


- Apex Triggers
    The Apex triggers included are
    - Account trigger. Creates a Box folder automatically when a new account is created
    - Opportunity trigger. Creates a Box folder automatically when a new opportunity is created and moves under the account associated
    - App user trigger. Creates a Box app user when a new portal user registeres. See [APP](appuser.md)

    ![TRIGGER](/images/22-triggers.png)


## Setting up Box with Community
Now you can setup your community and use the Box UI Elements in your Builder. For this sample there are four seperate elements exposed as Custom Lightening Components.

![Preview Box Content Explorer](/images/12-components.png)

The Box Content Explorer component is related to a specific Salesforce record and will show the Box folder related to a record
![Preview Box Content Explorer](/images/13-record.png)

The Box Content Explorer Standalone component takes a folder ID at design time and will show the contents of this folder or folder '0' if nothing is given
![Preview Box Content Explorer](/images/14-standalone.png)

The Box Content Uploader component can take a folder Id at design time and display an upload UI component for the given folder.
![Preview Box Content Explorer](/images/15-uploader.png)

The Box Preview component can take a document ID at design time and display the preview of the document
![Box Preview](/images/16-preview.png)




Please note that the first time you load the UI element it will likely fail with a CORS error. You need to add the site of your community to the CORS exceptions in the Box application configuration and the URLs used by the builder. 

![Preview Box Content Explorer](/images/10-box-cors-configuration.png)




