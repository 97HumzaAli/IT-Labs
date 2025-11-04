# Windows Server 2022 Setup & Promotion to Domain Controller

**Host:** `fna-srv-dc01` (static IP `10.0.0.21`)  
**Domain:** `fna.test` (new forest)  
**Goal:** Build a Windows Server 2022 VM and promote it to the first Domain Controller (AD DS + DNS) for the `fna.test` lab domain.

## Prerequisites

- Windows Server 2022 VM installed and patched  
- **Static IP** configured (`10.0.0.21`) and **Preferred DNS** set to itself (`10.0.0.21`)  
- Local Administrator access

## Steps

### 1) Add AD DS role (Server Manager)

1. **Add roles and features** → **Role-based or feature-based installation**  
   ![Select installation type](images/01-installation-type.png)

2. **Server Selection:** choose `fna-srv-dc01 (10.0.0.21)`  
   ![Select destination server](images/02-server-selection.png)

3. **Server Roles:** check **Active Directory Domain Services** (AD DS)  
   ![Select server roles - AD DS](images/03-server-roles-add-ad-ds.png)

4. **Features:** confirm defaults (GPMC is included with AD DS)  
   ![Select features](images/04-features.png)

5. Click **Install**. When it finishes, use the yellow banner to **Promote this server to a domain controller**.  
   ![Post-deployment promote to DC](images/05-post-deployment-promote-dc.png)

### 2) Promote to first DC (new forest)

6. **Deployment Configuration:** **Add a new forest** → Root domain name: `fna.test`  
   ![Deployment configuration - new forest](images/06-deployment-config-new-forest.png)

7. **Domain Controller Options:** leave **DNS** and **Global Catalog** checked. Set a **DSRM password**.  
   ![DC options + DSRM](images/07-dc-options-dsrm.png)

8. **DNS Options:** delegation warning is expected (no parent zone yet). Continue.  
   ![DNS options](images/08-dns-options.png)

9. **Paths:** leave defaults for **NTDS** and **SYSVOL**.  
   ![NTDS & SYSVOL paths](images/09-paths-ntds-sysvol.png)

10. **Review Options:** confirm selections.  
    ![Review options](images/10-review-options.png)

11. **Prerequisites Check:** all checks pass (yellow triangles are informational).  
    ![Prerequisites check](images/11-prereqs-check.png)

12. Click **Install**. The server will reboot automatically after promotion.  
    ![Installation progress](images/12-installation-progress.png)

## Validation

- At the login screen, you should now see the **domain context** `FNA\Administrator`.  
  ![Domain logon screen](images/13-domain-logon-screen.png)

## What this proves

You built a **Windows Server 2022** VM, configured **static networking**, installed **AD DS + DNS**, and **promoted** the host to the first **Domain Controller** for `fna.test`.  
