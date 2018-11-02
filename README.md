# servo-wavefront
Optune servo driver for Wavefront

Note: this driver requires `measure.py` base class from the Optune servo core. It can be copied or symlinked here as part of packaging

The driver returns a single value for each metric specified in the config file. This value can be returned as complete time-series or as a single aggregated value computed from the time series as follows:

* time aggregation:  aggregates the time series to produce a single value using one of these methods:  avg, max, min, sum

The driver does not currently support multiple queries (e.g., so that a performance metric may be created by a formula using multiple query results).

# driver configuration

The following parameters can be configured for the driver. The configuration should be in a file named `config.yaml` in the current directory.

* `warmup`:  period after adjustment when a measurement is not taken (sleep). Default 0 seconds. Can be overwritten by OCO backend
* `duration`:  period of measurement.  Default 120 seconds. Can be overwritten by OCO backend.
* `api_host`: a Wavefront API base url, i.e. `https://my-company.wavefront.com`. Required.
* `api_key`: authentication token to be used when querying Wavefront. Required.
* `past`: additional time skew to add, in seconds. If set, take the time series between (now-past-duration) and (now-past). NOTE the time stamps in the data will not be changed, they will still be as reported by the Wavefront API. Optional, default is 0. Can be overwritten by OCO backend.
* `sleep`: disable sleeping if set to `false`. This is for use in testing only, allows taking time series from past data without waiting. Optional, default=`true`.
* `pre_cmd_async`:  Bash shell command to execute prior to warmup.  This optional command may be a string or a list.  This command is executed asynchronously with stdout/stderr directed to /dev/null.  If the process is still running after measurement, it is terminated.  This command is suitable for generating load during measurement, typically for testing purposes, as in the example above.
* `pre_cmd`:  Bash shell command to execute prior to warmup.  This optional command may be a string or a list.
* `post_cmd`:  Bash shell command to execute after measurement.  This optional command may be a string or a list.
* `metrics`: a dictionary with metrics specifications, keyed by metric name. Each metric supports the following parameters:
    * `unit`: metric unit, returned by the describe command. Required.
    * `granularity`: Wavefront query granularity. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
    * `summarization`: Wavefront query summarization. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
    * `query`: a Wavefront metric time series query. Required.
    * `time_aggr`:  aggregation method for time series pointlists. One of avg|max|min|sum|raw.  Default `avg`. If `raw` is specified, no aggregation is done and the time series is returned in its entirety (use this to allow the OCO backend to perform the time series aggregation and analysis).

Example `config.yaml`:

```
wavefront:
  warmup:    30   # Can be overwritted by OCO backend
  duration:  130  # Can be overwritted by OCO backend
  api_host: "https://my-company.wavefront.com"
  api_key: "changeme"
  pre_cmd_async: 'ab -c 10 -rkl -t 1000 http://c4:8080/' # Optional
  metrics:
    latency:
      unit: ms
      granularity: m
      summarization: LAST
      query:  'avg(ts(appdynamics.apm.overall.avg_resp_time_ms, env=foo and app=my-app))'
      time_aggr:   avg       # compute time-series value as the average
    throughput:
      unit: request/s
      granularity: m
      summarization: LAST
      query:  'avg(ts(appdynamics.apm.overall.calls_per_min,  env=foo and app=my-app))'
      time_aggr:   avg       # compute time-series value as the average

```

