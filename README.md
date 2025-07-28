# Installing and Configuring osTicket on Windows Server

Set up a fully functional osTicket helpdesk system hosted on a Windows Server virtual machine (VM) in Google Cloud Platform (GCP). This includes configuring IIS, PHP, MySQL, and related dependencies.

## Environments and Technologies

- Google Cloud Platform (GCP)
- Windows Server 2019
- osTicket
- MySQL
- PHP
- RDP

## Installation Files

Download required installation files:

- [osTicket-Installation-Files.zip](https://drive.google.com/uc?export=download&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD)

---

## Create and Connect to the VM

1. **Provision a VM in GCP**  
   - Name: `osticket-vm`  
   - Region: Your closest region  
   - Machine Type: `e2-medium` (2 vCPU, 4 GB RAM)  
   - OS: Windows Server 2019  

2. **Connect via RDP**  
   - Use *Set Windows password* to create credentials  
   - Use Remote Desktop (RDP) to log into the VM  

<br>

## Install Required Windows Features

### 1. Install IIS & CGI on Windows Server

- Open PowerShell as Administrator and run:

```powershell
Install-WindowsFeature -Name Web-Server, Web-CGI -IncludeManagementTools
```

- Install extra IIS components needed for PHP and osTicket:

```powershell
Install-WindowsFeature `
  Web-Server, `
  Web-CGI, `
  Web-Common-Http, `
  Web-Default-Doc, `
  Web-Dir-Browsing, `
  Web-Http-Errors, `
  Web-Static-Content, `
  Web-ISAPI-Ext, `
  Web-ISAPI-Filter, `
  Web-Mgmt-Console `
  -IncludeManagementTools
```

- Confirm installation:  
  - Open a browser on the VM and go to `http://localhost`  
  - You should see the IIS Welcome page.

### 2. Prepare osTicket Installation Files

- Download the ZIP archive `osTicket-Installation-Files.zip`  
- Extract (unzip) the contents  
- Rename the extracted folder to `osTicket-Installation`

<br>

## Install osTicket and Related Dependencies

### 1. Install PHP & Other Dependencies

- From the `osTicket-Installation` folder:  
  - Install PHP Manager for IIS (`PHPManagerForIIS_V1.5.0.msi`)  
  - Install IIS URL Rewrite Module (`rewrite_amd64_en-US.msi`)  
  - Create a new folder: `C:\PHP`  
  - Extract `php-7.3.8-nts-Win32-VC15-x86.zip` into `C:\PHP`  
  - Install Microsoft Visual C++ Redistributable (`VC_redist.x86.exe`)  
  - Install MySQL 5.5.62 (`mysql-5.5.62-win32.msi`)  
    - Choose *Typical Setup*  
    - Select *Standard Configuration*  
    - Set Username: `root`, Password: `root`  

### 2. Configure PHP in IIS

- Open IIS Manager as Administrator  
- Go to PHP Manager (under the Default Web Site)  
- Under PHP Setup, click *Register new PHP version*  
- Set the executable path to `C:\PHP\php-cgi.exe`  
- Restart IIS by running the following command in CMD (as Administrator):

```cmd
iisreset
```

### 3. Deploy osTicket

- From the `osTicket-Installation` folder, unzip `osTicket-v1.15.8.zip`  
- Copy the `upload` folder to `C:\inetpub\wwwroot`  
- Rename `upload` to `osTicket`   

### 4. Enable PHP Extensions

- In IIS Manager, open *PHP Manager*  
- Enable the following extensions:  
  - `php_imap.dll`  
  - `php_intl.dll`  
  - `php_opcache.dll`  
- Restart IIS again

<br>

## Configure osTicket and Set Up Database

### 1. Prepare the Config File

- Rename `ost-sampleconfig.php` to `ost-config.php`:  
  From: `C:\inetpub\wwwroot\osTicket\include\ost-sampleconfig.php`  
  To: `C:\inetpub\wwwroot\osTicket\include\ost-config.php`
- Set permissions on `ost-config.php`:  
  - Right-click → Properties → Security tab  
  - Disable inheritance and remove all inherited permissions 
  - Add `Everyone` with Full Control  

### 2. Create the Database with HeidiSQL

- Install HeidiSQL from the `osTicket-Installation` folder  
- Open HeidiSQL and create a new session:  
  - Username: `root`  
  - Password: `root`  
- Open the session and create a new database named `osTicket`

### 3. Complete osTicket Setup in Browser

- Open browser and go to `http://localhost/osTicket/`
- Click *Continue* and complete the required fields:  
  - System Settings and Admin User: Enter your details  
  - Database Settings:  
    - Database Name: `osTicket`  
    - Username: `root`  
    - Password: `root`  
- Click *Install Now*  
- After installation, access osTicket:  
  - Admin Panel: `http://localhost/osTicket/scp/login.php`  
  - User Portal: `http://localhost/osTicket/`  

### 4. Post-Install Cleanup

- Delete the `setup` folder `C:\inetpub\wwwroot\osTicket\setup`  
- Reset permissions on `ost-config.php` to remove write access:
```cmd
icacls "C:\inetpub\wwwroot\osTicket\include\ost-config.php" /reset
```
<br>

## Finalize Setup

### 1. Configure Roles and Departments  
   - Admin Panel → Agents → Roles → Add: `Supreme Admin`  
   - Admin Panel → Agents → Departments → Add: `System Administrators`  

### 2. Set Up Teams and Users  
   - Teams: Add `Level I` and `Level II Support`  
   - Agents: Add sample agents like `Jane` and `John`  
   - Users: Add users like `Karen` and `Ken`  

### 3. Configure SLAs  
   - Admin Panel → Manage → SLA  
     - Sev-A: 1 hour, 24/7  
     - Sev-B: 4 hours, 24/7  
     - Sev-C: 8 hours, business hours  

### 4. Create Help Topics  
   - Add topics like:  
     - `Business Critical Outage`  
     - `PC Issues`  
     - `Password Reset`
     - `Equipment Request`  

<br>

## Create and Manage Tickets

- Log in as a user → Create a ticket with a help topic  
- Log in as an agent → Assign, triage, and resolve based on SLA severity (Sev-A, B, C)

<br>

---

## Acknowledgments

This lab project was inspired by the Ticketing Systems lab concept from the Information Technology course by CourseCareers. The implementation is entirely my own, developed using freely available resources.
