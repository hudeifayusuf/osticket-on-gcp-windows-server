\# Installing and Configuring osTicket on Windows Server



Set up a fully functional osTicket helpdesk system hosted on a Windows Server virtual machine (VM) in Google Cloud Platform (GCP). This includes configuring IIS, PHP, MySQL, and related dependencies.



\## Environments and Technologies



\- Google Cloud Platform (GCP)

\- Windows Server 2019

\- osTicket

\- MySQL

\- PHP

\- RDP



\## Installation Files



Download required installation files:



\- \[osTicket-Installation-Files.zip](https://drive.google.com/uc?export=download\&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD)



---



\## Create and Connect to the VM



1\. \*\*Provision a VM in GCP\*\*  

&nbsp;  - Name: `osticket-vm`  

&nbsp;  - Region: Your closest region  

&nbsp;  - Machine Type: `e2-medium` (2 vCPU, 4 GB RAM)  

&nbsp;  - OS: Windows Server 2019  



2\. \*\*Connect via RDP\*\*  

&nbsp;  - Use \*Set Windows password\* to create credentials  

&nbsp;  - Use Remote Desktop (RDP) to log into the VM  



<br>



\## Install Required Windows Features



\### 1. Install IIS \& CGI on Windows Server



\- Open PowerShell as Administrator and run:



```powershell

Install-WindowsFeature -Name Web-Server, Web-CGI -IncludeManagementTools

```



\- Install extra IIS components needed for PHP and osTicket:



```powershell

Install-WindowsFeature `

&nbsp; Web-Server, `

&nbsp; Web-CGI, `

&nbsp; Web-Common-Http, `

&nbsp; Web-Default-Doc, `

&nbsp; Web-Dir-Browsing, `

&nbsp; Web-Http-Errors, `

&nbsp; Web-Static-Content, `

&nbsp; Web-ISAPI-Ext, `

&nbsp; Web-ISAPI-Filter, `

&nbsp; Web-Mgmt-Console `

&nbsp; -IncludeManagementTools

```



\- Confirm installation:  

&nbsp; - Open a browser on the VM and go to `http://localhost`  

&nbsp; - You should see the IIS Welcome page.



\### 2. Prepare osTicket Installation Files



\- Download the ZIP archive `osTicket-Installation-Files.zip`  

\- Extract (unzip) the contents  

\- Rename the extracted folder to `osTicket-Installation`



<br>



\## Install osTicket and Related Dependencies



\### 1. Install PHP \& Other Dependencies



\- From the `osTicket-Installation` folder:  

&nbsp; - Install PHP Manager for IIS (`PHPManagerForIIS\_V1.5.0.msi`)  

&nbsp; - Install IIS URL Rewrite Module (`rewrite\_amd64\_en-US.msi`)  

&nbsp; - Create a new folder: `C:\\PHP`  

&nbsp; - Extract `php-7.3.8-nts-Win32-VC15-x86.zip` into `C:\\PHP`  

&nbsp; - Install Microsoft Visual C++ Redistributable (`VC\_redist.x86.exe`)  

&nbsp; - Install MySQL 5.5.62 (`mysql-5.5.62-win32.msi`)  

&nbsp;   - Choose \*Typical Setup\*  

&nbsp;   - Select \*Standard Configuration\*  

&nbsp;   - Set Username: `root`, Password: `root`  



\### 2. Configure PHP in IIS



\- Open IIS Manager as Administrator  

\- Go to PHP Manager (under the Default Web Site)  

\- Under PHP Setup, click \*Register new PHP version\*  

\- Set the executable path to `C:\\PHP\\php-cgi.exe`  

\- Restart IIS by running the following command in CMD (as Administrator):



```cmd

iisreset

```



\### 3. Deploy osTicket



\- From the `osTicket-Installation` folder, unzip `osTicket-v1.15.8.zip`  

\- Copy the `upload` folder to `C:\\inetpub\\wwwroot`  

\- Rename `upload` to `osTicket`   



\### 4. Enable PHP Extensions



\- In IIS Manager, open \*PHP Manager\*  

\- Enable the following extensions:  

&nbsp; - `php\_imap.dll`  

&nbsp; - `php\_intl.dll`  

&nbsp; - `php\_opcache.dll`  

\- Restart IIS again



<br>



\## Configure osTicket and Set Up Database



\### 1. Prepare the Config File



\- Rename `ost-sampleconfig.php` to `ost-config.php`:  

&nbsp; From: `C:\\inetpub\\wwwroot\\osTicket\\include\\ost-sampleconfig.php`  

&nbsp; To: `C:\\inetpub\\wwwroot\\osTicket\\include\\ost-config.php`

\- Set permissions on `ost-config.php`:  

&nbsp; - Right-click → Properties → Security tab  

&nbsp; - Disable inheritance and remove all inherited permissions 

&nbsp; - Add `Everyone` with Full Control  



\### 2. Create the Database with HeidiSQL



\- Install HeidiSQL from the `osTicket-Installation` folder  

\- Open HeidiSQL and create a new session:  

&nbsp; - Username: `root`  

&nbsp; - Password: `root`  

\- Open the session and create a new database named `osTicket`



\### 3. Complete osTicket Setup in Browser



\- Open browser and go to `http://localhost/osTicket/`

\- Click \*Continue\* and complete the required fields:  

&nbsp; - System Settings and Admin User: Enter your details  

&nbsp; - Database Settings:  

&nbsp;   - Database Name: `osTicket`  

&nbsp;   - Username: `root`  

&nbsp;   - Password: `root`  

\- Click \*Install Now\*  

\- After installation, access osTicket:  

&nbsp; - Admin Panel: `http://localhost/osTicket/scp/login.php`  

&nbsp; - User Portal: `http://localhost/osTicket/`  



\### 4. Post-Install Cleanup



\- Delete the `setup` folder `C:\\inetpub\\wwwroot\\osTicket\\setup`  

\- Reset permissions on `ost-config.php` to remove write access:

```cmd

icacls "C:\\inetpub\\wwwroot\\osTicket\\include\\ost-config.php" /reset

```

<br>



\## Finalize Setup



\### 1. Configure Roles and Departments  

&nbsp;  - Admin Panel → Agents → Roles → Add: `Supreme Admin`  

&nbsp;  - Admin Panel → Agents → Departments → Add: `System Administrators`  



\### 2. Set Up Teams and Users  

&nbsp;  - Teams: Add `Level I` and `Level II Support`  

&nbsp;  - Agents: Add sample agents like `Jane` and `John`  

&nbsp;  - Users: Add users like `Karen` and `Ken`  



\### 3. Configure SLAs  

&nbsp;  - Admin Panel → Manage → SLA  

&nbsp;    - Sev-A: 1 hour, 24/7  

&nbsp;    - Sev-B: 4 hours, 24/7  

&nbsp;    - Sev-C: 8 hours, business hours  



\### 4. Create Help Topics  

&nbsp;  - Add topics like:  

&nbsp;    - `Business Critical Outage`  

&nbsp;    - `PC Issues`  

&nbsp;    - `Password Reset`

&nbsp;    - `Equipment Request`  



<br>



\## Create and Manage Tickets



\- Log in as a user → Create a ticket with a help topic  

\- Log in as an agent → Assign, triage, and resolve based on SLA severity (Sev-A, B, C)



---



\## Acknowledgments



This lab project was inspired by the Ticketing Systems lab concept from the Information Technology course by CourseCareers. The implementation is entirely my own, developed using freely available resources.



