# WiFi HTTPClient for Arduino

## Overview
This is a modified version of Interactive Matter's HTTPClient code that works with official Arduino WiFi Shield and has a few minor bugfixes.  Please generate issues if you find any bugs.

Thanks,
RWSDev Team

An Arduino HTTP Client that uses the Arduino WiFi Library to make HTTP requests
and handle responses.

## Usage
  
### Creating a HTTP Client

HTTP Client works with the Arduino WiFi Library. Before you can create
a HTTP client you must initialize your WiFi connection with something 
like:

```c++
char ssid[] = "YOUR_SSID"; //  your network SSID (name) 
char pass[] = "YOUR_PASSWORD";    // your network password (use for WPA, or use as key for WEP)
int keyIndex = 0;            // your network key Index number (needed only for WEP)

WiFiClient client;

void setup(void) {
  while ( status != WL_CONNECTED) { 
    Serial.print("Attempting to connect to SSID: ");
    Serial.println(ssid);
    status = WiFi.begin(ssid, pass);
    // wait 10 seconds for connection:
    delay(10000);
  } 
}
```

For details on this see http://arduino.cc/en/Reference/ServerBegin

To create a client (in this example for pachube) you can simply call one of 
the constructors:

```c++
//  The address of the server you want to connect to (pachube.com):
byte server[] = { 173,203,98,29 }; 
HTTPClient client("api.pachube.com",server);
```

which is equivalent to

```c++
HTTPClient client("api.pachube.com",server,80);
```

Now you are ready to go.

### Making a request

HTTP client supports three types of requests:

1. `GET` requests to get some data from a URL
2. `POST` requests to transfer a larger amount of data to a server
3. `PUT` requests as sepcified by REST APIs

`DELETE` requests have not yet been implemented.

All request take a number of parameters (depending on the request type):

* The URI - a string (char*) containing the uri - which is normally everything 
  following the hostname of a URL for http://arduino.cc/en/Reference/HomePage
  it would be Reference/HomePage
* Optional parameters as key value pairs. Parameters are appended to a URL like
  http://myhost/the/uri?parameter-name=parameter-value&other=parameter
  parameters are values of the struct http_client_parameter. It is easiest to
  do this like:

```c++
http_client_parameter parameters[] = {
  { "key","afad32216dd2aa83c768ce51eef041d69a90a6737b2187dada3bb301e4c48841" }
  ,{ NULL,NULL }
};
```

* For POST and PUT request a string with additional data can be passed as a string. The data
  has to be in memory. Future Versions may have future features.
* For all requests additional headers can be specified. It works exactly the same was as uri
  parameters:

```c++
http_client_parameter pachube_api_header[] = {
  { "X-PachubeApiKey","afad32216dd2aa83c768ce51eef041d69a90a6737b2187dada3bb301e4c48841" }
  ,{ NULL,NULL }
};
```

Even though the HTTPClient supports HTTP 1.1 request no keep alive requests are supported currently.

### Handling a response

The result code of a HTTP request can be read with `getLastReturnCode()`. It returns a 
integer containing the return code. 200 indicates that everything was ok.
For further details refer to http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html

The result of a request is a stream (aka `FILE*`) by that you can read the data
without the need to keep the whole answer in memory - which would fail for most
HTML pages.

`FILE*` streams are a bit more unusual the normal Arduino streams. They have been 
choosen since you can use all the nice fprintff and fscanf routines of avr-libc.

After reading the response from the stream it has to be closed with the method:

```c++
closeStream(stream)
```

**DO NOT FORGET IT**. Each stream has some data attached and if you forget to close the 
stream you get a memory leak, slowly filling up the precious memory of the Arduino.

### Debug mode

The HTTPClient has also a debug mode which can be switched on and off by using `debug()`
with parameter 0 as no debug and anything else � e.g. -1 � as enabling debug output.
By default the debug code is disabled. If debug is enabled the complete request and 
response is printed out on the serial connection. Very useful if your request does
not work.

## Contributions

* Thanks to [interactive-matter](https://github.com/interactive-matter/HTTPClient) for the original HTTPClient
* Thanks to [colagrosso](http://github.com/colagrosso) for fixing the URL encoding
* Thanks to [hex705](http://github.com/hex705) for properly porting it to Arduino 1.0

## Copyright and license

HTTPClientWiFi is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

HTTPClientWiFi is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.
You should have received a copy of the GNU General Public License
along with HTTPClientWiFi.  If not, see http://www.gnu.org/licenses/.
