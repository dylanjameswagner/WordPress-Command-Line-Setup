# WordPress Command Line Setup

## Standard Install (example.com)
```shell
cd ~/Sites
wget http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
rm -rf latest.tar.gz
mv wordpress/ example.com; cd $_
```

## Subdirectory Install (example.com)
```shell
mkdir ~/Sites/example.com; cd $_
wget http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
rm -rf latest.tar.gz
mv wordpress/ wp
cp wp/index.php index.php
sed -ie "s/'\/wp-blog-header.php/'\/wp\/wp-blog-header.php/g" index.php
rm index.phpe
```

## Breaking It Down

##### Project Directory
Make project directory, change directory into project directory 
```shell
mkdir ~/Sites/example.com; cd $_
```
```shell
mkdir ~/Sites/example.com
cd ~/Sites/example.com
```

##### Install WordPress
Download the latest release of WordPress, extract achive, and remove archive
```shell
wget http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
rm -rf latest.tar.gz
```

##### Rename Directory or Subdirectory
Move ```wordpress``` to ```example.com``` or ```wp```
```shell
mv wordpress/ example.com
```
```shell
mv wordpress/ wp
```

##### Dulicate index.php
Copy the ```index.php``` file to the project root
```shell
cp wp/index.php index.php
```

##### Link to the Subdirectory
Search and replace in the ```index.php``` to add the subdirectory, remove backup file
```shell
sed -ie "s/'\/wp-blog-header.php/'\/wp\/wp-blog-header.php/g" index.php
rm index.phpe
```

## Hosts
Setup a hosts entry for example.com.local

vhosts _or_ MAMP Pro

## Database

phpMyAdmin _or_ Sequel Pro

## Configuration (example.com)
Copy the ```wp-config-sample.php``` file to ```wp-config.php```
```shell
cp wp-config-sample.php wp-config.php
```
```shell
cp wp/wp-config-sample.php wp/wp-config.php
```

### Multi-environment MySQL Settings
Replace single environment settings with multi-environment switch settings

__Original__
```php
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'database_name_here');

/** MySQL database username */
define('DB_USER', 'username_here');

/** MySQL database password */
define('DB_PASSWORD', 'password_here');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
```
__Replace__
```php
/** Environments **/
$environments = array(
	'LIVE' => array(
		'example.com',
		'www.example.com',
		'www.example.com.php53-14.ord1-1.websitetestlink.com',
	),
	'DEV' => array(
		'dev.example.com',
		'dev.example.com.php53-10.ord1-1.websitetestlink.com',
	),
	'LOCAL' => array(
		'example.com.local:8888',
		'example.com.local.10.3.1.196.xip.io:8888',
	),
);

/** Define Environment **/
foreach ($environments AS $environment => $option) :
	if ((is_array($option)
	  && in_array($_SERVER['HTTP_HOST'],$option))
	  || strstr($_SERVER['HTTP_HOST'],$option)) :
		define('ENVIRONMENT',$environment);
		return;
	else :
		define('ENVIRONMENT','LIVE')
	endif;
endforeach;

/** Override WordPress address and installation directory **/
switch (ENVIRONMENT) :
case 'LIVE' :
	define('WP_HOME'	,'http://www.example.com');
	define('WP_SITEURL'	,'http://www.example.com/wp');
	define('WP_DEBUG'	, false);
	break;

default :
	define('WP_HOME'	,'http://'.$_SERVER['HTTP_HOST']);
	define('WP_SITEURL'	,'http://'.$_SERVER['HTTP_HOST'].'/wp');
	define('WP_DEBUG'	, false);
	break;

endswitch;

/** MySQL settings - You can get this info from your web host **/
switch (ENVIRONMENT) :
case 'LIVE' :
	define('DB_HOST'		,'');
	define('DB_NAME'		,'');
	define('DB_USER'		,'');
	define('DB_PASSWORD'	,'');
	break;

case 'DEV' :
	define('DB_HOST'		,'');
	define('DB_NAME'		,'');
	define('DB_USER'		,'');
	define('DB_PASSWORD'	,'');
	break;

case 'LOCAL' :
	define('DB_HOST'		,'localhost');
	define('DB_NAME'		,'example.com');
	define('DB_USER'		,'root');
	define('DB_PASSWORD'	,'root');
	break;

default :

	/** MySQL hostname */
	define('DB_HOST', 'localhost');

	/** The name of the database for WordPress */
	define('DB_NAME', 'database_name_here');

	/** MySQL database username */
	define('DB_USER', 'username_here');

	/** MySQL database password */
	define('DB_PASSWORD', 'password_here');

	break;

endswitch;

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
```

### Database Upgrade from utf8 to utf8mb4
Prevent WordPress from updating tables when an environment runs less than MySQL 5.5.3

Insert after database configuration.
```php
/** Database Upgrade from utf8 to utf8mb4 */
define('DO_NOT_UPGRADE_GLOBAL_TABLES', true);
```

### Update Authentication Unique Keys and Salts
https://api.wordpress.org/secret-key/1.1/salt/

## Database Table Prefix
```php
$table_prefix  = 'wp_';
```

### PHP Debugging
__Original__
```php
define('WP_DEBUG', false);
```
__Replace__
```php
switch (ENVIRONMENT) :
case 'LIVE' :
	define('WP_DEBUG',false);
 	define('WP_DEBUG_LOG',true);
	break;

default :
	define('WP_DEBUG',true);
	break;

endswitch;
```
