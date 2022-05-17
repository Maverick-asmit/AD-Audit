# ADRecon: Active Directory Recon [![Follow ADRecon on Twitter](https://img.shields.io/twitter/follow/ad_recon.svg?style=social&label=Follow%20%40ad_recon)](https://twitter.com/intent/user?screen_name=ad_recon "Follow ADRecon on Twitter")

ADRecon is a tool which extracts and combines various artefacts (as highlighted below) out of an AD environment. The information can be presented in a specially formatted Microsoft Excel report that includes summary views with metrics to facilitate analysis and provide a holistic picture of the current state of the target AD environment.

The tool is useful to various classes of security professionals like auditors, DFIR, students, administrators, etc. It can also be an invaluable post-exploitation tool for a penetration tester.

It can be run from any workstation that is connected to the environment, even hosts that are not domain members. Furthermore, the tool can be executed in the context of a non-privileged (i.e. standard domain user) account. Fine Grained Password Policy, LAPS and BitLocker may require Privileged user accounts. The tool will use Microsoft Remote Server Administration Tools (RSAT) if available, otherwise it will communicate with the Domain Controller using LDAP.

The following information is gathered by the tool:

* Forest;
* Domain;
* Trusts;
* Sites;
* Subnets;
* Schema History;
* Default and Fine Grained Password Policy (if implemented);
* Domain Controllers, SMB versions, whether SMB Signing is supported and FSMO roles;
* Users and their attributes;
* Service Principal Names (SPNs);
* Groups, memberships and changes;
* Organizational Units (OUs);
* GroupPolicy objects and gPLink details;
* DNS Zones and Records;
* Printers;
* Computers and their attributes;
* PasswordAttributes (Experimental);
* LAPS passwords (if implemented);
* BitLocker Recovery Keys (if implemented);
* ACLs (DACLs and SACLs) for the Domain, OUs, Root Containers, GPO, Users, Computers and Groups objects (not included in the default collection method);
* GPOReport (requires RSAT);
* Kerberoast (not included in the default collection method); and
* Domain accounts used for service accounts (requires privileged account and not included in the default collection method).

## Getting Started

These instructions will get you a copy of the tool up and running on your local machine.

### Prerequisites

* .NET Framework 3.0 or later (Windows 7 includes 3.0)
* PowerShell 2.0 or later (Windows 7 includes 2.0)
* A Windows host (not working with Powershell for Linux)

### Optional

* Microsoft Excel (to generate the report)
* Remote Server Administration Tools (RSAT):
    * Windows 10 (October 2018 Update or 1809 and later), RSAT is included as a set of `Features on Demand`.
        * Click on Start --> Settings --> Apps --> Apps & features --> Manage optional features --> Add a feature --> Select the following:
            * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
            * RSAT: Group Policy Management Tools
    * Windows 10 (https://www.microsoft.com/en-au/download/details.aspx?id=45520)
    * Windows 7 (https://www.microsoft.com/en-au/download/details.aspx?id=7887)

### Installing

If you have git installed, you can start by cloning the [repository](https://github.com/adrecon/ADRecon/):

```
git clone https://github.com/adrecon/ADRecon.git
```

Otherwise, you can [download a zip archive of the latest release](https://github.com/adrecon/ADRecon/archive/master.zip). The intent is to always keep the master branch in a working state.

## Usage

### Examples

To run ADRecon on a domain member host.

```
PS C:\> .\ADRecon.ps1
```

To run ADRecon on a domain member host as a different user.

```
PS C:\>.\ADRecon.ps1 -DomainController <IP or FQDN> -Credential <domain\username>
```

To run ADRecon on a non-member host using LDAP.

```
PS C:\>.\ADRecon.ps1 -Method LDAP -DomainController <IP or FQDN> -Credential <domain\username>
```

To run ADRecon with specific modules on a non-member host with RSAT. (Default OutputType is STDOUT with -Collect parameter)

```
PS C:\>.\ADRecon.ps1 -Method ADWS -DomainController <IP or FQDN> -Credential <domain\username> -Collect Domain, DomainControllers
```

To generate the ADRecon-Report.xlsx based on ADRecon output (CSV Files).

```
PS C:\>.\ADRecon.ps1 -GenExcel C:\ADRecon-Report-<timestamp>
```

When you run ADRecon, a `ADRecon-Report-<timestamp>` folder will be created which will contain ADRecon-Report.xlsx and CSV-Folder with the raw files.

### Parameters

```
-Method <String>
    Which method to use; ADWS (default), LDAP

-DomainController <String>
    Domain Controller IP Address or Domain FQDN.

-Credential <PSCredential>
    Domain Credentials.

-GenExcel <String>
    Path for ADRecon output folder containing the CSV files to generate the ADRecon-Report.xlsx. Use it to generate the ADRecon-Report.xlsx when Microsoft Excel is not installed on the host used to run ADRecon.

-OutputDir <String>
    Path for ADRecon output folder to save the CSV/XML/JSON/HTML files and the ADRecon-Report.xlsx. (The folder specified will be created if it doesn't exist) (Default pwd)

-Collect <String>
    Which modules to run (Comma separated; e.g Forest,Domain. Default all except ACLs, Kerberoast and DomainAccountsusedforServiceLogon)
    Valid values include: Forest, Domain, Trusts, Sites, Subnets, SchemaHistory, PasswordPolicy, FineGrainedPasswordPolicy, DomainControllers, Users, UserSPNs, PasswordAttributes, Groups, GroupChanges, GroupMembers, OUs, ACLs, GPOs, gPLinks, GPOReport, DNSZones, DNSRecords, Printers, Computers, ComputerSPNs, LAPS, BitLocker, Kerberoast DomainAccountsusedforServiceLogon.

-OutputType <String>
    Output Type; Comma seperated; e.g CSV,STDOUT,Excel (Default STDOUT with -Collect parameter, else CSV and Excel).
    Valid values include: STDOUT, CSV, XML, JSON, HTML, Excel, All (excludes STDOUT).

-DormantTimeSpan <Int>
    Timespan for Dormant accounts. (Default 90 days)

-PassMaxAge <Int>
    Maximum machine account password age. (Default 30 days)

-PageSize <Int>
    The PageSize to set for the LDAP searcher object. (Default 200)

-Threads <Int>
    The number of threads to use during processing objects (Default 10)

-Log <Switch>
    Create ADRecon Log using Start-Transcript
```


