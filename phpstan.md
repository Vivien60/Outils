Analyseur statique de code

## Installation

```bash
composer require --dev phpstan/phpstan
```

## Analyse

```bash
vendor/bin/phpstan analyse src tests
```

Ajouter `--level [number]` pour augmenter le niveau d'analyse. `0<=niveau<=10`

Here's a brief overview of what's checked on each level. Levels are cumulative - for example running level 5 also gives you all the checks from levels 0-4.

1. basic checks, unknown classes, unknown functions, unknown methods called on `$this`, wrong number of arguments passed to those methods and functions, always undefined variables
2. possibly undefined variables, unknown magic methods and properties on classes with `__call` and `__get`
3. unknown methods checked on all expressions (not just `$this`), validating PHPDocs
4. return types, types assigned to properties
5. basic dead code checking - always false `instanceof` and other type checks, dead `else` branches, unreachable code after return; etc.
6. checking types of arguments passed to methods and functions
7. report missing typehints
8. report partially wrong union types - if you call a method that only exists on some types in a union type, level 7 starts to report that; other possibly incorrect situations
9. report calling methods and accessing properties on nullable types
10. be strict about explicit `mixed` type - the only allowed operation you can do with it is to pass it to another `mixed`
11. (New in PHPStan 2.0) be even more strict about the `mixed` type - reports errors even for implicit mixed (missing type), not just explicit mixed

## Configuration

Certains symbloes (alias, etc) ont besoin d'être ajoutés. *Ca me semble assez lourd, mais le LLM pourrait m'aider peut-être. Cependant, questionb maintenabilité, bof. Ca pourrait être un no-go*

Doc pour config

When using a config file, you have to pass the `--level|-l` option to `analyse` command (default value `0` does not apply here), or provide it as a `level` parameter in the config file itself. Learn more about other command line options »

If you do not provide a config file explicitly, PHPStan will look for files named `phpstan.neon`, `phpstan.neon.dist`, or `phpstan.dist.neon` in the current directory.

The resolution priority is as such:

1. If config file is provided as a command line option, it will be used.
2. Otherwise, if `phpstan.neon` exists in the current working directory, it will be used.
3. Otherwise, if `phpstan.neon.dist` exists in the current working directory, it will be used.
4. Otherwise, if `phpstan.dist.neon` exists in the current working directory, it will be used.
5. If none of the above is true, no config will be used.

```bash
vendor/bin/phpstan analyse -c phpstan.neon
```

Config file :

```yaml
parameters:
    level: 6
    paths:
        - src
        - tests
```

## Class aliases

This is similar to global constants above. Class aliases used in the analysed code need to be defined in bootstrap files.

Create a file that looks like this:

```php
<?php
class_alias(\Foo::class, 'Bar');
```

And add it to your configuration file:

```yaml
parameters:
    bootstrapFiles:
        - classAliases.php
```
