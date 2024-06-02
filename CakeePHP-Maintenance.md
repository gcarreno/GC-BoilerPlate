# Step-by-Step Guide to add Maintenance to `CakePHP`

## Create a Maintenance Middleware
Middleware in CakePHP can intercept requests and responses, making it perfect for implementing a maintenance mode.

First, create a new middleware class, e.g., `MaintenanceMiddleware.php` in the `src/Middleware` directory.

```php
<?php
namespace App\Middleware;

use Cake\Http\Response;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\MiddlewareInterface;
use Psr\Http\Server\RequestHandlerInterface;

class MaintenanceMiddleware implements MiddlewareInterface
{
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): Response
    {
        // Check if maintenance mode is enabled
        if (file_exists(TMP . 'maintenance.flag')) {
            // Render a maintenance page
            $response = new Response();
            $response = $response->withStringBody('Site is under maintenance. Please check back later.');
            return $response->withStatus(503); // Service Unavailable status code
        }

        // Continue to the next middleware
        return $handler->handle($request);
    }
}
```

## Register the Middleware
Next, you need to register your middleware in the `Application.php` file, found in the `src` directory.

```php
// In src/Application.php

use App\Middleware\MaintenanceMiddleware;

public function middleware(MiddlewareQueue $middlewareQueue): MiddlewareQueue
{
    // Add the maintenance middleware at the beginning of the queue
    $middlewareQueue->add(new MaintenanceMiddleware());

    // Other middlewares
    // $middlewareQueue->add(new SomeOtherMiddleware());

    return $middlewareQueue;
}
```

## Create a Mechanism to Enable/Disable Maintenance Mode
Use a file, `maintenance.flag`, to switch maintenance mode on and off. You can create a simple shell command or a controller action to manage this flag.

```php
// In src/Command/MaintenanceCommand.php

namespace App\Command;

use Cake\Console\Arguments;
use Cake\Console\Command;
use Cake\Console\ConsoleIo;
use Cake\Console\ConsoleOptionParser;

class MaintenanceCommand extends Command
{
    public function execute(Arguments $args, ConsoleIo $io)
    {
        $action = $args->getArgument('action');

        if ($action === 'enable') {
            file_put_contents(TMP . 'maintenance.flag', 'enabled');
            $io->out('Maintenance mode enabled.');
        } elseif ($action === 'disable') {
            unlink(TMP . 'maintenance.flag');
            $io->out('Maintenance mode disabled.');
        } else {
            $io->out('Invalid action. Use "enable" or "disable".');
        }
    }

    public function buildOptionParser(ConsoleOptionParser $parser): ConsoleOptionParser
    {
        $parser->addArgument('action', [
            'help' => 'Enable or disable maintenance mode',
            'required' => true
        ]);

        return $parser;
    }
}
```

Register this command in your `src/Command/AppCommand.php`:

```php
// In src/Command/AppCommand.php

namespace App\Command;

use Cake\Console\CommandCollection;

class AppCommand extends Command
{
    public static function buildCommands(CommandCollection $commands): CommandCollection
    {
        $commands->add('maintenance', \App\Command\MaintenanceCommand::class);

        return $commands;
    }
}
```

## Run the Command
To enable maintenance mode, run:

```sh
bin/cake maintenance enable
```

To disable maintenance mode, run:

```sh
bin/cake maintenance disable
```

## Customize the Maintenance Page
You can further customize the maintenance response by rendering a view instead of a simple string message.

```php
// In MaintenanceMiddleware.php

public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): Response
{
    if (file_exists(TMP . 'maintenance.flag')) {
        $controller = new \App\Controller\AppController();
        $controller->viewBuilder()->setTemplatePath('Error')->setTemplate('maintenance');
        $controller->render();
        return $controller->getResponse()->withStatus(503);
    }

    return $handler->handle($request);
}
```

Create a new template `maintenance.php` in `src/Template/Error`:

```php
<!-- In src/Template/Error/maintenance.php -->

<!DOCTYPE html>
<html>
<head>
    <title>Maintenance</title>
</head>
<body>
    <h1>Site is under maintenance</h1>
    <p>We are currently performing scheduled maintenance. We should be back online shortly.</p>
</body>
</html>
```

And that's it! You now have a simple and effective way to enable and disable maintenance mode in your CakePHP application. Let me know if you need further customization or have any other questions!