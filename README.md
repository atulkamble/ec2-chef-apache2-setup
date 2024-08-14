# ec2-chef-apache2-setup
To create an Apache2 cookbook using Chef, you'll need to follow a structured approach to set up your cookbook, manage configurations, and ensure Apache2 is properly installed and configured. Here’s a step-by-step guide to create and configure an Apache2 cookbook on a Chef server:

### 1. Create the Cookbook

Generate a new cookbook for Apache2:
```bash
chef generate cookbook cookbooks/apache2
```

### 2. Define Cookbook Dependencies

Update the `metadata.rb` file in the `apache2` cookbook to specify any dependencies. Open `cookbooks/apache2/metadata.rb` and add:
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

### 3. Configure Recipes

#### Install Apache2

Edit the `default.rb` file in `cookbooks/apache2/recipes/` to include Apache2 installation and basic configuration:
```ruby
# Install Apache2 package
package 'apache2' do
  action :install
end

# Ensure Apache2 service is enabled and running
service 'apache2' do
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

#### Add Apache2 Configuration Template

To customize Apache2 configurations, you can use templates. Create a template file for the Apache2 configuration in `cookbooks/apache2/templates/default/`.

For example, create a configuration template `apache2.conf.erb`:
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

Update the `default.rb` recipe to include the template:
```ruby
# Create Apache2 configuration from template
template '/etc/apache2/sites-available/000-default.conf' do
  source 'apache2.conf.erb'
  notifies :restart, 'service[apache2]', :immediately
end
```

### 4. Test Your Cookbook

Upload the cookbook to your Chef server:
```bash
knife cookbook upload apache2
```

Create a role or environment on the Chef server and assign the `apache2` cookbook to it, then apply the role to your node. For a quick test, you can also run the recipe locally using `chef-client`:
```bash
sudo chef-client --local-mode --runlist 'recipe[apache2::default]'
```

### 5. Verify Installation

Check that Apache2 is running and serving content by visiting the public IP of your node in a web browser. You should see the "Apache2 is working!" message.

### Summary

This guide covers creating an Apache2 cookbook, managing configurations, and setting up Apache2 on a Chef server. Adjust configurations and templates based on your specific needs and environment.
