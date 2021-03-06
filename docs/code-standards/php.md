---
layout: default
title: PHP
description: PHP Code Standards
parent: Code Standards
nav_order: 4
---

# PHP
{: .no_toc }

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## The Basics

First and foremost, we make an attempt to adhere to the [WordPress PHP coding standards](https://make.wordpress.org/core/handbook/best-practices/coding-standards/php/). Additionally, we attempt to follow a number of [PSR-2 standards](http://www.php-fig.org/psr/psr-2/) where they don't conflict with WordPress.

## Overview

* Visibility MUST be declared on all properties and methods; abstract and final MUST be declared before the visibility; static MUST be declared after the visibility.
* The soft limit on line length MUST be 120 characters; automated style checkers MUST warn but MUST NOT error at the soft limit.

## General

### Files

* All PHP files MUST use the Unix LF (linefeed) line ending.
* The closing `?>` tag MUST be omitted from files containing only or ending in PHP.

### Keywords and true/false/null

* PHP keywords MUST be in lowercase.
* The PHP constants `true`, `false`, and `null` MUST be in lowercase.

### Namespace and use declarations

Where we are able to use Namespaces (tests, SaaS code, etc), we should adhere to the [PSR-12 standards for Namespaces and Use Declarations](https://www.php-fig.org/psr/psr-12/#3-declare-statements-namespace-and-import-statements).

### Classes, properties, and methods

When creating a new class, please try to ensure that the class has a single responsibility. Use inheritance when possible to keep things simple, organized and DRY.

### Class naming

Classes should be named with the Modern Tribe namespace ("Tribe"), the plugin namespace ("Events"), and then the classname. Those three parts should be separated by double underscores (which will translate to directory slashes in the autoloader). Since we're using double underscores as namespace separators, you can (and should) use single underscores in the class names.

**Example: Naming a Class**

Wrong (not namespaced, uses CamelCase):

```php
class DateUtilities {
  // define some things
}
```

Right:

Classes should be namespaced with a hierarchy that is separated by double-underscores (__). The hierarchy is as such: src/Tribe/Class_Name. In the class name, that translates to:

```php
class Tribe__Events__Date_Utilities {
  // define some things
}
```

### Properties

We adhere to the [PSR-2 guidelines for properties](http://www.php-fig.org/psr/psr-2/#4-2-properties).

### Constants

Due to the difficulty of deprecating constants (`const`), we prefer the use of `public static` class properties. This way, if we wish to deprecate the property, we can communicate that deprecation via magic `__get()` methods.

```php
// bad
const BACON = 'yummy';

// preferred
public static $bacon = 'yummy';
```

### Methods

* Visibility MUST be declared on all methods.
* Method names SHOULD NOT be prefixed with a single underscore to indicate protected or private visibility.
* Method names MUST NOT be declared with a space after the method name.
* For public methods that we want to deter public use, use the `@internal` docblock tag rather than an underscore prefix.
* The default visibility for class members should be `private`. Increase the visibility incrementally (to protected, and then public), as needed.
* Static methods and properties should be used sparingly and avoided whenever possible.
* Be as DRY as possible.

### Method arguments

Argument lists MAY be split across multiple lines, where each subsequent line is indented once. When doing so, the first item in the list MUST be on the next line, and there MUST be only one argument per line.

When the argument list is split across multiple lines, the closing parenthesis and opening brace MUST be placed together on their own line with one space between them.

Example:
```php
public function star_wars(
  $episode,
  $character,
  $species,
  $weapon
) {
  // do stuff
}
```

### Abstract, final, and static

We adhere to the [PSR-2 guidelines for abstract, final and static](http://www.php-fig.org/psr/psr-2/#4-5-abstract-final-and-static).

### Method and function calls

Argument lists MAY be split across multiple lines, where each subsequent line is indented once. When doing so, the first item in the list MUST be on the next line, and there MUST be only one argument per line.

Example:

```php
star_wars(
  'IV',
  'Greedo',
  'Rodian',
  'DT-12'
);
```

## Functions

### When to create global functions
If there is a piece of code that is frequently referenced throughout the addons, or could be useful to users who want to create custom templates or addons, that’s when it’s appropriate to create a global function.

Keep in mind that whenever a global function is created and released, from that point on, it’ll be “in the wild”. Customers may be using it in their themes or plugins, and we will need to support it from that point on. With this in mind, please be sure not to create unnecessary functions that we won’t want to support.

### Where to put function code
There are template tag files for most areas of the plugin. They are located in the `template-tags/` directory. Take care in selecting the appropriate file for your new function, and place the new function at the end of the file. If it closely references another function in the file, place it next to that function.

It’s usually appropriate to place the logic for a function into a class, and then use a function as a wrapper for the class method. When you have some code that you’d like to make publicly available, but it would also make sense as a method of one of our classes, you should make the method within the class, and then make a global function that calls that method and returns its result.

### Echo or return?
When a function returns a scalar value that might be used in templates, be sure to return the value instead of echoing it. There is no need to take `$echo` as a boolean argument to the function; if a user wants to echo the value, they can simply directly echo the function’s result in their template.

### Applying filters to the result
Note that whenever you create a global function, be sure to run `apply_filters()` on the result before it’s returned.

The filter should have the same name as the function, and should be passed the return value, followed by (at minimum), all parameters that were passed to the function initially.

### Function naming
Since we’re creating code that is distributed, has been downloaded over 1 million times, and will be installed alongside myriad themes and plugins, it’s very important that you prefix your global namespace definitions with `tribe_` -- so there’s far less chance another plugin’s or theme’s definitions will have the same name.

**Example: Naming a Global Function**

Wrong (not namespaced, uses camelCase):

```php
function getEventStartTime() {
  // do something
}
```

Right (prefixed with `tribe_`, uses underscores):

```php
function tribe_get_event_start_time() {
  // do something
}
```

## Control structures

### Switch, case

We adhere to the [PSR-2 guidelines for `switch` and `case` statements](http://www.php-fig.org/psr/psr-2/#5-2-switch-case) _except_ for the standards around spacing within parentheses.

### Multi-line control structures

Conditionals MAY be split across multiple lines, where each subsequent line is indented once. When doing so, the first conditional in the list MUST be on the next line, and there MUST be only one conditional per line.

**Yup!**
```php
// simple
if (
  $a == $b
  && $b == $c
) {
  // do stuff
}

// something a bit more complex
if (
  (
    $a == $b
    || $a == c
  )
  && $b == $d
  && $q == $banana_pancake
) {
  // do stuff
}
```

**Nope!**
```php
// this only looks pretty when your tabs are set to 2 spaces
if ( $a == $b
  && $b == $c
) {
  // do stuff
}

// alignment beyond a single tab can be fraught with differences between devs
if ( $a == $b
     && $b == $c
) {
  // do stuff
}

// this one causes needless churn in Git as conditionals are added to the control structure
if (
  $a == $b &&
  $b == $c
) {
  // do stuff
}

// since we use tabs, we could be bitten by the tab/space nature of alignment here
if (
     $a == $b
  && $b == $c
) {
  // do stuff
}

// something a bit more complex - this violates the 1 conditional per line rule
if (
  ( $a == $b || $a == c )
  && $b == $d
  && $q == $banana_pancake
) {
  // do stuff
}
```


## Closures

We adhere to the [PSR-2 guidelines for closures](http://www.php-fig.org/psr/psr-2/#6-closures) _except_ for the standards around spacing within parentheses.


## Documentation

We encourage developers to extensively document functions, method, classes and hooks following the [WordPress PHP Documentation Standards](https://make.wordpress.org/core/handbook/best-practices/inline-documentation-standards/php/).
