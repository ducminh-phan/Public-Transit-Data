# Public Transit Data

Information extracted from GTFS transit feeds to use with
[RAPTOR algorithm](https://github.com/ducminh-phan/RAPTOR).

Each folder corresponding to the public transport data of a location, and consists of the following files:

- [`in_hubs.gr.gz`](README.md#in_hubsgrgz)
- [`out_hubs.gr.gz`](README.md#out_hubsgrgz)
- [`stop_routes.csv.gz`](README.md#stop_routescsvgz)
- [`stop_times.csv.gz`](README.md#stop_timescsvgz)
- [`transfers.csv.gz`](README.md#transferscsvgz)
- [`trips.csv.gz`](README.md#tripscsvgz)
- [`walking_graph.gr.gz`](README.md#walking_graphgrgz)

All the files are in `gzip` format, each line of the decompressed files can be considered as a row of a table,
consisting of the same number of columns, with the following differences:

- `*.csv.gz` files: the elements in each row are separated by a comma, and each file has a header,
- `*.gr.gz` files: the elements in each row are separated by a space, and there is no header.

## `stop_routes.csv.gz`

Header: `stop_id,route_id`

This file contains the information about the routes serving a stop in the timetable. The rows `s,r` indicate that
the stop `s` is served by the route `r`.

## `trips.csv.gz`

Header: `route_id,trip_id`

This file contains the information about the trips belong to a route. The rows `r,t` indicate that the trip `t` belongs
to the route `r`. Every trip of a route has the same stop sequence, which can be found using `stop_times.csv.gz`.

Moreover, the rows are sorted by the id of the routes. And in each group of trips belong to the same
route, the trips are sorted so that the departure times at each stop of a trip is after that of the
previous trip at the same stop. For example, if we organize the departure times of a route into a table as below,
where `s[0], s[1], s[2]` are the stops, `t[0], t[1], t[2]` are the trips after being ordered, `dep[i, j]` is the
departure time of the trip `t[i]` at stop `s[j]`, then `dep[i+1, j] >= dep[i, j]` for all `i, j`.

|        | `s[0]`      | `s[1]`      | `s[2]`      |
| ------ | ----------- | ----------- | ----------- |
| `t[0]` | `dep[0, 0]` | `dep[0, 1]` | `dep[0, 2]` |
| `t[1]` | `dep[1, 0]` | `dep[1, 1]` | `dep[1, 2]` |
| `t[2]` | `dep[2, 0]` | `dep[2, 1]` | `dep[2, 2]` |

## `stop_times.csv.gz`

Header: `trip_id,arrival_time,departure_time,stop_id,stop_sequence`

- `trip_id`/`stop_id`: The id of the trip/stop to which the event represented by a row belongs.
- `arrival_time`/`departure_time`: The arrival/departure times at the stop `stop_id` for the trip `trip_id`. The unit
is second, counted from midnight.
- `stop_sequence`: The `stop_sequence` field identifies the order of the stops for a particular trip.
The values for `stop_sequence` are non-negative integers, and increasing along the trip.

## `transfers.csv.gz`

Header: `from_stop_id,to_stop_id,min_transfer_time`

- `from_stop_id`: The `from_stop_id` field contains a stop ID that identifies a stop
where a connection between routes begins.
- `to_stop_id`: The `to_stop_id` field contains a stop ID that identifies a stop
where a connection between routes ends.
- `min_transfer_time`: The `min_transfer_time` field defines the amount of time in seconds to transfer in a connection.

## `walking_graph.gr.gz`

This file represents the unrestricted walking graph for each location. The graph is directed and weighted. Each arc
of the graph is represented by a line in the decompressed file.

The format of each line is `s t d`, where `s`/`t` are the id of the source/target. `d` is the walking distance
from `s` to `t`, which is an integer with the unit of 10 cm.

## `in_hubs.gr.gz`

This file contains the information about the in-hubs of each stop in the timetable.

The format of each line is `h s d`, where `h` is the id of the in-hub, which is a node in the walking graph. `h` is an
in-hub of the stop `s`, and `s` is the shortest-path distance from `h` to `s`.

## `out_hubs.gr.gz`

This file contains the information about the out-hubs of each stop in the timetable.

The format of each line is `s h d`, where `h` is the id of the out-hub, which is a node in the walking graph. `h` is an
out-hub of the stop `s`, and `s` is the shortest-path distance from `s` to `h`.

## Covering property

The in-hubs and out-hubs satisfy the following covering property:

![](https://latex.codecogs.com/png.latex?%5CLARGE%20d%28u%2Cv%29%3D%5Cmin_%7Bh%7B%5Cin%7DH%5E&plus;%28u%29%7B%5Ccap%7DH%5E-%28v%29%7D%5C%7Bd%28u%2Ch%29&plus;d%28h%2Cv%29%5C%7D)

where `H⁺(u)` is the set of out-hubs of `u` and `H⁻(v)` is the set of in-hubs of `v`.

# Statistics

|              | London  | Paris   | Switzerland |
| ------------ | ------- | ------- | ----------- |
| routes       | 1622    | 1973    | 13930       |
| trips        | 122047  | 78757   | 369744      |
| stops        | 19746   | 23519   | 25427       |
| events       | 4695285 | 1915253 | 4740869     |
| transfers    | 61254   | 116579  | 5604        |
| nodes        | 279622  | 528903  | 603544      |
| edges        | 774568  | 1409709 | 1844286     |
| avg in hubs  | 73.27   | 112.97  | 80.74       |
| avg out hubs | 73.27   | 111.65  | 80.74       |
