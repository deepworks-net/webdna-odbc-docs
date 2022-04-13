# MSSQL (Microsoft SQL Server) Configuration Guide
## ODBC Configuration Guide for WebDNA Server & FCGI on Linux

Pre-requisites:
1. Make sure Apache and WebDNA are installed and working properly.
1. Ensure External DBMS is configured correctly (with user) 
1. [Install the proper driver manager](README.md) (depends on OS Flavor)

General Steps:
1. Install the proper driver (depends on OS Flavor and DBMS) 
1. Update WebDNA prefs and reload (depends on OS Flavor)
1. Add DSN to odbc.ini (depends on installed driver)
1. Test Connection

## 1. Install the Proper Driver
Based on the [official documentation](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver15#17) from Microsoft - currently tested against Microsoft ODBC 17

NOTE: You must run these commands as a user with sudo (admin) access to the unix environment!

### CentOS/Redhat (x64) 7.9.2009:
Install msodbc driver from Microsoft:
```SHELL
# Install repository in order to get updates via the package manager
sudo su
# Red Hat Enterprise Server 7 and Oracle Linux 7
curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssql-release.repo
# Exit su
exit

sudo yum remove unixODBC-utf16 unixODBC-utf16-devel #to avoid conflicts
# Install the odbc driver sql tools
sudo ACCEPT_EULA=Y yum install -y msodbcsql17
# Optional mssql-tools (for troubleshooting)
sudo ACCEPT_EULA=Y yum install -y mssql-tools
```

### CentOS/Redhat (x64) 8.4.2105:
Install msodbc driver from Microsoft:
```SHELL
# Install repository in order to get updates via the package manager
sudo su
# Red Hat Enterprise Server 8 and Oracle Linux 8
curl https://packages.microsoft.com/config/rhel/8/prod.repo > /etc/yum.repos.d/mssql-release.repo
# Exit su
exit

sudo yum remove unixODBC-utf16 unixODBC-utf16-devel #to avoid conflicts
# Install the odbc driver sql tools
sudo ACCEPT_EULA=Y yum install -y msodbcsql17
# Optional mssql-tools (for troubleshooting)
sudo ACCEPT_EULA=Y yum install -y mssql-tools
```

You can verify that the driver has been installed by seeing if the following is contained in your /etc/odbcinst.ini file:
```FILE
[ODBC Driver 17 for SQL Server]
Description=Microsoft ODBC Driver 17 for SQL Server
Driver=/opt/microsoft/msodbcsql17/lib64/libmsodbcsql-17.9.so.1.1
UsageCount=1
```
NOTE: the Driver path may be different based on the version installed.

## 2. Update the WebDNA preferences file and reload WebDNA
### CentOS/Redhat (x64) 7.9.2009/8.4.2105:
#### FCGI
Substitute the location of '/path/to/WebDNA' with the location of your WebDNA FCGI installation. Then run the following script with your location:
```SHELL
sudo sed -i.bak -e "s/SQLLibraryPath.*/SQLLibraryPath	\/usr\/lib64\/libodbc.so/g" "/path/to/WebDNA/webdna.ini"
```
<b>OR</b>

manually add value '/usr/lib64/libodbc.so' the SQLLibraryPath preference in your webdna preferences like so (with a tab inbetween!):
```FILE
SQLLibraryPath	/usr/lib64/libodbc.so
```
After one of the above steps are completed, restart apache:
 ```SHELL
sudo systemctl restart httpd
```

## 3. Add a DSN connection to odbc.ini system file
Add a new DSN connection to your /etc/odbc.ini file so WebDNA can utilize it (with the information to your MSSQL server):
```FILE

[MSSQL] # This is the DSN connection name you will reference in WebDNA and is whatever you want to name it
Driver=ODBC Driver 17 for SQL Server # This must match the Driver name in the /etc/odbcinst.ini file!
Description=My MSSQL ODBC Connection # The description of your ODBC connection
Server=tcp:{Server Name/IP},1433 # Replace {Server Name/IP} with the name or IP of your MSSQL server
Database={MyDatabase} # Replace {MyDatabase} with the name of the database you wish to connect to

```

NOTE: It is recommended to use an IP address instead of domain name for your MSSQL server as there can be some hiccups with DNS lookups. Utilize a static IP for the best results.

DSN Keywords and format are based on the driver. For MSSQL, refer to the [Microsoft docs](https://docs.microsoft.com/en-us/sql/connect/odbc/dsn-connection-string-attribute?view=sql-server-ver15) for all DSN Keywords.

## 4. Test your connection
Assuming everything is set correctly, you should be able to use WebDNA to connect to MSSQL. To test it, use the SQL context like so:
```WEBDNA
[SQL dsn=MSSQL&username=myuser&password=myusers_password&statement=SELECT firstname,lastname FROM myusers WHERE status = 'active' ORDER BY lastname,firstname;]
	Found '[numfound]' Active Users!
	[Founditems]
			User [index]: [lastname], [firstname]
	[/Founditems]
[/SQL]
```

## 5. Troubleshooting Tips
Coming Soon!