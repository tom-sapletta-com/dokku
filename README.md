# dokku
My experiences with dokku


https://github.com/dokku/dokku

wget https://raw.githubusercontent.com/dokku/dokku/v0.13.1/bootstrap.sh
sudo DOKKU_TAG=v0.13.1 bash bootstrap.sh


    Testing the platform
    Conclusions

How to Get Started with Dokku Debian
Janne Ruostemaa
staff
Integrations 0

Having the freedom of cloud infrastructure can be liberating but sometimes you just want a simple and easy solution to develop and deploy web applications. Dokku is an extensible, open source Platform-as-a-Service that can run on any single server of your choosing. With Dokku Debian you can easily set up your very own PaaS in a very small implementation that is not hungry for server resources. Powered by Docker, Dokku can help you build and manage your application lifecycle in the cloud.

Dokku logo with name

This guide will help you get Dokku Debian installed, configured, and tested, along with tips on getting the most out of your personal cloud platform. The instructions in this guide are intended for Debian 8, but Dokku is also available on Ubuntu as well as on CentOS.

Installing Dokku

Dokku runs on almost any hardware and is well at home in the cloud. To run Dokku, your server should have at least 1GB of RAM which makes the preconfigured 1CPU/1GB host an ideal starting point for testing your own PaaS.

Start by deploying a server with at least the minimum requirements.

If you have a domain name available, set the server hostname to a subdomain such as dokku.example.com. You should configure your DNS provider to point the subdomain including a wildcard to the public IP of your Dokku server. This will allow you to personalise the platform from the get-go.

We also recommend including your SSH keys to your server at deployment to make logging in more secure but also to make configuring Dokku easier later on. If you have not already added your public SSH keys at your UpCloud control panel, here is a quick introduction to Managing Your SSH Keys.

Once you have deployed your new Dokku server, follow the steps below to install the platform.

Install the apt-transport-https to allow the package manager using the libapt-pkg library to access metadata and packages available in sources available over HTTPS.

# Install the prerequisites
sudo apt-get update -qq
sudo apt-get install -qq -y apt-transport-https

Dokku is built on Docker and employs it to isolate applications, continue by installing Docker with the command below.

# Install Docker
wget -nv -O - https://get.docker.com/ | sh

Dokku is available in a public package cloud repository, add their key and source to your sources list, and then update your package list.

# Add Dokku apt repository
wget -nv -O - https://packagecloud.io/gpg.key | sudo apt-key add -
export SOURCE="https://packagecloud.io/dokku/dokku/ubuntu/"
echo "deb $SOURCE trusty main" | sudo tee /etc/apt/sources.list.d/dokku.list
sudo apt-get update -qq

Then finally install Dokku and its core dependencies.

# Install Dokku, when asked, select YES to enable web setup
sudo apt-get install -qq -y dokku
sudo dokku plugin:install-dependencies --core

The commands above will install the required dependencies such as Docker Engine and Herokuish along with Dokku Debian itself. The installation takes only a couple of minutes and works the best on a freshly deployed system.

If you get an error saying your hostname could not be resolved or you do not have a domain name available you can use the default reverse DNS name of your UpCloud server for testing. It can be found at your UpCloud control panel in server settings under IP Addresses tab. To change the hostname after deployment, set the system domain name in the /etc/hostname file on your server then restart the host.

Configuring the basic settings

When the installation is complete, you will need to perform a basic configuration available at a temporary web page on your Dokku server. Browse to your server domain name or IP address to finalise the installation.

Note: To secure your Dokku host, it is important to complete the web setup process.

# Dokku setup

If you already included an SSH key at the server deployment it should show in the Public Key list for Admin Access. The keys in this list will be added to the dokku user account that is used to manage the application. Set at least one SSH key or include multiples each starting on their own line.

The Hostname field should show your domain name set when deploying the server. If you do not have a domain name available, you can use the default reverse DNS name again here.

Lastly, if you have a domain name set and configured at your DNS provider, you may wish to enable the virtual host naming for apps. The option tells Dokku to make installed services accessible through <app-name>.dokku.example.com subdomains. Make sure to configure a wildcard A record for your domain to allow access to the application subdomains.

A   *.dokku.example.com   <public IP address>

When you have made the changes and selections you wish, click the Finish Setup button at the bottom of the page to apply the settings. Finalising the settings will close the setup page preventing unauthorised access to adding public keys to your host.

Your Dokku Debian platform should now be installed and ready to deploy applications. Continue below with testing the platform with a simple example app.

Testing the platform

Dokku integrates well with GitHub and its tools. Deploying applications can be done through git commands from your local machine allowing Heroku-compatible applications to be easily pushed onto a server. The applications are built with Heroku build packs and run isolated in their own containers creating a single-host cloud Heroku setup.

To get the most out of Dokku, you will need a GitHub account. Sign up if you have not already and add an SSH key to your account for secure authentication from your local system. Make sure to add keys to your local SSH agent for both the GitHub and your Dokku server.


# SSH Key Creation

http://dokku.viewdocs.io/dokku/deployment/user-management/

We'll be using SSH to connect to our server and it's a best practice to use SSH keys for authentication rather than passwords. Generate a new pair of SSH keys for use with your DigitalOcean account by running ssh-keygen on your local machine.

It'll prompt you for a name for the SSH key files (specify id_rsa_digital_ocean) and a passphrase (pick something long you can remember) to secure the private key file.

It should look something like this:

$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/sehrope/.ssh/id_rsa): id_rsa_digital_ocean
Your identification has been saved in id_rsa_digital_ocean.
Your public key has been saved in id_rsa_digital_ocean.pub.
The key fingerprint is:
e2:44:e3:0b:52:86:40:2a:bd:5d:63:70:a3:88:01:ef sehrope@ls01

I like to keep all my SSH keys in the ~/.ssh directory so to lets move them there:

$ mv id_rsa_digital_ocean* ~/.ssh/.

## SSH Config

Normally, to log in to a server using an SSH key you need to specify it on the command line via -i. Rather than specifying it everytime, you can also add default SSH settings to your SSH config file, ~/.ssh/config.

Edit the file (or create it if it doesn't exist) and add an entry for your domain name:

Host launchbylunch.com
  User root
  IdentityFile ~/.ssh/id_rsa_digital_ocean

Now, whenever I SSH to launchbylunch.com it'll default to using that specific SSH key file for authentication. I've also specified the default user to connect to on the remote server.

As an added bonus, if you have shell completions setup properly then you can just type ssh l and then hit tab and it'll autocomplete server names for you.

# Check
Listing SSH Keys
http://dokku.viewdocs.io/dokku/deployment/user-management/

    dokku ssh-keys:list


# Start Example Deyployment

https://upcloud.com/community/tutorials/get-started-dokku-debian/

Start by creating a new application on your Dokku server using the command below.

## On your Dokku server
    
    dokku apps:create ruby-rails-sample

The app will require a Postgres plugin which can be installed with the following command.

## Install Postgres plugin your Dokku server
    
    sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git

Then create a new database for the sample app.

## Create a database on your Dokku server
    
    dokku postgres:create rails-database

The official datastore offers a method to link the database service to an application. Use the command underneath to enable the sample app to access the database reserved for it.

## Link the database to the application on your Dokku server
    
    dokku postgres:link rails-database ruby-rails-sample

With the Dokku server ready to receive the application, continue by downloading the sample app from GitHub on your local computer. In case you do not already have Git installed, you can find quick installation instructions for many different operating systems over at the git download page. The commands below are intended to be run on a Linux system.

## On your local system
    
    git clone https://github.com/heroku/ruby-rails-sample.git ~/ruby-rails-sample

Then change into the downloaded repository directory and add a new remote location for git that points to your Dokku server with the application name added to the end of the domain name as shown in the example command below. Replace the <dokku.example.com> with your domain name.

## On your local system
  
    cd ~/ruby-rails-sample
    
    git remote add dokku dokku@<dokku.example.com>:ruby-rails-sample

You can then push the application onto your Dokku server with the following command.

## On your local system
  
    git push dokku master

Once the deployment finishes, the application should be available on your Dokku server at the URL shown at the end of the output ruby-rails-sample.<dokku.example.com> for example. Click the link in your terminal or browse to the address to test the application.

Ruby rails sample

http://dokku.viewdocs.io/dokku~v0.13.1/deployment/application-deployment/



