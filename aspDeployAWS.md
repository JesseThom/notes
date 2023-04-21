- Create a EC2 instance of Ubuntu Server 20.04 
- create key pair  (if needed) .pem
- create security group 
- check - Allow ssh traffic from anywhere 0.0.0.0/0
- click connect ssh client
- copy ssh 
- example
- ssh -i "its_a_secret_to_everyone.pem" ubuntu@ec2-3-139-69-183.us-east-2.compute.amazonaws.com
- open get bash in folder that pem key is saved into
- paste ssh line into terminal
- while logged into remote terminal<br>
First, let's make sure all of our packages are up to date
```
sudo apt update
```
 now install nginx
```
sudo apt install nginx -y
```
In this next step, we will edit the nginx configuration file
```
sudo rm /etc/nginx/sites-available/default

sudo vim /etc/nginx/sites-available/default
```
paste the following code into the file you opened using vim in your ubuntu server and change the localhost port from Properties/launchsettings.json  will be 5000 series number
```
server {    
    listen 80;    
    location / {        
        proxy_pass http://localhost:5106;        
        proxy_http_version 1.1;        
        proxy_set_header Upgrade $http_upgrade;        
        proxy_set_header Connection keep-alive;        
        proxy_set_header Host $host;        
        proxy_cache_bypass $http_upgrade;    
    }
}
```
restart nginx
```
sudo service nginx restart
```
## MySQL
Install MySql Server to your EC2 instance using the following commands
```
sudo apt update
sudo apt install mysql-server -y
sudo mysql
```
Once inside the MySQL shell edit your password with the following commands
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
FLUSH PRIVILEGES;
exit;
```
## Dotnet SDK
Download a Microsoft product key
```
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
```
Register the Microsoft packages to the package manager
```
sudo dpkg -i packages-microsoft-prod.deb
```
Now, run the following commands to update the package manager with the newly registered packages
```
sudo apt-get install apt-transport-https
sudo apt-get update
```
Now we can download the dotnet SDK
```
sudo apt-get install -y dotnet-sdk-6.0
```
Installing dotnet-ef tooling
```
dotnet tool install dotnet-ef --global
```
After this installation, we will need to reboot the server to ensure we can use dotnet properly
```
sudo reboot
<!-- this will take a few minutes -->
```
- create .gitignore file on the same level as the program.cs file
Inside the file, add this line of code:
```
appsettings.json
```
- push project to github public repository 
log back into remote server using ssh file then navigate to /var/www
```
cd /var/www
```
Using the link to your remote repository copy the files to the remote using, remove angle brackets
```
sudo git clone <remote repo url>
```
grant permissions to this new directory to our EC2 instance users.
```
sudo chown -R ubuntu <new_dir_name>
```
## appsettings.json
- first cd to your repo name, use ls to make sure name is exact<br>
create appsettings.json file
```
sudo vim appsettings.json
```
This connection string is hooking up to the MySQL installed on your remote server. This is NOT your personal version. As such, you must be aware of these potential differences:

- Userid: will be root, this is the default user for MySQL.
- Password: will be whatever password you put in when you installed MySQL earlier on the remote server. Ideally, you should have called it "root". If you had not been prompted to set up a password, it is root by default.
- Port: will be 3306. If you are using MAMP or had to make any changes to your port in order to get MySQL to work on your local computer, those things will not be present in the remote server.
- Database: will be whatever you called your database locally.
```json
{  
    "Logging": {    
        "LogLevel": {      
            "Default": "Information",      
            "Microsoft.AspNetCore": "Warning"    
        }  
    },
    "AllowedHosts": "*",    
    "ConnectionStrings":    
    {        
        "DefaultConnection": "Server=localhost;port=3306;userid=root;password=root;database=projectdb;"    
    }
}
```
next migrate/update your database tables
```
dotnet restore
```
if our migrations folder is already present we do not need to re-migrate
```
dotnet ef migrations add FirstMigration
```
update the database to set create the MySQL schema
```
dotnet ef database update
```
Now, we can run "dotnet run" to get our app up and running. We can make sure that it all worked out as intended by navigating to the public IP of the server CTRL + C to end dotnet
## ASP.NET with Supervisor
First, we need to install Supervisor:
```
sudo apt install supervisor -y
```
Next, we need to publish our app. Navigate to your project on the server and run
```
dotnet publish
```
Next, we need to tell Supervisor how to run our published app. Navigate to Supervisor's configuration folder:
```
cd /etc/supervisor/conf.d
```
Now we can create the configuration for our specific project. Use sudo Vim or Nano to create and start editing the file in one command:
change to match project name and remove angle brackets
```
sudo vim <yourprojectname>.conf
```
Add the following code to the new file, substituting the name of your App and Repo:

yourAppName is the name of your project's .csproj file.

yourRepoName is the name of your project's directory.

On the third to last line, it will ask for your port in ASPNETCORE_URLS. Update port 5000 to the port you used earlier from launchsettings.json.
- 6 spots to change name
- change port number
```conf
[program:yourAppname]
command=/usr/bin/dotnet  /var/www/yourReponame/bin/Debug/net6.0/publish/yourAppname.dll
directory=/var/www/yourReponame/bin/Debug/net6.0/publish
autostart=true
autorestart=true
stderr_logfile=/var/log/yourAppname.err.log
stdout_logfile=/var/log/yourAppname.out.log
environment=ASPNETCORE_ENVIRONMENT=Production,ASPNETCORE_URLS="http://*:5106/"
user=www-data
stopsignal=INT
```
Now when we restart Supervisor, this conf file provides instructions for how to start our app as a background process.
```
sudo service supervisor restart
```
And that's it! If we visit our public IP address we should see our app running, even if we close our ubuntu terminal.