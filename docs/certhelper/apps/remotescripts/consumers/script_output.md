# ScriptOutputConsumer

A `WebsocketConsumer` child class, which represents the code run when
a client connects to the `ws/remotescripts/(?P<script_id>\d+)/$` URL.


## Attributes

### `group_name`

This represents the name of the `channel_layer` that will be created
and it's always formed based on the `script_id` part of the WS URL
(i.e. `f"output_{self.script_id}"`).

This, in effect, means that for each `remotescript` configuration created,
a separate `channel_layer` will be created (e.g. `output_1` for remotescript
with id = 1).

## Methods

### `connect`

This is run in the backend when a front-end WS client connects.

The `script_id` is stored (using the kwargs in the `url_route`) so 
that the appropriate `group_name` can be created.


### `script_output`

When this method is called, an `event` dictionary is passed to it.
This contains all the information that the caller wants to push to
the Websocket client connected to a specific channel group.

This method is run **indirectly** from the `ScriptExecutionBaseView` (`views.py`)
via the `channel_layer.group_send` function call. This is done by specifying the
`type` parameter to be `script.output` (see
[channels' documentation](https://channels.readthedocs.io/en/latest/topics/channel_layers.html#what-to-send-over-the-channel-layer)).
