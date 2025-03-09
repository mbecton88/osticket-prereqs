## Setting Up osTicket on an Azure Windows 10 Virtual Machine (Detailed Guide with Explanations)

This guide will walk you through installing osTicket, a fantastic open-source ticketing system, on a Windows 10 virtual machine (VM) in Microsoft Azure. 

**Prerequisites:**

* An Azure account
* Windows or Mac desktop or laptop
* Microsoft RDP installed on local machine
* Basic ability to navigate Windows files system

## 1. Creating Your Azure Virtual Machine (The Foundation)

* **Purpose:** We need a dedicated server to host osTicket. Azure provides a flexible and scalable environment.
* **Log in to the Azure Portal:** Go to portal.azure.com and log in with your Azure account.
* **Create a New VM:**
    * Click "Create a resource" -> "Virtual machine" -> "Create."
    * **Basics Tab:**
        * Subscription and Resource group: Choose your existing ones or create new ones. For this lab I will be using my default subscription and creating a new Resource group named itlab01.
        * Virtual machine name: osticket-vm (A descriptive name helps with organization.)
        * Region: Select a region close to your users for better performance. I will be choosing (US) East US.
        * Image: Windows 10 Pro, version 22H2 - gen2 (This gives us a familiar Windows environment.)
        * Size: Standard_D4s_v3 (4 vCPUs, 16 GiB memory) (This provides sufficient resources for osTicket and moderate usage.)
        * Username: labuser (A standard user account for security.)
        * Password: osTicketPassword1! (A strong, unique password is crucial.)
        * Check the Licensing box at the bottom.
        * The other default settings will suffice.
        * Review and Create: Review your settings and click "Create."
        * You should see the following image upon successful completion.

## 2. Connecting to Your VM (Establishing Access)

* **Purpose:** We need to access the VM's desktop to install and configure osTicket.
* **Remote Desktop Connection (RDP):**
    * In the Azure portal, navigate to your `osticket-vm`.
    * Click "Connect" -> "RDP" -> "Download RDP file."
    * Open the downloaded RDP file and enter your username (`labuser`) and password.
    * Click "Yes" to any certificate warnings.

## 3. Preparing Installation Files (Gathering Resources)

* **Purpose:** We need the osTicket installation files and other necessary software.
* **Download and Extract:**
    * Download `osTicket-Installation-Files.zip` inside the VM. (Copy and paste the link to this file inside the Microsoft Edge Browser in the virtual machine).
    * Extract the contents to your desktop (Drag the zip folder to your desktop before extracting). You'll have the `osTicket-Installation-Files` folder.

## 4. Installing IIS and CGI (Web Server Foundation)

* **Purpose:** osTicket is a web application, so we need a web server (IIS) to host it. CGI (Common Gateway Interface) allows the web server to run scripts.
* **Enable IIS and CGI:**
    * In the Type here to search field at the bottom of the screen, type Control Panel and select the application. Follow the following sequence for installing CGI. Screen shots will follow.
        * "Control Panel" -> "Programs" -> "Turn Windows features on or off."
        * Expand "Internet Information Services" -> "World Wide Web Services" -> "Application Development Features" -> Check "CGI."
        * Click "OK."

## 5. Installing Required Components (Dependencies)

* **Purpose:** osTicket relies on PHP, MySQL, and other components to function.
* **PHP Manager for IIS:**
    * Run `PHPManagerForIIS_V1.5.0.msi` (This simplifies PHP configuration in IIS.)
* **URL Rewrite Module:**
    * Run `rewrite_amd64_en-US.msi` (This allows us to create user-friendly URLs.)
* **Create PHP Directory:**
    * PHP `C:\PHP` (This is where we'll install PHP.)
* **Extract PHP:**
    * Extract `php-7.3.8-nts-Win32-VC15-x86.zip` to `C:\PHP` (This installs the PHP interpreter.)
* **Visual C++ Redistributable:**
    * Run `VC_redist.x86.exe` (This provides libraries required by PHP.)
* **MySQL Installation:**
    * Run `mysql-5.5.62-win32.msi` (This installs the MySQL database server.)
    * Choose "Typical Setup" -> "Launch the MySQL Instance Configuration Wizard" -> "Standard Configuration."
    * Set the root password to `root`.
    * Click next through remaining options then click Execute.

## 6. Configuring IIS and PHP (Bridging the Gap)

* **Purpose:** We need to tell IIS where to find PHP and how to use it.
* **Open IIS Manager:**
    * Search for "IIS Manager" and run it as administrator.
* **Register PHP:**
    * In IIS Manager, double-click "PHP Manager" -> "Register new PHP version" -> Browse to `C:\PHP\php-cgi.exe`.
* **Restart IIS:**
    * Select the server node in IIS Manager -> "Stop" and then "Start" (This applies our changes.)

## 7. Installing osTicket (The Core Application)

* **Purpose:** We're installing the osTicket application files.
* **Extract osTicket:**
    * Extract `osTicket-v1.15.8.zip` -> Copy the `upload` folder to `C:\inetpub\wwwroot`.
* **Rename the Folder:**
    * Rename `upload` to `osTicket` in `C:\inetpub\wwwroot` (This creates a logical directory for our application.)
* **Restart IIS:**
    * Restart IIS.
* **Open osTicket in Browser:**
    * In IIS Manager, expand "Sites" -> "Default Web Site" -> "osTicket" -> "Browse *:80.", or follow screenshot below:
* **Enable PHP Extensions:**
    * If there are errors about extensions, enable `php_imap.dll`, `php_intl.dll`, `php_opcache.dll` in PHP Manager. (These extensions add functionality to PHP.)
    * Refresh the osTicket page to confirm the extensions are enabled. Ignore the bottom two extensions. We will get the necessary functionality for our purposes without them.
* **Rename Configuration File:**
    * Rename `ost-sampleconfig.php` to `ost-config.php` in `C:\inetpub\wwwroot\osTicket\include` (This activates the configuration file.)
* **Set File Permissions:**
    * Set `ost-config.php` permissions to "Everyone" "Full Control" then disable inheritance. (This allows osTicket to write to the file during setup.) Hit Apply and OK when finished for it to persist.

## 8. Completing osTicket Setup (Final Touches)

* **Purpose:** We're configuring osTicket's database connection and basic settings.
* **Continue Setup:**
    * Follow the on-screen instructions in your browser.
    * Enter information under System settings and Admin Users. The emails are not relevant but use two different ones.
* **Install HeidiSQL:**
    * Install HeidiSQL, connect to the local mysql server with `root/root`, create a database called `osTicket`.
* **Database Configuration:**
    * Enter database details (MySQL Database: `osTicket`, Username: `root`, Password: `root`).
    * Click "Install Now!"
    * Refresh the osTicket database to verify that the tables have populated.

## 9. Cleaning Up (Security and Maintenance) (optional)

* **Why?** We're removing unnecessary files and securing the configuration.
* **Delete Setup Folder:**
    * Delete `C:\inetpub\wwwroot\osTicket\setup` (This removes installation files.)
* **Set Read-Only Permissions:**
    * Set `ost-config.php` permissions to "Read" for everyone. (This prevents accidental modification.)
