hystrix-sample
==============

This app demonstrates issues with Hystrix streams on PCF and PWS.

Build the app with the following command: 

~~~
$ ./gradlew assemble
~~~

Run the app locally with the following command (assuming `java` is on the path):

~~~
$ java -jar build/libs/hystrix-sample.jar
~~~

Push to Cloud Foundry with the included `manifest.yml`:

~~~
$ cf push
~~~

Hitting the `/hystrix.stream` endpoint in the app should display a stream of `ping` responses to the client. This works when the app is run locally.

When running the app on PCF or PWS, the client (e.g. browser, curl or wget) hangs on the first request to the `/hystrix.stream` endpoint. No `RTR` messages appear in the app logs showing that the request was received.

When the app is stopped on CF (e.g. `cf stop hystrix-sample`), the stream of `ping:` responses is sent to the client, and the following entries are written to the app logs, including the `RTR` entry showing the request being processed.

~~~
2015-01-20T15:25:04.30-0800 [API]     OUT Updated app with guid 060ca8be-eac1-4756-9b59-f283e6c78a48 ({"state"=>"STOPPED"})
2015-01-20T15:25:06.58-0800 [RTR]     OUT hystrix-sample.cfapps.io - [20/01/2015:23:24:48 +0000] "GET /hystrix.stream HTTP/1.1" 200 288 "http://hystrix-sample.cfapps.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.99 Safari/537.36" 10.10.2.229:18534 x_forwarded_for:"207.114.222.38" vcap_request_id:fe01cc0e-106e-4600-6394-ed093b4f1113 response_time:17.838011919 app_id:060ca8be-eac1-4756-9b59-f283e6c78a48
2015-01-20T15:25:07.53-0800 [DEA]     OUT Stopping app instance (index 0) with guid 060ca8be-eac1-4756-9b59-f283e6c78a48
2015-01-20T15:25:07.53-0800 [DEA]     OUT Stopped app instance (index 0) with guid 060ca8be-eac1-4756-9b59-f283e6c78a48
~~~
