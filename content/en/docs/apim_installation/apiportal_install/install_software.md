{
"title": "Install API Portal",
  "linkTitle": "Install API Portal",
  "weight": "5",
  "date": "2019-08-09",
  "description": "Install or uninstall API Portal on Red Hat Enterprise Linux (RHEL) or CentOS."
}

This section describes the steps to install or uninstall API Portal as software installation on Red Hat Enterprise Linux (RHEL) or CentOS.

Before you start, check the [Installation prerequisites](/docs/apim_installation/apiportal_install/install_software_prereqs/).

## Install API Portal software

1. Download the installation package for your OS from Axway Support at [https://support.axway.com](https://support.axway.com/), and upload it to your host machine.
2. Log in to the host machine as the `root` user.
3. Extract the installation package:

    ```
    # tar xpvzf <package_name>.tgz
    ```

4. Run the install script:

    ```
    # sh apiportal_install.sh
    ```

5. Enter the appropriate values when prompted by the installation script. The options you are prompted for include:
    * Change the default install path. The default path that API Portal is installed in is `/opt/axway/apiportal/htdoc` or you can specify a custom path. The folders specified in the custom path are created if they do not already exist.
    * Use MySQL in SSL mode (with one way authentication or two way authentication). The certificates generated from MySQL Server must be located in `/etc/mysql/certs/`.
    * Database connection details. The default port is `3306` or you can specify a different one. The database user is the user you created for API Portal. See [Configure the database server](/docs/apim_installation/apiportal_install/install_software_configure_database/).
    * Install API Portal in a high availability cluster setup with database replication.
    * Do you want to encrypt your database password (Password will be stored encrypted)(Y/N)?
    * Enter passphrase (It will be used as encryption key for the database password).
    * Locations of `php.ini` and `apiportal.conf` configuration files.
    * Encrypt the Public API mode user password and store the encryption key in a specified directory. The directory is created along with a file. The last segment of the directory is the file name. For example: `/sample/directory/for/encryption/key` creates an empty file named "key" in the desired directory. You can also use a script to encrypt the password later. For more details, see [Encrypt the Public API user password (optional)](/docs/apim_installation/apiportal_install/upgrade_automatic/#encrypt-the-public-api-mode-user-password-optional).
    * Configure API Portal with SSL/TLS. For HTTPS, you can either provide a certificate and private key, or use a self-signed certificate. For more details, see [Configure API Portal to run with HTTP or HTTPS](#configure-api-portal-to-run-with-http-or-https).

6. To configure the SE Linux, enter the following commands:

    ```
    setsebool -P httpd_read_user_content 1

    setsebool -P httpd_can_network_connect 1

    setsebool -P httpd_can_network_connect_db 1

    setsebool -P httpd_unified 1

    chcon -R -t httpd_sys_content_t /opt/axway/apiportal/htdoc/

    semanage fcontext -a -t httpd_sys_rw_content_t '/opt/axway/apiportal/htdoc(/.*)?'

    restorecon -R -v '/opt/axway/apiportal/htdoc'
    ```

### Configure API Portal to run with HTTP or HTTPS

This section describes the options to configure API Portal with HTTP or HTTPS.

#### Run API Portal with HTTP

If you choose not to configure SSL/TLS, API Portal runs with plain HTTP.

{{< alert title="Caution" color="warning" >}}This option increases the risk of security vulnerabilities. {{< /alert >}}

#### Run API Portal with HTTPS

If you choose to configure SSL/TLS, API Portal runs with HTTPS and you can choose one of the following options:

1. Custom certificate:
    * The installation prompts you for the path to a certificate and private key.
    * It checks whether the private key is generated with a passphrase. If it is, the script prompts you for the passphrase and a path to store it. The last segment of the passphrase path is the file name. For example, if you enter `/home/passphrase`, the passphrase is stored in a file with the name `passphrase` in the `/home` directory.
    * It prompts you for the host name.
2. API Portal is configured to run with HTTPS using the provided certificate and key.
3. Self-signed certificate: The installation generates a self-signed certificate and API Portal is configured to run with HTTPS using the self signed certificate.

To complete the HTTP/HTTPS configuration, you must restart Apache. The installation script tries to detect the Apache service and prompts you to restart it. If the script cannot detect Apache you must manually restart Apache.

## Install Joomla! components

You must also install the EasyBlog and EasyDiscuss components.

1. Log in to the Joomla! Administrator Interface (JAI) (`https://<API Portal host>/administrator`) using the default Joomla! administrator credentials.

    Contact your Axway Account Manager to retrieve the default administrator credentials. It is mandatory that you change these credentials when you first log in.

    If after the installation you experience difficulties with the Joomla! administrator password, you can try to reset the password. For more details, see [How do you recover or reset your admin password?](https://docs.joomla.org/How_do_you_recover_or_reset_your_admin_password%3F)

2. Click **Components > EasyBlog**, and follow the instructions in the EasyBlog installer.
3. If prompted to select the installation method, select **Installation via Directory**, select the package from the drop-down list, and follow the instructions in the installer to the finish.

    Do not install any of the modules and plugins unless you plan to use them. To prevent installing any modules, click **Modules** and deselect **Select All**, then repeat the same for **Plugins**.

4. Click **Components > EasyDiscuss**, and repeat the component installation as described for EasyBlog.
5. If a newer version is available for **EasyBlog** or **EasyDiscuss**, click **Update Now** to update the component.

{{< alert title="Note" color="primary" >}} To resolve a known issue (caused by EasyBlog) with broken menu paths when creating new custom menus for your API Portal in JAI, you must rebuild the menu paths. In JAI, select **Menus > Main Menu** and click **Rebuild**. You only need to rebuild the menu paths once after installation or upgrade. {{< /alert >}}

## Uninstall API Portal software

To uninstall the API Portal software:

1. Log in to the host machine as the `root` user.
2. Change to the directory containing the API Portal installation package.
3. Run the uninstall script:

    ```
    # ./apiportal_uninstall.sh
    ```

4. Enter the appropriate values when prompted by the uninstall script.

    When prompted for the MySQL user name, do not use the SSL MySQL user. Use an ordinary MySQL user that requires only a user name and password. The user must have privileges to drop the API Portal database.
