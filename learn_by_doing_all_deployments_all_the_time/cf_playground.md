# CF Playground

Install difficulty: **Easy** with **Difficult** option<br />
Reason for difficulty rating: Requires significant code edits prior to deploy; however, these have already been completed in the branch we will deploy. Difficult option: reader can complete code edits prior to deploy using current main branch. <br />
Primary Language: **Go**

---

###What is Cloud Foundry Playground?

Cloud Foundry Playground is a Go app that allows users to start learning Cloud Foundry without installing all the necessary tools or using an online service such as [Pivotal Web Services](https://run.pivotal.io/). Although intended to be run locally, it *can* be run in Cloud Foundry as well. Cloud Foundry Playground is still under development, so it is important to note that:

* At the time of this writing not all of the tutorials have been complete.
* Partially by design, partially because the application is still under development, not all CF CLI commands are available. A list of what CF CLI commands are supported is listed on the project's [README](https://github.com/cloudfoundry-community/cfplayground).

###Prequisities
####You will need two spaces

To protect against malicious activity, you cannot deploy the app to the same space that you will be "playing" in.  There are a couple ways you can handle this. If you have an existing instance of Cloud Foundry, you can either create another space there or you can create a TryCF instance to play with a second tool. To create a TryCF instance you will need an existing Amazon Web Services account. You can create a TryCF instance using your AWS credentials at:

<https://trycf.starkandwayne.com/>

####You cannot use the application's master branch

As mentioned above, Cloud Foundry Playground is written to be run locally, which means that we cannot deploy the application directly to Cloud Foundry. Instead, we are going to use the `cf-ready` branch that has been updated to include the appropriate server information for a web deploy. (For details, please look at the [diff](https://github.com/cloudfoundry-community/cfplayground/compare/cf-ready).) To clone the correct branch, use the -b flag:

```
git clone -b cf-ready https://github.com/cloudfoundry-community/cfplayground.git
```

###Deploying the app

Once you have cloned the application, you will need to create a config.json file (see step 4 from the [README file](https://github.com/cloudfoundry-community/cfplayground)). The config.json file supplies the credentials of the space the application will access for the tutorials. Below is an example of the config.json file where Cloud Foundry Playground will use a space I created named "cfplay". on a TryCF instance:

PWS:
```
{
  "server": {
    "url": "https://api.run.pivotal.io",
    "login": "quinn@thecodingmant.is",
    "pass": "Jet25*^8",
    "org": "quinn",
    "space": "cfplay"
  }
}
```

TryCF:
```
{
  "server": {
    "url": "https://api.54.174.38.27.xip.io",
    "login": "admin",
    "pass": "admin",
    "org": "default",
    "space": "development"
  }
}
```


Before deploying the application to Cloud Foundry, make sure that you are not targeting the same space that the application will use for the tutorials:
```
$ cf target
API endpoint: https://api.54.164.17.171.xip.io (API version: 2.2.0)
User:         admin
Org:          default
Space:        development
```
Also, make sure there is a Go buildpack available (as this is a Go application):
```
$ cf buildpacks
Getting buildpacks...

buildpack          position   enabled   locked   filename
java_buildpack     1          true      false    java-buildpack-v2.1.2.zip
ruby_buildpack     2          true      false    buildpack_ruby_v46-245-g2fc4ad8.zip
nodejs_buildpack   3          true      false    buildpack_nodejs_v8-177-g2b0a5cf.zip
go_buildpack       4          true      false    buildpack_go_cf-v1-3-g55574f0.zip
```
Since there is a Go buildpack available in my example, I can push the app using the defaults (and let Cloud Foundry auto-detect the buildpack):
```
$ cf push cfplay
Updating app cfplay in org default / space development as admin...
OK

Uploading cfplay...
Uploading app files from: ~/Go/src/github.com/cloudfoundry-community/cfplayground
Uploading 623.8K, 112 files
OK

Stopping app cfplay in org default / space development as admin...
OK

Starting app cfplay in org default / space development as admin...
OK
-----> Downloaded app package (5.6M)
-----> Downloaded app buildpack cache (68M)
Cloning into '/tmp/buildpacks/go-buildpack'...
Submodule 'buildpack-packager' (https://github.com/cf-buildpacks/buildpack-packager.git) registered for path 'buildpack-packager'
Submodule 'compile-extensions' (https://github.com/cf-buildpacks/compile-extensions.git) registered for path 'compile-extensions'
Cloning into 'buildpack-packager'...
Submodule path 'buildpack-packager': checked out 'ca18bc639e5cb8ce55db20e2594a84e7f6b758f5'
Cloning into 'compile-extensions'...
Submodule path 'compile-extensions': checked out '134c97d7fb9430dcb088d8b96301e5e5b7a40089'
-----> Using go1.2.1
-----> Running: godep go install -tags cloudfoundry ./...
-----> Uploading droplet (7.7M)

0 of 1 instances running, 1 down
0 of 1 instances running, 1 down
1 of 1 instances running

App started

Showing health and status for app cfplay in org default / space development as admin...
OK

requested state: started
instances: 0/1
usage: 256M x 1 instances
urls: cfplay.54.164.17.171.xip.io

     state     since                    cpu    memory          disk
#0   running   2014-09-12 03:38:05 PM   0.0%   31.2M of 256M   0 of 1G
```
I could also specify the Go buildpack by name with the -b flag (output not included below):
```
$ cf push cfplay -b go_buildpack
```
If you are using an instance of Cloud Foundry that does not have a Go buildpack available, you can specify Cloud Foundry Go buildpack by specifying its address:
```
$ cf push cfplay -b https://github.com/cloudfoundry/go-buildpack.git
```
Once the application has started, you can go to the application's URL (specified at the end of the cf push output). In this example, I am using cfplay.54.164.17.171.xip.io. The initial splash screen gives me the option to start a new session:

![](http://cl.ly/image/2n1P0a3O2x2T/CFPlaygroundSplash-20140912.png)

In the new session, you can see that the "playground" has used the credentials supplied earlier in the config.json file:

![](http://cl.ly/image/2u0w0r2s0x0A/CFPlaygroundCLI-20140912.png)

Each time you load Cloud Foundry Playground you will be given the option to start a new session or return to an old one. If you want to return to a prior session, such as the session above, you simply supply the session ID generated at the top of the page (third line of text in the above screenshot) in the session ID field on the splash screen.

** Addendum **

Instructions for this deploy are also available on [Stark and Wayne's blog](https://blog.starkandwayne.com/2014/09/13/deploying-cfplayground-to-cloud-foundry/).
