# Laravel Cashier - Gerencianet

- [Introduction](#introduction)    
- [Installation](#installation)
    - [Composer](#composer)
    - [Service Provider](#service_provider)
    - [Gerencianet App Keys](#app_keys)
    - [Migrations](#migrations)
    - [Model](#model)

<a name="introduction"></a>
## Introduction

Laravel Cashier for Gerencianet provides an expressive, fluent interface to [Gerencianet subscription billing services](https://gerencianet.com.br/solucoes/assinaturas/).
 
<a name="installation"></a>
## Installation

<a name="composer"></a>
### Via Composer

Antes de mais nada, adicione o package Cashier para Gerencianet no seu ```composer.json```:

    "tonetlds/cashier-gerencianet": "~1.0" 

ou, através do seu terminal execute o comando 
    
    composer require tonetlds/cashier-gerencianet

<a name="service_provider"></a>
### Service Provider

Depois, registre o service provider `Laravel\Cashier\CashierServiceProvider` no arquivo ```config/app.php```.
```php
// config/app.php

'providers' => [
    ...
    Laravel\Cashier\CashierServiceProvider
],
``` 

<a name="app_keys"></a>
### Gerencianet App Keys

Coloque as suas **chaves** no seu arquivo ```.env```:

    GERENCIANET_MODEL=App\User
    GERENCIANET_CLIENT_ID=XXXXXXXXXXXXXXXXX
    GERENCIANET_CLIENT_SECRET=XXXXXXXXXXXXXXXXX
    GERENCIANET_SANDBOX=true


<a name="migrations"></a>
### Migrations

Antes de usar, nós também precisamos preparar o banco de dados.
Precisaremos adicionar várias colunas à nossa tabela `users` e também criar uma nova tabela chamada `subscriptions` que conterá todas as assinaturas de nossos clientes:

```php    
// Users table
Schema::table('users', function ($table) {            
    $table->string('gerencianet_id')->nullable();
    $table->string('card_brand')->nullable();
    $table->string('card_last_four')->nullable();
    $table->timestamps();
});

// Subscriptions table
Schema::create('subscriptions', function ($table) {
    $table->increments('id');
    $table->string('name')->nullable();
    $table->integer('user_id');  
    $table->integer('subscription_id'); // gerencianet subscription id
    $table->string('status')->default("new");
    $table->string('custom_id')->nullable();
    $table->string('notification_url')->nullable();
    $table->string('payment_method')->nullable();
    $table->datetime('next_execution')->nullable();
    $table->datetime('next_expire_at')->nullable();
    $table->integer('plan_id')->nullable();
    $table->integer('occurrences')->default(0);
    // For local trial managment
    $table->datetime('trial_ends_at')->nullable();
    $table->datetime('ends_at')->nullable();
    $table->timestamps();  
});

// Charges table
Schema::create('charges', function ($table) {
    $table->increments('id');
    $table->integer('charge_id');
    $table->integer('user_id');  
    $table->integer('total');  
    $table->string('status')->default("new");
    $table->string('custom_id')->nullable();
    $table->string('notification_url')->nullable();
    $table->timestamps();  
});
```

Assim que as nossas migrations estiverem prontas, já podemos executá-las através do comando `migrate`:

    $ php artisan migrate

<a name="model"></a>
### Model

Depois, adicione o trait `Billable` no seu model `App\User`:

```php
use Laravel\Cashier\Billable;

class User extends Authenticatable
{
    use Billable;
}
```

Pronto. Você já pode começar a usar!

Próximo: [Criando cobranças](/docs/{{version}}/charge)