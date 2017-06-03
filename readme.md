# About Ionic Cloud PHP Package

This package help you to work with Ionic Cloud Services. To use, first verify the requirements.

Actually, work only registering users in Auth Service and sending push notifications, but I'm working to add all functions of Ionic Cloud ;-)

The Auth Service of Ionic Cloud is the better way to manager device tokens and sessions of users in your Ionic mobile app. Manager the user session and all device tokens of users loggeds. Using the Auth Service, you can send Push Notifications for all devices easily!

I use this package with ***Laravel Framework*** but you can use with any PHP application.

## Requirements:

- Laravel >= 5.1;
- guzzlehttp/guzzle;
- Create an account in [Ionic IO](https://apps.ionic.io/signup);
- Have an Ionic Cloud API Token (*In your ionic.io account, access your app > Settings > API Tokens*);
- Implements [Ionic Cloud features supported](https://docs.ionic.io/services/) - ([Push](https://docs.ionic.io/services/push/) or [Auth](https://docs.ionic.io/services/auth/))in your mobile app;
- For Push Notifications Service, create and configure a Profile and Certificate in Ionic IO ([for Android](https://docs.ionic.io/services/profiles/#android-fcm-project--server-key) / [for iOS](https://docs.ionic.io/services/profiles/#ios-push-certificate))

--------------------

## Installation

Install with composer:

```
composer require brunocouty/ionic-cloud
```

Open "*config/app.php*" and register this package in providers array:

``` 
BrunoCouty\IonicCloud\IonicCloudServiceProvider::class,
```

Now, publish the configuration file:

```
php artisan vendor:publish --provider="BrunoCouty\IonicCloud\IonicCloudServiceProvider"
```
--------------------

## Configuration

Open "*config/ionic-cloud.php*".

You can create three vars in your *.env* file or set the values in self config file.

The vars to *.env* file are:

Need to Push Notifications:

- IONIC_CLOUD_API_TOKEN (*Ionic IO Account / Your App / Settings / API Keys*);
- IONIC_CLOUD_PROFILE (*Ionic IO Account / Your App / Settings / Certificates*);

Need to Auth:

- IONIC_CLOUD_APP_ID (*The ID of your app*);

```
'api_token' => env('IONIC_CLOUD_API_TOKEN', 'your-api-token'), // required!
'profile' => env('IONIC_CLOUD_PROFILE', 'your-profile-certificates'), // for push notifications
'app_id' => env('IONIC_CLOUD_APP_ID', 'your-app-id'), // for auth service
```

--------------------

## Usage

### Register an User

See the list of data that can be sent:

**data** an array with the user data:
- email [**required**];
- password [**required**];
- username [optional];
- name [optional];
- image [optional];
- custom [optional] - custom data array;

**token** and **app_id** are strings with the API Token and APP ID of you app. This is optional. Use **if you need send several different tokens in your application**. If your application use only one app on Ionic Cloud, you not need send this parameter, only configure the vars in "*config/ionic-cloud.php*" file.

Below see an example of register with vars configured in "*config/ionic-cloud.php*" file:

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

Below, see an example of register in different applications, sending the API Token and App ID on method:

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
    $token = 'your-api-token';
    $app_id = '891IaDs';
    $response = $auth->register($data, $token, $app_id);
```


If everything it's ok, your will receive as return status 200 with the fields:

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

If you want get the ***uuid***, for example, use:
 
```
$uuid = $response['data']['uuid']; 
```

### Retrieve the user data

Method parameters:

- $uuid: *string* [**required**];
- $token: *string* [optional]

```
$response = $auth->get($uuid);
```

Return data:

```
{  
   "meta":{  
      "request_id":"b87f9aae-8fdd-4ab4-ca97-4eb09812117b",
      "status":200,
      "version":"2.0.0-beta.0"
   },
   "data":{  
      "app_id":"your-app-id",
      "social":[  
          ...
      ],
      "groups":[  
          ...
      ],
      "custom":[  
          ...
      ],
      "uuid":"uuid-of-your-user",
      "details":{  
         "name":"user-name",
         "email":"user-email",
         "username":null,
         "image":"path-to-image"
      },
      "type":"ionic",
      "created":"2017-06-03T18:23:10Z"
   }
}
```

Get email:

```
echo $response['data']['email'];
```

### Update the user data

Method parameters:

- $uuid: *string* [**required**];
- $data: *array* [**required**];
- $token *string* [optional];

```
$data = [
        'email' => 'brunocouty@gmail.com',
        'password' => 'foxtrot fired!',
        'name' => 'Bruno Couty',
        'custom' => [
            'gender' => 'M',
            'country' => 'Brazil'
        ]
    ];
$response = $auth->update($uuid, $data);
```

Response:

```
{  
   "meta":{  
      "request_id":"b87f9aae-8fdd-4ab4-ca97-4eb09812117b",
      "status":200,
      "version":"2.0.0-beta.0"
   },
   "data":{  
      "app_id":"your-app-id",
      "social":[  
          ...
      ],
      "groups":[  
          ...
      ],
      "custom":[  
          ...
      ],
      "uuid":"uuid-of-your-user",
      "details":{  
         "name":"user-name",
         "email":"user-email",
         "username":null,
         "image":"path-to-image"
      },
      "type":"ionic",
      "created":"2017-06-03T18:23:10Z"
   }
}
```

### Delete an user

Method parameters:

- $uuid: *string* [**required**];
- $token: *string* [optional];

```
$response = $auth->delete($uuid);
```

Return:

```
[]
```

### Send a Push Notification

You need have the *uuid* (*User id*) to send a push notification. With the *uuid*, this library send a push notification to all devices where the user is logged.

Parameters of method:

- $title: *string* [**required**];
- $message: *string* [**required**];
- $uuid: *array* [**required**];
- $data: *array* [**optional**] (data that you want send to mobile app);
- $token: *string* [**optional**] (for default, the library use the token configured in "*config/ionic-cloud.php*" file, but if you want, can send the token here);
- $profile: *string* [**optional**] (for default, the library use the certificate profile configured in "*config/ionic-cloud.php*" file, but if you want, can send the certificate profile here);

```
    $push = new \BrunoCouty\IonicCloud\Services\Push();
    $title = "New Notification!"; // The notification title (string)
    $message = "You have a new notification!"; // The notification message (string)
    $uuid = ["123abc-asd8asdhiasdh0asdhoad0a"]; // An array with the user-ids of the users that will receive the push notification
    $data = [
        'foo' => '1',
        'bar' => '2'
    ]; // Some data that you want send throught push notification (optional)
    $response = $push->send(
                                $title, 
                                $message, 
                                $uuid, 
                                $data // if there's not data, send empty array or delete this parameter
                            ); // If ok, return 200
```

If you want work with more than one app, you need send the authentication data as parameters in *send* method:

```
$token = 'your-token';
$profile = 'your-profile';
$response = $push->send(
                            $title, 
                            $message, 
                            $uuid, 
                            $data,
                            $token,
                            $profile
                        );
```

## Like this content? Pay me a coffee!

Yeah! You like of this package? Pay me a coffee and help me to keep this package updated!

When you are my support, you have access to **exclusive posts** with a lot that cool things about PHP, Laravel, AngularJS, VueJS, Ionic, and much more! You will see haw create your own PHP Packages, resolve mistakes on your code... The great content!

Help me with only $1 / month and you will have access to my private content! 
And more, you need help to code your project? I can help you! Access my [Patreon Profile](https://www.patreon.com/brunocouty), I can help you via e-mail or skype!

[https://www.patreon.com/brunocouty](https://www.patreon.com/brunocouty)