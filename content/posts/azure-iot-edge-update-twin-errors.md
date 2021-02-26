+++
Description = "A look at a few obscure errors in Azure IoT Edge"
Tags = [
  "Azure",
  "IoT",
  "Python",
]
Categories = [
  "Azure",
]
title = "Azure IoT Edge Update Twin Errors"
publishdate = "2021-02-26T14:31:56-08:00"
date = "2021-02-26T14:31:56-08:00"
type = "blog"
[image]
    feature = "/images/abstract-3-short.png"
    postheader = "/images/abstract-3-short.png"
+++

I've been working more with Azure IoT Edge Devices recently and with versions of the IoT Hub EdgeAgent and the Python SDKs I noticed a few very non-descriptive  errors when trying to patch device properties with the `patch_twin_reported_properties()` method.

Here's a quick look at them in case anyone else notices these later on.

<!--more-->

I tried to catch and log a bit more about them here:

```python
Exception when patching twin reported properties: ClientError('Unexpected failure') caused by ServiceError('twin operation returned status 400')
Error 400 received from twin operation
response body: b''
PatchTwinReportedPropertiesOperation: completing with error twin operation returned status 400 caused by None
Callback completed with error twin operation returned status 400 caused by None
azure.iot.device.exceptions.ServiceError: twin operation returned status 400 caused by None
Traceback (most recent call last):
  File "/usr/local/lib/python3.7/site-packages/azure/iot/device/iothub/sync_clients.py", line 32, in handle_result
    return callback.wait_for_completion()
  File "/usr/local/lib/python3.7/site-packages/azure/iot/device/common/evented_callback.py", line 70, in wait_for_completion
    raise self.exception
azure.iot.device.exceptions.ServiceError: twin operation returned status 400 caused by None

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "./whereicalledit.py", line 90, in <module>
    main()
  File "./whereicalledit.py", line 70, in main
    update_device_properties(module_client)
  File "./whereicalledit.py", line 57, in update_device_properties
    client.patch_twin_reported_properties(patch_payload)
  File "<string>", line 1, in patch_twin_reported_properties
  File "/usr/local/lib/python3.7/site-packages/azure/iot/device/iothub/sync_clients.py", line 295, in patch_twin_reported_properties
    handle_result(callback)
  File "/usr/local/lib/python3.7/site-packages/azure/iot/device/iothub/sync_clients.py", line 42, in handle_result
    raise exceptions.ClientError(message="Unexpected failure", cause=e)
azure.iot.device.exceptions.ClientError: Unexpected failure caused by twin operation returned status 400 caused by None
```

After extensive searching I couldn't find many related explanations for:

`Unexpected failure caused by twin operation returned status 400 caused by None`

After more debugging I determined there were a few possible causes for these errors:

**Invalid data types**

The Azure IoT Hub Device and Module Twins are finnicky beasts. With strangely limited ability to store some data types depending on the version of the IoT Edge version and Edge Agent version you are using.

Specifically, if you're using Python lists/arrays/tuples or data that contains nested list/arrays/tuples this might be what's causing the issue.

You *might* be able to fix this by upgrading to a more recent version of the Azure IoT Edge Agent or the SDKs but this is only a possibility.

One way to easily coerce your data to be a little nicer (removing tuples at least) is to:

```python
import json

from azure.iot.hub import IoTHubRegistryManager

module_client = IoTHubRegistryManager("CONNECTION_STRING")

payload = {
    "my_key": {
      "my_bad_tuple": ("thing1", "thing2")
    }
}

payload = json.loads(json.dumps(payload))

# print(payload)
# {
#     "my_key": {
#         "my_bad_tuple": ["thing1", "thing2"]
#     }
# }
# The tuple is now a list

module_client.patch_twin_reported_properties(payload)
```

Doing a conversion like this might help you coerce a bunch of bad types to what you need to avoid this issue.

But, you might also have nested arrays/lists that IoT Hub still doesn't like. In that case. Your best bet might just be to leave them as a JSON string and load them back out when required:


```python
import json

from azure.iot.hub import IoTHubRegistryManager

module_client = IoTHubRegistryManager("CONNECTION_STRING")

payload = {
    "my_key": {
      "my_bad_tuple": ("thing1", "thing2")
    }
}

payload["my_key"] = json.dumps(payload["my_key"])
module_client.patch_twin_reported_properties(payload)
```

I hope this helps you if you're stuck in an Azure IoT Hub debugging black hole with Azure IoT Edge! 

Have you seen similar issues? What helped solve them for you?
