# Installation 

## Requirements

The classification pipeline runs on Ubuntu Linux. The test environment used here is: 

* Ubuntu Server 24.04 LTS
* 64 GB RAM  
* 1 TB SSD storage  
* 6-core / 12-thread CPU (AMD EPYC 4244P - 6c/12t - 3.8 GHz/5.1 GHz)  
* 100 MB/sec symmetric network connection  

This approximately corresponds to an Amazon Web Services (AWS) **m7g.4xlarge** instance.  

To run the classification service, an AWS account with an associated payment method is required. This applies regardless of whether the server is running in the same region as the upstream S3 data buckets (`us-west2`). 

## Setup

Install Ubuntu Server and provision an SSH user with root access. Keyfile authentication is recommended. For Amazon AWS, refer to the official documentation: [AWS EC2 Setup Guide](https://docs.aws.amazon.com/ec2/)  

In this documentation, we assume that the user is named `helium`, and the hostname is `server`. 

## Create Data Directories

The classification service requires a directory structure to be created. The directories can be placed anywhere, as long as they are under the same root directory and have read / write / execute permissions for the `helium` user. In these steps, the root folder is assumed to be `/data`. Execute the following Bash commands to create this directory structure:

```bash
sudo mkdir /data
sudo chown -R $(whoami):$(whoami) /data
mkdir /data/sled_checkpoint
mkdir /data/sled_db
```

The `sled_db` and `sled_checkpoint` are used internally to store intermediate results.

## Obtain and Upload SRTM Data

NASA Shuttle Radar Topography (SRTM) data represent the elevation over most of the earth in a set of ASCII `.hgt` files. The **30-meter resolution tiles** are required. These are available from several sources:

* [NASA EarthData SRTM](https://www.earthdata.nasa.gov/data/instruments/srtm)  
* [AWS Open Data Terrain Tiles](https://registry.opendata.aws/terrain-tiles/)  

The Helium Foundation also provides a `.zip` file of the 30m terrain tiles:  [Google Drive ZIP Archive (16GB)](https://drive.google.com/file/d/18MJYXliRvXia7eKa9sDMaQKv0OWVeZPH/view?usp=drive_link).

Once the zip file is downloaded, upload it to `/data/srtm` using a utility such as `rsync`:  

```bash
sudo apt-get install rsync 
mkdir /data/srtm
rsync -avh --progress srtm.zip helium@server:/data/srtm
```

Extract the 30m tiles to `/data`. The `.hgt` files should be in `/data/3-arcsecond`, e.g. `/data/3-arcsecond/*.hgt`. There are 26157 `.hgt` files in the archive. Verify this by running `ls -l | wc -l` in `/data/3-arcsecond`. 

## ArangoDB Database Installation

The classification pipeline depends on a database 
(ArangoDB) to store proof of coverage data. ArangoDB is an open-source graph database (see: https://arangodb.com/). The recommended way to install ArangoDB is using Docker. Install Docker by following the steps here: https://docs.docker.com/engine/install/, and then run:

```
docker run -d -p 8529:8529 -e ARANGO_ROOT_PASSWORD=helium --name arangodb-instance arangodb
```

Note the arango root password is set to `helium`. A different value may also be used, but must correspond to the value used in the configuration files in the following section (ArangoDB ETL Installation).

## ArangoDB ETL Installation

The ETL (extract, transform, load) component of the pipeline downloads binary data from the Helium Foundation Amazon S3 bucket, then transforms and saves it in the ArangoDB Graph Database. The ArangoDB ETL is not open-source, and must be obtained from Nova Labs (nova.xyz). Write to Marc Nijdam, CTO (marc@nova-labs.com) to obtain access to this repository: https://github.com/novalabsxyz/arango-etl. 

Clone the repository and copy the example `.env` file:

```
git clone https://github.com/novalabsxyz/arango-etl && cd arango-etl
cp .env.template .env
```

The `.env` file contains only one line, which specifies the root password for the Arango DB installed in the last step. 

```
ARANGO_ROOT_PASSWORD=helium
```

The `.settings.template.toml` file contains parameters which must be changed to match the local environment. Copy the example `.settings.template.toml` to `settings.toml`:

```
cp settings.template.toml settings.toml
``` 

An example `settings.toml` follows:

```
[ingest]
log = "info"
[ingest.filestore]
bucket = "foundation-poc-data-requester-pays"
region = "us-west-2"
[ingest.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"
[ingest.tracker]
interval = 10
[ingest.matchmaker]
ttl = 900
[ingest.history]
after = "2025-01-01T00:00:00"
before = "2025-01-02T00:00:00"
[ingest.terrain]
srtm_dir = "/data/srtm/3-arcsecond/"
[ingest.sleddb]
path = "/data/sled_db"
cache_capacity = 10737418240
checkpoint = "/data/sled_checkpoint"

[classifier]
log = "info"
classifiers = [ "Similarity", "AntennaSplit" ]
commit_batch_size = 100
read_batch_size = 1000
channel_buffer_size = 16
stream_limit = 16
[classifier.terrain]
max_terrain_intersection = 500.0
[classifier.similarity]
max_terrain_intersection = 500.0
free_zone = 5.0
exp = -0.02
[classifier.antenna_split]
# colocation_distance_m
colocation_distance_m = 30
# fudge_factor_db - in deci-dB
fudge_factor_db = 10
# min_occurances (u16) = 5
min_occurances = 5
# distance_multiplier, no unit, used as colocation_distance_m * distance_multiplier
distance_multiplier = 20
[classifier.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"

[secondary_classifier]
log = "info"
[secondary_classifier.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"

[edq]
log = "info"
out_csv = "/data/edq/out.csv"
ignore_csv = "/data/edq/ignore.csv"
descriptor_bin = "/data/edq/descriptor.bin.gz"
edgecount_json = "/data/edq/edgecount.json"
manifest_url = "https://raw.githubusercontent.com/helium/denylist/main/manifest.json"
chunk_size = 1000
carryover_types = [ "antenna_splitter", "disjoint_reciprocity", "distance_insensitivity", "ingest_latency", "terrain_intersection"]
[edq.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"
[edq.reciprocity]
run_reciprocity = false
run_disjoint_reciprocity = true
reciprocity = 0.0
min_witnesses = 50
[edq.terrain]
max_distance = 100.0 # in km
max_terrain_wt_avg = 500.0
[edq.di]
max_distance = 100.0 # in km
max_terrain_intersection = 500.0 # in m*km
exp = -0.02
free_zone = 5.0
[edq.ingest_latency]
bucket_size = 50
[edq.itm]
enabled = false
max_distance = 100000.0
rssi_count = 5
fudge_factor = 3
[edq.impossibly_low_rssi]
enabled = false
max_distance = 100000.0
rssi_count = 5
rssi_lower_limit = -141

[analyzer]
log = "info"
db_dir = "/data/analyzer"
db_name = "analyzer.db"
[analyzer.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"

[server]
log = "info"
host = "127.0.0.1"
port = 3001
[server.report]
manifest = "https://raw.githubusercontent.com/helium/denylist/main/manifest.json"
report_dir = "/data/reports/"
edgecount_json = "/data/edq/edgecount.json"
[server.rfprop]
sdf_dir = "/path/to/rfprop/data"
debug = true
[server.terrain]
srtm_dir = "/data/srtm/3-arcsecond"
[server.cache]
expiration = 3600
[server.arangodb]
endpoint = "http://localhost:8529"
user = "root"
password = "helium"
database = "adb"
```

The following are the required changes from the default values:

```
bucket = "foundation-poc-data-requester-pays"
out_csv = "/data/edq/out.csv"
ignore_csv = "/data/edq/ignore.csv"
descriptor_bin = "/data/edq/descriptor.bin.gz"
edgecount_json = "/data/edq/edgecount.json"
srtm_dir = "/data/srtm/3-arcsecond/"
checkpoint = "/data/sled_checkpoint"
db_dir = "/data/analyzer"
report_dir = "/data/reports/"
edgecount_json = "/data/edq/edgecount.json"
srtm_dir = "/data/srtm/3-arcsecond"
```

The data folders provide locations for the output of the classification pipeline. The `bucket` must be `foundation-poc-data-requester-pays`, instead of the default `helium-mainnet-iot-verified-rewards`. This is because `helium-mainnet-iot-verified-rewards` belongs to Nova Labs, and is not public. It contains the same data as ``foundation-poc-data-requester-pays`. 

The `password` value must correspond to the ArangoDB root username and password (`root`, and `helium`).

## Patch Helium Oracle Software

The ArangoDB ETL loads data from the Nova Labs Amazon S3 bucket by default. Since this bucket is not public, the oracle software must be directed to load data from `foundation-poc-data-requester-pays` instead. This bucket has the `requester-pays` attribute, which means the Amazon S3 account requesting the data pays for the file transfer. This process may be expensive due to the large amount of data in the S3 bucket. The Arango ETL depends on the Helium Oracle software, where the changes to the bucket must be made to the rust source code. 

The oracle software is open-source. The required modification is available on the following branch: https://github.com/mrpatrick1991/oracles. The changes are to `file_store.rs`:

```
From e81643a4bfefda835d32f1e847399fa60904f749 Mon Sep 17 00:00:00 2001
From: Matthew Patrick <64496719+mrpatrick1991@users.noreply.github.com>
Date: Tue, 21 Jan 2025 11:16:49 -0700
Subject: [PATCH] Update file_store.rs

add options for request_payer
---
 file_store/src/file_store.rs | 4 ++++
 1 file changed, 4 insertions(+)

diff --git a/file_store/src/file_store.rs b/file_store/src/file_store.rs
index 0b886dee5..5f2d55aca 100644
--- a/file_store/src/file_store.rs
+++ b/file_store/src/file_store.rs
@@ -112,6 +112,7 @@ impl FileStore {
             .list_objects_v2()
             .bucket(&self.bucket)
             .prefix(file_type.to_string())
+            .request_payer("requester")
             .set_start_after(after.map(|dt| FileInfo::from((file_type, dt)).into()));
 
         futures::stream::unfold(
@@ -168,6 +169,7 @@ impl FileStore {
                 .key(file.file_name().map(|name| name.to_string_lossy()).unwrap())
                 .body(byte_stream)
                 .content_type("application/octet-stream")
+                .request_payer("requester")
                 .send()
                 .map_ok(|_| ())
                 .map_err(Error::s3_error)
@@ -182,6 +184,7 @@ impl FileStore {
                 .delete_object()
                 .bucket(&self.bucket)
                 .key(key)
+                .request_payer("requester")
                 .send()
                 .map_ok(|_| ())
                 .map_err(Error::s3_error)
@@ -268,6 +271,7 @@ where
         .get_object()
         .bucket(bucket)
         .key(key)
+        .request_payer("requester")
         .send()
         .map_ok(|output| output.body)
         .map_err(Error::s3_error)
```

The GitHub diff is available here: https://github.com/helium/oracles/commit/e81643a4bfefda835d32f1e847399fa60904f749.

Once the above changes to `file_store.rs` have been implemented, the Arango ETL is built using the following:

```cd arango-etl && cargo build --release```. 

## Running the Arango ETL

The classification pipeline assumes that two weeks of PoC data are ingested and stored. The Arango ETL is run in batch mode to accomplish this. Set the time period in `settings.toml`, for example:

```[ingest.history]
after = "2025-01-01T00:00:00"
before = "2025-01-14T00:00:00"
```

Then run `just ingest`. You may need to install the `just` command using `sudo apt-get install just`. The expected output when this is running successfully looks like the following:

```
./target/release/ingest -c settings.toml history
2025-03-27T19:38:17.260199Z  INFO ingest::cli::history: history mode started after: 2025-03-01T00:00:00Z, before: 2025-03-14T00:00:00Z
2025-03-27T19:38:17.986761Z  INFO provide_credentials{provider=default_chain}: aws_config::profile::credentials: constructed abstract provider from config file chain=ProfileChain { base: AccessKey(Credentials { provider_name: "ProfileFile", access_key_id: "** redacted **", secret_access_key: "** redacted **" }), chain: [] }
2025-03-27T19:38:17.986780Z  INFO provide_credentials{provider=default_chain}: aws_config::profile::credentials::exec: first credentials will be loaded from AccessKey(Credentials { provider_name: "ProfileFile", access_key_id: "** redacted **", secret_access_key: "** redacted **" }) base=AccessKey(Credentials { provider_name: "ProfileFile", access_key_id: "****", secret_access_key: "** redacted **" })
2025-03-27T19:38:17.986785Z  INFO provide_credentials{provider=default_chain}: aws_config::profile::credentials: loaded base credentials creds=Credentials { provider_name: "ProfileFile", access_key_id: "** redacted **", secret_access_key: "** redacted **" }
2025-03-27T19:38:19.708185Z  INFO ingest::file_processor: fetched 7721 iot_poc files between 2025-03-01T00:00:00Z - None
2025-03-27T19:38:19.709605Z  INFO ingest::file_processor: processing file_type: IotPoc, file_key: "foundation-iot-verified-rewards/iot_poc.1740787407268.gz"
2025-03-27T19:38:23.825635Z  INFO ingest::file_processor: fetched 1 iot_invalid_beacon files between 2025-03-01T00:00:00Z - Some(2025-03-01T00:03:27.268Z)
2025-03-27T19:38:23.825652Z  INFO ingest::file_processor: processing file_type: IotInvalidBeaconReport, file_key: "foundation-iot-verified-rewards/iot_invalid_beacon.1740787407081.gz"
2025-03-27T19:38:24.735400Z  INFO ingest::file_processor: fetched 0 iot_invalid_witness files between 2025-03-01T00:00:00Z - Some(2025-03-01T00:03:27.268Z)
2025-03-27T19:38:24.735420Z  INFO ingest::file_processor: before pop matchmaker size: 10142
2025-03-27T19:38:24.735423Z  INFO ingest::file_processor: after pop matchmaker size: 10142
2025-03-27T19:38:24.735466Z  INFO ingest::file_processor: inserted file: FileDoc {
    _key: "foundation-iot-verified-rewards/iot_poc.1740787407268.gz",
    timestamp: 2025-03-01T00:03:27.268Z,
    unix_ts: 1740787407268,
    size: 16437320,
    ft: "iot_poc",
}
2025-03-27T19:38:24.735474Z  INFO ingest::file_processor: processing file_type: IotPoc, file_key: "foundation-iot-verified-rewards/iot_poc.1740787737420.gz"
2025-03-27T19:38:27.202353Z  INFO ingest::file_processor: fetched 0 iot_invalid_beacon files between 2025-03-01T00:03:27.268Z - Some(2025-03-01T00:08:57.420Z)
2025-03-27T19:38:27.904311Z  INFO ingest::file_processor: fetched 1 iot_invalid_witness files between 2025-03-01T00:03:27.268Z - Some(2025-03-01T00:08:57.420Z)
2025-03-27T19:38:27.904327Z  INFO ingest::file_processor: processing file_type: IotInvalidWitnessReport, file_key: "foundation-iot-verified-rewards/iot_invalid_witness.1740787737158.gz"
2025-03-27T19:38:29.352003Z  INFO ingest::file_processor: before pop matchmaker size: 14660
2025-03-27T19:38:29.372692Z  INFO ingest::file_processor: after pop matchmaker size: 13797
2025-03-27T19:38:29.372720Z  INFO ingest::file_processor: inserted file: FileDoc {
    _key: "foundation-iot-verified-rewards/iot_poc.1740787737420.gz",
    timestamp: 2025-03-01T00:08:57.420Z,
    unix_ts: 1740787737420,
    size: 20166846,
    ft: "iot_poc",
}
2025-03-27T19:38:29.372729Z  INFO ingest::file_processor: processing file_type: IotPoc, file_key: "foundation-iot-verified-rewards/iot_poc.1740788037303.gz"
```

 The ingest process will take several hours to complete for the full two-week time period. 
 
 Once the ingest is complete, export the ingested PoC data to ArangoDB using `just export-all`. The expected output is:

 ```
 ./target/release/ingest -c settings.toml export all
2025-03-27T19:44:20.409022Z  INFO ingest::cli::export: loaded checkpoint from "/data/sled_checkpoint", ts: Some(2025-01-28T17:28:29.210902209Z)
2025-03-27T19:44:20.409138Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.409770Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.410029Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.410272Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.410473Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.410669Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.410843Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411016Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411184Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411355Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411531Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411706Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.411969Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412147Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412310Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412476Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412637Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412794Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.412962Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.413121Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.413288Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.413451Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.413615Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.413865Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414035Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414193Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414352Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414524Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414690Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.414850Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.415007Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.415164Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.415311Z  INFO ingest::cli::export: Inserting batch of 10 files
2025-03-27T19:44:20.415460Z  INFO ingest::cli::export: Inserting leftover batch of 4 files
2025-03-27T19:44:20.415601Z  INFO ingest::cli::export: hotspot sentinel already set, skipping hotspot export
2025-03-27T19:44:20.415604Z  INFO ingest::cli::export: edge sentinel already set, skipping edge export
2025-03-27T19:44:20.415606Z  INFO ingest::cli::export: export took: 9.795414ms
```

The classification stage is then run with `just classifier`. The expected output looks like:

```./target/release/classifier -c settings.toml
2025-03-27T19:47:58.810777Z  INFO classifier::runner: starting classification
2025-03-27T19:47:58.810803Z  INFO classifier::runner: done classification
```

The `secondary_classifier`, `analyzer`, and `edq` stages are run after the `classifier`, and use its output to do analysis on the classification results. Both the `secondary_classifier` and `analyzer` are designed to identify problematic nodes (hotspots), while the `edq` stage identifies edges (relationships) between hotspots.

Finally, running `just generate_reports` produces a set of PDF "report cards" for each hotspot in the ArangoDB. The report cards contain graphs and statistics for each hotspot and the output of the classifiers, which are described in detail in the next section.

The entire pipeline may be run at once with `just pipeline`. It is recommended to run the above steps sequentially prior to doing this, to verify that all stages of the pipeline are functioning correctly. 

There is a web frontend for viewing classifier output data, but it is not functional at the time of writing. This would be run using `just serve`. 