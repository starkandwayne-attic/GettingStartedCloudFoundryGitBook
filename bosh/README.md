# BOSH

#### Sections outline?
* What is BOSH
 * microBOSH
 * BOSH
 * BOSHLite
  * [Optimizing BOSH Lite deployments](http://blog.pivotal.io/cloud-foundry-pivotal/products/optimizing-bosh-lite-deployment-times)
* Why BOSH instead of, say, Terraform (is that even an option?)
* Some BOSH components, e.g. stemcells
 * What is a stemcell
* How to interact with BOSH
* What is a BOSH deployment/purpose
* How to create a BOSH deployment
* How to deploy CF with BOSH
* [Updated Google Compute with BOSH/CF](http://blog.pivotal.io/cloud-foundry-pivotal/products/deploy-and-update-your-google-compute-engine-vms-using-cloud-foundry-bosh)



## Why BOSH

** Per conversation with Wayne include in the intro/why BOSH ** : "You may find other mechanisms outside of BOSH to deploy Cloud Foundry; however, these will limit what releases you are able to deploy and will lock you into a particular vendor."

## Flashback: How we figured out the admin secret

In an earlier chapter, we were faced with the dilemma that our org and space manager `quinn@quinn.com` did not have adequate permissions to add new users to orgs and spaces, or to view the users in orgs and spaces, due to inadequate permissions:

```
$ cf org-users natasha
Getting users in org natasha as quinn@quinn.com...

ORG MANAGER
FAILED
Failed fetching org-users for role ORG MANAGER.
Server error, status code: 403, error code: access_denied, message: Invalid token does not contain resource id (scim)

$ cf space-users natasha legacy
Getting users in org natasha / space legacy as quinn@quinn.com

SPACE MANAGER
FAILED
Failed fetching space-users for role SPACE MANAGER.
Server error, status code: 403, error code: access_denied, message: Invalid token does not contain resource id (scim)
```

In order to resolve this issue we needed the admin secret, and at the time I simply provided the admin secret. Now let's discuss how I figured out the admin secret.

### What we know about TryCF

Let's start from the vantage point that all that we know of TryCF is what has been published in the TryCF documentation. That is, we will not be assuming any inside knowledge of the deployment.

From an outsider's perspective, we know that TryCF is an application that installs Cloud Foundry and an AWS EC2 instance. Based on what we've learned about BOSH and Cloud Foundry, we know that there must be a BOSH manifest that was used to configure the instance.

First, let's take a look at the TryCF Power User [doc page](https://trycf.starkandwayne.com/docs/#!upgradeTryCFPowerUser.md). There, we see that we can target BOSH using the IP address in the email. In my case, since I've spun up a new TryCF instance, my new API target is:

```
 cf api https://api.54.173.119.222.xip.io --skip-ssl-validation
```

According to the doc, that means that I can target BOSH by using the IP address `54.173.119.222`:

```
$ bosh target 54.173.119.222 trycf
Target set to 'Bosh Lite Director'
Your username: admin
Enter password: *****
Logged in as 'admin'
```

Reading the document further, I can see there is a section that explains how I can edit the Cloud Foundry deployment. Although I don't want to edit the deployment, this tells me how to access the manifest file that was used to deploy TryCF:

```
$ bosh download manifest cf-warden cf.yml
Deployment manifest saved to 'cf.yml'
```

This will save the manifest file as `cf.yml` in your current working directory. Open `cf.yml` in your text editor of choice, and look for the UAA section:

```yaml
  uaa:
    admin:
      client_secret: admin-secret
    batch:
      password: batch-password
      username: batch-username
    catalina_opts: -Xmx384m -XX:MaxPermSize=128m
    cc:
      client_secret: cc-secret
```

Here we can see the the admin secret is `admin-secret`. If we use this secret as we did in the UAA chapter, we are able to successfully obtain the client token:

```
$ uaac target https://uaa.54.173.119.222.xip.io --skip-ssl-validation

Target: https://uaa.54.173.119.222.xip.io

$ uaac token client get admin -s admin-secret

Successfully fetched token via client credentials grant.
Target: https://uaa.54.173.119.222.xip.io
Context: admin, from client admin
```
