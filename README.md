<h1>Ticket 002 — Shared Folder Access Denied</h1>

<b>Overview</b>

Simulated a real-world IT support incident in which a domain user could see a mapped network drive but could not access a specific folder due to incorrect NTFS permissions.

<b>User Report</b>

The user reported that the SHARED (S:) network drive was visible in File Explorer, but attempting to open the Restricted folder resulted in a network access error.

<b>Error message:</b>

Windows cannot access \\JH-DC01\shared\Restricted. You do not have permission to access this resource.

<b>Environment</b>

- Server: Windows Server domain controller (JH-DC01)

- Client: Windows 11 domain-joined workstation

- Domain: jakehomelab.test

- Affected user: John Smith (jakehomelab\johnsmith)

- Network share: \\JH-DC01\shared

- Mapped drive: S:

- Affected folder: C:\SHARED\Restricted

<b>Investigation</b>

- Reproduced the access-denied error when opening S:\Restricted from the Windows 11 client.

<img src="Screenshots/Restricted error.png"/>

- Inspected the folder's Advanced Security Settings on the server.

- Identified that the Restricted folder had inheritance disabled and did not contain an access permission for the intended user or security group.

 <img src="Screenshots/Restricted folder permissions.png"/>

- Checked the SharedFolder-Test-Access Active Directory security group and confirmed that John Smith was a member.

 <img src="Screenshots/Adding to Security group.png"/>

- Reviewed the NTFS permissions to determine why the user was still being denied access.

<b>Root Cause</b>

The Restricted folder had explicit NTFS permissions that excluded the intended security group. Although John Smith was a member of SharedFolder-Test-Access, the group did not have the required permissions on the affected folder.

This demonstrated that membership of an Active Directory security group does not automatically grant access to a folder; the group must also be assigned the appropriate permissions on that resource.

<b>Resolution</b>

- Added the SharedFolder-Test-Access security group to the NTFS permissions for the Restricted folder.

 <img src="Screenshots/Changing Shared permissions.png"/>

- Granted the group Read & execute permissions, applying them to the folder, subfolders and files.

- Preserved the existing permissions for SYSTEM and Administrators.

- Retested access from the Windows 11 client using John Smith's domain account.

<b>Verification</b>

- Successfully opened the Restricted folder through the mapped SHARED (S:) network drive and confirmed that TestFile.txt was accessible.

- Ran the whoami command to verify the signed-in domain identity: jakehomelab\johnsmith

- The user could access the previously restricted folder after the permissions were corrected.

 <img src="Screenshots/Restricted Access Confirmation.png"/>

<b>Skills Demonstrated</b>

- Troubleshooting network share access issues

- Active Directory security group administration

- NTFS permissions and access control

- Understanding permission inheritance

- Diagnosing Access Denied errors

- Windows Server and Windows 11 administration

- Testing and verifying a resolution

- Documenting technical incidents and evidence
