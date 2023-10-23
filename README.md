# pontos-data-format

The Pontos Data Format is a specification of the data format conceived within the [Pontos project](https://pontos.ri.se). The Pontos Data Format adheres to the technical requirements imposed by the [Pontos Hub](https://github.com/MO-RISE/pontos-hub) and adds a set of specifications on top in order to produce a data format that is easly adoptable and informative.

The technical requirements imposed by the [Pontos Hub](https://github.com/MO-RISE/pontos-hub) can be summarized as:
* Data ingress and egress on the MQTT bus is separated by two root namespaces (`PONTOS_INGRESS` and `PONTOS_EGRESS`)
* Eventually, a single data sample should fit into the narrow database table layout used by the backing database
* The job to map the data format into the database layout is performed by the [pontos-data-ingestor](https://github.com/MO-RISE/pontos-data-ingestor). It requires configuration according to [here](https://github.com/MO-RISE/pontos-data-ingestor#specifics).
* Which requires the payloads on the MQTT bus to be valid JSON.

## Specification

The Pontos Data Format specifies the following:
* The topic structure on the MQTT bus shall adhere to the following structure
  ```
  {root_namespace}/{vessel_id}/{tag}/{index}
  ```
  where:
  * `root_namespace` is either `PONTOS_INGRESS` (used for data ingress) or `PONTOS_EGRESS` (used for data egress) as imposed by Pontos Hub
  * `vessel_id` is a unique identifier for the vessel and take the format `<type>_<value>`. `<type>` is one of `name`, `imo` or `mmsi` with `<value>`s as vessel name, vessel IMO number or vessel MMSI number respectively.
  * `tag` is one of a finite set of pre-agreed strings encoding information about the data value in the payload. The tags follow the general format: `<entity>_<property>_<unit>` where `entity`, `property` and `unit` are constrained to alphanumeric characters. This repository contains the valid data "tags" [here](./tags.md)
  * `index` is an unsigned integer in the integer interval [1..+∞)
* Payloads are JSON with the following format
  ```
  {
    "timestamp: <seconds since unix epoch [int|float]>,
    "value": <data value [int|float|string]>
  }
  ```

### Example configuration of pontos-data-ingestor
The specification outlined above assumes the following configuration of the `pontos-data-ingestor`:
```yaml
TOPIC_PARSER_FORMAT=PONTOS_INGRESS/{vessel_id:w}/{tag:w}/{index:d}
PAYLOAD_MAP_FORMAT=timestamp=timestamp,value=value
```



This repository adheres to semantic versioning. The list of releases are available at https://github.com/MO-RISE/pontos-data-format/tags


