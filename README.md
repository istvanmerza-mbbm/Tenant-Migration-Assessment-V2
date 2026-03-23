# Tenant-Migration-Assessment-V2
Version 2 of the tenant migration assessment with improved logic and updated functionality
 Prerequisites:
        For full instructions on how to use this script, please visit the blog posts below:
        https://practical365.com/office-365-migration-plan-assessment/
        https://practical365.com/microsoft-365-tenant-to-tenant-migration-assessment-version-2/


 1) Disable Concealed Names in Reports
 2) ImportExcel, Microsoft.Graph and ExchangeOnlineManagement PowerShell modules (Latest Releases)
 3) Global Administrator role is required to run the prepare script
 4) The Application Registration must be granted consent during the prepare- operation
 5) PowerBI Desktop is required to use the Power BI report

Notes:
1) You may need to run disconnect-MgGraph after the prepare script if you wish to run in another tenant

## HOW TO

1. Read original README above and the Author's Blog Post:

   [Microsoft 365 Tenant-to-Tenant Migration Assessment Version 2](https://practical365.com/microsoft-365-tenant-to-tenant-migration-assessment-version-2/)

2. Disable concealed names in the SOURCE Tenant 

   Refer to [Show user, group, or site details in the reports](https://learn.microsoft.com/en-us/microsoft-365/admin/activity-reports/activity-reports?view=o365-worldwide&WT.mc_id=365AdminCSH_inproduct#show-user-group-or-site-details-in-the-reports)

   Direct Link [M365 Admin Center Reports Settings](https://admin.cloud.microsoft/#/Settings/Services/:/Settings/L1/Reports)

3. Install all required PowerShell modules (Latest Release)

   ```PowerShell
   Install-Module ImportExcel -Scope CurrentUser
   Install-Module Microsoft.Graph -Scope CurrentUser
   Install-Module ExchangeOnlineManagement -Scope CurrentUser
   ```

4. Copy all files in this repo to **C:\\TenantAssessment**

   *NOTE: If you do not copy **TenantAssessment-Template.xlsx** the Highlevel tab will be missing from your Excel report.*

5. Run **Prepare-TenantAssessment.ps1**

   * Login as a **Global Administrator** to your SOURCE Tenant "To be migrated"
   * Consent Powershell and/or App Registration permissions
   * Take note of the **Tenant ID**, **Client ID**, **Certificate Thumbprint**. You will need them in Step 7.

6. Review and Adjust **Perform-TenantAssessment.ps1** script, if necessary:

   * Search for **##NOTE: Change for Non-English Tenants** lines in source
   * Append localized **Documents** library name and **Shared%20Documents** folder name

   *NOTE: Script already adjusted for German Tenants in this fork*
7. Run **Perform-TenantAssessment.ps1**

   ```PowerShell
   .\Perform-TenantAssessment.ps1 -clientId $ClientId -tenantId $TenantId -certificateThumbprint $CertificateThumbprint -IncludePlans -IncludeGroupMembership -IncludeMailboxPermissions -IncludeDocumentLibraries -IncludeLists
   Get-MgReportSharePointSiteUsageDetail_Get1: C:\TenantAssessment\Perform-TenantAssessment.ps1:415
   Line |
    415 |  … SharePointSiteUsageDetail -Period "D30" -OutFile "$($FilePath)\ShareP …
        |                                                        ~~~~~~~~~
        | Cannot set percent because PercentComplete cannot be greater than 100. (Parameter 'value') Actual value was
        | 2147483647.
   ```

   *NOTE: You might get exception(s) related to PercentComplete value > 100. This is due to a* [*Microsoft Graph SDK bug*](https://github.com/microsoftgraph/msgraph-sdk-powershell/issues/3488)*. Ignore these errors*

8. Process **C:\\TenantAssessment\\TenantAssessment.xlsx**
   * Review the file
   * Append SOURCE Tenant name, current ISO8601 date to the filename
   * Upload to your Migration project

   *NOTE: Don't Forget to clean up privileged "**Tenant Assessment Tool**" App Registration in the SOURCE tenant after completing your migration project!*
