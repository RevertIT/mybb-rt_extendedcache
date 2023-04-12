## RT LiveSearch
RT Extended Cache is a plugin that extends the native MyBB cache handler with additional functionalities to simplify the work of both users and developers. The plugin provides new functions, including auto expiration time for the cache, auto increment and decrement methods, and the ability to cache database queries on the fly.

### Table of contents

1. [❗ Dependencies](#-dependencies)
2. [📃 Features](#-features)
3. [➕ Installation](#-installation)
4. [🔼 Update](#-update)
5. [Usage](#load-rt-cache-into-pluginfile)

### ❗ Dependencies
- MyBB 1.8.x
- PHP >= 8.0

### 📃 Features
- Note: RT Caches are stored with custom prefix, so you can't use it to load default caches. For that use MyBB's `$cache`
- Set auto expiration time to the cache.
- Auto increment (convenient method for incrementing value).
- Auto decrement (convenient method for decrementing value).
- Cache database queries on the fly.

### ➕ Installation
- Copy the directories from the plugin inside your root MyBB installation.

### 🔼 Update
- Copy/paste new files into your MyBB root directory.

## Usage

### Load RT Cache into plugin/file
To load RT Cache into your plugin or file, you should include the following snippet at the top of your plugin or below require `./global.php` if including inside custom file.

- **Load RT Cache inside plugin**
```php
<?php

if (!defined("IN_MYBB"))
{
    die("Direct initialization of this file is not allowed.");
}

// START: Include from here
if (!defined('RT_EXTENDEDCACHE'))
{
    define('RT_EXTENDEDCACHE', MYBB_ROOT.'inc/plugins/rt_extendedcache.php');
}
require_once RT_EXTENDEDCACHE;
// END: Include from here
```
- **Load RT Cache inside custom `file.php`**
```php
<?php

define("IN_MYBB", 1);
define('THIS_SCRIPT', 'customfile.php');
require_once "./global.php";

// START: Include from here
if (!defined('RT_EXTENDEDCACHE'))
{
    define('RT_EXTENDEDCACHE', MYBB_ROOT.'inc/plugins/rt_extendedcache.php');
}
require_once RT_EXTENDEDCACHE;
// END: Include from here
```

Then you should include `global $rt_cache` inside your functions or hooks. For example:

```php
function misc_hook()
{
    global $rt_cache;

    $rt->cache->get('some_cache');
}
```

### Set cache
To set cache data, use the `set()` method with the cache name, cache data, and optional expiration time in seconds as parameters:

```php
$rt_cache->set('cache_name', $data, 3600);
```
The code above will store `$data` in the cache with the key `cache_name` for one hour (3600 seconds).

### Get cache
To get cache data, use the `get()` method with the cache name:

```php
$rt_cache->get('cache_name');
```
The code above will retrieve `$data` in the cache with the key `cache_name`.

### Auto increment
The increment() method is a convenient way to increase the value of a cache key. Use it with the key name and the amount to increase the value:
```php
// Increment the value by 1
$new_value = $rt_cache->increment('foo_bar');

// Increment the value by 5
$new_value = $rt_cache->increment('foo_bar', 5);

// Delete the cache // Will always return 0
$new_value = $rt_cache->increment('foo_bar', 0);
```

### Auto decrement
The increment() method is a convenient way to decrease the value of a cache key. Use it with the key name and the amount to decrease the value:
```php
// Decrement the value by 1
$new_value = $rt_cache->decrement('foo_baz');

// Decrement the value by 5
$new_value = $rt_cache->decrement('foo_baz', 5);

// Delete the cache
$new_value = $rt_cache->decrement('foo_baz', 0);
```

### Cache database query
The `query()` method allows you to cache the results of database queries. Here's an example:

```php
$uid = 1;
$user = $rt_cache->query("SELECT * FROM " . TABLE_PREFIX . "users WHERE uid = '{$db->escape_string($uid)}'")
                  ->cache('cached_user_data_' . $uid, 3600);
```
The code above will cache the query and store it with a key `cached_user_data_$uid` for `3600` seconds, after that it will refresh the query and get new data.

### Delete cache
The `delete()` method allows you to delete cache. Here's an example:

```php
$rt_cache->delete("cached_user_data_1");
```