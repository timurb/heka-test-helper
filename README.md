Helper for testing Lua sandboxes
================================

This is a helper to do unit testing of Heka lua sandboxes: encoders and decoders.

It is in Proof-Of-Concept stage: use at own risk.

Contributions are welcome

Usage
-----

Install busted:
```
luarocks install busted
```

Create `spec/` subdir in the dir with your lua modules:
```
---+--- spec/ -+- heka_mock.lua
   |           -- my_encoder_spec.lua
   ---- my_encoder.lua
```

Run tests:
```
$ busted
●
1 success / 0 failures / 0 errors / 0 pending : 0.004586 seconds
```

API
---

(See also [Heka docs](http://hekad.readthedocs.org/en/v0.10.0/sandbox/index.html) for real behaviour of the functions in heka)


#### add_to_payload(val)

Add val to payload.


#### inject_payload(payload_type, payload_name, ...)

Inject payload to injection list and clear current payload.


#### inject_message(msg)

Converts `msg` to string and injects payload.


#### decode_message()

Processes message returned by `read_message("raw")` to table.

**NOTE:** This only works in this scenario and does not works with protobuf structures and other native Heka formats.


#### injected()
(NOT IMPLEMENTED IN HEKA UPSTREAM)

All injected messages since last `reset_payload()` merged into a single string.


#### reset_payload()
(NOT IMPLEMENTED IN HEKA UPSTREAM)

Reset payload and injection list. Run it before every test.


#### write_message(),

Produces error.
These function provided by Heka are not implemented as I don't quite understand what is their use case in lua code. Probably you should use Heka-provided test suite to test again these.


### Mocks

#### mock_read_message(hash)

Set values for `read_message()` called by encoder.
As in upstream `read_message("raw")` should return table with original unprocessed message for further handling with `decode_message()`.

**Note:** no checking is done for non-standard Heka fields -- no errors are produced and they are not dropped while Heka will do either of these

**Note:** array values (`read_message("Fields[foo]", 1, 2)` -- 2+ params) are not supported as they are not widely used. Probably they'll be supported in a later release of this library.


#### mock_read_config(hash)

Set values for `read_config()` called by encoder.
If no paramater passed only the default config params will be set (see `default_config()`).


#### set_default_config(hash)

If you need to set some config values shared by all tests define them here.
Note: you still need to run `mock_read_config()` to set them.
