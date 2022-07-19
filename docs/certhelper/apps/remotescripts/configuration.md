# Configuration

Most of the settings were adapted after following the 
[channels tutorial](https://channels.readthedocs.io/en/latest/tutorial/).

Files that play a role in this configuration:

- `dqmhelper/asgi.py`: The `application` variable is edited to include the
`"websocket"` key.
- `remotescripts/routing.py`: The equivalent of `urls.py` for channels. Routes
a URL pattern to a `Consumer` (equivalent to a **view**).
