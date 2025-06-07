Full credit to CredSniper this was modified as a POC for a presentation.  I'm using mitm proxy to capture creds sent to the github template.  Steps Below:
1. Install mitmproxy: sudo apt install mitmproxy -y
2. Generate cert: mitmproxy
3. Install cert in browser cert located at: ~/.mitmproxy/mitmproxy-ca-cert.pem
4. Run using: sudo mitmproxy --listen-port 8080
5. Set foxy proxy to use the following socket for forwarding traffic to mitmproxy: 127.0.0.1:8080
6. Install repo: git clone git@github.com:PCarra/credsniper_modified.git
7. Install a python virtual environment: python3 -m venv venv
8. Launch a python virtual environment: source venv/bin/activate
9. Install requirements: pip install -r requirements
10. Inside of CredSniper directory run: touch .cache
11. Run credsniper: python3 credsniper.py --module github --twofactor --port 5000 --final https://github.com/login --hostname github.com
12. Navigate to http://127.0.0.1:5000
13. Enter creds
14. Go to mitmproxy tab
15. Find the packets of interest and use the down error to move the cursor then select by hitting enter to inspect the packet

CredSniper
==================
- [Overview](#overview)
	- [Benefits](#benefits)
- [Basic Usage](#basic-usage)
- [Installation](#installation)
- [Screenshots](#screenshots)
	- [Gmail Module](#gmail-module)
- [Maintainer](#maintainer)
- [Contributing](#contributing)

## Overview ##
Easily launch a new phishing site fully presented with SSL and capture credentials along with 2FA tokens using CredSniper. The API provides secure access to the currently captured credentials which can be consumed by other applications using a randomly generated API token.

**Brought to you by:**

![Black Hills Information Security](https://www.blackhillsinfosec.com/wp-content/uploads/2016/03/BHIS-logo-L-300x300.png "Black Hills Information Security")

### Benefits ##

 * Fully supported SSL via Let's Encrypt
 * Exact login form clones for realistic phishing
 * Any number of intermediate pages
	 * (i.e. Gmail login, password and two-factor pages then a redirect)
 * Supports phishing 2FA tokens
 * API for integrating credentials into other applications
 * Easy to personalize using a templating framework

## Basic Usage ##
usage: **credsniper.py** [-h] --module MODULE [--twofactor] [--port PORT] [--ssl]
                     [--verbose] --final FINAL --hostname HOSTNAME
```
optional arguments:
  -h, --help           show this help message and exit
  --module MODULE      phishing module name - for example, "gmail"
  --twofactor          enable two-factor phishing
  --port PORT          listening port (default: 80/443)
  --ssl                use SSL via Let's Encrypt
  --verbose            enable verbose output
  --final FINAL        final url the user is redirected to after phishing is done
  --hostname HOSTNAME  hostname for SSL
```
### Credentials
**.cache** : Temporarily store username/password when phishing 2FA

**.sniped** : Flat-file storage for captured credentials and other information

### API End-point
* View Credentials (GET)
`https://<phish site>/creds/view?api_token=<api token>`

* Mark Credential as Seen (GET)
`https://<phish site>/creds/seen/<cred_id>?api_token=<api token>`

* Update Configuration (POST)
`https://<phish site>/config`
```
	{
	   'enable_2fa': true,
	   'module': 'gmail',
	   'api_token': 'some-random-string'
	}
```  
### Modules
All modules can be loaded by passing the `--module <name>` command to CredSniper. These are loaded from a directory inside `/modules`. CredSniper is built using [Python Flask](http://flask.pocoo.org/) and all the module HTML templates are rendered using [Jinja2](http://jinja.pocoo.org/docs/2.9/).

* **Gmail**: The latest Gmail login cloned and customized to trigger/phish all forms of 2FA
	* **modules/gmail/gmail.py**: Main module loaded w/ --module gmail
	* **modules/gmail/templates/error.html**: Error page for 404's
	* **modules/gmail/templates/login.html**:  Gmail Login Page
	* **modules/gmail/templates/password.html**: Gmail Password Page
	* **modules/gmail/templates/authenticator.html**: Google Authenticator 2FA page
	* **modules/gmail/templates/sms.html**: SMS 2FA page
	* **modules/gmail/templates/touchscreen.html**: Phone Prompt 2FA page

**GMAIL UPDATE:** Google requires a backup form of 2FA when using U2F. Bypassing U2F is possible by forcing one of the fall-back options instead of prompting the user to use their U2F device. CredSniper attempts to force SMS if it's available otherwise it forces TOTP. For security savvy victims, they may be weary if they are prompted for the SMS or TOTP token instead of their U2F device. 

* **Example**: An example module that demonstrates standard phishing w/ 2FA tokens
	* **modules/example/example.py**: Main module loaded w/ --module example
	* **modules/example/templates/login.html**: Standard login form
	* **modules/example/templates/twofactor.html**: Standard 2FA token form
         
## Installation ##

### Ubuntu 16.04

You can install and run automatically with the following command:

```bash
$ git clone https://github.com/ustayready/CredSniper
$ cd CredSniper
~/CredSniper$ ./install.sh
```

Then, to run manually use the following commands:

```bash
~/$ cd CredSniper
~/CredSniper$ source bin/activate
(CredSniper) ~/CredSniper$ python credsniper.py --help
```

Note that Python 3 is required.


## Screenshots
### Gmail Module
![GmailLogin](https://raw.githubusercontent.com/ustayready/CredSniper/master/screenshots/gmail_login.png "GmailLogin")

![GmailPassword](https://raw.githubusercontent.com/ustayready/CredSniper/master/screenshots/gmail_password.png "GmailPassword")

![GmailSMS](https://raw.githubusercontent.com/ustayready/CredSniper/master/screenshots/gmail_sms.png "GmailSMS")

![GmailAuthenticator](https://raw.githubusercontent.com/ustayready/CredSniper/master/screenshots/gmail_authenticator.png "GmailAuthenticator")

![GmailTouch](https://raw.githubusercontent.com/ustayready/CredSniper/master/screenshots/gmail_touch.png "GmailTouch")

## Maintainer
- CredSniper was originally created by [Mike Felch](https://github.com/ustayready) ([@ustayready](https://twitter.com/ustayready)) 

## Contributing

1. Create an issue to discuss your idea
2. Fork CredSniper (https://github.com/ustayready/CredSniper/fork)
3. Create your feature branch (`git checkout -b my-new-feature`)
4. Commit your changes (`git commit -am 'Add some feature'`)
5. Push to the branch (`git push origin my-new-feature`)
6. Create a new Pull Request

**Bug reports, feature requests and patches are welcome.**

[![Analytics](https://ga-beacon.appspot.com/UA-109055908-1/CredSniper/Readme)](https://github.com/ustayready/CredSniper)
# credsniper_modified
