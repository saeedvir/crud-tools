# Laravel Crud Tools
Easy to use Laravel CRUD package with Controller, Model and Log system built in


## Installation
Install through composer using: ``composer install thiagoprz\crud-tools``

Run after install scripts for Spatie Activity Logger:

``php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="migrations"``

Run migrations:

``php artisan migrate``

You can read Spatie Activity Log [Documentations](https://github.com/spatie/laravel-activitylog)


## Usage

- CRUD Controller:
A CRUD Controller can be achieve by just creating a standard controller class using ControllerCrud trait.

The next step is to create a folder inside ``resources/views`` with the desired namespace or on root folder if the controller won't be using a specific namespace (admin on the example).
```
<?php

namespace App\Http\Controllers\Admin;

use App\Models\User;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Thiagoprz\CrudTools\Http\Controllers\ControllerCrud;

class UserController extends Controller
{
    use ControllerCrud;
    public $modelClass = User::class;
}
```

Views directory structure used by Controller CRUD based on the above example:

Folder: 
> views/admin/user

Files:
> create.blade.php

> edit.blade.php 

Available vars: $model (the model being updated) 

> form.blade.php

Available vars: $model (the model being updated - only on edit action)

> index.blade.php

Available vars: $items (the pagination object containing a filtered collection of the model)

> show.blade.php

Available vars: $model (the model being displayed)

- CRUD Model:

For models you just need to add the trait ModelCrud and after that create a static property declaring model's validations based on the create, update and/or delete scenarios.

```
<?php
...
use Thiagoprz\CrudTools\Models\ModelCrud;
class User extends Authenticatable
{
    use ModelCrud;
    
    /**
     * Model validations
     *
     * @var array
     */
    static $validations = [
        'create' => [
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
            'password' => ['required', 'string', 'min:8', 'confirmed'],
        ],
        'update' => [
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
            'password' => ['required', 'string', 'min:8', 'confirmed'],
        ],
    ];
    ...
}
```
Searchable fields:

You can create a $searchable property that will hold fields allowed to be searched on the static method **search()** - very useful with the ControllerCrud. 

```
<?php
...
use Thiagoprz\CrudTools\Models\ModelCrud;
class User extends Authenticatable
{
    use ModelCrud;
    /**
     * Fields that can be searched by (static)method search()
     *
     * @var array
     */
    static $searchable = [
        'id' => 'int',
        'name' => 'string',
    ];
    ...
}
```
Upload fields:

You can create a fileUploads method to define which and where your uploadable fields will store the files: 

```
<?php
...
use Thiagoprz\CrudTools\Models\ModelCrud;
class User extends Authenticatable
{
    use ModelCrud;
    ...
    /**
     * @param Campaign $model
     * @return array
     */
    public static function fileUploads(Campaign $model)
    {
        return [
            'FIELD_NAME' => [
                'path' => 'FOLDER', // Mandatory
                'name' => 'FILE_NAME', // (OPTIONAL)if not provided will be the file original name 
            ],
        ];
    }
    ...
}
```