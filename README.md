JGoogleAPI Extension for Yii
=======================

JGoogleAPI it's an Yii extension to use the Google Api PHP Client in a easy way.


Author
-------

**João Parreira**


Version
-------
0.2.0


Requirements
------------

Yii 1.1.8
PHP >= 5.3


Resources
---------

This extension uses google-api-php-lib-0.6 - Version 0.6.2
http://code.google.com/p/google-api-php-client/


How to Use
----------

--Copy

----Copy files under extension folder

--Configuration

----config/main.php

    // application components
    'components' => array(
        'JGoogleAPI' => array(
            'class' => 'ext.JGoogleAPI.JGoogleAPI',
            //Default authentication type to be used by the extension
            'defaultAuthenticationType'=>'serviceAPI',
            
            //Account type Authentication data
            'serviceAPI' => array(
                'clientId' => 'YOUR_SERVICE_ACCOUNT_CLIENT_ID',
                'clientEmail' => 'YOUR_SERVICE_ACCOUNT_CLIENT_EMAIL',
                'keyFilePath' => 'THE_PATH_TO_YOUR_KEY_FILE',
            ),
            /*
            //You can define one of the authentication types or both (for a Service Account or Web Application Account) 
            webAppAPI = array(
                'clientId' => 'YOUR_WEB_APPLICATION_CLIENT_ID',
                'clientEmail' => 'YOUR_WEB_APPLICATION_CLIENT_EMAIL',
                'clientSecret' => 'YOUR_WEB_APPLICATION_CLIENT_SECRET',
                'redirectUri' => 'YOUR_WEB_APPLICATION_REDIRECT_URI',
                'javascriptOrigins' => 'YOUR_WEB_APPLICATION_JAVASCRIPT_ORIGINS',
            ),
            */
            'simpleApiKey' => 'YOUR_SIMPLE_API_KEY',
            
            //Scopes needed to access the API data defined by authentication type
            'scopes' => array(
                'serviceAPI' => array(
                    'drive' => array(
                        'https://www.googleapis.com/auth/drive.file',
                    ),
                ),
                'webappAPI' => array(
                    'drive' => array(
                        'https://www.googleapis.com/auth/drive.file',
                    ),
                ),
            ),
            //Use objects when retriving data from api if true or an array if false
            'useObjects'=>true,
        ),
        ...
    ),
    
    
--Usage

----Create a service

        $service = Yii::app()->JGoogleAPI->getService('Drive');
        
        or
        
        $service = Yii::app()->JGoogleAPI->getService('Drive','webappAPI');
        if the authentication type is diferent of the default

----Create a object

        $file = Yii::app()->JGoogleAPI->getObject('DriveFile','Drive');
        we pass the object name that we want to create and the service where it belongs
        
        or
        
        $file = Yii::app()->JGoogleAPI->getObject('DriveFile','Drive','webappAPI');
        if the authentication type is different of the default
        
        
----If you choose 'webappAPI' authentication method you need aditional steps, because you need to save the token from
    the authenticate method
    
        API Ex: http://code.google.com/p/google-api-php-client/
    
        Ex:
        //Create an extension Instance
        $jgoogleapi = Yii::app()->JGoogleAPI;

        try {
            if(!isset(Yii::app()->session['auth_token'])) {
                //Get the instance of the client from the api
                $client = $jgoogleapi->getClient();
                //or
                //$client = Yii::app()->JGoogleAPI->getClient();    #Without creating an extension instance            
                
                //Web Application User authentication
                //You want to use a persistence layer like the DB or memcached to store the token for the current user
                $client->authenticate();
                //or
                //$jgoogleapi->getClient()->authenticate();
                //or
                //Yii::app()->JGoogleAPI->getClient()->authenticate();
                
                Yii::app()->session['auth_token']=$client->getAccessToken();
                
            } else {
                $client->setAccessToken(Yii::app()->session['auth_token']);
                //List files from Google Drive
                $files = $jgoogleapi->getService('Drive')->files->listFiles();
                //Check the api documentation to see other ways to interact with api
                
                // We're not done yet. Remember to update the cached access token.
                // Remember to replace $_SESSION with a real database or memcached.
                Yii::app()->session['auth_token'] = $client->getAccessToken();
            }
        }catch(Exception $exc) {
            //Becarefull because the Exception you catch may not be from invalid token
            Yii::app()->session['auth_token']=null;
            throw $exc;
        }
 

CHANGELOG
---------
0.1   - Initial Release
0.2.0 - Update to google-api-client-php version 0.6.2

TODO
----
Keep it up-to-date when google lib changes


Inspiration
-----------

This extension is inspired in googleapis made by dkhan
http://www.yiiframework.com/extension/googleapis/


Versioning
----------

For transparency and insight into our release cycle, and for striving to maintain 
backward compatibility, Bootstrap will be maintained under the Semantic Versioning 
guidelines as much as possible.

Releases will be numbered with the follow format:

`<major>.<minor>.<patch>`

And constructed with the following guidelines:

* Breaking backward compatibility bumps the major
* New additions without breaking backward compatibility bumps the minor
* Bug fixes and misc changes bump the patch

For more information on SemVer, please visit http://semver.org/.


Copyright and license
---------------------

The MIT License

Copyright 2012 João Parreira <joaofrparreira@gmail.com>.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
