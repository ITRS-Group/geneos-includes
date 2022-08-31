# Gateway Configuration

This page contains include files that are related to the Gateway's own configuration as opposed to the monitored environment.

These are the current collections:

* [`base`](base/) - Baseline configuration, sensible and useful defaults
* [`load`](load/) - Load monitoring using a standalone Gateway unaffected by actual load on the gateways being monitored

## Notes

The examples given on the pages in this section are structured as they are for a number of reasons:

1. It is possible to *Paste XML* in the Includes section but only Include Groups and not Include configurations themselves, hence all examples wrap their Include(s) in one or more groups.
2. Also, the example XML sets the `Required` flag to false and the `Reload Interval` to 5 minutes so that if you choose to use the examples as-is, they will continue to work once initially loaded, even if Internet connectivity stops. Both of these settings can be set to their defaults for local file copies.
