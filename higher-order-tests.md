---
title: Higher Order Tests
description: Higher Order Tests
---

# Higher Order Tests

- [Overview](#overview)
- [Working with Datasets](#working-with-datasets)

<a name="overview"></a>
## Overview

Pest also provides support for **higher order tests**, which are shortcuts for performing
common actions while writing your tests.

The best way to think about this is: If you
don't provide a closure, the chained methods are going to create a closure for
you. Here is the most basic example:

```php
test('TRUE is TRUE')->assertTrue(true);
```

The code above is equivalent to:
```php
test('TRUE is TRUE', function () {
    $this->assertTrue(true);
});
```

Pest will properly delegate to any given method and its arguments of the test itself.

Sometimes, you'll need to access methods not available until runtime. Higher order tests provide
the `tap` method, which receives a closure and executes the code inside:

```php
it('writes to the database after the command is run')
    ->tap(fn() => $this->artisan('your-command'))
    ->assertDatabaseHas('users', ['id' => 1]);
```

If you want to perform an expectation on a runtime value in higher order tests, you can pass 
a closure to the `expect` method:

```php
it('points to the correct URL')
    ->expect(fn() => route('dashboard'))
    ->toBe('http://example.com/dashboard');
```

The exact same rules apply to global functions like `beforeEach` or `afterEach`. Let's
take a look at an example using the Laravel Framework:

```php
beforeEach()->withoutMiddleware();

it('has home')
    ->get('/admin')
    ->assertSee('Hello World');
```

Pest also provides support for [higher order expectations](/docs/expectations#higher-order-expectations)
when writing higher order tests:

```php
test('the user has the correct values')
    ->expect(fn() => Auth::user())
    ->first_name->toEqual('Nuno')
    ->last_name->toEqual('Maduro')
    ->withTitle('Mr')->toEqual('Mr Nuno Maduro');
```

## Working with Datasets

When working with [datasets](/docs/datasets), Pest allows you to access the dataset values in higher order
tests when using the `expect`, `and` or `tap` methods:

```php
it('validates the user names')
    ->with('badUsernames')
    ->expect(fn($name) => ValidateUserName::isValid($name)) // We receive the data as a parameter to `expect`
    ->toBeFalse();

it('inserts a user in the database')
    ->with('users')
    ->tap(fn($name, $email) => User::create(['name' => $name, 'email' => $email])) // You may receive multiple arguments
    ->assertDatabaseHas('users', 1);
```

---

Next section: [Custom Helpers →](/docs/helpers)
