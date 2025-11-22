## Using the SharePoint Documents factbox
The DocuVault factbox replaces the standard Business Central Attachments factbox.
  
 ![alt text](./img/image-5.png)

The following functions are provided:
- Open folder in SharePoint: Opens the folder in the browser. ()If the folder does not exist, you may get a 404 NOT FOUND error page). Folders are automatically created when files are uploaded.
- Show details: A list page for the attachments that allows you to rename files, view the credentials of the user that uploaded the files and the timestamp.
- Upload files: Displays a dialog that allows the user to upload documents. Multiple documents can be uploaded at a time.
- Delete: Delete the link to this record. If there are no more links to the file, the file is also deleted from SharePoint.
- Get folder Content: If the record was created retrospectively, i.e. there is a SharePoint folder for the record that will be reachable using the folder mapping and record naming conventions, or files have been added via a different interface, this feature can be used to link to those files to the record automatically.

## Migrate Document Attachments to SharePoint
![alt text](./img/image-6.png)

Allows the user to move all embedded files to SharePoint. It will maintain record links where available. Orphaned files will be added to an orphaned folder so that the user can action them manually where and if needed. 

[**⬆️ Back to Top**](#using-the-sharepoint-documents-factbox) &nbsp;&nbsp;&nbsp;&nbsp; [**🏠 Home**](/DocuVault)
