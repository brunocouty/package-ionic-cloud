# About Ionic Cloud Package

This package help you to work with Ionic Cloud Services. To use, first verify the requirements.

Actually, work only registering users in Auth Service and sending push notifications, but I'm work to add all functions of Ionic Cloud ;-)

The Auth Service of Ionic Cloud is the better way to manager device tokens and sessions of users in your Ionic mobile app. Manager the user session and all device tokens of users loggeds. Using the Auth Service, you can send Push Notifications for all devices easily!

## Requirements:

- Laravel >= 5.1;
- Create an account in [Ionic IO](https://apps.ionic.io/signup);
- Implements [Ionic Cloud features supported](https://docs.ionic.io/services/) - ([Push](https://docs.ionic.io/services/push/) or [Auth](https://docs.ionic.io/services/auth/))in your mobile app;
- For Push Notifications Service, create and configure a Profile and Certificate in Ionic IO ([for Android](https://docs.ionic.io/services/profiles/#android-fcm-project--server-key) / [for iOS](https://docs.ionic.io/services/profiles/#ios-push-certificate))

Laravel is accessible, yet powerful, providing tools needed for large, robust applications. A superb combination of simplicity, elegance, and innovation give you tools you need to build any application with which you are tasked.

## Installation

Install with composer:

```
composer require brunocouty/ionic-cloud
```

After the composer require is completed, publish the configuration file:

```
php artisan vendor:publish --tag=brunocouty/ionic-cloud
```

## Configuration

Open "*config/app.php*" and register this package in providers array:

``` 
BrunoCouty\IonicCloud\IonicCloudServiceProvider::class,
```

Open "*config/ionic-cloud.php*".

You can create three vars in your *.env* file or set the values in self config file.

The vars to *.env* file are:

Need to Push Notifications:

- IONIC_CLOUD_API_TOKEN (*Ionic IO Account / Your App / Settings / API Keys*);
- IONIC_CLOUD_PROFILE (*Ionic IO Account / Your App / Settings / Certificates*);

Need to Auth:

- IONIC_CLOUD_APP_ID (*The ID of your app*);

## Usage

### Register an User

Below, a list of data that can be sended:

- email [**required**];
- password [**required**];
- username [optional];
- name [optional];
- image [optional];
- custom [optional] - custom data array;

```
    $auth = new \BrunoCouty\IonicCloud\Services\Auth();
    $data = [
        'email' => 'brunocouty@gmail.com',
        'password' => 'foxtrot',
        'name' => 'Bruno Couty',
        'custom' => [
            'gender' => 'M',
            'country' => 'Brazil'
        ]
    ];
    $response = $auth->register($data);
```

If everything it's ok, your will receive as return status 201 with the fields:

- data:
     - app_id;
     - created;
     - custom;
     - details [object];
     - social [object]
     - uuid;
- meta:
    - request_id;
    - status;
    - version;
     

### Send a Push Notification

You need have the *uuid* (*User id*) to send a push notification. With the *uuid*, this library send a push notification to all devices where the user is logged.

```
    $push = new \BrunoCouty\IonicCloud\Services\Push();
    $title = "New Notification!"; // The notification title (string)
    $message = "You have a new notification!"; // The notification message (string)
    $uuid = ["123abc-asd8asdhiasdh0asdhoad0a"]; // An array with the user-ids of the users that will receive the push notification
    $data = [
        'foo' => '1',
        'bar' => '2'
    ]; // Some data that you want send throught push notification (optional)
    $response = $push->send($title, $message, $uuid, $data); // If ok, return 200
```
