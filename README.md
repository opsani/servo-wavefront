# servo-wavefront
Optune servo driver for Wavefront

Note: this driver requires `measure.py` base class from the Optune servo core. It can be copied or symlinked here as part of packaging

The driver returns a single value for each metric specified in the config file.  This value is computed from the time series returned from a single query as follows:

* time aggregation:  aggregates the time series to produce a single value using one of these methods:  avg, max, min, sum

The driver does not presently support multiple queries (e.g., so that a performance metric may be created by a formula using multiple query results).

# driver configuration

The following parameters can be configured for the driver. The configuration should be in a file named `config.yaml` in the directory as the driver.

* `warmup`:  period after adjustment when a measurement is not taken (sleep). Default 0 seconds. Can be overwritten by OCO backend
* `duration`:  period of measurement.  Default 120 seconds. Can be overwritten by OCO backend.
* `api_host`: a Wavefront API base url, i.e. `https://my-company.wavefront.com`. Required.
* `api_key`: authentication token to be used when querying Wavefront. Required.
* `metrics`: a dictionary with metrics specifications, keyed by metric name. Each metrics supports the following parameters:
    * `unit`: metric unit, returned by the describe command. Required.
    * `granularity`: Wavefront query granularity. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
    * `summarization`: Wavefront query summarization. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
    * `query`: a Wavefront metric time series query. Required.
    * `time_aggr`:  aggregation method for time series pointlists. One of avg|max|min|sum.  Default `avg`.

Example `config.yaml`:

```
wavefront:
  warmup:    30   # Can be overwritted by OCO backend
  duration:  130  # Can be overwritted by OCO backend
  api_host: "https://my-company.wavefront.com"
  api_key: "changeme"
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

