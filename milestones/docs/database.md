# Database Layout

## Graph Databases

The Arango database stores a representation of the aggregate state of the IoT network over a two-week time period. Data is ingested from the upstream Amazon S3 bucket protocol buffer files and stored in ArangoDB as a collection of nodes (hotspots) and edges (relationships). Data stored in this way is referred to as a graph—ArangoDB is a graph database. 

The key property of a graph database is that queries are structured differently than in a relational database, such as one based on SQL. Information is not stored in tables but instead as a collection of edge attributes and a set of nodes. This has a key benefit: most of the information about the IoT network is contained in the relationships between hotspots, rather than in a table of beacons and witnesses, which would be represented as a function of time in a relational database. 

Because the ETL is graph-based, queries such as "find the set of hotspots that witnessed this beacon" are simpler and less computationally expensive than their relational equivalents, which would typically require multiple table joins across millions of rows. 

The drawback to this approach is that there is only *one* edge relating pairs of hotspots, so the properties of received beacons are stored as averages and statistical distributions over the two-week aggregation period, rather than as a table of specific records for each beacon as a function of time. This tradeoff makes storage requirements and query times practical for the Arango ETL.

The Arango database stores two primary collections: `hotspots` and `witnesses`. These are described in detail in the next section.

## Hotspot Node Collection

The hotspot collection consists of LoRaWAN gateways on the Helium IoT network. Each hotspot has the following attributes:

* `_id`: A unique key identifying a particular hotspot, consisting of the base64 address and prepended with `hotspots/`. Example:  
  `hotspots/1111Nxuhy6Uaum9MNiQ2pNAhs21sU4nSbAHjgAE1dxqe28B98r`  

* `_key`: A redundant, unique key identifying a particular hotspot, consisting of the same base64 hash as above. Example:  
  `1111Nxuhy6Uaum9MNiQ2pNAhs21sU4nSbAHjgAE1dxqe28B98r`  

* `_geo_loc`: A set of two floating-point numbers representing the latitude and longitude of the last asserted hotspot position. Example:  
  `[44.73208321213389, 41.71068957880505]`  

* `_name`: An automatically generated, human-readable string representation of the hotspot `_key`, which may not be unique. Example:  
  `attractive-green-dog`

* `_assertions`: A set of assertions for the hotspot during the two-week ingest period, representing the physical location and antenna properties set by the hotspot owner. Each assertion consists of:
  * `created_at`: A UTC timestamp for when the assertion was made.
  * `beacon_count`: The integer number of beacons transmitted by the given hotspot while asserted at the specified location.
  * `elevation`: The antenna elevation above ground in meters.
  * `gain`: The antenna gain in dB x 10. Example: `58` (5.8 dB).  
  * `geo_loc`: The asserted latitude and longitude of the hotspot.
  * `location`: An integer H3 index at resolution 12. See: <https://h3geo.org/>.
  * `nonce`: A nonce for the assertion transaction, used internally by the blockchain oracles. 

It is common for a hotspot to have multiple assertions, but a hotspot must have at least one to exist on the IoT network, participate in PoC, and be reflected in the Arango database.

## Witness Edge Collection

The witness collection consists of edges (relationships) between hotspots on the IoT network. For example, if two hotspots `A` and `B` exist, the edge `A -> B` contains statistics about beacons transmitted by `A` and witnessed by `B`. Each edge has the following attributes:

* `_id`: A unique key identifying a pair of hotspots, consisting of each `_id` separated by an underscore and prepended by `witnesses/`. Example:  
  `witnesses/111E1nCn6zUy3g3mpjohtw5vAyvW8KuYa6qm4VnbJnQ6eJwTQ7A_111Avj5jphedXsjPUw4p2VmbbRGtJtFaPP93oPxNiZ882KTQZjV`  

* `_key`: A redundant, unique key identifying a pair of hotspots. Example:  
  `111E1nCn6zUy3g3mpjohtw5vAyvW8KuYa6qm4VnbJnQ6eJwTQ7A_111Avj5jphedXsjPUw4p2VmbbRGtJtFaPP93oPxNiZ882KTQZjV`  

* `_from`: The `_id` of the transmitting hotspot for the given edge. Example:  
  `hotspots/111E1nCn6zUy3g3mpjohtw5vAyvW8KuYa6qm4VnbJnQ6eJwTQ7A`  

* `_to`: The `_id` of the receiving hotspot for the given edge. Example:  
  `hotspots/111Avj5jphedXsjPUw4p2VmbbRGtJtFaPP93oPxNiZ882KTQZjV`  

* `beaconer_tx_power`: The transmit power of the beaconing hotspot in dBm. Example: `12`

* `count`: The integer count of beacons transmitted by `_from` and received by `_to`.

* `day_hist`: A histogram of beacon counts per day. Example: `{"1": 10, "31": 2}`.

* `distance`: The distance in kilometers between the most recently asserted positions of `_from` and `_to`. Example: `12.8`.

* `hour_hist`: A histogram of beacon counts per hour. Example: `{"5": 3,"7": 1,"11": 2,"14": 1,"17": 2}`.

* `ingest_latency_hist`: A histogram of ingest latency in milliseconds. Example: `{"3413": 1,"3415": 2,"3416": 2}`.

* `loss`: The expected signal loss in dB for LoRa packets based on the ITM/Longley-Rice model. Example: `-148.12`.

* `noise_floor_hist`: A histogram of the estimated local noise floor in dB x 10. Example: `{"-1138": 1,"-1158": 4,"-1168": 1,"-1170": 2}`.

* `signal_hist`: A histogram of RSSI values in dBm x 10. Example: `{"-1330": 1,"-1360": 2, "-1370": 4}`.

* `snr_hist`: A histogram of SNR values in dB x 10. Example: `{"-175": 2,"-185": 1,"-188": 2}`.

* `terrain_intersection`: Line-of-sight intersection across terrain in meters × kilometers. Example: `0.0`.

* `witness_elevation`: The latest asserted antenna height in meters. Example: `6`.

* `witness_gain`: The latest asserted antenna gain in dB x 10. Example: `54`.

* `witness_geo_loc`: The latest asserted geographic location. Example: `[21.416176706204286, 43.872956915688164]`.

* `tx_jaccard` and `rx_jaccard`: Similarity scores for receiver and transmitter witnesses. Refer to the distance insensitivity classifier for how this is calculated and used. 

