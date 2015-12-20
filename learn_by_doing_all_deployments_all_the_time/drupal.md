# Drupal

## Need to return later. Posted question [PWS blog post](http://blog.pivotal.io/cloud-foundry-pivotal/products/how-to-deploy-drupal-to-pivotal-cf-within-seconds#comment-437533) - 11/18/2014

Install difficulty: **Average**<br />
Reason for difficulty rating: Minimal edits prior to deploy, optional edits to use environmental variables<br />
Primary Language: **PHP**

---

Before you begin, please open terminal and make sure that:
* You have targeted the PWS API (`https://api.run.pivotal.io`)
* You are logged into the correct account (if you have multiple)
* You are targeting the desired org and space (if you have multiple)

---

**Note: ** The download instructions for the current release of Drupal are located [here](https://www.drupal.org/documentation/install/download) and the link to the current release is [here](https://www.drupal.org/project/drupal). At the time of this writing, the current stable release of Drupal is version 7.x.

## Database

The latest versions of Drupal support both postgreSQL and MySQL. For this deployment, we will use the free tier for postgreSQL:

```
$ cf create-service elephantsql turtle drupaldb
Creating service drupaldb in org quinn / space development as quinn@thecodingmant.is...
OK
```

## Download and deploy

We need to download the tarball, extract the files, and delete the tarball:

```
$ curl -O http://ftp.drupal.org/files/projects/drupal-7.33.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 3153k  100 3153k    0     0   776k      0  0:00:04  0:00:04 --:--:--  776k

$ tar -xzf drupal-7.33.tar.gz

$ rm drupal-7.33.tar.gz
```

The buildpack we are going to use to deploy this application is [dmikusa's PHP buildpack](https://github.com/dmikusa-pivotal/cf-php-build-pack). In order to deploy Drupal, we need to add the `bz2`, `zlib`, `curl`, `mcrypt`, and `gd` extensions. To do this, in the application directory create a `.bp_config/options.json` file that contains the following:

```json
{
  "PHP_EXTENSIONS": [ "bz2", "zlib", "curl", "mcrypt", "gd"]
}
```

** Manifest **

To deploy our application, we're going to use a manifest file. Right now, we can keep our manifest pretty simple since we won't be using any elastic storage for our little demo:

```yaml
---
applications:
- name: drupal
  random-route: true
  services:
  - drupaldb
```
