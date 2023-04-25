M<>M Relay, at its core relays messages in this fashion:

`Meshnet <-> Matrix <-> Meshnet(s)`

Through a plugins system, we are able to extend M<>M Relay's functionality.  The plugin framework is still in development, but we plan to support checking a Mesh's health status via a `!health` command, a `!seen` command that checks the database and reports when we last saw a certain user or users from a meshnet, and much more to come.

We invite everyone to contribute their own plugins too.