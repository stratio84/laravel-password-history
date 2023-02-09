# Laravel password history
Keep a password history of your users to prevent them from reusing the same password, for security reasons like what google does.

Special thanks to imanghafoori whom this library is forked from.
It's all his credit!

# Installation
```
composer require stratio84/laravel-password-history
```

To publish the config file and migrate the database:
```
php artisan vendor:publish
php artisan migrate
```

Visit the `config/password_history.php` file to see all the possibilities.

# Usage

This package will observe the `saved` event of the models (which are mentioned in the config file) and records the password hashes automatically.
```php
<?php
// When inserting, it will also log the password hash in the "password_histories" table
 User::create($data);

// Sample for changing the password
$user = User::find($id);
$passHash = Hash::make(request('new_password'));

$user->password = $passHash;
$user->save(); // after saving the model, the password change  will be recorded, automatically
```

We suggest to use `saveOrFail` to do all the queries in a transaction
```
$user->saveOrFail();
```

Be careful that changing the model like below does not fire any model event hence to password change would be recorded behind the scenes.

```php
<?php
// Here we do NOT get the model from db and only send  an update query
// So laravel does NOT fire model events
User::where('id', $id)->update($data);
```

### Validation Rules

And there is a validation rule for you to check the entire password history agaist the new password in laravel validation rules.
```php
<?php
use Imanghafoori\PasswordHistory\Rules\NotBeInPasswordHistory;
//...

$rules = [
    // ... 
    'password' => [
       'required',
       'confirmed',
       NotBeInPasswordHistory::ofUser($this->user),
    ]
    // ... 
];

$this->validate(...);
```

Again you may want to take a quick look at the source code to see what is going on there.


# QA

- I have a `users` table and an `admins` table (User model and Admin model), can I also track password changes for admins?
```
Yeah, the package supports it, visit the config file.
```

--------------------

### :raising_hand: Contributing 
If you find an issue or have a better way to do something, feel free to open an issue or a pull request.
If you use laravel-widgetize in your open source project, create a pull request to provide its URL as a sample application in the README.md file. 


### :exclamation: Security
If you discover any security-related issues, please use the `security tab` instead of using the issue tracker.


### :star: Your Stars Make Us Do More :star:
As always if you found this package useful and you want to encourage us to maintain and work on it. Just press the star button to declare your willingness.


