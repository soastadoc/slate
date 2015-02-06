---
title: mPulse Beacons API Reference

language_tabs:
  - javascript: JavaScript
  - c: iOS
  - java: Android
  - java: Java

toc_footers:
  - <a href='http://cloudlink.soasta.com'>SOASTA CloudLink</a>
  - <a href='http://www.soasta.com'>SOASTA.com</a>

search: true
---

# Beacons API

The mPulse Beacons API allows mPulse customers to send beacon data from mPulse-configured native and web apps.

This API exposes a simple way to associate beacon data with specific sessions and send beacon data to the beacon log for the given app.

Developers will create HTTP requests for two different URLs:

The configuration URL (config.js) is the configuration response returns a JavaScript block that includes the session ID, session start timestamp, a unique crumb, and so forth.

The beacon URL is constructed from required values found in the config.js response, as well as any additional optional parameters. All of the query parameters are appended to the beacon URL using the techniques in the following steps. 

# Getting Config

In the following steps, use GET to get back the Configuration object from which to parse the subsequent beacon URL and its appended sends. You will need the API Key for the configured mPulse app (e.g. the key found in mPulse's Configure Apps, General tab, API key field).

1.  Issue a GET request to the config.js URL:

     https://c.go-mpulse.net/boomerang/config.js?
      
   For example, if the mPulse mobile app configured is:
   
    'mobile-beacons.example.com'
    
And if the API key is `XXXXX-YYYYY-ZZZZZ-XXXXX-YYYYY`, then use the following URL to get config.js:

`https://c.go-mpulse.net/boomerang/config.js?key=XXXXX-YYYYY-ZZZZZ-XXXXX-YYYYY&d=mobile-beacons.example.com`

This request will get a JavaScript response that is similar to the following example response:

`BOOMR_configt=new Date().getTime();BOOMR.session.ID = "6ad5f44a-466f-4373-b3c7-b5cfdd22118d";BOOMR.addVar( {"h.key": "XXXXX-YYYYY-ZZZZZ-XXXXX-YYYYY","h.d": "mobile-beacons.example.com","h.t": 1421874698124,"h.cr": "131b1721c986a42bb8f7f8488ad556f12115fa3d"} ).init( {"site_domain": "mobile-beacons.example.com","beacon_url": "//36d71138.mpstat.us/","beacon_interval": 5,"RT": {"session_exp": 1800}} );`


# Required Fields to Parse from config.js

2.  From here, extract the following required fields (or, in the case of session ID, provide a replacement value). Note the Collector value (e.g. `"//36d71138.mpstat.us/"`) for use in the subsequent beacon URL.

<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr style="background-color: #d8eefa;">
<td width="314" valign="top">
BOOMR.session.ID
</td>
<td width="803" valign="top">
 The BOOMR. session.ID is used for the entire user session. It can be parsed from the response or you can provide a different session ID  (for example, by generating a UUID to use). 

BOOMR.session.ID, h.t and beacon_url will be used for the entire user session. 

</td>
</tr>
<tr style="background-color: #ffffff;">
  <td width="314" valign="top">

h.t

</td>
<td width="803" valign="top">

parsed from config.js, is used for the entire user session

</td>
</tr>
<tr style="background-color: #d8eefa;">
<td width="314" valign="top">h.cr</td>
<td width="803" valign="top">

The crumb expires after 10 minutes. After which, obtain a new crumb in subsequennt requests to config.js
</td>
</tr>
<tr style="background-color: #ffffff;">
  <td width="314" valign="top">
  
<p style="font-family: Arial, Helvetica, sans-serif; margin-top: 5px;">beacon_url

</td>
<td width="803" valign="top">


This is used for the entire user session.

</td>
</tr>
<tr style="background-color: #d8eefa;">
<td width="314" valign="top">v</td>
<td width="803" valign="top">


Specify the Boomerang version. Such as `v=0.9.1406564410`.
</td>
</tr>
<tr style="background-color: #ffffff;">
  <td width="314" valign="top">
  
<p style="font-family: Arial, Helvetica, sans-serif; margin-top: 5px;">ua.os

</td>
<td width="803" valign="top">


Specify the mobile device OS, such as `ua.os=iOS` or `ua.os=Android`.

</td>
</tr>
<tr style="background-color: #d8eefa;">
<td width="314" valign="top">

ua.osv
</td>
<td width="803" valign="top">
<p style="font-family: Arial, Helvetica, sans-serif; margin-top: 5px;">

Specify the mobile device OS version. Such as `ua.osv=8.1`.
</td>
</tr>   
</tbody>
</table>


# Construct a Beacon URL

3.  Next, issue a beacon to the `beacon_url` value, as follows:

    https://&lt;beacon_url&gt;/?

      h.key=&lt;API-KEY&gt;
    
      &h.d=&lt;registered domain name&gt;
    
    &h.cr=&lt;cached h.cr value&gt;
    
    &h.t=&lt;cached h.t value&gt;

    &rt.si=&lt;session id&gt;

    &rt.ss=&lt;timestamp when session started&gt;

    &rt.sl=&lt;number of actions/pages/views in this session&gt;

    &rt.start=manual

    &rt.tstart=&lt;timestamp when all timings start&gt;

    &t_done=&lt;time to complete request&gt;

    &u=&lt;url&gt;
`

Constructing a beacon for a native app is usually more straightforward than for a web app because of the limited number of parameters involved. For example, to learn that example.com takes five seconds to load, only the following parameters are necessary:

   `
    https://&lt;beacon_url&gt;/?

      h.key=&lt;API-KEY&gt;

      &h.d=&lt;registered domain name&gt;

      &h.cr=&lt;cached h.cr value&gt;

      &h.t=&lt;cached h.t value&gt;

      &rt.ss=&lt;timestamp when session started&gt;

      &t_done=&lt;time to complete request&gt;


### Appending Other Query Parameters

Custom query parameters can be appended to a query string in order to log useful values. To do so, simply append a custom query parameter. Such values will NOT be ingested into mPulse but WILL be logged in the beacon logs.

For example, to track a platform-specific sessionID from a third-party app such as an Omniture session ID, you can append something like:

`h.OmnSessionID=&lt;value of session ID&gt;`


# CURL Examples


The following section details specific examples using curl in iOS, Android, JavaScript, and Java development.</p>

## JavaScript

Use the example on the right to construct your own CURL syntax.

```javascript

` curl -H "Authentication: da7be4d72030656559f3e41a98924a6b2a544730" '<mPulse URL>/concerto/mpulse/api/soasta.com/summary?date=2013-05-10&page-group=Home&user-agent=Chrome/26&test=B%20Test'`
```


## iOS 


Use the example on the right to construct your own CURL syntax.

```c

` curl -H "Authentication: da7be4d72030656559f3e41a98924a6b2a544730" '<mPulse URL>/concerto/mpulse/api/soasta.com/summary?date=2013-05-10&page-group=Home&user-agent=Chrome/26&test=B%20Test'`
```

## Android


Use the example on the right to construct your own CURL syntax.

```java

` curl -H "Authentication: da7be4d72030656559f3e41a98924a6b2a544730" '<mPulse URL>/concerto/mpulse/api/soasta.com/summary?date=2013-05-10&page-group=Home&user-agent=Chrome/26&test=B%20Test'`
```

## Java


Use the example on the right to construct your own CURL syntax.

```java

` curl -H "Authentication: da7be4d72030656559f3e41a98924a6b2a544730" '<mPulse URL>/concerto/mpulse/api/soasta.com/summary?date=2013-05-10&page-group=Home&user-agent=Chrome/26&test=B%20Test'`
```