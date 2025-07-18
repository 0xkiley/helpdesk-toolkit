# 🧪 Tier 1 Training Lab: Shared Mailbox Setup with Microsoft Cloud Shell

## 🧠 Objective

This lab guides Tier 1 technicians through creating a shared mailbox in Microsoft 365 using **Cloud Shell + PowerShell**, assigning/removing access, verifying permissions, and cleaning up.

---

## 🔐 Prerequisites

- Microsoft 365 admin account  
- Permissions to create mailboxes and assign mailbox access  
- PowerShell experience (basic level)

---

## 🧭 Steps

### 1️⃣ Log into Microsoft Cloud Shell

1. Go to: [https://shell.azure.com](https://shell.azure.com)  
2. Select **PowerShell** (not Bash)  
3. Log in with your Microsoft 365 admin account  

---

### 2️⃣ Connect to Exchange Online

```powershell
Connect-ExchangeOnline -UserPrincipalName admin@yourdomain.com

### 3️⃣ Create a Shared Mailbox


New-Mailbox -Shared `
  -Name "T1 Lab Mailbox" `
  -DisplayName "T1 Lab Mailbox" `
  -Alias "t1lab" `
  -PrimarySmtpAddress "t1lab@domain.com"


  4️⃣ Define a List of Users to Assign Access
Optionally create a user list:

$Users = @(
    "user1@domain.com",
    "user2@domain.com",
    "user3@domain.com"
)
Then assign FullAccess permissions to each:


foreach ($User in $Users) {
    Add-MailboxPermission -Identity "t1lab@domain.com" `
        -User $User `
        -AccessRights FullAccess `
        -InheritanceType All
}
5️⃣ Verify Mailbox Permissions

Get-MailboxPermission -Identity "t1lab@domain.com" |
  Where-Object { $_.User -notlike "NT AUTHORITY\SELF" } |
  Select User, AccessRights
🔁 Optional: Lookup Users Before Assigning
Using Microsoft Graph:


Get-MgUser -Filter "startswith(UserPrincipalName,'user')"
Using Azure AD:


Get-AzADUser -Filter "startswith(userPrincipalName,'user')"
Use one or the other depending on your available modules.

🧹 Cleanup (Reverse Actions)
Remove User Access

foreach ($User in $Users) {
    Remove-MailboxPermission -Identity "t1lab@domain.com" `
        -User $User `
        -AccessRights FullAccess `
        -Confirm:$false
}
Delete the Shared Mailbox

Remove-Mailbox -Identity "t1lab@domain.com" -Confirm:$false

