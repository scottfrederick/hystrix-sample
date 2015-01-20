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

Hitting the `/hystrix.stream` endpoint in the app should display a stream `ping` responses to the client. This works when the app is run locally.

When running the app on PCF or PWS, the client (e.g. browser, curl or wget) hangs on the first request. No `RTR` messages appear in the app logs showing that the request was processed.

Stopping the app on CF causes the stream of `ping:` responses to be sent to the client, and the following entries are written to the app logs, including the `RTR` entry showing the request being processed.

~~~
2015-01-20T12:03:07.75-0800 [API]     OUT Updated app with guid 11c4d855-624a-428e-807c-72d9613f63b6 ({"state"=>"STOPPED"})
2015-01-20T12:03:07.77-0800 [App/0]   OUT Dropped a message because of read error: read unix /var/vcap/data/warden/depot/187jsaiifbj/jobs/2649/stdout.sock: use of closed network connection
2015-01-20T12:03:07.77-0800 [App/0]   ERR
2015-01-20T12:03:07.77-0800 [App/0]   ERR Dropped a message because of read error: read unix /var/vcap/data/warden/depot/187jsaiifbj/jobs/2649/stderr.sock: use of closed network connection
2015-01-20T12:03:10.04-0800 [RTR]     OUT hystrix-sample.cfapps.io - [20/01/2015:20:01:59 +0000] "GET /hystrix.stream HTTP/1.1" 200 1128 "http://hystrix-sample.cfapps.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.99 Safari/537.36" 192.168.1.103:45570 x_forwarded_for:"207.114.222.38" vcap_request_id:ed923661-da31-42bb-6374-8dc4acc9878d response_time:70.449193496 app_id:11c4d855-624a-428e-807c-72d9613f63b6
2015-01-20T12:03:10.91-0800 [DEA]     OUT Stopping app instance (index 0) with guid 11c4d855-624a-428e-807c-72d9613f63b6
2015-01-20T12:03:10.91-0800 [DEA]     OUT Stopped app instance (index 0) with guid 11c4d855-624a-428e-807c-72d9613f63b6
~~~