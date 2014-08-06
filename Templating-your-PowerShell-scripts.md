Once you run ```Install-DSPModule``` to install the Dynamite PowerShell Toolkit commands, you gain access to the following command:

```Update-DSPTokens -UseHostName```

In striving for [completely automated deployments](https://github.com/GSoft-SharePoint/Dynamite/wiki/On-the-evils-of-Visual-Studio-based-deployments#automate-your-test-and-production-site-initialization-process), we use this command to maximize the reusability of our scripts.

The issue we're trying to solve is this: we want to re-use the same scripts when deploying our solution on our local development machine and on QA and production environments. Settings change between these environments which usually require manually editing a PowerShell script to hard-code its variables in. However, once the number of PowerShell scripts you maintain grows, this becomes unmaintainable.

The solution: "templated" PowerShell and XML input files.

## 1) The Tokens.<YOUR-MACHINE-NAME-HERE>.ps1 file

All application settings need during the automated PowerShell should be initialized in this file:
```
## Common ##
$DSP_Project_TaxonomyAdmin = "spsdev\dev"
$DSP_Project_TaxonomyServiceName = "Managed Metadata Service"
$DSP_Project_SearchServiceName = "Search Service Application"

## Portal ##
$DSP_Project_WebApplicationUrl = "http://sptao"
$DSP_Project_SiteCollectionAdmin = "spsdev\dev"
$DSP_Project_SiteCollectionDatabase = "Company_Project"
$DSP_Project_SiteCollectionUrl = "http://sptao/sites/test
```

## 2) The .template files

You should build most of your PowerShell scripts (and their XML input files) as "*.template" files. In them, use the names of the variables initialized in the Tokens files. For example, you could have a file ```Deploy-ProjectSolutions-input.xml.template``` with the following contents:

```
<?xml version="1.0" encoding="utf-8"?>
<Solutions>
  <Solution Path=".\GSoft.Dynamite.wsp">
    <WebApplications>
      <WebApplication>[[DSP_Project_WebApplicationUrl]]</WebApplication>
    </WebApplications>
  </Solution>
  <Solution Path=".\Company.Project.Dependencies.wsp">
    <WebApplications>
      <WebApplication>[[DSP_Project_WebApplicationUrl]]</WebApplication>
    </WebApplications>
  </Solution>
  <Solution Path=".\Company.Project.Branding.wsp">
    <WebApplications> 		
      <WebApplication>[[DSP_Project_WebApplicationUrl]]</WebApplication>
    </WebApplications>	
  </Solution>
...
```

## 3) The output of Update-DSPTokens

When you run ```Update-DSPTokens -UseHostName```, it will:

1. Scan the current folder for a file matching the pattern Tokens.CurrentHostName.ps1
2. Run that script
3. Look for files with the *.template extension in the current folder (and subfolders)
4. Generate a new file for each template, replacing the tokens in them, and outputting the final .ps1 scripts and .xml inputs that will be actually used for setup on the machine.

The result:
```
<?xml version="1.0" encoding="utf-8"?>
<Solutions>
  <Solution Path=".\GSoft.Dynamite.wsp">
    <WebApplications>
      <WebApplication>http://sptao</WebApplication>
    </WebApplications>
  </Solution>
  <Solution Path=".\Company.Project.Dependencies.wsp">
    <WebApplications>
      <WebApplication>http://sptao</WebApplication>
    </WebApplications>
  </Solution>
  <Solution Path=".\Company.Project.Branding.wsp">
    <WebApplications> 		
      <WebApplication>http://sptao</WebApplication>
    </WebApplications>	
  </Solution>
...
```

This is a great time saver and it helps us avoid stupid mistakes.