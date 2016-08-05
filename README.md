![](http://www.optretina.com/wp-content/uploads/2014/11/logo-optretina.png)

The OPTRETINA Raspberry API. Connect event Raspberries to OPTretina

# Features

This package provides tools for the following:

- Authentication
- Log control

## Authentication
Authentication is made through the Raspberry serial number. The **serial_number** param must be present in every request.

Params are sent in the POST body in **JSON** format *(NOT FORM FORMAT).*

Raspberries are supposed to work in unstable internet connections and request could be generated and sent in different times. Requests must include a “time” parameter in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)

### Request

**HTTP Method**: POST

**URL**: https://raspberry.optretina.com/auth

Param  | Description
------------- | -------------
serial_number  | serial_number
time  | Time when the request/event was generated. In ISO 8601


#### Example

```
#!bash
curl -H 'Content-Type: application/json' -X POST -d '{"serial_number": "XXXXX", "time": "2016-08-05T16:19:15+00:00"}' https://raspberry.optretina.com/auth
```

#### Example Request
```json
{
    "serial_number": "00003947622",
    "time": "2016-08-05T11:40:21+02:00"
}
```


#### Response
Param  | Description
------------- | -------------
success  |  **true** if the serial number is good, **false** otherwise
message  | A descriptive message of the response
data  |  Object containing response data
retry_time  |  In case of error, the time the system should wait to retry (In seconds)

#### Example Success Response
```json
{
  "success": true,
  "message": "Linked",
  "data": {
    "auth": {
      "host": "XXX.XXX.XXX.XXX",
      "port": 00,
      "username": "yyyy",
      "password": "zzzz"
    }
  }
}
```

#### Example Fail Response
```json
{
  "success": false,
  "message": "No serial number provided",
  "retry_time": 1800
}
```

## Logging events
The Raspberry device will generate a series of events that must be sent to the server to store and/or react to them.

All log events will be sent to this method

#### Valid code events

Code  | Description
------------- | -------------
ping  |  Event to check if the device can contact the server
copy-device | The images were copied to the local disk
delete-files-camera  |  The images were deleted from the camera
ftp-transfer  |  The images were transferred to the server
delete-files-device | The images were deleted from the local disk

### Request

**HTTP Method**: POST

**URL**: https://raspberry.optretina.com/log

Param  | Description
------------- | -------------
serial_number  | serial_number
time  | Time when the request/event was generated. In ISO 8601
event  | Event code
success  | **true** if the event was successful, **false** otherwise
data  | Event data object, optional. It can include as many data as needed to provide details of the event.

#### Example

```
#!bash
curl -H 'Content-Type: application/json' -X POST -d '{"serial_number": "XXXXX", "time": "2016-08-05T16:19:15+00:00", "event":"delete-files-camera", "success":"true"}' https://raspberry.optretina.com/log
```

#### Example Request
```json
{
    "serial_number": "00003947622",
    "time": "2016-08-05T11:40:21+02:00",
    "event": "delete-files-camera",
    "success": true
    "data": {
      "images_deleted":8
    }
}
```
#### Response
Param  | Description
------------- | -------------
success  |  **true** if the serial number is good, **false** otherwise
message  | A descriptive message of the response
data  |  Object containing response data. It could be empty.

#### Example Success Response
```json
{
  "success": true,
  "message": "Log added",
  "data": []
}
```

#### Example Fail Response
```json
{
  "success": false,
  "message": "Success property not defined",
}
```
