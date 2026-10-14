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

In Active Directory Users and Computers,
