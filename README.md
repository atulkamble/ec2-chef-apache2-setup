# ec2-chef-apache2-setup
To create an Apache2 cookbook using Chef, you'll need to follow a structured approach to set up your cookbook, manage configurations, and ensure Apache2 is properly installed and configured. Here’s a step-by-step guide to create and configure an Apache2 cookbook on a Chef server:

Here’s how to configure `knife` credentials and handle related tasks for Amazon Linux 2023 based on your provided Chef code and additional instructions:

### EC2 - Chef Configuration

Comprehensive guide for configuring an EC2 instance using Chef to install and manage Nginx. Follow the steps below to set up the Chef environment, create a cookbook, and deploy Nginx on an EC2 instance.

#### 1. Create Project Folder

Initialize a new Chef repository with the following command:
```bash
chef generate repo myproject
```

Navigate to the project directory:
```bash
cd myproject
```

Change to the cookbooks directory:
```bash
cd cookbooks/
cd ..
```


### Configure Knife Credentials for Amazon Linux 2023

#### 1. Create and Configure `knife.rb`

Create a `knife.rb` configuration file in the `.chef` directory of your Chef repository. This file should be located at `~/.chef/knife.rb` or `.chef/knife.rb` within your Chef repo.

Here’s an example `knife.rb` configuration:

```ruby
# Chef server URL
chef_server_url 'https://chef-server.example.com/organizations/myorg'

# Path to the client key (used to authenticate with the Chef server)
client_key '/etc/chef/client.pem'

# Path to the validation key (used to register new nodes with the Chef server)
validation_key '/etc/chef/validation.pem'

# Organization name
organization 'myorg'

# Path to the knife configuration file (default is knife.rb in the Chef repo)
knife[:editor] = 'nano'  # Specify your preferred text editor

# Path to the Chef repo directories
cookbook_path ['/path/to/your/chef-repo/cookbooks']
roles_path '/path/to/your/chef-repo/roles'
environments_path '/path/to/your/chef-repo/environments'
data_bag_path '/path/to/your/chef-repo/data_bags'
```

#### 2. Configure the Client Key

Create the `client.pem` file in `/etc/chef/`:

```bash
sudo touch /etc/chef/client.pem
sudo nano /etc/chef/client.pem
```

Paste your client key content into `client.pem` and save the file. Ensure proper permissions:

```bash
sudo chmod 400 /etc/chef/client.pem
```

#### 3. Configure the Validation Key

Create the `validation.pem` file in `/etc/chef/`:

```bash
sudo touch /etc/chef/validation.pem
sudo nano /etc/chef/validation.pem
```

Paste your validation key content into `validation.pem` and save the file. Ensure proper permissions:

```bash
sudo chmod 400 /etc/chef/validation.pem
```

### Chef Code for Apache2 Setup

#### 1. Create the Cookbook

Generate the Apache2 cookbook:
```bash
chef generate cookbook cookbooks/apache2
```

#### 2. Define Cookbook Dependencies

Update `metadata.rb`:
```ruby
name 'apache2'
maintainer 'Your Name'
maintainer_email 'your.email@example.com'
license 'All Rights Reserved'
description 'Installs/Configures Apache2'
long_description 'Installs/Configures Apache2'
version '0.1.0'
chef_version '>= 14.0'

depends 'apt', '~> 7.1.1' # If using an Ubuntu-based system
```

#### 3. Configure Recipes

Edit `default.rb`:
```ruby
# Install Apache2 package (httpd for Red Hat-based systems)
package 'httpd' do
  action :install
end

# Ensure Apache2 service is enabled and running
service 'httpd' do
  action [:enable, :start]
end

# Create a basic index.html page
file '/var/www/html/index.html' do
  content '<html>
             <head>
               <title>Welcome to Apache2</title>
             </head>
             <body>
               <h1>Apache2 is working!</h1>
             </body>
           </html>'
  action :create
end
```

#### 4. Add Apache2 Configuration Template

Create `apache2.conf.erb`:
```erb
# Template for Apache2 configuration
ServerAdmin webmaster@localhost
DocumentRoot /var/www/html

<Directory /var/www/html>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
```

Update `default.rb` to use the template:
```ruby
# Create Apache2 configuration from template
template '/etc/httpd/conf/httpd.conf' do
  source 'apache2.conf.erb'
  notifies :restart, 'service[httpd]', :immediately
end
```

#### 5. Test and Verify Your Cookbook

Upload the cookbook:
```bash
knife cookbook upload apache2
```

Run the recipe locally:
```bash
sudo chef-client --local-mode --runlist 'recipe[apache2::default]'
```

Check Apache2 status:
```bash
sudo systemctl status httpd
```

### Deleting a Cookbook from Chef Server

1. **List Cookbooks**
   ```bash
   knife cookbook list
   ```

2. **Delete the Cookbook**
   ```bash
   knife cookbook delete apache2 0.1.0
   ```
   Or delete all versions:
   ```bash
   knife cookbook delete apache2 --all
   ```

3. **Confirm Deletion**
   Confirm by typing `Y` when prompted.

## for Chef Client Architecture 
```
sudo cp client.pem validation.pem
```
// Copy EC2 url in knife.rb file
```
id
ls -l /etc/chef/client.pem
sudo chmod 400 /etc/chef/client.pem
sudo chown ec2-user:ec2-user /etc/chef/client.pem
sudo chown ec2-user:ec2-user /etc/chef/validation.pem
```
```
sudo nano /etc/chef/client.pem
sudo nano /etc/chef/validation.pem
```
```
sudo systemctl restart chef-client
```
   

### Summary

This configuration guide helps you set up `knife` on Amazon Linux 2023, configure client and validation keys, and manage an Apache2 cookbook. Adjust paths and configurations based on your specific environment and requirements.
