# Yii2 [CacheCleanerBehavior](/CacheCleanerBehavior.php)

[![Latest Stable Version](https://poser.pugx.org/consik/yii2-cachecleaner/v/stable)](https://packagist.org/packages/consik/yii2-cachecleaner)
[![Total Downloads](https://poser.pugx.org/consik/yii2-cachecleaner/downloads)](https://packagist.org/packages/consik/yii2-cachecleaner)
[![License](https://poser.pugx.org/consik/yii2-cachecleaner/license)](https://packagist.org/packages/consik/yii2-cachecleaner)

Used [Ratchet](http://socketo.me/)

## Installation

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
composer require consik/yii2-cachecleaner
```

or add

```json
"consik/yii2-cachecleaner": "^1.0"
```

## CacheCleanerBehavior class description

### Properties

1. ``` array $events = []``` - Associative array where key is event name, value(array|string|null) is a cache key(s) to delete
2. ``` string $cacheComponent = 'cache'``` - Name of the application cache component

### Public methods

1. ``` boolean deleteCache(string|array $key)``` - Deletes cache value(s)
2. ``` boolean flushCache()``` - Deletes all cache values

## Examples

### Autodelete cache after AR update

Simple use case:
We have cached AR object somewhere in our app:
```php
<?php
...
if (!$model = Yii::$app->cache->get('cachedModel' . $modelID)) {
	$model = ARModel::findOne($modelID);
	Yii::$app->cache->set('cachedModel' . $modelID, $model);
}
...
```

So, if somewhere in applicaton we change or delete this AR data, we have to delete our cache value.

Just use CacheCleanerBehavior in your ARModel:

```php
<?php
public function behaviors()
{
    return [[
        'class' => CacheClearBehavior::className(),
        //'cacheComponent' => 'cache',  //you can define your app cache component
        'events' => [
            ActiveRecord::EVENT_AFTER_UPDATE => 'cachedModel' . $this->id
            ActiveRecord::EVENT_BEFORE_DELETE => 'cachedModel' . $this->id
        ]
    ]];
}
```

### Using [CacheUpdateEvent](/events/CacheUpdateEvent.php)

There is a special class in package for triggering events when you change cache values, where you can send to handler action with values and define changed cache key(s).

You can use this class with CacheCleanerBehavior if you want delete different keys using one event name.

Example:

```php
<?php
public function behaviors()
{
    return [[
        'class' => CacheClearBehavior::className(),
        'events' => [
            YOUR_EVENT_NAME => null
        ]
    ]];
}

...

function someComponentAction()
{
...
	$this->trigger(YOUR_EVENT_NAME, new CacheUpdateEvent([
		'keys' => ['keyName1', 'keyName2'],
	]));
...
}
```

See the doc block for [CacheCleanerBehavior::$events](/CacheCleanerBehavior.php#L32)

### Other options for defining $events property

See the doc block for [CacheCleanerBehavior::$events](/CacheCleanerBehavior.php#L32)
