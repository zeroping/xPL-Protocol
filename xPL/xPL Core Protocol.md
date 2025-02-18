## The xPL Protocol: "Lite on the wire, by design"

There are three styles of xPL Message, which enable all of the various
communication processes which may occur within a network of xPL applications.
xPL Messages are line based, with each line ending with a linefeed (ASCII: 10
decimal) Character. The following is an example of a typical xPL Message:

```
xpl-cmnd
 {
 hop=1
 source=xpl-xplhal.myhouse
 target=acme-cm12.server
 }
 x10.basic
 {
 command=dim
 device=a1
 level=75
 }
```

All messages conform to this structure:

  * The message type (xpl-cmnd, xpl-stat or xpl-trig) 
  * A header block of variable (but restricted) size. All name=value pairs must be present. 
  * The message schema, in the format ''schema.type'' 
  * A single message "body", containing name=value pairs 

The elements in the example above are explained in detail in the following
table:

<table border="1" cellpadding="2">
<tr>
<th width="200">xPL Message Element </th>
<th width="100">Size (in bytes)</th>
<th> Description
</th></tr>
<tr>
<td>xpl-cmnd </td>
<td> 8 </td>
<td> The message type identifier. Valid values are <i>xpl-cmnd</i> (for a command), <i>xpl-stat</i> (for a status update) and <i>xpl-trig</i> (for a trigger), for each of the three styles of xPL Message, as defined below.
</td></tr>
<tr>
<td>{ </td>
<td> 1 </td>
<td> "Open Section" delimiter. (ASCII: 123 decimal)
</td></tr>
<tr>
<td>hop=1 </td>
<td> 5 </td>
<td> 9 - max value<br /><br /> Hop count. This is incremented each time the xPL message is transferred from one physical network to another, for instance by a bridge application passing traffic between an RS485 Serial bus and Ethernet. The Hop count is used as a fail safe mechanism to prevent messages from looping forever, and to allow management nodes to determine the number of connected xPL networks.
</td></tr>
<tr>
<td>source=xpl-xplhal.myhouse </td>
<td> Tag - 7</td>
<td> The Source Tag allows nodes on the xPL network to determine the origins of a command or message. It is assembled from three components:<br /><br /> The string "xpl" is the Vendor ID, which is allocated (assigned) by the xPL team.  This may be considered a unique address space as no other vendor may use the same ID.  The Vendor ID can be a maximum of 8 characters.  The Vendor ID is separated from the Device ID with a "-" character.<br /><br /> The string "xplhal" is the Device ID which is allocated by the manufacturer/developer at design time.  The Device ID can be a maximum of 8 characters.  The Device ID is separated from the Instance ID with a "." character.<br /><br /> The string "myhouse" is the Instance ID, which is assigned an initial value by the manufacturer, but may be re-configured by the end user. Re-configuration of Instance IDs is not mandatory, but is highly recommended.  The Instance ID can be a maximum of 16 characters.<br /><br />
<p>Note: each component of the source tag has a maximum size, which may not be exceeded, or divided up in any other way. It is not legal, for instance, to use a 4 character long vendor ID, and a 12 character Device ID.<br /><br />Vendor ID max 8<br /> Device ID max 8<br /> Instance ID max 16<br /><br /> 
</p>
</td></tr>
<tr>
<td>target=acme-cm12.server </td>
<td> Tag - 7</td>
<td> Vendor ID max 8<br />Device ID max 8<br />Instance ID max 16<br /><br /> For Directed command messages (explained in detail within the text), the Target Tag is assembled in the same manner as the Source Tag. For broadcast command messages, the target may be set to a single wildcard symbol "*" (ASCII: 42 decimal). For tigger and status messages the target should always be set to a single wildcard symbol "*".
</td></tr>
<tr>
<td> } </td>
<td> 1 </td>
<td> "Close Section" delimiter. (ASCII: 125 decimal)
</td></tr>
<tr>
<td>x10.basic</td>
<td> class.type</td>
<td> Class max 8<br />Type max 8<br /><br />Message Schema Identifier. Message contents are defined by means of schemas which allow devices and applications from different vendors to communicate in an open fashion. Each message belongs to a specific class, and each class will define a number of types of message. Examples are given within the text.
</td></tr>
<tr>
<td> { </td>
<td> 1 </td>
<td> "Open Section" delimiter. (ASCII: 123 decimal)
</td></tr>
<tr>
<td> command=dim<br /> device=a1<br /> level=75 </td>
<td> name=value </td>
<td> Name max 16<br />Value no maximum<br /><br />Commands, Status and Information are expressed within an xPL Message body as a series of Name/Value Pairs. An xPL Message may contain many pairs. The specific contents of the message, as well as the order of those contents will be defined within the message schema used. Developers are free to add additional information into messages, however, the elements mandated by the message schema must always be present in the appropriate order. Values may be of zero length, i.e. the linefeed character immediately follows the equals character, provided that the particular schema in question permits them.
<p>As for 'no maximum' length of the value, please see the note below this table regarding UTF8.
</p>
</td></tr>
<tr>
<td> } </td>
<td> 1 </td>
<td> "Close Section" delimiter. (ASCII: 125 decimal)
</td></tr></table>

##Notes

  * All structural elements of an xPL message, i.e. all header fields, schema class and type names, and the names of name/value pairs, must contain only the following characters: a-z, 0-9 and "-" (lower case letters, numbers and the hyphen/dash character -- ASCII 45). 
  * The hyphen/dash character (ASCII 45) is not valid within a Vendor ID, or within the Device ID. For example, xpl-xplhal.myhouse is valid, whilst xpl-xpl-hal.myhouse is not. Vendor IDs containing a hyphen will not be issued - vendors should ensure that the hyphen character is not used within a Device ID. 
  * All structural elements of an xPL message should be lower case. For example, the line source=xpl-xplhal.myhouse is valid, whilst Source=XPL-XPLHAL.MyHouse is not. 
  * While the names of name/value pairs MUST be in lower case, the values of name/value pairs in the message body are case-insensitive, unless the schema being used explicitly states otherwise. 
  * No binary data (that is, anything outside the ASCII range of 32 to 126 (decimal) is allowed in an xPL message, with 2 exceptions; the ASCII 10 LF delimiter between message elements, and the values of key/value pairs with are allowed to be UTF8 encoded (range of 32 to 255) 
  * If the Line Feed (ASCII: 10 decimal) character is required within the data section of a name/value pair (for instance, in a multi-line On Screen Display message) it should be represented thus: '''\n''' (that is, a backslash character and a letter '''n''' -- this is not a shorthand for an actual LF/CHR(10) character in the value -- the receiving device would have to understand that sequence and convert it to a line feed) 
  * Other than the above restrictions, the value of a name/value pair may contain any UTF8 character, resulting in message bytes in the range of (space) to 255. 
  * When the ASCII restriction on values was lifted, to allow for UTF8, also the value length limit of 128 characters was dropped (UTF8 has an unpredictable byte-length). Developers are urged to consider potential backward compatibility problems with older applications. 

## Command Messages

These messages instruct a device to perform an action. The controlling node
may determine the completion of this action by the sending of another xPL
message

### Directed Command Messages

Where a device wishes to exert control over a specific device within the home,
such as the curtain controller in the lounge, or the central heating boiler,
messages may be directed to that device by means of the target field. All
devices and applications within an xPL network must respond to a message which
is specifically targetted at them in this way.

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-cm12.server
}
x10.basic
{
command=on
device=b2
}
```

In this example, the CM12 device manufactured by acme which has been connected
to the host server is sent the command command=on, device=b2. This message is
a .basic type message of class x10, which allows the device to interpret the
command in a meaningful way. A device such as a curtain controller which did
not support messages of class x10 would ignore the message.

### Broadcast Command Messages

Where a home controller is issuing commands to more intelligent devices
capable of complex parsing (PC Applications, for instance), broadcast
messaging may be used. In this case, the target contains the wildcard
character "*" (ASCII: 42 decimal), indicating that any capable device should
parse the message to determine whether the contents are intended for them.
Simple devices may not support broadcast messaging, and instead require
targetted commands.

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=*
}
lamp.basic
{
action=off
} 
```

In this example, the simple Mk1 lamp device installed in the location
livingroom does not support broadcast addressing, and ignores the message, as
it is not specifically targetted at it.

ACME's updated product, the lamp controller MkII however, is a more advanced
model capable of greater intelligence. On the basis of the source, which
identifies the sending node, and the fact that this message is a .basic type
message of class lamp, the device is able to decide that the message is
appropriate, and act on the command "action=off". This method of addressing is
particularly appropriate for "all lights off" commands using a single message,
or the setting of "scene" lighting across several controllers.

## Status Messages

Status messages are sent to notify the network about the condition of a
device. Status messages are always broadcast. All devices and applications
should send a regular status "heartbeat", and may also optionally respond to
requests for current status.

### Heartbeat Messages

Heartbeat Messages should be sent by all devices on an xPL network (with the
exception of hubs). The heartbeats allow for configuration, health monitoring,
diagnosis of problems, and logging of events. The heartbeat interval is
defined by the developer, and may be anything from five minutes to thirty
minutes. There are two template heartbeat schemas currently defined.

_Small Devices, such as PIC Systems_

```
xpl-stat
{
hop=1
source=acme-lamp.livingroom
target=*
}
hbeat.basic
{
interval=[interval in minutes]
[version=[version info]]
(...additional info defined by the developer)
}
```
_PC Based Applications, using Ethernet_
```
xpl-stat
{
hop=1
source=xpl-xplhal.myhouse
target=*
}
hbeat.app
{
interval=[interval in minutes]
port=[listening port]
remote-ip=[local IP address]
[version=[version info]]
(...additional info defined by the developer)
}
```

It should be apparent to the reader that the .app heartbeat type is an
extension of the .basic type. Heartbeat messages must use one of these schemas
as a template. They may include additional elements within the message body,
however, the base elements must be present.

#### Heartbeat behaviour at initial device startup

For situations where multiple xPL applications and/or devices may run on the
same hardware and as such, need an xPL hub, there is a recommended hub
discovery period the device and/or application go through at startup. For
devices that do not need a hub (embedded or dedicated hardware devices), this
is not needed (or even possible).

When the xPL device/application starts, it should start sending xPL heartbeats
as soon as it can and send them every 3-10 seconds until it hears/receives one
of it's own heartbeats. The reception by a device of that devices own
heartbeat indicates that there is a functioning xPL hub. Once the heartbeat is
received, the application can drop back to sending a heartbeat once every
"interval" minutes.

Until the device/application hears it's own echo, it should not send or expect
to receive any other message. In fact, until a hub is confirmed, the device is
not really part of the xPL network.

If no echoed heartbeat has been received after 2 minutes, the device should
drop to sending a heartbeat once every 30 seconds indefinetly until it hears
it's own echo.

It is not MANDATORY that xPL application/devices do this, but it is
recommended to insure the device/application truly is part of the xPL network
before it starts. It makes the application/device more reliable (knowing it's
actually "attached" to the xPL network before it starts transmitting and
receiving) as well as handles cases where an application/device and the system
hub start about the same time and the app/device gets up and going before the
hub is ready.

#### Heartbeat behaviour at device shutdown or identity change

When an xPL device is about to shutdown or if the device is about to change
any part of it's identity (vendor ID, device ID or instance ID), the device
should send a special "shutdown" heartbeat message as it's last act. That
message should have a schema type of "end" (i.e. hbeat.end or config.end).
Many xPL devices, upon reception of such a message, will remove the device
from any internal caches or uses. Other than using the schema type of "end",
the message is otherwise a normal heartbeat message for the device.

This is not mandatory. If not done, the device will eventually be considered
"timed out" by xPL programs/devices monitoring for it (if no heartbeat is
heard for twice the devices published interval period). However, it does make
the xPL network much more responsive and "real-time".

#### Heartbeats and device discovery

When a xPL device wants to quickly discover all xPL devices available, it can
send a request out requesting each xPL device respond with an immediate
heartbeat. This allows the program/device to quickly populate a list of
available resources on the xPL network.

To request all devices identify themselves, the program sends a braodcast
command message with a schema class/type of hbeat.request and a single body
element of command=request. The message would look like this:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=*
}
hbeat.request
{
command=request
}
```

When an xPL device receives this message, it should reply by sending a
standard heartbeat message. The heartbeat is no different than the regular
heartbeat the device normally sends out other than being sent right after
receiving this vs. at the next regular heartbeat interval. Devices that are
capable of it should try to wait a random amount of time between 2 and 6
seconds before reply to help prevent flooding the network with responses. It
is up to the devices implemented whether sending a heartbeat in response to a
request should reset the devices internal timekeeping on when the next regular
heartbeat is to be sent or not. Either is fine.

For simpler xPL devices (i.e. embedded devices and such), they can key in on
just receiving a command broadcast message with a schema type of
hbeat.request. They do not have to read the message body. Such devices can
handle receiving such a message by immediatly responding with its heartbeat.
For devices with more abilities, the message body should be checked/verified
to have the command=request name/value and then wait between 2 and 6 seconds
before responding (the reason for the delay starting at 2 seconds is to allow
all simpler devices that cannot do a delay to respond in the first second).

Support for device discovery is NOT required. If your program/device does not
support device discovery, it will still be eventually "found" by any xPL
application looking for devices, but that may take 5-30 minutes (depending on
your devices heartbeat rate). If your device can support this (and it
generally is very easy to refit the minimum, even into an embedded device), it
will benefit the entire xPL network and make working with your device much
easier.

## Status Requests

As well as using the regular heartbeat, a device on an xPL network which
wishes to know the current status of a device can employ the status request
mechanism.

<table border="1" cellpadding="2">
<tr>
<th width="50%">The Requesting Node </th>
<th width="50%">The Targetted Node
</th></tr>
<tr>
<td>xpl-xplhal.myhouse  </td>
<td>acme-tempsens.garage
</td></tr>
<tr>
<td>The House Manager wishes to know the temperature of the garage. It therefore constructs a command message, specifically targetted at this device, using the .request type of the sensor class of messages. </td>
<td>
</td></tr>
<tr>
<td>
<pre>xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-tempsens.garage
}
sensor.request
{
command=status
}
</pre>
</td>
<td>(listens...)
</td></tr>
<tr>
<td> </td>
<td>The Garage Temperature Sensor recognises that this message is directed at it. The message uses class sensor, and is of type .request which it supports. Therefore, it forms a status message. The Temperature sensor uses the sensor.basic message schema for this message.
<p>NOTE: Status Messages are not directed, instead the device simply broadcasts the status information using target=* 
</p>
</td></tr>
<tr>
<td>(listens...)
</td>
<td>
<pre>xpl-stat
{
hop=1
source=acme-tempsens.garage
target=*
}
sensor.basic
{
device=garage
type=temp
current=12
max1hr=13
min1hr=11
max24hr=18
min24hr=3
}
</pre>
</td></tr>
<tr>
<td>The House Manager hears a sensor.basic message whose source matches the sensor.request which it sent. It can then take further actions, such as updating a webpage, sending an SMS to the owner, or switching on the heating.  </td>
<td>
</td></tr></table>

## Trigger messages

Trigger messages should be sent whenever the status changes, whether this
happens as a result of an external event, such as the pressing of a button, an
IR detection, a temperature being reached, or a device powered on, for
example:

```
xpl-trig
{
hop=1
source=acme-pir.frontdoor
target=*
}
alarm.basic
{
sensor=PIR
status=ON
tripcnt=3
} 
```

Trigger messages are sent whenever the state of a device changes, whether in
response to an xPL Command, a direct human action (as in the pressing of a
switch) or by another condition (such as a PIR activating, or the light level
changing). Trigger messages are always broadcast.

## Device Configuration

xPL provides a powerful yet simple mechanism for device configuration,
intended to suit the requirements of devices ranging from embedded micro-
controllers through to powerful PC applications.

### Broadcast of config heartbeat message

When a device or application is started for the first time, it should begin
sending out config.basic or config.app messages at intervals of 1 minute.
These messages contain the same information as their hbeat.basic and hbeat.app
counterparts, and are used to alert an xPL Configuration Manager that the
device is waiting to be configured. If a device has previously been
configured, and was able to retain that configuration information locally, it
should not go into configuration mode, but should instead go directly to
sending out regular heartbeat messages, indicating that it is ready for
operation. Likewise, if a device does not support any level of remote
configuration, it should go straight to sending out normal hbeat.basic or
hbeat.app messages, and should not respond to any requests for configuration
information. If possible, the instance of the device should be set to a unique
value, allowing multiple devices of the same type to be configured
simultaniously without the risk of conflicts occurring. For example, an
embedded xPL device that is connected to an Ethernet network may use the
instance of defaultXXXX, where XXXX could be four digits from the Ethernet MAC
address. In the case of a PC-based application, the instance could be the
hostname of the computer, appended with a unique identifier. If a device is
not able to generate a unique instance, it should use the instance of
"default", however in such circumstances, the vendor should make it clear to
the user that only a single instance of their device should be configured at
any one time, to prevent conflicts from occurring as a result of multiple
devices sharing the instance name "default". Note that there is no limit on
the length of time a device should wait to be configured.

### Retrieving device configuration capabilities

When an xPL Configuration Manager detects a config heartbeat message from a
device that is awaiting configuration, it will then attempt to query the
device to determine what items of information can be configured. The following
is a typical capabilities request message from the Configuration Manager xpl-
xplhal.myhouse to the recently installed lamp module acme-lamp.default:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-lamp.default
}
config.list
{
command=request
}
```

The config.list schema is used for listing the configurable items supported by
a device, and the command=request element instructs the device that it should
respond with a list of all supported configurable items. Note that a device
can receive a request for configuration information at any time, not just
during the configuration process.

### Response to request for configuration capabilities

When a device receives a targetted command message requesting a list of
configuration items, such as that described in the previous example, it should
respond with a config.list message, as follows:

```
xpl-stat
{
hop=1
source=acme-lamp.default
target=*
}
config.list
{
reconf=newconf
option=interval
option=group[16]
option=filter[16]
}  
```

The name of the element indicates the type of configuration item, and the
value of the element specifies the name of the configuration item. Three types
of elements can be present in the body of a config.list message: config
elements specify items that are mandatory for the device to function, and that
cannot be changed once a device is running. reconf elements specify items
which are mandatory for the device to operate, but who's value can be changed
at any time while the device is operating. option elements specify items that
are not required for device operation, e.g. those items where the device has a
suitable default value that can be used if the item is not specified. For
example, in the above message option=interval indicates that the interval
setting is optional, and will be defaulted (usually to a value of 5 minutes)
if it is omitted.

The newconf item is used to specify the instance of the device, and is
mandatory for all devices wishing to support remote configuration. The
interval, group and filter items are optional, and vendors are free to add
device-specific configuration items if they wish. The presence of a decimal
numeric value in square brackets, immediately after the name of the
configuration item, indicates that the device supports multiple values for
that particular item. In the case of the acme-lamp.default device shown above,
the numbers indicate that it supports a maximum of 16 groups and 16 filters
(see the section later in this document on groups and filters).

### Constructing a Configuration Response Message

Having obtained the configuration information required to configure the
device, the Configuration Manager must now construct a message of type
config.response containing all the values that are required to configure the
device. Usually this will be through the end-user specifying details via a
user-interface provided by the Configuration Manager.

The following is a typical config.response message sent by the Configuration
Manager:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-lamp.default
}
config.response
{
newconf=lounge
interval=30
group=
filter=
} 
```

In the above example, the newconf and interval items have been specified. As
the device now has a valid instance, it can now complete it's configuration
process.

### Completing the Configuration Process

Once a device receives a config.response message from the Configuration
Manager, it can begin functioning normally, and begin sending out normal
heartbeat messages rather than config heartbeats. A device should send a
heartbeat message as soon as it receives the config.response message so that
it's new instance is immediately recognised by the xPL network (se NOTE below)
A device can receive a config.response message at any time after it's initial
configuration, and should be able to reconfigure any of the configuration
items that were specified as type option or type reconf in the config.list
message.

NOTE: If a devices identification (vendorID, deviceID or instanceID) is going
to change (as a result of a received configuration message or due to some
internally driven event), the device should first send a hbeat.end or
config.end (as appropriate) heartbeat using the original
vendor/device/instance ID (before the change). Once that is sent, it should
send a heartbeat (config.app or hbeat.app) using the new device
identification. Doing this will allow other xPL devices that are tracking your
device to quickly cleanup leftovers from the previous device definition (hubs
and other device tracking services).

This is not absolutely necessary as if you just start sending heartbeats with
the new name, the devices tracking the old name will eventually time the old
name out when they no longer hear heartbeats, but it is highly recommended.

### Retrieving Current Configuration Values

The Configuration Manager may wish to query a device to determine the values
currently assigned to it's configurable items. It does this by issuing a
message of type config.current as shown in the following example:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-lamp.lounge
}
config.current
{
command=request
} 
```

The device responds to the request by issuing a message containing the current
values for all configurable items:

```
xpl-stat
{
hop=1
source=acme-lamp.lounge
target=*
}
config.current
{
newconf=lounge
interval=5
group=
filter=
}
```

### Retaining Configuration Information

If possible, a device should retain it's configuration information during
power cycles, to prevent the configuration process from being initiated upon
each device power-up. If a device cannot retain it's configuration state, and
is not able to generate a unique default instance, then only a single instance
of that device should be present on the xPL network.

### Device Groups

When a device or application is configured, the management station may wish to
configure the device to be logically grouped with other units. xPL provides a
powerful mechanism for this to happen in the form of the special "group="
configuration tag. Simple devices may not support group membership, whereas PC
applications with large processing power may potentially have many group
memberships. Group support is not mandatory, however, it is advisable for
developers to support at least one group.

The presence of a "group=" tag within a configuration message clears the
existing group memberships, therefore when the group memberships of a device
are to be changed, all desired groups must be specified within the config
message.

Example One Using a Group to Control Multiple Devices config message:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=acme-curtain.default
}
config.response
{
newconf=lounge_front
interval=2
group=xpl-group.loungedrapes
group=xpl-group.alldrapes
}
```

The automator has installed two curtain controllers in his lounge, and wishes
to configure them as a group, to be opened and closed together. This
configuration message assigns the friendly name, 'lounge_front' to the newly
installed device, and tells it that it is now part of both the 'loungedrapes'
and 'alldrapes' groups.

command message:

```
xpl-cmnd
{
hop=1
source=xpl-xplhal.myhouse
target=xpl-group.loungedrapes
}
drapes.extended
{
curtain=open
dawndusk=disabled
}
```

Each of the curtain controllers in the lounge recognises that this message is
targetted at the reserved xpl-group Vendor + Device ID. This device supports
groups, therefore it reads the group name 'loungedrapes', determines that it
belongs to this group, and therefore executes the command.

## Installer and User Defined Filters

Message Filters may also be specified via a configuration message. Filters
provide an incredibly powerful method of addressing devices and applications.
Filters are only applied to incoming broadcast messages, and are intended to
reduce the number of messages that a device will act upon. If multiple filters
are present, only one of the filters needs to match for the message to be
processed. As with groups, support for user defined filters is not mandatory,
especially on smaller devices, however, incorporating this support is
advisable. Even with very simple applications, intelligent filtering provides
the most flexible way to provide for complex environments.

Filters are defined using the filter= tag, within a configuration message,
according to the following syntax:

```
filter = [msgtype].[vendor].[device].[instance].[class].[type]
```

Elements must be present, but may be wildcarded by the use of a "*" character.
The address elements (vendor, device and instance) refer to the source address
of a message arriving.

As with groups, the presence of a filter= tag within a configuration message
clears the existing filters which the application may be using, therefore when
the filters are to be changed, all desired filters must be specified within
the config message.

xpl-cmnd.wmute.k400.bedroom.drapes.basic This filter is a complete match. This
is the filter used by a device which will react to a specific command message
only. In practice, a developer using the .basic type of a given message class
would use the following filter string instead:

xpl-cmnd.wmute.k400.bedroom.drapes.* By wildcarding the type of the message,
the developer allows the application to respond to messages with any message
using the drapes class. This can be done, because any message of class drapes
will inherit all of the elements of the drapes.basic type.

xpl-cmnd.wmute.k400.bedroom.*.* Wildcarding both class and type allows the
application to process messages of any schema.

xpl-cmnd.wmute.k400.*.drapes.basic In this case, drapes.basic messages from
all instances of a given application or device will be acted upon.

## Using xPL Message Schemas

Several schemas have been defined for use with common devices within the xPL
network. The following examples indicate how these message schemas may be used
and extended.

Any extension to the .basic type of a given class which will be released to
the general public should first be sent to the xpl team, who can provide
guidance on interoperability with existing extensions. Similarly, any new
class of message should be registered, preventing the duplication of effort.

### Example One A Curtain Controller

```
using drapes.basic (example schema)
xpl-stat
{
hop=1
source=acme-curtain.livingroom
target=*
}
drapes.basic
{
curtain=[open/closed]
dawndusk=DISABLED
}
```

In this case, the developer (ACME) has opted to continue using the .basic type
of drapes message, which simply specified that the curtain status "open" or
"closed" should be returned. Additional status info is added as required to
the bottom of the xPL Message. This additional information may or may not be
present in an xPL message, using the drapes.basic schema. A similar controller
from XYZCORP whilst producing similar information, may use a different format,
different names for the same info, or a different order.

```
using drapes.extended 
xpl-stat
{
hop=1
source=acme-curtain.livingroom
target=*
}
drapes.extended
{
curtain=[open/closed]
dawndusk=[enabled/disabled]
lightlvl=75
} 
```

Acme gets together with the other curtain controller manufacturer, XYZCORP and
agrees that all of their controllers should report the same information, so
they decide to define a new drapes type: .extended Any device wishing to use
the new .extended status message type must provide curtain= and dawndusk=
information. ACME are, of course, still free to provide yet more additional
status info, added to the bottom of the Message

In programming terms, drapes.extended inherits the properties of drapes.basic
on which it is based.

### Example Two A PC Media Application

```
using audio.basic: 
xpl-stat
{
hop=1
source=acme-mplayer.myhomepc
target=*
}
audio.basic
{
status=[PLAYING | STOPPED | PAUSED]
}
```

ACME have developed a Media Player interface which they hope will allow any
xPL device on the network which can send messages to the PC to control the
playback of music throughout the house. They begin by using the template
.basic message type for status updates. A display device can be configured to
show what track is playing, however, all extra information is using ACME's own
format and layout. If a new streaming music application by XYZCorp was
introduced onto the network, the display would not be able to show this
additional playback information as it would be using a different format.

```
using an extenstion of audio.basic, audio.winamp:

xpl-stat
{
hop=1
source=acme-winamp.myhomepc
target=*
}
audio.winamp
{
status=[PLAYING | STOPPED | PAUSED]
current=[current track name up to 32 chars]
next=[next track name, up to 32 chars]
ttotal=[hh:mm:ss]
tremain=[hh:mm:ss]
}
```

Acme again gets together with XYZCorp and together they agree to define a
common extension to the audio.basic type, in this case 'audio.winamp'. All
devices or applications supporting this extended message schema can use xPL
messages with confidence that the expected information will be present, in the
correct order.

In the meantime, any device which supports the audio.basic type will be able
to display the current information, as this is present in the new, extended
type.

Note: In defining a new type, the developer is mandating that all devices
using this type must supply the specified data values as a minimum.

## Transmission Media-specific Guidelines

The xPL protocol has been engineered to operate over a variety of transmission
media, including Ethernet, RS232 and RS485. This section explains the various
methods of encapsulation used to transmit an xPL message over various types of
transmission media.

### xPL-over-Ethernet

When xPL devices are connected to an Ethernet network, messages are
transmitted using the TCP/IP protocol, in the form of UDP packets. Messages
should be sent on UDP port 3865, which has been allocated to the xPL protocol
by the Internet Assigned Numbers Authority (IANA).

#### xPL Hubs

Because the TCP/IP protocol permits only a single application to listen to a
specific port at any one time, a "hub" mechanism is employed to allow multiple
xPL-enabled applications to function on the same host: A hub application
begins listening on the allocated xPL port of 3865. Applications that wish to
listen to xPL traffic should bind to a dynamic UDP port (i.e. a port number
between 49152 and 65535) and begin sending out heartbeat messages specifying
the port number they are listening on through use of the port= element in
their hbeat.app or config.app messages. The hub receives these messages and
adds the application to it's list of clients. When incoming xPL messages are
received on the xPL port, the hub will then forward them on to all registered
clients. When xPL programs send messages, they broadcast them directly on to
the network, the hub plays no role in sending whatsoever. xPL messages
transmitted over Ethernet support a maximum size of 1,500 bytes.

Detailed information about xPL hubs is available at the xPL hubs
specification page
