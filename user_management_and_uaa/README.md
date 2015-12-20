# Basics of User Management and Roles

## How do I manage my users?

Similar to the commands for orgs and spaces, PWS does not allow certain user management tasks to be done via the CLI, so we are going to manage our users two ways: first we are going to use the CLI and TryCF and then we will use PWS's web UI.


### Create the user accounts

We'll start by creating three users: `tony`, `bruce`, and our own account. I'm naming my account `quinn` after myself and using the domain `quinn.com` for the email addresses:

```
$ cf create-user tony@quinn.com passw0rd; cf create-user bruce@quinn.com passw0rd; cf create-user quinn@quinn.com passw0rd

Creating user tony@quinn.com as admin...
OK

TIP: Assign roles with 'cf set-org-role' and 'cf set-space-role'
Creating user bruce@quinn.com as admin...
OK

TIP: Assign roles with 'cf set-org-role' and 'cf set-space-role'
Creating user quinn@quinn.com as admin...
OK

TIP: Assign roles with 'cf set-org-role' and 'cf set-space-role'
```
Notice that Cloud Foundry doesn't check the validity of the email addresses - it simply uses them as the user names. In fact, Cloud Foundry user names do not need to be email addresses, although they typically are by convention.

### Assign roles

At the org level, we'll make `tony` and `bruce` org auditors and our account the the manager:

```
$ cf set-org-role tony@quinn.com natasha OrgAuditor; cf set-org-role bruce@quinn.com natasha OrgAuditor; cf set-org-role quinn@quinn.com natasha OrgManager

Assigning role OrgAuditor to user tony@quinn.com in org natasha as admin...
OK
Assigning role OrgAuditor to user bruce@quinn.com in org natasha as admin...
OK
Assigning role OrgManager to user quinn@quinn.com in org natasha as admin...
OK
```

The spaces will look a bit more complicated because there are three users and four spaces in this org. First, we'll make `tony` a developer in `development` and `bruce` a developer in `testing`:

```
$ cf set-space-role tony@quinn.com natasha development SpaceDeveloper; cf set-space-role bruce@quinn.com natasha testing SpaceDeveloper

Assigning role SpaceDeveloper to user tony@quinn.com in org natasha / space development as admin...
OK
Assigning role SpaceDeveloper to user bruce@quinn.com in org natasha / space testing as admin...
OK
```

Now we'll set our own roles. We want to be a "super user" of sorts, so we'll make our account a developer and manager for all spaces. Since you cannot set multiple space roles simultaneously, even for the same user, we have to call `cf set-space-role` a total of eight times. The first "block" of commands will set our account as a developer for all spaces and the second "block" of commands will set our account as a manager for all spaces:

```
$ cf set-space-role quinn@quinn.com natasha development SpaceDeveloper; cf set-space-role quinn@quinn.com natasha testing SpaceDeveloper; cf set-space-role quinn@quinn.com natasha staging SpaceDeveloper; cf set-space-role quinn@quinn.com natasha production SpaceDeveloper

Assigning role SpaceDeveloper to user quinn@quinn.com in org natasha / space development as admin...
OK
Assigning role SpaceDeveloper to user quinn@quinn.com in org natasha / space testing as admin...
OK
Assigning role SpaceDeveloper to user quinn@quinn.com in org natasha / space staging as admin...
OK
Assigning role SpaceDeveloper to user quinn@quinn.com in org natasha / space production as admin...
OK

$ cf set-space-role quinn@quinn.com natasha development SpaceManager; cf set-space-role quinn@quinn.com natasha testing SpaceManager; cf set-space-role quinn@quinn.com natasha staging SpaceManager; cf set-space-role quinn@quinn.com natasha production SpaceManager

Assigning role SpaceManager to user quinn@quinn.com in org natasha / space development as admin...
OK
Assigning role SpaceManager to user quinn@quinn.com in org natasha / space testing as admin...
OK
Assigning role SpaceManager to user quinn@quinn.com in org natasha / space staging as admin...
OK
Assigning role SpaceManager to user quinn@quinn.com in org natasha / space production as admin...
OK
```

### How do roles restrict access?

To provide a firmer understanding of how the org and space roles restrict access, let's play around with our newly created user accounts.

First, let's log in as `tony`. Since the API will still be targeted, we only need to specify the user name and password:

```
$ cf login -u tony@quinn.com -p passw0rd
API endpoint: https://api.54.173.26.223.xip.io
Authenticating...
OK

Targeted org natasha

Targeted space development



API endpoint:   https://api.54.173.26.223.xip.io (API version: 2.2.0)
User:           tony@quinn.com
Org:            natasha
Space:          development
```

Remember that we made `tony` and org auditor for `natasha` and a developer for `development`. We did not assign any roles for the org `default` or any of the three other spaces in `natasha`, so `tony` cannot see the `default` org or the other spaces in `natasha`:

```
$ cf orgs
Getting orgs as tony@quinn.com...

name
natasha

$ cf spaces
Getting spaces in org natasha as tony@quinn.com...

name
development
```

Let's say that `tony` wants to create a new space for legacy applications, aptly named `legacy`. Is he able to do so?

```
$ cf create-space legacy
Creating space legacy in org natasha as tony@quinn.com...
FAILED
Server error, status code: 403, error code: 10003, message: You are not authorized to perform the requested action
```

The answer is no: since he does not have manager permissions he cannot create a new space.

In the same vein, if `tony` wants to add `bruce` to his team, he cannot be cause he is not a space manager:

```
$ cf set-space-role bruce@quinn.com natasha development SpaceDeveloper

FAILED
User bruce@quinn.com not found
```

In fact, the user `bruce` is not even visible to `tony`!

By contrast, let's see what happens when we log into our own account:

```
$ cf login -u quinn@quinn.com -p passw0rd
API endpoint: https://api.54.173.26.223.xip.io
Authenticating...
OK

Targeted org natasha

Select a space (or press enter to skip):
1. development
2. testing
3. staging
4. production

Space> 1
Targeted space development



API endpoint:   https://api.54.173.26.223.xip.io (API version: 2.2.0)
User:           quinn@quinn.com
Org:            natasha
Space:          development
```

Immediately you can see there is a change: since our account has roles assigned to all four spaces in org `natasha`, we are prompted to choose a space to target when we log in. At this point it doesn't matter, so you can choose to target whichever space you please.

First, let's try and create that legacy space for `tony`:

```
$ cf create-space legacy
Creating space legacy in org natasha as quinn@quinn.com...
OK
Assigning role SpaceManager to user quinn@quinn.com in org natasha / space legacy as quinn@quinn.com...
OK
Assigning role SpaceDeveloper to user quinn@quinn.com in org natasha / space legacy as quinn@quinn.com...
OK

TIP: Use 'cf target -o natasha -s legacy' to target new space
```

A couple of things happened. First, the new space was created. Then, Cloud Foundry automatically assigned our account the roles of space manager and developer.

Let's say we want both `tony` and `bruce` to `legacy` as developers:

```
$ cf set-space-role bruce@quinn.com natasha legacy SpaceDeveloper; cf set-space-role tony@quinn.com natasha legacy SpaceDeveloper

FAILED
User bruce@quinn.com not found

FAILED
User tony@quinn.com not found
```

Uh oh. What now?

To get `tony` and `bruce` squared away, let's head back over to our `admin` account and try again (output condensed for brevity):

```
$ cf login -u admin -p admin
API endpoint: https://api.54.173.26.223.xip.io
Authenticating...
OK
...

API endpoint:   https://api.54.173.26.223.xip.io (API version: 2.2.0)
User:           admin
Org:            natasha
Space:          legacy

$ cf set-space-role bruce@quinn.com natasha legacy SpaceDeveloper; cf set-space-role tony@quinn.com natasha legacy SpaceDeveloper

Assigning role SpaceDeveloper to user bruce@quinn.com in org natasha / space legacy as admin...
OK
Assigning role SpaceDeveloper to user tony@quinn.com in org natasha / space legacy as admin...
OK
```

Now let's see who's in the `legacy` space:

```
$ cf space-users natasha legacy
Getting users in org natasha / space legacy as admin

SPACE MANAGER
  quinn@quinn.com

SPACE DEVELOPER
  tony@quinn.com
  bruce@quinn.com
  quinn@quinn.com

SPACE AUDITOR
```

Success!

### Addenda

#### Who's the admin?

You may have noticed that there is an `admin` account crated with TryCF that had "superuser" priviledges that are beyond those provided by the various org and space roles that we assigned even our account.

#### `whoami`/`$USER`

Typically, wehen you are using Cloud Foundry you will only need a single user account and your roles determine your access to various orgs and spaces. There may be some cases, for example testing accounts or working through some of the sections in this book, where you will be logging in under different accounts. Eventually you may forget which user you are actually logged in as!

There is not a `whoami`-esque command in Cloud Foundry, but most commands include who is executing the command as part of the output. You can see this above when we execute `cf org-users` and `cf space-users` - the output includes `Getting users in org natasha / space legacy as admin`. This tells us we ran the command from the `admin` account. You can also run commands like `cf orgs` or `cf spaces` to see your current user name in the output.

#### CF CLI Commands

A quick review of the commands covered in this section:

| Command | Usage |
|----------|--------|
| `cf create-user` | Create a user |
| `cf delete-user` | Delete a user |
| `cf passwd` | Change password |
| `cf set-org-role` | Sets org role. Options are OrgAuditor, BillingManager, and OrgAuditor |
| `cf unset-org-role` | Unsets org role. |
| `cf org-users` | View all users in an org. |
| `cf set-space-role` | Sets space role. Options are SpaceManager, SpaceDeveloper, and SpaceAuditor. |
| `cf unset-space-role` | Unsets space role. |
| `cf space-users` | View all users in a space. |



# To add

* Why couldn't we assign `tony` and `bruce` to `legacy` using our own account?
* Why can't we use `cf space-users` or `cf org-users` as `quinn`?
* There is no admin account, except when we use TryCF we are `admin`. What's up with that?
* Org and space roles are case sensitive
* `cf passwd`, unset org/space roles, `cf delete-user`
* View org/space users


# To add: User management: UI exercises with PWS

**Notice:** This section is for paid PWS users only - free users are limited to a single org. If you are using the free trial and attempt the exercises below you will run into an issue where you can create a second org, but you will not be able to access it (or even delete it) without entering billing information.

Since PWS does not allow CLI management of users, these exercises will be significantly simplified from what we could do with the CF CLI and UAAC. Nonetheless, it is important to be able to understand GUI user management.
