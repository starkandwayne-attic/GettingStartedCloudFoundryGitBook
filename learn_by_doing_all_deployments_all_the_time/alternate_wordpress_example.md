# Wordpress (dmikusa)

Install difficulty: **Easy**<br />
Reason for difficulty rating: Minor file edits (some optional)<br />
Primary Language: **PHP**

---

Before you begin, please open terminal and make sure that:
* You have targeted the PWS API (`https://api.run.pivotal.io`)
* You are logged into the correct account (if you have multiple)
* You are targeting your desired org and space (if you have multiple)

---

This deployment of Wordpress has been specifically edited for Cloud Foundry deployment. For a more in-depth explanation of the steps below, please refer to the Wordpress deployment.

## How to Deploy Wordpress (dmikusa)

1. Create the MySQL service instance (here named `dmikusa-wp`): <br />`cf create-service cleardb spark dmikusa-wp`
1. Clone the github repository: <br />`git clone https://github.com/dmikusa-pivotal/cf-ex-wordpress.git`
1. Edit the `manifest.yml` file:
  * If you do not plan to use persistent storage, delete the various `SSH` environmental variables declared under `env`.
  * Under `applications`, change the `name`, `host`, and `services` fields to the appropriate names.
    * If you want the `host` name to be the same as the application name (default behavior) you can delete the `host` line.
    * `path: .` simply declares that the app is being deployed from the same path as the `manifest.yml` file. Since this is the default behavior, there will be no change if you delete this line.
  * You might want to specify the disk limit (memory is already specified). From the prior Wordpress deploy, we can see that 128M is sufficient for both disk and memory, so I added the line `disk_quota: 128M`.
1. If you are planning to use this app to deploy Wordpress, then I strongly encourage you to [generate the secret keys](https://api.wordpress.org/secret-key/1.1/salt) and add them to your `wp-config.php` file. This step is mainly optional in the sense that the app will deploy and function normally without the keys. It ~~should not be~~ is not optional if you have security concerns/plan on running the app for more than just testing.
1. Although not specified in the README, if you take a moment to look through the code in the repo you'll see that `dmikusa` has specified his email address as the `ADMIN_EMAIL` in the [`.bp-config/options.json`](https://github.com/dmikusa-pivotal/cf-ex-wordpress/blob/master/.bp-config/options.json) file. I recommend changing this to your email.
1. Push the application: <br />`cf push`
1. Setup the application and test that it is ready for use: <br />![](../images/wordpressInstall-8.png)

---

#### <sup>\*</sup> - The files I used to deploy this app:

`manifest.yml`:

```yaml
---
applications:
- name: cf-ex-wordpress
  memory: 128M
  disk_quota: 128M
  buildpack: https://github.com/dmikusa-pivotal/cf-php-build-pack.git
  host: wordpress-dmikusa
  services:
  - dmikusa-wp
```

`.bp-config/options.json`:<br />
<small>(Except I used my actual email.)</small>
```json
{
    "ADMIN_EMAIL": "myEmail@email.com",
    "PHP_EXTENSIONS": ["mbstring", "mysqli", "mcrypt", "gd", "zip", "curl", "openssl", "sockets"]
}
```

`wp-config.php`:<br />
<small>(I recommend generating different keys or using none)</small>
```php
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, WordPress Language, and ABSPATH. You can find more information
 * by visiting {@link http://codex.wordpress.org/Editing_wp-config.php Editing
 * wp-config.php} Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** Read MySQL service properties from _ENV['VCAP_SERVICES']
$services = json_decode($_ENV['VCAP_SERVICES'], true);
$service = $services['cleardb'][0];  // pick the first MySQL service

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', $service['credentials']['name']);

/** MySQL database username */
define('DB_USER', $service['credentials']['username']);

/** MySQL database password */
define('DB_PASSWORD', $service['credentials']['password']);

/** MySQL hostname */
define('DB_HOST', $service['credentials']['hostname'] . ':' . $service['credentials']['port']);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'y?<D;iZgGf++VMyj(O/aMVxzTFaefA<T|w2niki|SE] R0^1D9Z.UaChFlus|&PW');
define('SECURE_AUTH_KEY',  'yKN|-W-WslU9_s!Gl&< m @{^*Vl#/w./%7r@<u!SLU*Fh&>+R%A[GJWU8XfBz*-');
define('LOGGED_IN_KEY',    ']k(K/5}.7,Q/ww5BeZ/F#zw9,<G_X!-}VG.LN-H&sD@|M_iTKAF7-nT 61l3%Sn.');
define('NONCE_KEY',        'nlM.+<|cZ-{-homB~H&oYW8vKq%O!eLg`^O^Wi#=/cq_*`EL5P-wn=>sSiCq*^,L');
define('AUTH_SALT',        'A_n`t0$KFd-&/cnO,V!BeGlirOYr%8;E&=|qeo9OTRYh&rT3:U_/<cgTI~tN1T(d');
define('SECURE_AUTH_SALT', ',z|d{,m8N)Wyv-e84Br=,|P1E-QmrxKN@rB|nf#p(5%ZlAlj%gkr!c|p]30V.6Z5');
define('LOGGED_IN_SALT',   '9IG?@u9kM$+1:(lU*p`>3axe5f1S+TIAaGGuT%}K3V0QSvFA%?`=mo84I4HL;?8~');
define('NONCE_SALT',       'vvdtYrFDThGa0;8-lv}.k@*Ha-?c_6Cqg+[vcw+LJks1%;3;LD#{0,qirE%lZC#;');

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * WordPress Localized Language, defaults to English.
 *
 * Change this to localize WordPress. A corresponding MO file for the chosen
 * language must be installed to wp-content/languages. For example, install
 * de_DE.mo to wp-content/languages and set WPLANG to 'de_DE' to enable German
 * language support.
 */
define('WPLANG', '');

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', false);

/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
        define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```
