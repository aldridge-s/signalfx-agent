<!--- GENERATED BY gomplate from scripts/docs/top-config.md.tmpl --->
# Agent Configuration

The agent is configured primarily by a YAML document conventionally located at
`/etc/signalfx/agent.yaml`.  The location of the config file can be specified
by the `-config` flag to the agent binary (`signalfx-agent`).

## Config Schema

  
| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `signalFxAccessToken` | **yes** | string | The access token for the org that should receive the metrics emitted by the agent. |
| `ingestUrl` | no | string | The URL of SignalFx ingest server.  Can be overridden if using the Metric Proxy. (**default:** `"https://ingest.signalfx.com"`) |
| `apiUrl` | no | string | The SignalFx API base URL (**default:** `"https://api.signalfx.com"`) |
| `hostname` | no | string | The hostname that will be reported as the `host` dimension. If blank, this will be auto-determined by the agent based on a reverse lookup of the machine's IP address |
| `useFullyQualifiedHost` | no | bool | If true (the default), and the `hostname` option is not set, the hostname will be determined by doing a reverse DNS query on the IP address that is returned by querying for the bare hostname.  This is useful in cases where the hostname reported by the kernel is a short name. |
| `intervalSeconds` | no | integer | How often to send metrics to SignalFx.  Monitors can override this individually. (**default:** `15`) |
| `globalDimensions` | no | map of string | Dimensions that will be added to every datapoint emitted by the agent |
| `sendMachineID` | no | bool | Whether to send the machine-id dimension on all host-specific datapoints generated by the agent.  This dimension is derived from the Linux machine-id value. (**default:** `false`) |
| `observers` | no | [list of object (see below)](#observers) | A list of observers to use (see observer config) |
| `monitors` | no | [list of object (see below)](#monitors) | A list of monitors to use (see monitor config) |
| `writer` | no | [object (see below)](#writer) | Configuration of the datapoint/event writer |
| `logging` | no | [object (see below)](#logging) | Log configuration |
| `collectd` | no | [object (see below)](#collectd) | Configuration of the managed collectd subprocess |
| `metricsToExclude` | no | [list of object (see below)](#metricstoexclude) | A list of metric filters |
| `pythonEnabled` | no | bool | (**NOT FUNCTIONAL**) Whether to enable the Python sub-agent ("neopy") that can directly use DataDog and Collectd Python plugins.  This is not the same as Collectd's Python plugin, which is always enabled. (**default:** `false`) |
| `diagnosticsSocketPath` | no | string | The path where the agent will create its diagnostic output UNIX socket (**default:** `"/var/run/signalfx-agent/diagnostics.sock"`) |
| `internalMetricsSocketPath` | no | string | The path where the agent will create a socket that serves internal metrics (used by the internal-metrics monitor) (**default:** `"/var/run/signalfx-agent/internal-metrics.sock"`) |
| `profiling` | no | bool | Enables Go pprof endpoint on port 6060 that serves profiling data for development (**default:** `false`) |
| `bundleDir` | no | string | Path to the directory holding the agent dependencies.  This will normally be derived automatically. Overrides the envvar SIGNALFX_BUNDLE_DIR if set. |
| `scratch` | no | any | This exists purely to give the user a place to put common yaml values to reference in other parts of the config file. |
| `configSources` | no | [object (see below)](#configsources) | Configuration of remote config stores |


## observers
The **nested** `observers` config object has the following fields:

The following are generic options that apply to all observers.  Each observer type has its own set of additional configuration options, detailed in [Observer Config](./observer-config.md).

| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `type` | no | string | The type of the observer |



## monitors
The **nested** `monitors` config object has the following fields:

The following are generic options that apply to all monitors.  Each monitor type has its own set of additional configuration options, detailed in [Monitor Config](./monitor-config.md).

| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `type` | no | string | The type of the monitor |
| `discoveryRule` | no | string | The rule used to match up this configuration with a discovered endpoint. If blank, the configuration will be run immediately when the agent is started.  If multiple endpoints match this rule, multiple instances of the monitor type will be created with the same configuration (except different host/port). |
| `extraDimensions` | no | map of string | A set of extra dimensions to include on datapoints emitted by the monitor(s) created from this configuration |
| `intervalSeconds` | no | integer | The interval (in seconds) at which to emit datapoints from the monitor(s) created by this configuration.  If not set (or set to 0), the global agent intervalSeconds config option will be used instead. (**default:** `0`) |
| `solo` | no | bool | If one or more configurations have this set to true, only those configurations will be considered -- useful for testing (**default:** `false`) |



## writer
The **nested** `writer` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `datapointBufferCapacity` | no | unsigned integer | These are soft limits and affect how much memory will be initially allocated for datapoints, not the maximum memory allowed. Capacity options get applied at startup and subsequent changes require an agent restart. (**default:** `1000`) |
| `datapointBufferHardMax` | no | integer | A hard limit on the number of buffered datapoints.  If this is hit, new datapoints will be dropped until the buffer has room. (**default:** `100000`) |
| `eventBufferCapacity` | no | unsigned integer | The anaologue of `datapointBufferCapacity` for events (**default:** `1000`) |
| `eventBufferHardMax` | no | integer | A hard limit on the number of buffered events, beyond which all events will be dropped until the buffer length drops below this. (**default:** `10000`) |
| `datapointSendIntervalSeconds` | no | integer | The agent does not send datapoints immediately upon a monitor generating them, but buffers them and sends them in batches.  The lower this number, the less delay for datapoints to appear in SignalFx. (**default:** `1`) |
| `eventSendIntervalSeconds` | no | integer | The analogue of `datapointSendIntervalSeconds` for events (**default:** `1`) |
| `logDatapoints` | no | bool | If the log level is set to `debug` and this is true, all datapoints generated by the agent will be logged. (**default:** `false`) |
| `logEvents` | no | bool | The analogue of `logDatapoints` for events. (**default:** `false`) |



## logging
The **nested** `logging` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `level` | no | string | Valid levels include `debug`, `info`, `warn`, `error`.  Note that `debug` logging may leak sensitive configuration (e.g. passwords) to the agent output. (**default:** `"info"`) |



## collectd
The **nested** `collectd` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `disableCollectd` | no | bool | If you won't be using any collectd monitors, this can be set to true to prevent collectd from pre-initializing (**default:** `false`) |
| `timeout` | no | integer | How many read intervals before abandoning a metric. Doesn't affect much in normal usage. See [Timeout](https://collectd.org/documentation/manpages/collectd.conf.5.shtml#timeout_iterations). (**default:** `40`) |
| `readThreads` | no | integer | Number of threads dedicated to executing read callbacks. See [ReadThreads](https://collectd.org/documentation/manpages/collectd.conf.5.shtml#readthreads_num) (**default:** `5`) |
| `writeThreads` | no | integer | Number of threads dedicated to writing value lists to write callbacks. This should be much less than readThreads because writing is batched in the write_http plugin that writes back to the agent. See [WriteThreads](https://collectd.org/documentation/manpages/collectd.conf.5.shtml#writethreads_num). (**default:** `2`) |
| `writeQueueLimitHigh` | no | integer | The maximum numbers of values in the queue to be written back to the agent from collectd.  Since the values are written to a local socket that the agent exposes, there should be almost no queuing and the default should be more than sufficient. See [WriteQueueLimitHigh](https://collectd.org/documentation/manpages/collectd.conf.5.shtml#writequeuelimithigh_highnum) (**default:** `500000`) |
| `writeQueueLimitLow` | no | integer | The lowest number of values in the collectd queue before which metrics begin being randomly dropped.  See [WriteQueueLimitLow](https://collectd.org/documentation/manpages/collectd.conf.5.shtml#writequeuelimitlow_lownum) (**default:** `400000`) |
| `logLevel` | no | string | Collectd's log level -- info, notice, warning, or err (**default:** `"notice"`) |
| `intervalSeconds` | no | integer | A default read interval for collectd plugins.  If zero or undefined, will default to the global agent interval.  Some collectd python monitors do not support overridding the interval at the monitor level, but this setting will apply to them. (**default:** `0`) |
| `writeServerIPAddr` | no | string | The local IP address of the server that the agent exposes to which collectd will send metrics.  This defaults to an arbitrary address in the localhost subnet, but can be overridden if needed. (**default:** `"127.9.8.7"`) |
| `writeServerPort` | no | integer | The port of the agent's collectd metric sink server.  If set to zero (the default) it will allow the OS to assign it a free port. (**default:** `0`) |
| `configDir` | no | string | This is where the agent will write the collectd config files that it manages.  If you have secrets in those files, consider setting this to a path on a tmpfs mount.  The files in this directory should be considered transient -- there is no value in editing them by hand.  If you want to add your own collectd config, see the collectd/custom monitor. (**default:** `"/var/run/signalfx-agent/collectd"`) |



## metricsToExclude
The **nested** `metricsToExclude` config object has the following fields:

For more information on filtering see [Datapoint Filtering](./filtering.md).


| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `dimensions` | no | map of string | A map of dimension key/values to match against.  All key/values must match a datapoint for it to be matched. |
| `metricNames` | no | list of string | A list of metric names to match against, OR'd together |
| `metricName` | no | string | A single metric name to match against |
| `monitorType` | no | string | Limits this scope of the filter to datapoints from a specific monitor. If specified, any datapoints not from this monitor type will never match against this filter. |
| `negated` | no | bool | Negates the result of the match so that it matches all datapoints that do NOT match the metric name and dimension values given. This does not negate monitorType, if given. (**default:** `false`) |



## configSources
The **nested** `configSources` config object has the following fields:

For more information about how to use config sources, see [Remote Config](./remote-config.md).

| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `watch` | no | bool | Whether to watch config sources for changes.  If this is `true` and any of the config changes (either the main agent.yaml, or remote config values), the agent will dynamically reconfigure itself with minimal disruption.  This is generally better than restarting the agent on config changes since that can result in larger gaps in metric data.  The main disadvantage of watching is slightly greater network and compute resource usage. This option itself ironically enough is not subject to watching and changing it to false after the agent was started with it true will require an agent restart. (**default:** `true`) |
| `file` | no | [object (see below)](#file) | Configuration for other file sources |
| `zookeeper` | no | [object (see below)](#zookeeper) | Configuration for a Zookeeper remote config source |
| `etcd2` | no | [object (see below)](#etcd2) | Configuration for an Etcd 2 remote config source |
| `consul` | no | [object (see below)](#consul) | Configuration for a Consul remote config source |


## file
The **nested** `file` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `pollRateSeconds` | no | integer | How often to poll files (in seconds) to test for changes.  There are so many edge cases that break inotify that it is more robust to simply poll files than rely on that. This option is not subject to watching and changes to it will require an agent restart. (**default:** `5`) |



## zookeeper
The **nested** `zookeeper` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `endpoints` | no | list of string | A list of Zookeeper servers to use for the client |
| `timeoutSeconds` | no | unsigned integer | Client timeout (**default:** `10`) |



## etcd2
The **nested** `etcd2` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `endpoints` | no | list of string | A list of Etcd2 servers to use |
| `username` | no | string | An optional username to use when connecting |
| `password` | no | string | An optional password to use when connecting |



## consul
The **nested** `consul` config object has the following fields:



| Config option | Required | Type | Description |
| --- | --- | --- | --- |
| `endpoint` | no | string | A Consul server URL |
| `username` | no | string | An optional username to use when connecting |
| `password` | no | string | An optional password to use when connecting |
| `token` | no | string | An authentication token, if needed |
| `datacenter` | no | string | The Consul datacenter to use |





## Example YAML

Here is an autogenerated example of a YAML config file, with default values
where applicable:


```yaml
  signalFxAccessToken: 
  ingestUrl: "https://ingest.signalfx.com"
  apiUrl: "https://api.signalfx.com"
  hostname: 
  useFullyQualifiedHost: 
  intervalSeconds: 15
  globalDimensions: 
  sendMachineID: false
  observers: []
  monitors: []
  writer: 
    datapointBufferCapacity: 1000
    datapointBufferHardMax: 100000
    eventBufferCapacity: 1000
    eventBufferHardMax: 10000
    datapointSendIntervalSeconds: 1
    eventSendIntervalSeconds: 1
    logDatapoints: false
    logEvents: false
  logging: 
    level: "info"
  collectd: 
    disableCollectd: false
    timeout: 40
    readThreads: 5
    writeThreads: 2
    writeQueueLimitHigh: 500000
    writeQueueLimitLow: 400000
    logLevel: "notice"
    intervalSeconds: 0
    writeServerIPAddr: "127.9.8.7"
    writeServerPort: 0
    configDir: "/var/run/signalfx-agent/collectd"
  metricsToExclude: []
  pythonEnabled: false
  diagnosticsSocketPath: "/var/run/signalfx-agent/diagnostics.sock"
  internalMetricsSocketPath: "/var/run/signalfx-agent/internal-metrics.sock"
  profiling: false
  bundleDir: 
  scratch: 
  configSources: 
    watch: true
    file: 
      pollRateSeconds: 5
    zookeeper: 
      endpoints: []
      timeoutSeconds: 10
    etcd2: 
      endpoints: []
      username: 
      password: 
    consul: 
      endpoint: 
      username: 
      password: 
      token: 
      datacenter: 

```