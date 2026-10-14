# SMB File Sharing & NTFS Permissions Lab (Active Directory)

A hands-on homelab project where I set up department-based file shares on a Windows Server domain controller and tested access control from a domain-joined Windows 11 client.

**Goal:** Prove that a Sales user (`ibirt`) **can** access the Sales shared folder and **cannot** access other departments' folders (like HR).

---

## Lab Environment

| Component | Details |
|---|---|
| Host | Windows 11 Pro running Hyper-V |
| Domain Controller | `DC-SERVER2026` (Windows Server 2025), domain `EvilCorp.com` |
| Client | `win11Client` (Windows 11 Enterprise Evaluation) |
| Test user | `ibirt` (Sales department) |
| Tools | Active Directory Users and Computers, SMB shares, NTFS security |

---

## Concept: Share Permissions + NTFS Permissions

Shared folders are protected by two layers, and the **most restrictive one wins**:

1. **Share permissions**: control access over the network
2. **NTFS permissions**: control access on the file system itself

In this lab, share permissions are left open (Authenticated Users) and **NTFS permissions with AD security groups** do the real restriction.

---

## Steps

### Step 1: Create the Shares folder and department subfolders

Created `C:\Shares` on the domain controller with one folder per department.

```
C:\Shares
├── HR-shares
├── IT-shares
└── Sales-shares
```

![Shares folder on C drive](images/createdsHarefolderinDriveC.jpg)
![Department subfolders](images/SAlesfoldershares.jpg)

### Step 2: Create AD security groups

In Active Directory Users and Computers, created a Global security group in each department OU:

- `HR-Share` in `_HRDepartment`
- `Sales-shares` in `_SalesDepartment`

![HR group created](images/createdHRgroup.jpg)
![Sales group created](images/createdSalesGroup.jpg)

### Step 3: Add the user to the Sales group

Added `ibirt` as a member of the `Sales-shares` group.

![ibirt is a member of Sales-shares](images/ibirtIsOnsalesgroup.jpg)

### Step 4: Share the folder over SMB

`Sales-shares` > Properties > Sharing > Advanced Sharing > Share this folder > Permissions.
Share permissions set to `Authenticated Users` (Full Control / Change / Read).

![Share permissions](images/salesfolderSettings.jpg)

### Step 5: Set NTFS permissions

`Sales-shares` > Properties > Security tab. Added the `EVILCORP\Sales-shares` group and allowed access.

![NTFS permissions with Sales group added](images/NTFSsecuritySetingsaddedSalesGroup.jpg)

### Step 6: Confirm the folder is shared

Ran `net share` on the server. `Sales-shares` shows up as `C:\Shares\Sales-shares`, along with the HR and IT shares.

![net share output](images/confirmingsalesFolderisshared.jpg)

---

## Testing from the Client (user: ibirt)

### Test 1: Can ibirt access the Sales folder? ✅ Yes

From `win11Client`, opened `\\SERVER-ARDYPC\Sales-shares`. Access worked and `salesdata.txt` is visible.

![ibirt can access Sales folder](images/IBIRTCanaccessSalesFolder.jpg)

### Test 2: Can ibirt access the HR folder? ❌ No

Tried to open `\\SERVER-ARDYPC\HR-Shares` and got:

> Windows cannot access \\SERVER-ARDYPC\HR-Shares. You do not have permission to access this folder. Contact your network administrator to request access.

![ibirt denied on HR folder](images/ibirtfromsalesCantaccessHR.jpg)

---

## Results

| Test | Expected | Result |
|---|---|---|
| ibirt (Sales) to Sales-shares | Allowed | ✅ Pass |
| ibirt (Sales) to HR-Shares | Denied | ✅ Pass |

---

## Key Takeaways

- Share permissions and NTFS permissions work together, and the stricter one applies.
- Assign access to **AD security groups**, not individual users. Adding or removing someone is then just a group membership change.
- Always test with a real user from a client machine, not only as an admin.
- `net share` is a quick way to check what a server is publishing.

## Next Steps

- Apply the same group-based permissions to HR and IT shares
- Auto-map drives with Group Policy
- Try the AGDLP model (Accounts > Global groups > Domain Local groups > Permissions)
- Enable Access-Based Enumeration so users only see folders they can open

## Skills Demonstrated

`Active Directory` · `SMB File Sharing` · `NTFS Permissions` · `Security Groups` · `Windows Server 2025` · `Hyper-V` · `Testing & Troubleshooting`
