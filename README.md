# google-home-notifier with DNS PHP API v2
Original project forked from https://github.com/noelportugal/google-home-notifier and modified with API added.
When you install and run local NodeJS, it connects to ngrock.io Service.
Every time it's connected, RANDOM-NUMBER is generated and ( URL https://RANDOM-NUMBER.ngrok.io/google-home-notifier ) on ngrock server side. We can not know what number we get every single time. 
For that reason, I'v created PHP API and MySQL database to store those data (final URL) ex. 4a27ae68.ngrok.io
Every time app connects, it push new updates to MySQL and then we can use cURL and POST methods from our subdomain ex. https://push.my-website.com/api3/public/index_forward.php?device=1&userdata=How-are-you?

REMEMBER: Messages are sent and formated with "-" in place of space. ex. userdata=How-are-you?

# google-home-notifier
Send notifications to Google Home

#### Installation
```sh
$ npm install google-home-notifier
```

#### Usage
```javascript
var googlehome = require('google-home-notifier');
var language = 'pl'; // if not set 'us' language will be used

googlehome.device('Google Home', language); // Change to your Google Home name
// or if you know your Google Home IP
// googlehome.ip('192.168.1.20', language);

googlehome.notify('Hey Foo', function(res) {
  console.log(res);
});
```

#### Listener
If you want to run a listener, take a look at the example.js file. You can run this from a Raspberry Pi, pc or mac. 
The example uses ngrok so the server can be reached from outside your network. 
I tested with ifttt.com Maker channel and it worked like a charm.

```sh
$ git clone https://github.com/noelportugal/google-home-notifier
$ cd google-home-notifier
$ npm install
$ node example.js
Endpoints:
    http://192.168.1.20:8091/google-home-notifier
    https://xxxxx.ngrok.io/google-home-notifier
GET example:
curl -X GET https://xxxxx.ngrok.io/google-home-notifier?text=Hello+Google+Home  - to play given text
curl -X GET https://xxxxx.ngrok.io/google-home-notifier?text=http%3A%2F%2Fdomain%2Ffile.mp3 - to play from given url
POST example:
curl -X POST -d "text=Hello Google Home" https://xxxxx.ngrok.io/google-home-notifier - to play given text
curl -X POST -d "http://domain/file.mp3" https://xxxxx.ngrok.io/google-home-notifier - to play from given url

```
#### Raspberry Pi
If you are running from Raspberry Pi make sure you have the following before nunning "npm install":
Use the latest nodejs dist.
```sh
curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
sudo apt-get install nodejs
```
Also install these packages:
```sh
sudo apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev
```

## After "npm install"

Modify the following file "node_modules/mdns/lib/browser.js"
```sh
vi node_modules/mdns/lib/browser.js
```
Find this line:
```javascript
Browser.defaultResolverSequence = [
  rst.DNSServiceResolve(), 'DNSServiceGetAddrInfo' in dns_sd ? rst.DNSServiceGetAddrInfo() : rst.getaddrinfo()
, rst.makeAddressesUnique()
];
```
And change to:
```javascript
Browser.defaultResolverSequence = [
  rst.DNSServiceResolve(), 'DNSServiceGetAddrInfo' in dns_sd ? rst.DNSServiceGetAddrInfo() : rst.getaddrinfo({families:[4]})
, rst.makeAddressesUnique()
];
```

## PHP API "Slim API"

Find PHP-Server.zip file and unpack it on your web server, example: https://your-domain.com/api3/
Make sure files are accessible from the web, like:
http://your-domain.com/api3/public/index_forward.php?device=0&userdata=Hello-Google-Home

Make sure you ceate MySQL database and modify db.php files located in 
```sh
api3/src/config/db.php
api3/public/index_forward.php
```
Created MySQL database and one empty table like:
```javascript
database name:  storedns
table name: storedDNS
columns: 
tb_id   Auto increment  size 10, 
storedDNS   Text size  255

```
And change to:
```javascript
Browser.defaultResolverSequence = [
  rst.DNSServiceResolve(), 'DNSServiceGetAddrInfo' in dns_sd ? rst.DNSServiceGetAddrInfo() : rst.getaddrinfo({families:[4]})
, rst.makeAddressesUnique()
];
```
