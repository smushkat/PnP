# SharePoint High-Trust Add-In with Cert in Windows Certificate Store #

### Summary ###
This sample shows how a high-trust SharePoint add-in can use a certificate that has been stored in the Windows Certificate Store on the Windows Server that is hosting the web application.

### Applies to ###
-  SharePoint 2013 on-premises

### Prerequisites ###
None.

### Solution ###
Solution | Author(s)
---------|----------
Core.OnPrem.S2S.WindowsCertStore | Ricky Kirkham (**Microsoft**)

### Version history ###
Version  | Date | Comments
---------| -----| --------
1.0  | December 5th 2014 | Initial release

### Disclaimer ###
**THIS CODE IS PROVIDED *AS IS* WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING ANY IMPLIED WARRANTIES OF FITNESS FOR A PARTICULAR PURPOSE, MERCHANTABILITY, OR NON-INFRINGEMENT.**


----------

# Overview #
The purpose of this sample is to show how the certificate that is used as the access token issuer in a SharePoint high-trust add-in can be stored in the Windows Certificate Store on the computer that is hosting the remote web application. This is a much more secure way to store the certificate than keeping it on the file system. 

As of the initial release of this sample, the Office Developer Tools for Visual Studio (12.0.31105) generate a TokenHelper.cs file and a web.config file for high-trust SharePoint apps that assume the certificate is stored on the file system. These two files have been modified in this sample. All the other files are exactly as generated by the tools.

- The web.config has been changed to use an appSetting for the serial number of the certificate, rather than settings for the password of the certificate and it's location on the file system. 
- The TokenHelper.cs has been changed:
	- The private fields for the certificate password and file location have been replaced with a field for the certficate's serial number.
	- The initialization code for the `SigningCredentials` field has been changed. You can see the new/changed fields in the `private fields` region of the file.
	- Two helper methods have been added to the `private methods` region of the file that are used to initialize `SigningCredentials`. They are `GetSigningCredentials()` and `GetCertificateFromStore()`.


# To use this sample #
1. Obtain a digital certificate. It can be self-signed, domain-issued, or from a certficate authority. For details, see [Obtain a certificate or create a public and private test certificate](http://msdn.microsoft.com/en-us/library/office/fp179901(v=office.15).aspx#Cert2) on MSDN.
2. Configure your on premise test SharePoint farm to trust the certificate as a root authority and as a token issuer. For details, see [Configure SharePoint 2013 to use certificates and configure trust for your add-in ](http://msdn.microsoft.com/en-us/library/office/fp179901(v=office.15).aspx#Configure2). _Make a record of the GUID used as the issuer ID._ 
3. Move the *.pfx version of the certificate to any temporary directory on your **Visual Studio** computer. 
4. On the same computuer, open the **Microsoft Management Console (MMC)** and as described in [Open MMC 3.0](http://technet.microsoft.com/en-us/library/cc766121.aspx).
5. Add the **Certificates** snap-in for the computer account as described in [Add the Certificates Snap-in to an MMC](http://technet.microsoft.com/en-us/library/cc754431.aspx). Be sure to use the procedure for the _computer_, not a user or service. Choose the local computer, not "another" computer, when prompted.
6. Import the certificate as described in [Import a Certificate](http://technet.microsoft.com/en-us/library/cc754489.aspx). Be sure to specify the **Personal** store. (The terminology is quirky here. This is the "personal" store of the computer.)
7. Optionally, you can delete the *.pfx file from the file system as you would in a production server. 
8. In the **MMC**, navigate to the **Certificates** folder under the **Personal** folder of the **Certificates (Local Computer)** snap-in, if it is not already open.
9. Double-click the certificate for your add-in for SharePoint to open it, and then open the **Details** tab.
10. Select the **Serial Number** field to make the entire serial number is visible in the box.
11. Copy the serial number, without the spaces, to a text file. (Some developer blog posts and forum questions report that copying the serial number directly into the clipboard creates a string with hidden characters that makes the serial number unrecognizable to code in the add-in for SharePoint. Consider manually typing the number instead of copying it.)
12. Open the *.sln file for the sample in **Visual Studio**.
13. In Solution Explorer, highlight the SharePoint add-in project and replace the **Site URL** property with the URL of your SharePoint developer site.
13. Open the web.config file and, in the `appSettings` section, replace the value for the `IssuerId` key with the value you saved after step 2. It must be hyphenated and lower case.
14. Replace the value for the `ClientSigningCertificateSerialNumber` key with the value you saved in step 11. It has no hyphens.
15. Leave the `ClientId` and `ClientSecret` values as they are. The first changes every time you press F5 anyway and the second is not used in high-trust apps. 

You can now run the sample with F5. The first time you do, you are prompted to trust the add-in. The default page of the web application then opens and displays the name of the SharePoint website (your SharePoint developer site).

 




