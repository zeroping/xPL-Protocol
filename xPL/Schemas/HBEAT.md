## HBEAT Message Specification

This schema provides the description of heartbeat message send by xPL devices on the xPL network. This schema is part of the core [xPL Protocol](XPL_Specification_Document).

## XPL-STAT Structure

If a device is configurable, but has not yet been configured, it should send heartbeats according to the [config schema](Schema_-_CONFIG).

### HBEAT.BASIC

The _hbeat.basic_ heartbeat is for use by small devices, such as PIC systems. PC based applications must use the _hbeat.app_ schema, because of hub requirements.

```
hbeat.basic
{
interval=[interval in minutes]
[version=[version info]]
(...additional info defined by the developer)
}
```

Each device is required to send a _hbeat.basic_ or _hbeat.app_ at a regular interval. Version info is preferably formatted as 1 to 4 integers separated by a dot ('.') (eg. _version=0.4_ or _version=1.2.0.1234_).

### HBEAT.APP

The _hbeat.app_ heartbeat is for use by PC based applications, using ethernet. They must use this schema, because it contains the necessary information for the hub to communicate with the xPL application. The schema is identical to the _hbeat.basic_ version except for the addition of keys _remote-ip_ and _port_.

```
hbeat.app
{
interval=[interval in minutes]
port=[listening port]
remote-ip=[local IP address]
[version=[version info]]
(...additional info defined by the developer)
}
```

Each device is required to send a _hbeat.basic_ or _hbeat.app_ at a regular interval. PC based applications should start sending _hbeat.app_ messages approximately every 3 to 10 seconds, until they receive their own echo (which means that the hub on the PC the application is running on is working), then they should revert to regular intervals. If the PC based application has not received an echo within 2 minutes, then it should revert to sending a heartbeat every 30 seconds until it gets its own echo.

Version info is preferably formatted as 1 to 4 integers separated by a dot ('.') (eg. _version=0.4_ or _version=1.2.0.1234_).

### HBEAT.END

The _hbeat.end_ heartbeat will notify other xPL devices that the device is no longer available. It allows devices that track other devices to clean up.

```
hbeat.end
{
(...additional info defined by the developer)
}
```

_hbeat.end_ should only be sent if the heartbeat messages sent so far were _hbeat.basic_ or _hbeat.app_. If they were _config.basic_ or _config.app_ then _config.end_ should be sent instead. It is not required for devices to sent the _hbeat.end_.

## XPL-CMND Structure

### HBEAT.REQUEST

```
hbeat.request
{
command=request
}
```

The target device(s) will respond with a _hbeat.app_ or _hbeat.basic_ response. It is not required for devices to support this request.

## XPL-TRIG Structure

Not applicable for heartbeats.

## Standard Schema Notes

The heartbeat interval depends on the state of a device. Regular heratbeat intervals are anywhere between 5 and 9 minutes.

