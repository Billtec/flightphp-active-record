# activerecord
[![Build Status](https://travis-ci.org/lloydzhou/activerecord.svg?branch=master)](https://travis-ci.org/lloydzhou/activerecord)

micro activerecord library in PHP(only 400 lines with comments), support chain calls and relations(HAS_ONE, HAS_MANY, BELONGS_TO).

##API Reference
[API Reference](http://lloydzhou.github.io/activerecord/)

## Install

    composer require lloydzhou/activerecord 

There's one [Blog demo](https://github.com/lloydzhou/blog), work with [Router](https://github.com/lloydzhou/router) and [MicoTpl](https://github.com/lloydzhou/microtpl).

## Demo
### Include base class ActiveRecord
```php
include "ActiveRecord.php";
```
### Define Class
```php
class User extends ActiveRecord{
	public $table = 'user';
	public $primaryKey = 'id';
	public $relations = array(
		'contacts' => array(self::HAS_MANY, 'Contact', 'user_id'),
		'contact' => array(self::HAS_ONE, 'Contact', 'user_id'),
	);
}
class Contact extends ActiveRecord{
	public $table = 'contact';
	public $primaryKey = 'id';
	public $relations = array(
		'user' => array(self::BELONGS_TO, 'User', 'user_id'),
	);
}
```
### Init data
```php
ActiveRecord::setDb(new PDO('sqlite:test.db'));
ActiveRecord::execute("CREATE TABLE IF NOT EXISTS user (
                                id INTEGER PRIMARY KEY, 
                                name TEXT, 
                                password TEXT 
                        );");
ActiveRecord::execute("CREATE TABLE IF NOT EXISTS contact (
                                id INTEGER PRIMARY KEY, 
                                user_id INTEGER, 
                                email TEXT,
                                address TEXT
                        );");
```
### Insert one User into database.
```php
$user = new User();
$user->name = 'demo';
$user->password = md5('demo');
var_dump($user->insert());
```
### Insert one Contact belongs the current user.
```php
$contact = new Contact();
$contact->address = 'test';
$contact->email = 'test1234456@domain.com';
$contact->user_id = $user->id;
var_dump($contact->insert());
```
### Example to using relations 
```php
$user = new User();
// find one user
var_dump($user->notnull('id')->orderby('id desc')->find());
echo "\nContact of User # {$user->id}\n";
// get contacts by using relation:
//   'contacts' => array(self::HAS_MANY, 'Contact', 'user_id'),
var_dump($user->contacts);

$contact = new Contact();
// find one contact
var_dump($contact->find());
// get user by using relation:
//    'user' => array(self::BELONGS_TO, 'User', 'user_id'),
var_dump($contact->user);
```

