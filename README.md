# piko-monitoring

This repository contains code to run InfluxDB and Telegraf to collect metrics from a [Kostal PIKO BA](https://www.kostal-solar-electric.com/de-de/download/-/media/document-library-folder---kse/2020/12/15/12/32/db_piko-ba-system_web_en_20171001.pdf) inverter.

## Getting started

To start InfluxDB and Telegraf run ``docker compose up -d``. The UI can then be accessed at ``http://localhost:8086``.

## Configuration

InfluxDB is configured to store all it's data in the volume ``$PWD/.influxdb``.

Create an environment file ``.env`` to configure InfluxDB and Telegraf. It must contain the following variables.

`````
INFLUX_USERNAME=user
INFLUX_PASSWORD=password
INFLUX_ORG=my-org
INFLUX_BUCKET=my-bucket
INFLUX_TOKEN=my-secret
`````

For documentation see

* Telegraf https://github.com/influxdata/telegraf/blob/master/docs/CONFIGURATION.md
* HTTP input https://github.com/influxdata/telegraf/tree/master/plugins/inputs/http
* JSON parser https://github.com/influxdata/telegraf/tree/master/plugins/parsers/json
* Enum processor https://github.com/influxdata/telegraf/blob/release-1.17/plugins/processors/enum

## Metrics provided by the Piko BA inverter

Metrics are collected via HTTP

`````bash
curl --location --request GET 'http://piko-solar/api/dxs.json?dxsEntries=83886336&dxsEntries=83886592&dxsEntries=83886848'
`````

which returns a JSON

````json
{
  "dxsEntries": [
    {
      "dxsId": 83886336,
      "value": 821.729736
    },
    {
      "dxsId": 83886592,
      "value": 0.000000
    },
    {
      "dxsId": 83886848,
      "value": 102.137512
    }
  ],
  "session": {
    "sessionId": 0,
    "roleId": 0
  },
  "status": {
    "code": 0
  }
}
````

### Power values

| Description        | Parameter           | Unit  |
| ------------------ | ------------------- | ----- |
| Total DC input     | 33556736            | W     |
| Output power       | 67109120            | W     |
| Operating status   | 16780032            | int   |

#### Operating status

```
0 = Aus
1 = Leerlauf
2 = Anfahren
3 = Einspeisen MPP
4 = abgeregelt
5 = Einspeisen
```

### Current values

#### PV Generator

| Description        | Parameter           | Unit  |
| ------------------ | ------------------- | ----- |
| DC input 1 Voltage | 33555202            | V     |
| DC input 1 Current | 33555201            | A     |
| DC input 1 Power   | 33555203            | W     |
| DC input 2 Voltage | 33555458            | V     |
| DC input 2 Current | 33555457            | A     |
| DC input 2 Power   | 33555459            | W     |

#### Battery

| Description        | Parameter           | Unit  |
| ------------------ | ------------------- | ----- |
| Voltage            | 33556226            | V     |
| Charging status    | 33556229            | %     |
| Charging           | 33556238            | A     |
| Charge cycles      | 33556230            | int   |
| Temperature        | 33556227            | °C    |

### House

#### Home consumption covered by

| Description        | Parameter           | Unit  |
| ------------------ | ------------------- | ----- |
| Solar generator    | 83886336            | W     |
| Battery            | 83886592            | W     |
| Grid               | 83886848            | W     |

#### Phase selective home consumption

| Description        | Parameter           | Unit  |
| ------------------ | ------------------- | ----- |
| Phase 1            | 83887106            | W     |
| Phase 2            | 83887362            | W     |
| Phase 3            | 83887618            | W     |
