# Artisan view composer
This package adds a handful of view-related commands to Artisan in your Laravel project.  view composer class files that can called on views.

[Laravel View Composers Documentation](https://laravel.com/docs/8.x/views#view-composers)

## Options
```
λ php artisan make:composer --help
Description:
  Create a new Eloquent model class

Usage:
  make:composer [options] [--] <name>

Arguments:
  name                  The name of the class

Options:
  -d, --dir[=DIR]       Create directory for the view composer
  -m, --model[=MODEL]   add model to the view composer
  -b, --views[=VIEWS]   impliments view blades to the view composer (multiple values allowed)
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
      --env[=ENV]       The environment the command should run under
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

## Example Usage

### Creating view composer
```bash
# Create view composer
$ php artisan make:composer UserViewComposer
```

### Generated View Composer
```php
//App/Http/View/Composers/UserViewComposer.php

<?php

namespace App\Http\View\Composers;

use Illuminate\View\View;

class UserViewComposer
{
    protected $user;
    /**
     * Create the view composer
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Bind data to the view.
     *
     * @param  Illuminate\View\View $view
     * @return $view
     */
    public function compose(View $view): View
    {
        return $view->with($this->getAll());
    }

    /**
     * create data that can called on blade views
     *
     * @return array
     */
    public function getAll(): array
    {
        return [
            'data' => null;
        ];
    }
}
```


### Creating view composer with model
```bash
# Create view composer with model
$ php artisan make:composer UserViewComposer --model=User
```

### Generated View Composer with model
```php
//App/Http/View/Composers/UserViewComposer.php

<?php

namespace App\Http\View\Composers;

use App\User;
use Illuminate\View\View;

class UserViewComposer
{
    protected $user;
    /**
     * Create the view composer
     *
     * @return void
     */
    public function __construct(User $user)
    {
        $this->user = $user
    }

    /**
     * Bind data to the view.
     *
     * @param  Illuminate\View\View $view
     * @return $view
     */
    public function compose(View $view): View
    {
        return $view->with($this->getAll());
    }

    /**
     * create data that can called on blade views
     *
     * @return array
     */
    public function getAll(): array
    {
        return [
            'data' => null;
        ];
    }
}
```

### getAll Method
The `getAll()`  is a array values that pass on the blades views.
Example: we pass a user object on `getAll()` we can call attribute of the views via calling array key we can call directly the email in views via `{{ $email }}` without calling `{{ $user->email }}`

```
public function getAll(): array
{
    return $this->user->first();
}
 ```

### Merging data
the array_merge is the default function of PHP we merge it on the `auth()->user()` so now we call the 'data' array key via {{ $data }} on views

```
public function getAll(): array
{
    return array_merge(auth()->user(), [
        'data' => null;
    ]);
}
 ```

### Insert View composer class on the folder
```bash
# insert class on the folder using --dir option
$ php artisan make:composer Payment --dir=Payment

# insert also on Payment directory
$ php artisan make:composer PaymentGateWay --dir=Payment

```


### Registering view composer on provider automatically
```bash
# register automatically UserViewComposer with views 
$ php artisan make:composer UserViewComposer --views=user.profile, user.post

# register automatically UserViewComposer with views in shortcut way
$ php artisan make:composer UserViewComposer -b user.profile, user.post

# Flexibility
$ php artisan make:composer UserViewComposer --views=user.profile, user.post -b user.settings, post.*

```

### ViewServiceProvider
The `ViewServiceProvider` is automatically generating if not exist
when the command have options views the view composer code well be automatically insert on the boot function the also the class are automatically imported.

```php
//App/Providers/ViewServiceProvider.php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;
use App\Http\View\Composers\Backup\Composer as BackupComposer;
use App\Http\View\Composers\User\UserViewComposer; 

class ViewServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        View::composer(['user.profile', 'user.post', 'user.settings', 'post.*'], UserViewComposer::class);
        View::composer(['system.backup.settings', 'backup.index'], BackupComposer::class);
    }
}
```
