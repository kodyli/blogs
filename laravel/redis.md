# [Redis](https://laravel.com/docs/9.x/redis)
-----------
### Introduction
Redis is an open source, advanced key-value store. It is often referred to as a data structure server since keys can contain strings, hashes, lists, sets, and sorted sets.
### [Source Code](https://github.com/laravel/framework/tree/9.x/src/Illuminate/Cache)
The [Factory](https://github.com/laravel/framework/blob/9.x/src/Illuminate/Contracts/Redis/Factory.php) uses [Strategy](https://refactoring.guru/design-patterns/strategy) design pattern, reads configration from Laravel, and creates [Connector](https://github.com/laravel/framework/blob/9.x/src/Illuminate/Contracts/Redis/Connector.php) based on the configuration, and then the connector will connect the [redis](https://redis.io/) and generate [Connection](https://github.com/laravel/framework/blob/9.x/src/Illuminate/Contracts/Redis/Connection.php).

![Laravel Cache](../img/laravel/redis.svg)