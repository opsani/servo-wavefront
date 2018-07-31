# servo-wavefront
Optune servo driver for Wavefront

Note: this driver requires `measure.py` base class from the Optune servo core. It can be copied or symlinked here as part of packaging

The driver presently returns a single value for a metric named `perf`.  This value is computed from the time series  returned from a single query as follows:

* time aggregation:  aggregates the time series to produce a single value using one of these methods:  avg, max, min, sum

The driver does not presently support multiple queries (e.g., so that a performance metric may be created by a formula using multiple query results).

# driver configuration

The following parameters can be configured for the driver. The configuration should be in a file named `config.yaml` in the directory as the driver.

* `warmup`:  period after adjustment when a measurement is not taken (sleep). Default 0 seconds. Can be overwritten by OCO backend
* `duration`:  period of measurement.  Default 120 seconds. Can be overwritten by OCO backend.
* `metric_name`: metric name. Default `perf`.
* `metric_unit`: metric unit, returned by the describe command. Default '' (empty string).
* `query`: a Wavefront metric time series query. Required.
* `api_host`: a Wavefront API base url, i.e. `https://my-company.wavefront.com`. Required.
* `api_key`: authentication token to be used when querying Wavefront. Required.
* `granularity`: Wavefront query granularity. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
* `summarization`: Wavefront query summarization. Required. See [Wavefront docs](https://github.com/wavefrontHQ/python-client/blob/master/docs/QueryApi.md#query_api)
* `time_aggr`:  aggregation method for time series pointlists.  One of avg|max|min|sum.  Default `avg`.

Example `config.yaml`:

```
wavefront:
  warmup:    30   # Can be overwritted by OCO backend
  duration:  130  # Can be overwritted by OCO backend
  metric_name: perf
  metric_unit: 'request/s'
  granularity: 'm'
  summarization: 'LAST'
  api_host: "https://my-company.wavefront.com"
  api_key: "changeme"
  query:  'avg(ts(appdynamics.apm.overall.calls_per_min,  env=foo and app=my-app))'
  time_aggr:  avg       # compute time-series value as the average
```

