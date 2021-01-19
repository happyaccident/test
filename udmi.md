ERROR:
undefined internal link to this URL: "#heading=h.t8579d6t47wg".link text: here
?Did you generate a TOC?


ERROR:
undefined internal link to this URL: "#heading=h.hrorfmpvzcf".link text: etag example
?Did you generate a TOC?


WARNING:
Inline drawings not supported: look for ">>>>  GDC alert:  inline drawings..." in output.


ERROR:
undefined internal link to this URL: "#heading=h.t8579d6t47wg".link text: Config set_value example
?Did you generate a TOC?


ERROR:
undefined internal link to this URL: "#heading=h.2cmz67mdpt2b".link text: State applied example
?Did you generate a TOC?


ERROR:
undefined internal link to this URL: "#heading=h.9gias3wosdi1".link text: State failure example
?Did you generate a TOC?


ERROR:
undefined internal link to this URL: "#heading=h.bqedxggnvs81".link text: State updating example
?Did you generate a TOC?

----->


<p style="color: red; font-weight: bold">>>>>  GDC alert:  ERRORs: 6; WARNINGs: 1; ALERTS: 7.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>  GDC alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>
<a href="#gdcalert4">alert4</a>
<a href="#gdcalert5">alert5</a>
<a href="#gdcalert6">alert6</a>
<a href="#gdcalert7">alert7</a>

<p style="color: red; font-weight: bold">>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>


<p style="color: red; font-weight: bold">>>>>  GDC alert: NOTE: You have 8 H1 headings. You may want to use the "H1 -> H2" option to demote all headings by one level.</p>



# UDMI Writeback Spec


# Objective

This document outlines the specifications of an Universal Device Management Interface used for controlling a device. The objective of this document is to:



*   Describe the UDMI specification for cloud to device control.
*   Detail the cloud-side requirements that drive the above specification.
*   Provide detailed JSON examples of the specification.


# Terminology 

**Device state**, sent from device to cloud, defined by [state.json](https://github.com/faucetsdn/udmi/blob/master/schema/state.json). There is one current state per device, which is considered sticky until a new state message is sent. It comprises several subsections (e.g. system or pointset) that describe the relevant sub-state components.

**Device config**, passed from cloud to device, defined by [config.json](https://github.com/faucetsdn/udmi/blob/master/schema/config.json). There is one active config per device, which is considered current until a new config is received.

State **status** is additional detail about the entity at the pointset level.



*   should provide additional human-readable detail about the current state of the point (e.g. explaining invalid, failure, overridden conditions)
    *   [Log-level conventions apply](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry#logseverity), indicating the overall severity of the condition (info, warning, error, etc...)

A **value\_state** indicates how the value is being determined. 

**Updating** boolean, a state-level field, indicates "things are being updated, please wait".



*   If the operation takes a few seconds this should be set immediately on receipt of a valid config.

A **pointset** is the set of points to be reported on in pointset telemetry/state



*   Device points not in pointset config should be ignored.
    *   A device may have 1,000 points but the cloud only cares about 2 points. So the pointset only reports 2 points in **[state](#bookmark=kix.6pardz53ymm2)** instead of 1,000 points.

Within the status block, the **message** field should be a one-line representation of the triggering condition.

The status **detail** field can be multi-line and include more detail, e.g. a complete program stack-trace.

The status **category** field is a device-specific representation of which sub-system the message comes from. In a Java environment, for example, it would be the fully qualified path name of the Class triggering the message.

A status **timestamp** field should be the timestamp the condition was triggered, or most recently updated. It might be different from the top-level message timestamp if the condition is not checked often or is sticky until it's cleared.

The status **level** should conform to the numerical Stackdriver [LogEntry levels](https://cloud.google.com/logging/docs/reference/v2/rest/v2/LogEntry#logseverity). The DEFAULT value of 0 is not allowed (lowest value is 100, maximum 800).


# Cloud Requirements

Several requirements are driving the UDMI specification from the cloud side. These requirements are:



*   The ability for the cloud to specify the value for a point.
*   The ability to determine how a point's value is currently determined. 
*   The ability to determine whether a specific config update is currently applied. 
    *   Additionally, there should be a timeout for how long the system attempts to apply a config update.
*   If a config update fails, the ability to determine the reason for the failure, and to distinguish the failure by these three categories:
    *   device-side failure, e.g. hardware failure
    *   cloud-side failure, e.g. invalid config
    *   retryable later, e.g. higher-priority system is overriding the update


# UDMI Specification

The specification for cloud to device control adds several new fields to UDMI’s existing schemas for state and config.


## Config Fields

Two new fields are added to the config at the pointset level. First, `set_value`, which tells the device what value a given point should be set to. Second, `etag`, is sent to avoid race conditions. For example, say the cloud thinks a device is using unit A for a given point, and sends a value in those units. While the config is in-transit the device changes to unit B. When the config reaches the device, the value is mistranslated as being unit B. To prevent this and similar situations, the config includes a device-generated etag that uniquely represents the point’s state. If the config-sent `etag` doesn’t match the point’s etag at the time of receiving the config, then the device should set an “invalid” `value_state` in the state message (described below).

An example of these fields in use is [here](#config-`set_value`-example).


## State Fields


### State-Level Fields

One field for state is added at the top level, `etag`. As briefly described above, `etag` is a value that uniquely represents the state message. The purpose of this field is to prevent race conditions that arise when the cloud sends a config based on stale state information. If the device receives a new config from the cloud where the `etag` in the config doesn’t match the `etag` in the state, then an invalid `value_state` should be returned for all points that the cloud tried to write to. See the 

<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>  GDC alert: undefined internal link (link text: "etag example"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>

[etag example](#config-set_value-example).


### Point-Level Fields

One new field for state is added at the pointset level, `value_state`. `value_state` is an enum representing the status of the point’s writeback attempt.

Possible states for `value_state`:



*   _ &lt;missing>_ -- The `value_state` field is missing from this point. This means one of two things:
    *   The device is sending telemetry specified by the device as per normal operation. This is the more likely case.
    *   The point’s config is invalid, i.e. does not follow the UDMI spec. E.g. instead of specifying a `set_value`, the config specified `fix`,` `which isn’t a valid field in UDMI.
*   _applied_ -- The cloud value is applied to the device point. The point should now be reporting the value defined in the device’s config.
*   _updating_ -- The system is working to make the device state match the requested config, similar to the reconciling field in go/declarative-friendly-apis.
    *   If the system hasn’t reconciled the config within 5 seconds or before the next telemetry message (whichever comes first), then the system should send a state message with `value_state` set to `updating.`
    *   While the system should never abort trying to reconcile the config, eventually the system should report a failure in the `value_state`.
    *   The time it takes to reach this failure state is left up to the implementation, and should be documented clearly by the manufacturer.
*   _overridden_ -- The device point has been superseded by another system/user with a higher priority. 
*   _invalid _-- The system failed to apply the cloud value to the point because the requested value cannot be applied to the point. This state indicates an error on the cloud side. Some examples:
    *   Point is not writable
    *   Requested value is out of the operating bounds of the point
*   _failure _-- The system failed to apply the cloud value to the point because an error occurred on the device side. This state indicates an error on the device side.

In the case of any of the error states (failure, invalid, overridden), the [status field](https://github.com/faucetsdn/udmi/blob/master/schema/state_pointset.json#L24) for the point should be populated to provide additional debugging information about the error.


## Example Flow Diagram

Example of how a device might determine what `value_state` state to use.



<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>  GDC alert: inline drawings not supported directly from Docs. You may want to copy the inline drawing to a standalone drawing and export by reference. See <a href=http://go/g3doc-drawings>go/g3doc-drawings</a> for details. The img URL below is a placeholder. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>


![drawing](https://docs.google.com/a/google.com/drawings/d/12345/export/png)


## Examples


### Config `set_value` example


```
{
  "version": 1,
  "timestamp": "2018-08-26T21:39:29.364Z",
   "etag": "abc",
  "pointset": {
    "points": {
      "return_air_temperature_setpoint": {
        "set_value": 21.1,  // Write the setpoint to 21.1
      }
    }
  }
}
```



### State “normal telemetry” example


```
"system": {
  // … omitted fields
  "etag": "abc",
},
"pointset": {
  "points": {
    "nexus_sensor": {
       // … no "value_state" field indicates the device is sending telemetry as normal.
    }
  } 
}
```



### State `updating` example


```
"system": {
  // … omitted fields
  "etag": "abc",
},
"pointset": {
  "points": {
    "nexus_sensor": {
      "value_state": "updating",  // indicates the system is reconciling the config for this point
    }
  } 
}
```



### State `applied` example


```
"system": {
  // … omitted fields
  "etag": "abc",
},
"pointset": {
  "points": {
    "nexus_sensor": {
      "value_state": "applied",  // indicates the value is coming from the cloud.
    }
  }
}
```



### State `failure` example


```
"system": {
  // … omitted fields
  "etag": "abc",
},
"pointset": {
  "points": {
    "nexus_sensor": {
      "value_state": "failure",  // indicates the value from the cloud could not be applied.
      "status": {
        "message": "Failure to communicate to device",
        "category": "state.pointset.points.config.failure",
        "timestamp": "2018-08-26T21:39:28.364Z",
        "level": 600
      }
    }
  }
}
```



### `Etag` example

Messages below are sent in chronological order.

State message 1:


```
"system": {
  // … omitted fields
  "updating": false, 
  "etag": "abc", // the starting etag
},
"pointset": {
  "points": {
    "nexus_sensor": {
    }
  }
}
```


Config message 1:


```
{
  "version": 1,
  "timestamp": "2018-08-26T21:39:29.364Z",
  "etag": "abc", // use the etag from the first state message
  "pointset": {
    "points": {
      "nexus_sensor": {
        "set_value": 21.1,  // Write the point to 21.1
      }
    }
  }
}
```


State message 2 (sent by the device before the device receives config message 1):


```
"system": {
  // … omitted fields
  "updating": false, 
  "etag": "xyz", // etag change from "abc" indicates the point's state has changed
},
"pointset": {
  "points": {
    "nexus_sensor": {
    }
  }
}
```


State message 3 (sent by the device after the device receives config message 1):


```
"system": {
  // … omitted fields
  "updating": false, 
  "etag": "xyz",
},
"pointset": {
  "points": {
    "nexus_sensor": {
      "value_state": "invalid",  // invalid because the config etag "abc" didn't match "xyz"
    }
  }
}
```



## Change On Value (COV)

Some devices are configured to send new values only when the value changes (COV). COV and writeback interact in two main cases. First, if a point is written to by the cloud and the value is changed, the device should send an update for that point. This behavior is consistent with COV. The behavior also provides telemetry confirmation that the write succeeded, although this confirmation isn’t strictly necessary, since the device state can confirm that the write is successful. Second, if the point is not changed by the write, the device should not send an update for that point. As mentioned, the cloud can determine that the write was successful from the device state.


# Cloud Behavior

It’s non-obvious how the specification above fulfills the requirements set out at the beginning of this doc. This section makes that fulfillment clear.


<table>
  <tr>
   <td><strong>Requirement</strong>
   </td>
   <td><strong>Cloud Behavior</strong>
   </td>
   <td><strong>Example</strong>
   </td>
  </tr>
  <tr>
   <td>Specify the value for a point
   </td>
   <td>The cloud could specify the value by sending a <strong>device config</strong> message with <code>set_value</code> field.
   </td>
   <td>

<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>  GDC alert: undefined internal link (link text: "Config set_value example"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>

<a href="#heading=h.t8579d6t47wg">Config set_value example</a>
   </td>
  </tr>
  <tr>
   <td>Determine how a point's value is currently determined
   </td>
   <td>The cloud can check <code>value_state</code> in the <strong>device</strong> <strong>state</strong> to see how the point is being determined.
<p>
If the <code>value_state</code> is an error state (e.g. “failure”), the cloud can look at the most recent successful state to know how the point is being determined.
   </td>
   <td>

<p id="gdcalert5" ><span style="color: red; font-weight: bold">>>>>  GDC alert: undefined internal link (link text: "State applied example"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert6">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>

<a href="#heading=h.2cmz67mdpt2b">State applied example</a> \


<p id="gdcalert6" ><span style="color: red; font-weight: bold">>>>>  GDC alert: undefined internal link (link text: "State failure example"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert7">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>

<a href="#heading=h.9gias3wosdi1">State failure example</a> \
… other states omitted, but look similar.
   </td>
  </tr>
  <tr>
   <td>Determine whether a specific config update is applied 
   </td>
   <td>To know whether the most recent config is applied at the pointset level, the cloud can look at whether <code>value_state</code> for the point is in the <code>updating</code> state or a different final state.
<p>
The cloud can also compare device state timestamps with config timestamps to know whether a given config was active at a given time.
   </td>
   <td>

<p id="gdcalert7" ><span style="color: red; font-weight: bold">>>>>  GDC alert: undefined internal link (link text: "State updating example"). Did you generate a TOC? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert8">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>> </span></p>

<a href="#heading=h.bqedxggnvs81">State updating example</a>
   </td>
  </tr>
  <tr>
   <td>Determine details/explanation for failure
   </td>
   <td>The cloud could check the <code>value_state</code> field in the <strong>device state</strong> to determine the error type. More error details are in the <strong>device state</strong>’s <code>status</code> block.
<p>
Roughly, <code>invalid</code> refers to a cloud-side error, <code>failure</code> refers to a device-side error, and <code>overridden</code> refers to something outside of both the device & cloud’s control.
   </td>
   <td>
   </td>
  </tr>
</table>


Additionally, upon receiving a new config, the device should abandon trying to reconcile any previous config and instead reconcile the new config. Once the new config is applied at the point level (i.e. the `value_state` of the point is not `updating`) , any previous configs (pending or not) are invalidated.
