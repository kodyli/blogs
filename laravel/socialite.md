# [Socialite](https://laravel.com/docs/9.x/socialite)
-----------
### Introduction
In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using Laravel Socialite. Socialite currently supports authentication via Facebook, Twitter, LinkedIn, Google, GitHub, GitLab, and Bitbucket.

### Usage
~~~ php
'github' => [
    'client_id' => env('GITHUB_CLIENT_ID'),
    'client_secret' => env('GITHUB_CLIENT_SECRET'),
    'redirect' => 'http://example.com/callback-url',
],
~~~
~~~php
use Laravel\Socialite\Facades\Socialite;
 
Route::get('/auth/redirect', function () {
    return Socialite::driver('github')->redirect();
});
 
Route::get('/auth/callback', function () {
    $user = Socialite::driver('github')->user();
 
    // $user->token
});
~~~

~~~php

use Laravel\Socialite\Facades\Socialite;
 
Route::get('/auth/callback', function () {
    $user = Socialite::driver('github')->user();
 
    // OAuth 2.0 providers...
    $token = $user->token;
    $refreshToken = $user->refreshToken;
    $expiresIn = $user->expiresIn;

    $user->getId();
    $user->getNickname();
    $user->getName();
    $user->getEmail();
    $user->getAvatar();
});

~~~
### [Source Code](https://github.com/laravel/socialite)
![Laravel Socialite](../img/laravel/socialite.svg)