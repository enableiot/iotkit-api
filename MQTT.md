# Communicating with IoT Analytics with MQTT
An extensive REST API is provided for users to interact with the IoT Analytics server.

## Basics

Users must publish to the IoT Analytics broker using a secure connection. Device authentication uses the device's ID and activation token. The CA certificate installed with the iotkit-agent must be used for the TLS connection.

### Connection options
* **broker**: `broker.us.enableiot.com`
* **port**: `8883`
* **username**: device-ID of the device to publish from
* **password**: device-token assigned to the device during initial activation
* **Use TLS**: yes
* **CA certificate(s)**: `/usr/lib/node_modules/iotkit-agent/certs/AddTrust_External_Root.pem` (default install location on Galileo/Edison)

### Supported Topics

|Action|Request Topic (Publish)|Response Topic (Subscribe)|Description|
|---------------|----------------|---------------------|----------------------------------------------|
|activate|device/*{deviceid}*/activation|server/devices/*{deviceid}*/activation|Send activation code and check device activation status|
|update device metadata|server/devices/*{deviceid}*/metadata|n/a|Update device metadata (attributes, tags, description, etc.)|
|submit data|server/metric/*{accountid}*/*{gatewayid}*|n/a|Submit data observation|
|add component|server/devices/*{deviceid}*/components/add|device/*{deviceid}*/components|Register a new component (sensor) and verify status|
|health|server/devices/*{deviceid}*/health|device/*{deviceid}*/health|Request health information and build version of Dashboard|
|component catalog|server/devices/*{deviceid}*/cmpcatalog|device/*{deviceid}*/cmpcatalog|Display the account's component-type catalog|
|control command|n/a|device/*{gatewayid}*/control|Receive actuations sent from Dashboard |


### Request Topics
#### Activate Device
<pre>
<b>Topic: device/(device id)/activation</b>
<b>Message:</b>
{
    "deviceId": (device id),
    "deviceToken": (device token),
    "activationCode": (activation code),
}</pre>


####Update Device Metadata	
<pre>
<b>Topic: server/devices/(device id)/metadata</b>
<b>Message:</b>
{
	"attributes": {
		"agent_version": "1.7.0",
		"hardware_vendor": "Intel(R)Core(TM)i5-2520MCPU@2.50GHz",
		"hardware_model": "win32",
		"ModelName": "x64",
		"FirmwareVersion": "6.1.7601"
	},
	"gatewayId": (gateway ID),
	"deviceToken": (device token)
}</pre>
*sample attributes

####Submit Data
<pre>
<b>Topic: server/metric/(account id)/(gateway id)</b>
<b>Message:</b>
{
	"accountId": (account id),
	"did": (device id),
	"on": (submission timestamp),
	"count": (data count),
	"data": [{
		"on": (data timestamp),
		"value": (data value),
		"cid": (component id)
	}]
}}</pre>

####Add Component	
<pre>
<b>Topic: server/devices/(device id)/components/add</b>
<b>Message:</b>
{
	"name": (component name),
	"type": (component type),
	"cid": (component id),
	"deviceToken": (device token)
}</pre>

####Test Dashboard Status	
<pre>
<b>Topic: server/devices/(device id)/health</b>
<b>Message:</b>
{
    "detail": "mqtt"
}</pre>

####Component Catalog	
<pre>
<b>Topic: server/devices/(device id)/cmpcatalog</b>
<b>Message:</b>
{
	"deviceToken": (device token),
	"deviceId": (device id)
}</pre>

**Common Parameters**

|Parameter|Description|
|--------------|------------------|
|device id|unique device name|
|gateway id|unique gatweway name (usually same as device ID)|
|account id | account ID for the account the device is activated under|
|device token| unique token assigned to the device by the Dashboard|
|component name| local name for the component/sensor|
|component type| type listed in the account's component catalog|
|component id | user-defined unique ID for a component (typically is a GUID)|


## Examples
Python and Node.JS [sxample scripts](https://github.com/enableiot/iotkit-samples/tree/master/mqtt_api) are provided to show how to submit sensor data or request server information via MQTT

**mqtt_submit_data** - Submits data observation to the Cloud<br>
**mqtt_health** - Requests Cloud server health status

**Note** Node.JS samples are broken with mqtt v1.1.2+<br>


