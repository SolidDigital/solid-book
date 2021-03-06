# Wordpress

tags: php, wordpress, mysql

## SVG Uploads

```php
function cc_mime_types($mimes) {
    $mimes['svg'] = 'image/svg+xml';
    return $mimes;
}
add_filter('upload_mimes', 'cc_mime_types');
```

## Create an admin user via wp cli
```
THE_ENV=production wp user create example.user exmple.user@soliddigital.com --role=administrator
```
This returns the new user's password.

## Create an admin user via mysql
Replace the dummy data with your info:
```sql
INSERT INTO `wp_users` (
	`user_login`,
	`user_pass`,
	`user_nicename`,
	`user_email`,
	`user_url`,
	`user_registered`,
	`user_activation_key`,
	`user_status`,
	`display_name`
) VALUES (
	'username',
	MD5('password'),
	'nice-name',
	'email_address',
	'website',
	NOW(),
	'',
	'0',
	'Full Name'
);
```

Then grab the `$user_id` of your new user to use in the next insert:
```sql
INSERT INTO `wp_usermeta` (
	`user_id`,
	`meta_key`,
	`meta_value`
) VALUES (
	$user_id,
	'wp_capabilities',
	'a:1:{s:13:"administrator";s:1:"1";}'
), (
	$user_id,
	'wp_user_level',
	'10'
);
```
---
###### Source: http://www.wpbeginner.com/wp-tutorials/how-to-add-an-admin-user-to-the-wordpress-database-via-mysql/

## Wp cron
Prefer calling `wp-cron.php` manually with server crontab, rather than default wp cron behavior, which checks if it needs to run on every page load, and then runs if needed, extending that user's page load. To implement:
* disable default wp cron behavior in `wp-config.php`
  ```
  define('DISABLE_WP_CRON', true);
  ```
* then add call to `wp-cron.php` at desired frequency in your server's crontab (using `wget` here but you can use whatever to make the request):
  ```
  0 * * * * wget http://www.example.com/wp-cron.php
  ```
* To actually implement a regular task to be run by `wp-cron.php`, use `wp_schedule_event()`. Full example:

```php
add_action('init', 'init');
add_action('my_hourly_task', 'my_hourly_task');

function init()
	// Check if your task is already scheduled.
	if (!wp_next_scheduled('my_hourly_task')) {
		// Schedule the event, passing the interval, and the name of the action hook to call.
		// Now when wp-cron.php is called (via crontab, maybe), if my_hourly_task is scheduled,
		// and the time interval is elapsed, it will run do_action('my_hourly_task');
		wp_schedule_event(time(), 'hourly', 'my_hourly_task');
	}
}

public function my_hourly_task() {
	// Do the work that needs to run every hour.
}
```

## Queries

To see the queries during a page render:

```php
define('SAVEQUERIES', true);
```

Then the queries are stored on:

```php
$wpdb->queries;
```

## Multisite network settings
* A wordpress multisite network has additional settings in admin at My Sites > Network Admin > Settings, or `/wp-admin/network/settings.php`
* Notable settings include file upload settings
  * Max upload file size: default is 1500k
