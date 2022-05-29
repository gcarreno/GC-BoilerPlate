# CakePHP

## Bare application with `composer`

To get a bare application using `composer` issue the following command:

```console
$ composer create-project --prefer-dist cakephp/app:4.* <folder-name>
```

## Users

Create a new migration:

```console
$ bin/cake migrations create CreateUsers
```

Change it to:

```php
<?php
declare(strict_types=1);

use Migrations\AbstractMigration;

class CreateUsers extends AbstractMigration
{
    private $table = 'users';

    /**
     * Up Method.
     *
     * More information on this method is available here:
     * https://book.cakephp.org/phinx/0/en/migrations.html#the-up-method
     * @return void
     */
    public function up()
    {
        $table = $this->table($this->table);
        $table
            ->addColumn('name', 'string', [
                'default' => null,
                'limit' => 255,
                'null' => false
            ])
            ->addColumn('email', 'string', [
                'default' => null,
                'limit' => 255,
                'null' => false
            ])
            ->addColumn('password', 'string', [
                'default' => null,
                'limit' => 255,
                'null' => false
            ])
            ->addColumn('user_level', 'integer', [
                'limit' => 256,
                'null' => false,
                'default' => 4 // 1 Developer; 2 Admin; 3 Moderator; 4 User
            ])
            ->addColumn('email_verification_hash', 'string', [
                'default' => null,
                'limit' => 255,
                'null' => true
            ])
            ->addColumn('verified', 'boolean', [
                'default' => false,
                'null' => true
            ])
            ->addColumn('created', 'datetime', [
                'default' => 'CURRENT_TIMESTAMP'
            ])
            ->addColumn('modified', 'datetime',[
                'default' => 'CURRENT_TIMESTAMP',
                'update' => 'CURRENT_TIMESTAMP()'
            ])
            ->addIndex(['email'],[
                'name' => 'email_idx',
                'type' => 'index'
            ])
            ->addIndex(['email_verification_hash', 'verified'],[
                'name' => 'email_verification_hash_verified_idx',
                'type' => 'index'
            ])
            ->create();
    }

    /**
     * Down Method.
     *
     * More information on this method is available here:
     * https://book.cakephp.org/phinx/0/en/migrations.html#the-down-method
     * @return void
     */
    public function down(){
        $this
            ->table($this->table)
            ->drop()
            ->save();
    }
}
```

## Authentication

This content is a compact version of the [CMS Tutorial - Authentication](https://book.cakephp.org/4/en/tutorials-and-examples/cms/authentication.html).

### Installing Authentication Plugin

Use composer to install the Authentication Plugin:

```console
$ composer require "cakephp/authentication:^2.0"
```

### Adding Password Hashing

In `src/Model/Entity/User.php` add the following:

```php
<?php
namespace App\Model\Entity;

use Authentication\PasswordHasher\DefaultPasswordHasher; // Add this line
use Cake\ORM\Entity;

class User extends Entity
{
    // Code from bake.

    // Add this method
    protected function _setPassword(string $password) : ?string
    {
        if (strlen($password) > 0) {
            return (new DefaultPasswordHasher())->hash($password);
        }
    }
}
```

### Adding Login

In `src/Application.php`, add the following imports:

```php
// In src/Application.php add the following imports
use Authentication\AuthenticationService;
use Authentication\AuthenticationServiceInterface;
use Authentication\AuthenticationServiceProviderInterface;
use Authentication\Middleware\AuthenticationMiddleware;
use Cake\Routing\Router;
use Psr\Http\Message\ServerRequestInterface;
```

Then implement the authentication interface on your `Application` class:

```php
// in src/Application.php
class Application extends BaseApplication
    implements AuthenticationServiceProviderInterface
{
```

Then add the following:

```php
// src/Application.php
public function middleware(MiddlewareQueue $middlewareQueue): MiddlewareQueue
{
    $middlewareQueue
        // ... other middleware added before
        ->add(new RoutingMiddleware($this))
        // add Authentication after RoutingMiddleware
        ->add(new AuthenticationMiddleware($this));

    return $middlewareQueue;
}

public function getAuthenticationService(ServerRequestInterface $request): AuthenticationServiceInterface
{
    $authenticationService = new AuthenticationService([
        'unauthenticatedRedirect' => Router::url('/users/login'),
        'queryParam' => 'redirect',
    ]);

    // Load identifiers, ensure we check email and password fields
    $authenticationService->loadIdentifier('Authentication.Password', [
        'fields' => [
            'username' => 'email',
            'password' => 'password',
        ]
    ]);

    // Load the authenticators, you want session first
    $authenticationService->loadAuthenticator('Authentication.Session');
    // Configure form data check to pick email and password
    $authenticationService->loadAuthenticator('Authentication.Form', [
        'fields' => [
            'username' => 'email',
            'password' => 'password',
        ],
        'loginUrl' => Router::url('/users/login'),
    ]);

    return $authenticationService;
}
```

In your `AppController` class add the following code:

```php
// src/Controller/AppController.php
public function initialize(): void
{
    parent::initialize();
    $this->loadComponent('RequestHandler');
    $this->loadComponent('Flash');

    // Add this line to check authentication result and lock your site
    $this->loadComponent('Authentication.Authentication');
```

In your `UsersController`, add the following code:

```php
public function beforeFilter(\Cake\Event\EventInterface $event)
{
    parent::beforeFilter($event);
    // Configure the login action to not require authentication, preventing
    // the infinite redirect loop issue
    $this->Authentication->addUnauthenticatedActions(['login']);
}

public function login()
{
    $this->request->allowMethod(['get', 'post']);
    $result = $this->Authentication->getResult();
    // regardless of POST or GET, redirect if user is logged in
    if ($result->isValid()) {
        // redirect to /articles after login success
        $redirect = $this->request->getQuery('redirect', [
            'controller' => 'Articles',
            'action' => 'index',
        ]);

        return $this->redirect($redirect);
    }
    // display error if user submitted and authentication failed
    if ($this->request->is('post') && !$result->isValid()) {
        $this->Flash->error(__('Invalid username or password'));
    }
}
```

Add the template logic for your login action:

```html
<!-- in /templates/Users/login.php -->
<div class="users form">
    <?= $this->Flash->render() ?>
    <h3>Login</h3>
    <?= $this->Form->create() ?>
    <fieldset>
        <legend><?= __('Please enter your username and password') ?></legend>
        <?= $this->Form->control('email', ['required' => true]) ?>
        <?= $this->Form->control('password', ['required' => true]) ?>
    </fieldset>
    <?= $this->Form->submit(__('Login')); ?>
    <?= $this->Form->end() ?>

    <?= $this->Html->link("Add User", ['action' => 'add']) ?>
</div>
```

We need to add a couple more details to configure our application.
We want all `view` and `index` pages accessible without logging in so we’ll add this specific configuration in `AppController`:

```php
// in src/Controller/AppController.php
public function beforeFilter(\Cake\Event\EventInterface $event)
{
    parent::beforeFilter($event);
    // for all controllers in our application, make index and view
    // actions public, skipping the authentication check
    $this->Authentication->addUnauthenticatedActions(['index', 'view']);
}
```

### Logout

Add the logout action to the `UsersController` class:

```php
// in src/Controller/UsersController.php
public function logout()
{
    $result = $this->Authentication->getResult();
    // regardless of POST or GET, redirect if user is logged in
    if ($result->isValid()) {
        $this->Authentication->logout();
        return $this->redirect(['controller' => 'Users', 'action' => 'login']);
    }
}
```

### View Helper Identity

Add the following code to `AppView`:

```php
public function initialize(): void
{
    // Identity Helper
    $this->loadHelper('Authentication.Identity');
}

```

## Authorization
