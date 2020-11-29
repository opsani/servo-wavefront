# servo-metrics

![Run Tests](https://github.com/opsani/servo-metrics/workflows/Run%20Tests/badge.svg)
[![license](https://img.shields.io/github/license/opsani/servo-metrics.svg)](https://github.com/opsani/servo-metrics/blob/master/LICENSE)
[![PyPI](https://img.shields.io/pypi/v/servo-metrics.svg)](https://pypi.org/project/servo-metrics/)
[![release](https://img.shields.io/github/release/opsani/servo-metrics.svg)](https://github.com/opsani/servo-metrics/releases/latest)
[![GitHub release date](https://img.shields.io/github/release-date/opsani/servo-metrics.svg)](https://github.com/opsani/servo-metrics/releases)

An Opsani [Servo](https://github.com/opsani/servox) connector that tracks
metrics about the servo and exposes them in Prometheus scrapeable format.

This connector is built on top of [aioprometheus](https://github.com/claws/aioprometheus).

The connector is currently of beta quality.

Out of the box, the connector tracks basic servo metrics such as a counter of
measurement and adjustment operations.

## Configuration

By default, the connector is configured at the `metrics` key.

* `address`: The address to bind to (default: `"127.0.0.1"`).
* `port`: The port to expose the metrics on (default: `9981`).

## Registering Metrics

TODO: Define a set of events that enable metrics to be defined by arbitrary
connectors. See https://aioprometheus.readthedocs.io/en/latest/user/index.html

What we want to do here is provide an abstract interface that enables connectors
to declare metrics and utilize a fire & forget mechanism to track them.

This probably means modeling generic type for Counter, Guage, and Histogram in
the `servo.types` module.

Such an interface will allow us to plug in additional backends and avoid tight
coupling in the servox core.

## Multiservo Handling

TODO: Metrics are really an Assembly rather than a Servo level concern but we
don't have support for that just yet.

## Testing

Automated tests are implemented via [Pytest](https://docs.pytest.org/en/stable/): `pytest .`

## License

servo-webhooks is distributed under the terms of the Apache 2.0 Open Source license.

A copy of the license is provided in the [LICENSE](LICENSE) file at the root of the repository.
