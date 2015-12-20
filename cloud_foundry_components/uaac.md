# Interacting with the UAA with the UAAC

So to go back a couple of sections, what happened when we tried to assign users to `legacy` as `quinn@quinn.com`? Let's log back in as `quinn@quinn.com` and see what we are allowed to do with the orgs and spaces:

```
$ cf login -u quinn@quinn.com -p passw0rd
...

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

Well this is new, but it does provide us with a little more information. Previously, when we tried to assign roles to `tony` and `bruce` we simply received an error that the users didn't exist, at least insofar as the `quinn` account was concerned. Now, the output tells us that we have inadequate permissions to perform these actions and that, in fact, we don't have `scim`.

### What do the permissions mean?

By default, a Cloud Foundry admin has the following scopes:

* `clients.read` - Allows the user to inspect client registration
* `clients.write` - Allows the user to add, update, or delete client registration
* `clients.secret` - Allows the client to change a secret.
  * If `uaa.admin` not present, client is limited to its own secret.
  * Client must always provide current secret to change the secret.
* `password.write` - Provides the ability to change passwords.
  * For a client, scope must include `uaa.admin`.
  * For a user, scope must include `uaa.admin` or the user must provide the old password to change the password. (e.g. The presence of `uaa.admin` allows a user to set a temporary password on another user's behalf without knowing their current password.)
* `scim.read` - Allows the user to list or search all users in the UAA
* `scim.write` - Allows the user to add, update, or delete any users in the UAA
* `uaa.admin` - Adds admin priviledges to the following scopes: `token`, `clients`, and `password`.

For a complete list of scopes and what they provide, please take a look at the [UAA Security](https://github.com/cloudfoundry/uaa/blob/master/docs/UAA-Security.md) page in the Cloud Foundry GitHub repository.


#### Let's install the UAAC

UAAC is the UAA CLI. Since the UAAC is currently a gem, you can install the latest version of the UAAC with:

```
$ gem install cf-uaac
Successfully installed cf-uaac-3.1.0
1 gem installed
```

To quickly verify that we can use the UAAC, let's check the version:

```
$ uaac --version
UAA client 3.1.0
```

## Now for some user management!

In order to add `tony` and `bruce` to the space `legacy`, we will need to give `quinn` scim read and write priviledges. To do this, we will use the UAAC to add these permissions from the admin user.

To start, we need to target the UAA server and obtain the access token. The admin secret, `admin-secret`, is required to obtain the token. (We will cover how to obtain the admin secret after we cover BOSH.)

```
$ uaac target https://uaa.54.173.119.222.xip.io --skip-ssl-validation

Target: https://uaa.54.173.119.222.xip.io

$ uaac token client get admin -s admin-secret

Successfully fetched token via client credentials grant.
Target: https://uaa.54.173.119.222.xip.io
Context: admin, from client admin
```

Now that we have the token we can create a new admin account. Since I have a user `quinn@quinn.com` I will create the new admin user with that email address:

```
$ uaac user add quinn -p passw0rd --emails quinn@quinn.com

error response:
{
  "error": "access_denied",
  "error_description": "Access is denied"
}
```

Access is still denied? Even as `admin`? What gives?

Let's take a look at `uaac contexts`. This will display the permissions available to the admin user under `scope`:

```
$ uaac contexts

[0]*[https://uaa.54.173.119.222.xip.io]
  skip_ssl_validation: true

  [0]*[admin]
      client_id: admin
      access_token: eyJhbGciOiJSUzI1NiJ9.eyJqdGkiOiJjNDAxN2U2Ny04ZGI2LTQxMGYtYjcyNC0wOWY0ZDcwMzZlMTEiLCJzdWIiOiJhZG1pbiIsImF1dGhvcml0aWVzIjpbInBhc3N3b3JkLndyaXRlIiwiY2xpZW50cy53cml0ZSIsImNsaWVudHMucmVhZCIsInNjaW0ucmVhZCIsInVhYS5hZG1pbiIsImNsaWVudHMuc2VjcmV0Il0sInNjb3BlIjpbInBhc3N3b3JkLndyaXRlIiwiY2xpZW50cy53cml0ZSIsImNsaWVudHMucmVhZCIsInNjaW0ucmVhZCIsInVhYS5hZG1pbiIsImNsaWVudHMuc2VjcmV0Il0sImNsaWVudF9pZCI6ImFkbWluIiwiY2lkIjoiYWRtaW4iLCJncmFudF90eXBlIjoiY2xpZW50X2NyZWRlbnRpYWxzIiwiaWF0IjoxNDE1MTMyNzM3LCJleHAiOjE0MTUxNzU5MzcsImlzcyI6Imh0dHBzOi8vdWFhLjU0LjE3My4xMTkuMjIyLnhpcC5pby9vYXV0aC90b2tlbiIsImF1ZCI6WyJzY2ltIiwidWFhIiwicGFzc3dvcmQiLCJjbGllbnRzIl19.TfCY_7bOu3SOJpD5wKw28dyfZQKFNbTUTByCSKM-9tDUn3hvwX6RGaoPaXG8UZCOVZBNLqcde3imQK8h45QI7GNTQ8L8m443yZeHmMy2LPeGVjEliITs1nZ24b1PFOR0KISju4MnOvZGzd0bG2KLcfhgSEjpRsL4wUJiNKAFD7k
      token_type: bearer
      expires_in: 43199
      scope: password.write clients.write clients.read scim.read uaa.admin clients.secret
      jti: c4017e67-8db6-410f-b724-09f4d7036e11
```

Under scope, we can see our admin user has `password.write clients.write clients.read scim.read uaa.admin clients.secret`, but no `scim.write`. `scim.write` is what allows the admin user to create other users, including another admin user. We can update the permissions by doing the following:

```
$ uaac client update admin --authorities "password.write clients.write clients.read scim.read uaa.admin clients.secret scim.write"
  scope: uaa.none
  client_id: admin
  authorized_grant_types: client_credentials
  authorities: password.write clients.write clients.read scim.write scim.read
      uaa.admin clients.secret
```

What this command does is takes the permissions listed under `scope` and appends `scrim.write`. If you run `uaac contexts` to check the scope, you'll see the scope hasn't yet been updated:

```
$ uaac contexts

[0]*[https://uaa.54.173.119.222.xip.io]
  skip_ssl_validation: true

  [0]*[admin]
      client_id: admin
      access_token: eyJhbGciOiJSUzI1NiJ9.eyJqdGkiOiJjNDAxN2U2Ny04ZGI2LTQxMGYtYjcyNC0wOWY0ZDcwMzZlMTEiLCJzdWIiOiJhZG1pbiIsImF1dGhvcml0aWVzIjpbInBhc3N3b3JkLndyaXRlIiwiY2xpZW50cy53cml0ZSIsImNsaWVudHMucmVhZCIsInNjaW0ucmVhZCIsInVhYS5hZG1pbiIsImNsaWVudHMuc2VjcmV0Il0sInNjb3BlIjpbInBhc3N3b3JkLndyaXRlIiwiY2xpZW50cy53cml0ZSIsImNsaWVudHMucmVhZCIsInNjaW0ucmVhZCIsInVhYS5hZG1pbiIsImNsaWVudHMuc2VjcmV0Il0sImNsaWVudF9pZCI6ImFkbWluIiwiY2lkIjoiYWRtaW4iLCJncmFudF90eXBlIjoiY2xpZW50X2NyZWRlbnRpYWxzIiwiaWF0IjoxNDE1MTMyNzM3LCJleHAiOjE0MTUxNzU5MzcsImlzcyI6Imh0dHBzOi8vdWFhLjU0LjE3My4xMTkuMjIyLnhpcC5pby9vYXV0aC90b2tlbiIsImF1ZCI6WyJzY2ltIiwidWFhIiwicGFzc3dvcmQiLCJjbGllbnRzIl19.TfCY_7bOu3SOJpD5wKw28dyfZQKFNbTUTByCSKM-9tDUn3hvwX6RGaoPaXG8UZCOVZBNLqcde3imQK8h45QI7GNTQ8L8m443yZeHmMy2LPeGVjEliITs1nZ24b1PFOR0KISju4MnOvZGzd0bG2KLcfhgSEjpRsL4wUJiNKAFD7k
      token_type: bearer
      expires_in: 43199
      scope: password.write clients.write clients.read scim.read uaa.admin clients.secret
      jti: c4017e67-8db6-410f-b724-09f4d7036e11
```

However, take a look at [step 7](http://docs.cloudfoundry.org/adminguide/uaa-user-management.html) on the Cloud Foundry UAA User Management page you'll see that in order to get the current permissions, they execute `uaac client get admin`:

```
$ uaac client get admin
  scope: uaa.none
  client_id: admin
  resource_ids: none
  authorized_grant_types: client_credentials
  authorities: password.write clients.write clients.read scim.write scim.read
      uaa.admin clients.secret
```

The output of `uaac client get admin` *does* show `scim.write` has been added to the credentials (here listed as `authorities`). So, which is it? Does our admin user now have `scim.write` or not?

Let's test it out by trying to add our new admin user again:

```
$ uaac user add quinn -p passw0rd --emails quinn@quinn.com

error response:
{
  "error": "access_denied",
  "error_description": "Access is denied"
}
```

Ah, the same as before. So it looks like we *don't* have `scim.write` just yet.

So, what's going on? Well we added `scim.write` to our admin user, but we did *not* update our token. Recall earlier that we ran `uaac token client get admin -s admin-secret` to obtain the token? Well, that token is still telling UAAC that our user does not have `scim.write`. Now we must delete our current token and obtain an updated one:

```
$ uaac token delete

$ uaac token client get admin -s admin-secret

Successfully fetched token via client credentials grant.
Target: https://uaa.54.173.119.222.xip.io
Context: admin, from client admin
```
Now when we run `uaac contexts` and look at `authorities` from `uaac client get admin`, we'll see the permissions match:

```
$ uaac contexts

[0]*[https://uaa.54.173.119.222.xip.io]
  skip_ssl_validation: true

  [0]*[admin]
      client_id: admin
      access_token: eyJhbGciOiJSUzI1NiJ9.eyJqdGkiOiI5ZTc2MzE3Ni1kYjg1LTQzNWQtYWY3MC1hNGZmMDRhYTI0YTgiLCJzdWIiOiJhZG1pbiIsImF1dGhvcml0aWVzIjpbInBhc3N3b3JkLndyaXRlIiwiY2xpZW50cy53cml0ZSIsImNsaWVudHMucmVhZCIsInNjaW0ud3JpdGUiLCJzY2ltLnJlYWQiLCJ1YWEuYWRtaW4iLCJjbGllbnRzLnNlY3JldCJdLCJzY29wZSI6WyJwYXNzd29yZC53cml0ZSIsImNsaWVudHMud3JpdGUiLCJjbGllbnRzLnJlYWQiLCJzY2ltLndyaXRlIiwic2NpbS5yZWFkIiwidWFhLmFkbWluIiwiY2xpZW50cy5zZWNyZXQiXSwiY2xpZW50X2lkIjoiYWRtaW4iLCJjaWQiOiJhZG1pbiIsImdyYW50X3R5cGUiOiJjbGllbnRfY3JlZGVudGlhbHMiLCJpYXQiOjE0MTUxNDk1MDEsImV4cCI6MTQxNTE5MjcwMSwiaXNzIjoiaHR0cHM6Ly91YWEuNTQuMTczLjExOS4yMjIueGlwLmlvL29hdXRoL3Rva2VuIiwiYXVkIjpbInNjaW0iLCJ1YWEiLCJwYXNzd29yZCIsImNsaWVudHMiXX0.mUdacPI1hJdpJHZpg3rU8CwgzUxoBUI2GDwUloYgSM8M9X8eoC9nTwtjl9VEZmxAo8pnvA6bedqwgqfKjiBoN97MzhLFE61aM2sqIoDO8QsGTXwBgkIMgYASKvbuvQT49-7rgXaIQIRNlyUeAgDdkmTUw4-bgjWVQlOwWjycq4w
      token_type: bearer
      expires_in: 43199
      scope: password.write clients.write clients.read scim.write scim.read uaa.admin clients.secret
      jti: 9e763176-db85-435d-af70-a4ff04aa24a8

$ uaac client get admin | awk '/:/{e=0}/authorities:/{e=1;if(e==1){$1="";print}}'
 password.write clients.write clients.read scim.write scim.read uaa.admin clients.secret
```
(For those unfamiliar, we used `awk` to print only the output of `authorities` instead of the entire output for `uaac client get admin`, although you can do that as well if you wish. For some more information about `awk`, please see the BASH prerequisite materials in the Appendix.)

Now we can see that `authorities` and `scope` match. So if we try to add our new admin user:

```
$ uaac user add quinn -p passw0rd --emails quinn@quinn.com
user account successfully added
```

Awesome, we have our user! Let's try logging in and seeing what our user can do:

```
$ cf login -u quinn -p passw0rd
API endpoint: https://api.54.173.119.222.xip.io
Authenticating...
OK

Select an org (or press enter to skip):

Org>

API endpoint:   https://api.54.173.119.222.xip.io (API version: 2.2.0)
User:           quinn@quinn.com
No org or space targeted, use 'cf target -o ORG -s SPACE'

$ cf orgs
Getting orgs as quinn...

name
No orgs found
```

So our work is still not done. We have our nice, shiny new admin user account but it can't do anything. Why? Well although we have provided our user with `scim.read` (allowing the user to list other users in orgs and spaces) and `scim.write` (allowing our user to manage users in orgs and spaces), we haven't actually added our user to any orgs or spaces. Since the `admin` account was created with the TryCF instance, and we did all of our work from `admin` initially, the `admin` account has access to all orgs and spaces.

To keep it simple, let's make our new admin user an org manager for `natasha` and a space manager for `development` while in the `admin` account:

```
$ cf set-org-role quinn natasha OrgManager
Assigning role OrgManager to user quinn in org natasha as admin...
OK

$ cf set-space-role quinn natasha development SpaceManager
Assigning role SpaceManager to user quinn in org natasha / space development as admin...
OK
```

Now let's flip back into our new admin user account and take a look:

```
quinn ~ $ cf login -u quinn -p passw0rd
API endpoint: https://api.54.173.119.222.xip.io
Authenticating...
OK

Targeted org natasha

Select a space (or press enter to skip):
1. development
2. testing
3. staging
4. production
5. legacy

Space> 1
Targeted space development

API endpoint:   https://api.54.173.119.222.xip.io (API version: 2.2.0)
User:           quinn@quinn.com
Org:            natasha
Space:          development
```

We can see all the spaces in org `natasha` because we are a manager for that org. What happens if we try to list the users in that org:

```
$ cf org-users natasha
Getting users in org natasha as quinn...

ORG MANAGER
FAILED
Failed fetching org-users for role ORG MANAGER.
Server error, status code: 403, error code: access_denied, message: Invalid token does not contain resource id (scim)
```

Uh oh! What are we missing now?

Based on step 9 of the documentation, we still need to add our new user to some groups:

```
$ uaac member add cloud_controller.admin quinn
success
$ uaac member add uaa.admin quinn
success
$ uaac member add scim.read quinn
success
$ uaac member add scim.write quinn
success
```

Now logging back in as our new admin user, let's see if we can list users:

```
$ cf org-users natasha
Getting users in org natasha as quinn...

ORG MANAGER
  quinn@quinn.com

BILLING MANAGER

ORG AUDITOR
  tony@quinn.com
  bruce@quinn.com

$ cf space-users natasha development
Getting users in org natasha / space development as quinn

SPACE MANAGER
  quinn
  quinn@quinn.com

SPACE DEVELOPER
  quinn
  tony@quinn.com
  quinn@quinn.com

SPACE AUDITOR
```

Success! Finally, right? Here we can see that our new admin account is listed as a manager in both `natasha` and `development`.

**Check it out: ** What happens if...
* ... you list the users in the `default` org or its `development` space as either your new admin user or as `admin`?
* ... you try to make your user account (e.g. `quinn@quinn.com`) an admin user?
* ...you make your second admin user *before* creating your second org and spaces as `admin`?
* ...you make your second admin user *before* creating your second org and spaces while logged into your new admin account?

#### UAAC Commands

A quick review of the commands covered in this section:

| Command | Usage |
|----------|--------|
| `uaac contexts` | "to display the users and applications authorized by the UAA server, and the permissions granted to each user and application." |
| `uaac member add` | asdf |


# To add

* Why couldn't we assign `tony` and `bruce` to `legacy` using our own account?
* Why can't we use `cf space-users` or `cf org-users` as `quinn`?
* There is no admin account, except when we use TryCF we are `admin`. What's up with that?
* Org and space roles are case sensitive
* `cf passwd`, unset org/space roles, `cf delete-user`
* View org/space users
