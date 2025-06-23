DOCUVAULT
---

- [1. Introduction](#1-introduction)
- [2. Setup and Configuration](#2-setup-and-configuration)
  - [2.1. Azure App Registration](#21-azure-app-registration)
  - [2.2. SharePoint Access](#22-sharepoint-access)
  - [2.3. DocuVault Setup](#23-docuvault-setup)
  - [2.4. SharePoint Folder Mapping](#24-sharepoint-folder-mapping)
  - [2.5. Extending the functionality](#25-extending-the-functionality)
- [3. SharePoint Documents factbox](#3-sharepoint-documents-factbox)
- [4. Migrate Document Attachments to SharePoint](#4-migrate-document-attachments-to-sharepoint)
- [5. Troubleshooting](#5-troubleshooting)


# <a name="Introduction"></a>1. Introduction

The DocuVault extension for Microsoft Dynamics 365 Business Central is the SharePoint integration offering from Braintree.

The extension includes the following features:

- Maintain SharePoint links to any (physical) record in Business Central. Includes Standard Business Central, ISV or custom tables.
- Out-of-the-box, everywhere there the standard Business Central documents functionality was, there you will find DocuVault's SharePoint Documents factbox.
- Extensibility
- Migrate embedded attachments to SharePoint.
- Maintains links from quote through to Posted Documents and archived versions
- Upload multiple documents at a time

# <a name="Setup and Configuration"></a>2. Setup and Configuration

To start using DocuVault, some initial setup and configuration are required.

## <a name="Azure App Registration"></a>2.1. Azure App Registration
You need to create an app registration in Azure with the Sites.Selected permission:
     ![alt text](./img/image.png)

> **NOTE**: Use the **Client ID** and **Client Secret** in the DocuVault Setup

To find your SharePoint Site ID, you can follow these steps:

1. Navigate to your SharePoint site: Open your SharePoint site in your web browser.
2. Modify the URL: Add /_api/site/id at the end of your site's URL. For example, if your site URL is https://companyname.sharepoint.com/sites/sitename, you would modify it to https://companyname.sharepoint.com/sites/sitename/_api/site/id.
3. Press Enter: This will display the site information, including the Site ID, in a JSON format

> **NOTE**: Use the **Site ID** in the DocuVault Setup and the below queries.


## <a name="SharePoint Access"></a>2.2. SharePoint Access

To find your SharePoint Drive ID and grant access to the App Registration in SharePoint:    
1. Go to: https://developer.microsoft.com/en-us/graph/graph-explorer , make sure to sign in before you do the next steps
2. Do a GET on https://graph.microsoft.com/v1.0/sites/{site-id}/permissions
3. Might prompt you to modify permissions. Grant Sites.FullControl.All
4. Then run POST on https://graph.microsoft.com/v1.0/sites/{site-id}/permissions
           with body:

           {
                "roles": ["write", "read"],
                "grantedToIdentities": [
                    {
                        "application": {
                            "id": "<your app registration client id>",
                            "displayName": "SharePoint Access"
                        }
                    }
                ]
            }
5. Run GET on https://graph.microsoft.com/v1.0/sites/{site-id}/drives

Find your id there:

 ![alt text](./img/image-1.png)

> **NOTE**: Use the **Drive ID** in the DocuVault setup.

## <a name="DocuVault Setup"></a>2.3. DocuVault Setup
 
![alt text](./img/image-2.png)
- SharePoint Usage: Specifies the value of the SharePoint Usage field. 
  - Manual: All SharePoint integration is strictly manual. 
  - Hybrid: The system will automatically upload to SharePoint when automated tasks upload to Document Attachments. 
  - Override: The system will override the default behavior and only use SharePoint as the default storage location and hide the standard Document Attachment factboxes.
- Hides Standard Attachments: Hides the standard BC Attachments factbox
- SharePoint Site Base URL: The base url to your SharePoint site
- Site ID: Populate with id retrieved in section 2.1
- Drive ID: Populate with id retrieved in section 2.2
- Client ID: App id for the registration done in section 2.1
- OAuth2.0 Client Secret: The client secret for the registration done in section 2.1
 
>**NOTE**: The token is stored securely in the database and cannot be accessed externally and will not be used for anything other than the SharePoint integration.



## <a name="SharePoint Folder Mapping"></a>2.4. SharePoint Folder Mapping
- As a default, DocuVault will create a folder called Business Central in your SharePoint site’s Shared Documents folder. 
- A subfolder is appended to that with the Environment Name, so that Sandbox and Production documents aren’t mixed.
- If a table is not specified, it will create a subfolder for that table using the table caption.
- A subfolder for each record is created using the primary key for that record. (Some special characters may have been removed)

>**NOTE**: If you make a copy of your Production Environment to Sandbox, existing links will still point to the Production folder, but new documents will be added to the Sandbox folder. 
Take care of file operations in Sandbox!

 ![alt text](./img/image-3.png)
 
- Subfolder Name: Provide a custom folder name (can use a folder path) for a table.
  - In provided example, Sales Header documents will be stored in the Sales folder and Customer and Vendor documents will be found in the Finance Folder.
- Append Table Name to Subfolder: Creates a subfolder in the parent folder with the Table Caption.
  - In provided example: It will create Finance\Customer and Finance\Vendor folders
- Split by Option Field: Creates subfolders per option value for the related record.
  - In provided example: It will create Quote, Order, Invoice, Credit Memo and Blanket Order for the Sales and Purchase Headers.

![alt text](./img/image-4.png)

- Upload Blocked: Prevents a user from uploading documents for that table.


For Sales and Purchases, a document linked to a quote will follow the record through its lifecycle all the way to a posted document. It will also carry that link on to archived versions of the document.
But keep in mind that, the links will refer to the folder where the link was created. It does not move the files; it copies the links to the existing file. E.g. If you create a quote and upload files, then convert to an order and upload files and then post and upload files. You will have documents linked on your posted invoice that reside in the original quote, order and posted invoice folders.

## <a name="Extending the functionality"></a>2.5. Extending the functionality
If required, we can assist you with extending your environment with SharePoint attachments, by adding the SharePoint factbox for custom tables or redirecting the default save location.

# <a name="SharePoint Documents factbox"></a>3. SharePoint Documents factbox
- We will endeavour to keep up with standard Business Central and with each major version, update DocuVault to have the factbox added where there was a Documents attachment factbox.
 ![alt text](./img/image-5.png)
- Open folder in SharePoint: Opens the folder in the browser (if the folder exists, else you might get a 404 NOT FOUND error page) – folders are automatically created when files are uploaded.
- Show details: A list page for the attachments that allows you to rename files, view the credentials of the user that uploaded the files and the timestamp.
- Upload files: Displays a dialog that allows the user to upload documents. Multiple documents can be uploaded at a time.
- Delete: Delete the link to this record. If there are no more links to the file, the file is also deleted from SharePoint.
- Get folder Content: If the record was created retrospectively, i.e. there is a SharePoint folder for the record that will be reachable using the folder mapping and record naming conventions, or files have been added via a different interface, this feature can be used to link to those files to the record automatically.

# <a name="Migrate Document Attachments to SharePoint"></a>4. Migrate Document Attachments to SharePoint
![alt text](./img/image-6.png)

Allows the user to move all embedded files to SharePoint. It will maintain record links where available. Orphaned files will be added to an orphaned folder so that the user can action them manually where and if needed. 


# <a name="Troubleshooting"></a>5. Troubleshooting
If you encounter any issues or errors while using the DocuVault extension please contact Braintree support, providing details such as error messages and the steps leading to the problem for efficient troubleshooting.

Email: bcsupport@braintree.co.za

