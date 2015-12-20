# Setting up Cloud Foundry Locally

## Getting Started

The 411 on my laptop:

* 2012 Retina Macbook Pro
* 16 GB RAM
* 768 GB SSD
* 2.7 GHz i7 processor
* Mac OS 10.10.1

FYI: I have already installed the CF CLI tools on my laptop, so although I will explain how to install the CF CLI tools I will not be installing it again at this time.

It's worth mentioning that I did also try running Cloud Foundry on a different Macbook with 8 GB of RAM with limited success. So for the RAM at least I would recommend having 16 GB+. This will provide enough memory for Cloud Foundry to run more comfortably alongside the system processes.

### Ruby, Go, Vagrant, and VirtualBox

Make sure that you have:

* the latest stable release of [Go](https://golang.org/)
* the latest stable release of [Ruby](https://www.ruby-lang.org/en/)
 * Optional: [RVM](https://rvm.io/) <br />Not directly required for Cloud Foundry, but this will come in handy if you need to install/manage more than one version of Ruby.
* the latest release of [Vagrant](https://www.vagrantup.com/)
* the latest release of [VirtualBox](https://www.virtualbox.org/)

Before proceeding, check the installed versions using `go version`, `rvm list` (or `ruby --version` if you do not have RVM), `vagrant --version`, and `vboxmanage --version`. I am currently running the latest stable releases for all the above, in addition to version 1.9.3 for Ruby.

```
$ go version
go version go1.3.3 darwin/amd64

$ rvm list

rvm rubies

   ruby-1.9.3-p551 [ x86_64 ]
=* ruby-2.1.5 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

$ vagrant --version
Vagrant 1.6.5

$ vboxmanage --version
4.3.18r96516
```

## Installing BOSH Lite

In order to run Cloud Foundry you must first install BOSH. To provide some basic familiarity, there are three "types" of BOSH (if you will):

* microBOSH
* BOSH
* BOSH Lite

BOSH is used to deploy Cloud Foundry and microBOSH is used to deploy BOSH. BOSH Lite is used for local instances of Cloud Foundry - for example on a laptop like I'm doing.

The instructions for installing BOSH Lite are available on the BOSH Lite [README](https://github.com/cloudfoundry/bosh-lite#install-bosh-lite). I followed the instructions for Vagrant and VirtualBox.

**BOSH Lite install failure: nokogiri**

When I first tried to install BOSH Lite with `gem install bosh_cli` the installation failed because it needed nokogiri:

```
...
Fetching: nokogiri-1.6.5.gem (100%)
Building native extensions.  This could take a while...
ERROR:  Error installing bosh_cli:
  ERROR: Failed to build gem native extension.
...
```

I had actually run into this issue before, on another Macbook running OS 10.9.x. I was only able to install nokogiri using the Xcode CLI tools:

```
$ xcode-select --install
xcode-select: note: install requested for command line developer tools

$ gem install nokogiri -- --with-xml2-include=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/libxml2
Building native extensions with: '--with-xml2-include=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/libxml2'
This could take a while...
Successfully installed nokogiri-1.6.5
Parsing documentation for nokogiri-1.6.5
Installing ri documentation for nokogiri-1.6.5
Done installing documentation for nokogiri after 3 seconds
1 gem installed
```

(The Xcode CLI tools install will pop up a software agreement via the App Store that you must agree to in order to install the software. The installation/updates for the Xcode CLI tools will subsequently be handled in the App Store.)

Once nokogiri was installed, I was able to install the BOSH CLI tools without difficulty.

The first time you start the VM, Vagrant will use the Vagrantfile in the BOSH Lite directory to install/create the VM. You should see something similar to the following:

```
$ vagrant up --provider=virtualbox
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'cloudfoundry/bosh-lite' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: 388
==> default: Loading metadata for box 'cloudfoundry/bosh-lite'
    default: URL: https://vagrantcloud.com/cloudfoundry/bosh-lite
==> default: Adding box 'cloudfoundry/bosh-lite' (v388) for provider: virtualbox
    default: Downloading: https://vagrantcloud.com/cloudfoundry/boxes/bosh-lite/versions/388/providers/virtualbox.box
==> default: Successfully added box 'cloudfoundry/bosh-lite' (v388) for 'virtualbox'!
==> default: Importing base box 'cloudfoundry/bosh-lite'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'cloudfoundry/bosh-lite' is up to date...
==> default: Setting the name of the VM: bosh-lite_default_1418694875085_89186
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 => 2222 (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Setting hostname...
==> default: Configuring and enabling network interfaces...
==> default: Mounting shared folders...
    default: /vagrant => /Users/quinn/Development/BOSHlite/bosh-lite
```
You should then be able to target the BOSH Lite director and update your routing table:

```
$ bosh target 192.168.50.4 lite
Target set to `Bosh Lite Director'
Your username: admin
Enter password: *****
Logged in as `admin'

$ bin/add-route
Adding the following route entry to your local route table to enable direct warden container access. Your sudo password may be required.
  - net 10.244.0.0/19 via 192.168.50.4
Password:
add net 10.244.0.0: gateway 192.168.50.4
```

Note: when I did this install on another laptop running OS 10.9.x, I ran into an issue where the route script could not run and terminated with the error `route: command not found`. Turns out, somehow my `PATH` variable had become foobar-ed (technical term) at some point. Fixing my `PATH` variable resolved the issue.

## Deploying Cloud Foundry

Now we're going to hop over to the [Cloud Foundry install instructions](https://github.com/cloudfoundry/bosh-lite/blob/master/docs/deploy-cf.md). Using the install script:

* Install [spiff](https://github.com/cloudfoundry-incubator/spiff) (requires [Homebrew](http://brew.sh/))
* Clone the `cf-release` repo
* Run `./bin/provision_cf`

**Install script failure: timeout**

The only real issue I encountered during the installation is that when I ran the script, I encountered the following error a few times (which halted the install):

```
Blobstore error: Failed to fetch object, underlying error: #<HTTPClient::ReceiveTimeoutError: execution expired>
```

After a quick search I found that people just restarted the install and it would complete. The install does not redownload packages that have already been downloaded, it just flags that there are already local copies until it gets to the packages it hasn't downloaded yet.

For reference, I had to run the script a total of three times before all the packages were downloaded successfully. I did not encounter any other issues with my installation.

## Installing the CF CLI tools

Personally, I installed the CF CLI tools using the latest binary installer (linked on the [README](https://github.com/cloudfoundry/cli#downloads)). Currently the development team is also experimenting with a Homebrew install, but that is still in the experimental phase at the time of this writing.

As a quick verification that the CLI tools are installed correctly, try checking the version. You should see something similar to the following:

```
$ cf --version
cf version 6.7.0-c38c991-2014-11-12T01:45:23+00:00
```

### Creating the initial Org and Space

Targeting the API and logging in is the same procedure that you would use with a PaaS provider (e.g. PWS) or TryCF (requires AWS):

```
$ cf api --skip-ssl-validation https://api.10.244.0.34.xip.io
Setting api endpoint to https://api.10.244.0.34.xip.io...
OK


API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.18.0)
Not logged in. Use 'cf login' to log in.

$ cf login
API endpoint: https://api.10.244.0.34.xip.io

Email> admin

Password>
Authenticating...
OK

Select an org (or press enter to skip):

Org>



API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.18.0)
User:           admin
No org or space targeted, use 'cf target -o ORG -s SPACE'
```

At first you won't have any orgs (or spaces):

```
$ cf orgs
Getting orgs as admin...

name
No orgs found
```

This is different from service providers like PWS and TryCF, both of which have an org and a `development` space when they are created. Although orgs and spaces will be discussed in greater detail separately, please go ahead and create an org and at least one space. Follow the output suggestions to target the org and space.

```
$ cf create-org quinn
Creating org quinn as admin...
OK
TIP: Use 'cf target -o quinn' to target new org

$ cf target -o quinn
API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.18.0)
User:           admin
Org:            quinn
Space:          No space targeted, use 'cf target -s SPACE'

$ cf create-space development
Creating space development in org quinn as admin...
OK
Assigning role SpaceManager to user admin in org quinn / space development as admin...
OK
Assigning role SpaceDeveloper to user admin in org quinn / space development as admin...
OK
TIP: Use 'cf target -o quinn -s development' to target new space

$ cf target -o quinn -s development
API endpoint:   https://api.10.244.0.34.xip.io (API version: 2.18.0)
User:           admin
Org:            quinn
Space:          development
```

---

# Review the following to keep/delete

http://docs.cloudfoundry.org/deploying/

http://docs.cloudfoundry.org/deploying/boshlite/

Setting up Cloud Foundry without BOSH: http://blog.altoros.com/deploying-cloud-foundry-in-a-single-click-with-juju-charms.html

### CF Wiki

https://github.com/cloudfoundry-community/cf-docs-contrib/wiki

### Xip.io

http://xip.io/
