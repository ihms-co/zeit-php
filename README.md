# Codeigniter PHP framework example v3.1.1
## Now PHP builder on zeit.co

#### https://github.com/juicyfx/now-php

### Steps 

1. move the application, system folders along with the index.php into the api directory
2. Open api/application/config/config.php and replace

	1. Find the code:
		$application_folder = 'application'; // On line 117 v3.1.1

		replace with:
		$application_folder = $_SERVER['DOCUMENT_ROOT'].'/api/'.'application';




3. git remote add origin https://github.com/rolandihms/node-api.git

4. git pull origin master

5. Check the .env file 

5. docker-compose up -d

Thats it REST API up. By default the REST API runs on port 3000

### Authentication

To interact with the API you will require a token. To authenticate yourself you will pass
the username and secret from the user table. Set these up in the PHP backend app. http://<HOST>:80 / 443


	endpoint: authenticate
	Methods accepted: GET / POST
	Authentication: Basic HTTP Authentication (Use already provided credentials)
	Parameters:

	user	    = username from users
	secret 		= secret from users


		eg: 
			{
			  "user": "foo",
			  "secret": "foosecret"
			}

			curl -X GET http://<HOST>:3000/authenticate?user=foo&secret=foosecret

	Response

		Success: 
		{
		  "success": true,
		  "message": "Here is your API access token.",
		  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1MzA1NTEwOTcsImV4cCI6MTUzMDU1NDY5N30.Y1uE1GD_LzehQIgSANkpGjwSqPdN_KxFGu_mNxqh36Y"
		}	

		Failure
		{
		  "success": false,
		  "message": "Authentication failed. Wrong secret. or other message"
		}


Use the token response for any further API requests. The token is valid for the next 3600 seconds or 2 hours(Can be adjusted).
The token can be passed into the API in 3 different HTTP methods.
	1. The token can be passed as a querystring in a GET request
	2. As a token parameter in the POST data
	3. Or set the header of api-access-token eg: header['api-access-token']



### SMS and Email based API

SMS integration with Jasmin
https://jasmin.readthedocs.io/en/latest/


### SMS Doc v1


	endpoint: sms/send
	Methods accepted: GET / POST
	Authentication: Basic HTTP Authentication (Use already provided credentials)
	Parameters:
	
	shortcode	= 5 digit SMSC Code (99911) (optional)
	user 		= username assigned
	password	= password assigned
	number		= MSISDN destination
	msg			= SMS content
	priority	= the priority of the message in queue 0,1,2 or 3 0 has lowest priority
	batch		= boolean default false. If yes, the number/message pair are ignored and messages array expected (optional)

		eg: 
			{
			  "shortcode": "99911",
			  "user": "foo",
			  "password": "foopass",
			  "number": "264818863437",
			  "msg": "You're lovely 160 chars message",
			  "token": "The valid Authentication token"
			}
			 

BULK  SMS message dispatch
if the batch parameter is set to true, the number and message field will be overridden. 
On bulk you build your own message objects.

see below

	messages	= Array of Message content (required if batch)
	batch		= boolean default false. If yes, the number/message pair are ignored and messages array expected (optional)

		eg: 
			{
			  "user": "foo",
			  "password": "foopass",
			  "messages": [
			    {
			      "from": "99900",
			      "to": [
			        "264817634444",
			        "264817114114",
			        "264814334224"
			      ],
			      "content": "Message 1 goes to 3 numbers"
			    },
			    {
			      "from": "66766",
			      "to": [
			        "264814334224",
			        "264817634444",
			        "264814334224"
			      ],
			      "content": "Message 2 goes to 3 numbers"
			    },
			    {
			      "from": "89999",
			      "to": "264817634444",
			      "content": "Message 3 goes to 1 number"
			    }
			  ]
			}

OR

			{
			  "user": "foo",
			  "password": "foopass",
			  "messages": [
			    {
			      "from": "99900",
			      "to": [
			        "264817634444",
			        "264817114114",
			        "264814334224",
			        "264814334224"
			        "264814334224"
			        "264814334224"
			        "264814334224"
			        "264814334224"
			        "264814334224"
			        "264814334224"
			      ],
			      "content": "Message 1 goes to 10 numbers"
			    }
			  ]
			}



### EMAIl DOc v1

Send Buld email via the following. Mail Service types available Mandrill, SMTP, Mailgun / SES

	endpoint: email/send
	Methods accepted: GET / POST
	Authentication: Basic HTTP Authentication (Use already provided credentials)
	Parameters:
	
	email		= single email string, OR array of name/email objects
	from_email	= From email address string
	subject 	= This is subject
	from_name	= The name of the Sender: Brand Name/Person. The from_email and name from make up the header <app@nampost.com.na>
	priority	= normal, low, high
	content  	= the text based content of the email
	html:       = the HTML version of the content
	attachments = array of files. //https://nodemailer.com/message/attachments/
				  path on server local, or URL of external file
	token 		= The valid Authentication token			  
	

		eg: 
			{
			  "email": "email@domain.com",
			  "from_email": "no-reply@omain.com",
			  "from_name": "Gerorgie Bowie",
			  "subject": "Important Subject Here",
			  "template_id": 1, //the ID from your templates,
			  "priority": "low",
			  "content": "Hello World",
			  "html": "<h1>Hello World</h1>",
			  "attachments": {
				  	{   // utf-8 string as an attachment
			            filename: 'text1.txt',
			            content: 'hello world!'
			        },
			        {   // file on disk as an attachment
			            filename: 'text3.txt',
			            path: '/path/to/file.txt' // stream this file
			        },
					{
			            // use pregenerated MIME node
			            raw: 'Content-Type: text/plain\r\n' +
			                 'Content-Disposition: attachment;\r\n' +
			                 '\r\n' +
			                 'Hello world!'
			        }
			  }

			}




### Management Utils with PM2

simply append the pm2 commands with docker exec

List All

	docker exec -it <container> pm2 ls

Monitor Process

	docker exec -it <container> pm2 monit         # Monitor all processes

Stop / Restart

	docker exec -it <container> pm2 stop all      # Stop all processes
	docker exec -it <container> pm2 restart all   # Restart all processes
	docker exec -it <container> pm2 stop 0 
