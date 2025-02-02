# tas-wrapper
Simple API wrapper for easy manage [TelegramApiServer](https://github.com/xtrime-ru/TelegramApiServer) in your project.

### Install 
```bash
composer require chipslays/tas-wrapper
```

### Example

#### Client
All methods from MadelineProto supported, methods list [here](https://docs.madelineproto.xyz/API_docs/methods/).

```php
use TelegramApiServer\Client;

require 'vendor/autoload.php';

$client = new Client([
    'host' => '127.0.0.1',
    'port' => '9503',
    'session' => 'session_name',
]);

$response = $client->get('getSelf')->execute(); // GET request
$response = $client->post('getSelf')->execute(); // POST request

$response = $client->get('messages.sendMessage', [
    'peer' => '@chipslays',
    'message' => 'Hello from tas-wrapper! 👋',
    'parse_mode' => 'html',
])->execute();
```

Also, you can define curl option by `option(NAME_OPTION, 'value')` method.
```php
$response = $client
                ->get('getSelf')
                ->option(CURLOPT_TIMEOUT, 60)
                ->option(CURLOPT_HEADER, false)
                ->execute();
```

Map your own methods (aliases), it so simple:
```php
$client->map('sendMessage', fn($peer, $text) => $client->get('messages.sendMessage', [
    'peer' => $peer,
    'message' => $text,
])->execute());

$response = $client->sendMessage('@chipslays', 'so simple!');
```

Each time the `sendMessage` method is called, the function from the second parameter **will be executed again**.

If you need the function to work **only once**, and then return only the result of execution in subsequent times, use the `mapOnce()` method instead of `map()`.
```php
$client->mapOnce('getSelf', fn() => $client->get('getSelf')->execute());

$me = $client->getSelf();
```

#### Session
Manage TelegramApiServer session, more detail [here](https://github.com/xtrime-ru/TelegramApiServer#session-management).
```php
use TelegramApiServer\Session;

require 'vendor/autoload.php';

// This is example of default config
// NOTE: If you have the same data, you can skip this
Session::setConfig([
    'host' => '127.0.0.1',
    'port' => '9503',
    'schema' => 'http',
]);

// Get sessions list
$response = Session::list();

// Adding new session
$response = Session::add('session_name');

// Removing session (session file will remain)
$response = Session::remove('session_name');

// Remove session file
// WARNING: Don`t forget to logout and call `removeSession` first!
$response = Session::unlink('session_name');
```
