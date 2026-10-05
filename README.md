# UWB Clock Synchronization and Antenna Delay Dataset

This repository contains Ultra-Wideband (UWB) ranging and timestamp data recorded during experiments. The dataset captures raw message transmission/reception timings, physical hardware clock bits, and full network parameters across multiple hardware nodes to facilitate research in clock synchronization, Time of Flight (ToF) estimation, and indoor localization.

## Directory & File Structure

Data is organized chronologically by date. Each folder captures a specific experimental setup variant (RUN) and sequential trial iteration (TRY). Inside each folder, separate CSV logs represent the data received by each connected device interface (ttyACMX), alongside the shared experimental configuration file.

```text
dataset/
└── YYYY-MM-DD_RUN_XX_TRY_YY/
    ├── config.yaml
    ├── YYYY-MM-DD_RUN_XX_TRY_YY_ttyACM0.csv
    ├── YYYY-MM-DD_RUN_XX_TRY_YY_ttyACM1.csv
    ├── YYYY-MM-DD_RUN_XX_TRY_YY_ttyACM2.csv
    └── YYYY-MM-DD_RUN_XX_TRY_YY_ttyACM3.csv
```

### Identifier Breakdown

- YYYY-MM-DD: The date when the recording took place (e.g., 2026-06-15).
- RUN_XX: Identifies a unique parameter configuration (e.g., specific antenna delays, node spacing, or transmission intervals).
- TRY_YY: Identifies the specific trial attempt or sequential repeat utilizing that exact RUN configuration.
- ttyACMX: The operating system serial port index (X) where the logging node was connected.

## Data Format

Each CSV file records the timestamps of messages flowing through that specific node. The logs follow this schema:

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `node_id` | Integer | Hex/Integer identifier of the node that is recording the data in the CSV. |
| `transmitter_id` | Integer | Hex/Integer identifier of the node that *sent* the UWB packet. |
| `message_id` | Integer | Sequential frame counter tracking packets across the network. |
| `timestamp_bit` | Integer | Raw transceiver internal hardware clock ticks/bits at TX or RX event. |
| `timestamp_seconds` | Float | The converted timestamp value evaluated in seconds. |
| `clock_offset(not_used)`| Float | Internal calculation tracking clock drift offset (`0.0` or `nan` if unused). |

```csv
transmitter_id,receiver_id,message_id,timestamp_bit,timestamp_seconds,clock_offset(not_used)
1,0,0,247335245131,3.870806495564779,nan
1,1,0,247729940992,3.876983501602564,0.0
1,0,1,248818999642,3.894027313107222,nan
1,1,1,249561376768,3.905645544871795,0.0
1,0,2,250302665094,3.9172467368727464,nan
1,1,2,251042585088,3.928826514423077,0.0
```

## Configuration Parameters (config.yaml)

Each RUN directory includes a configuration file containing physical parameters, ground-truth node baselines, and radio-frequency timeouts.

### Key Fields:

- `message_interval`: The scheduled transmission period delay between sequential node bursts (in seconds).
- `message_measure`: Sequential alternative distribution mapping how many messages are allocated per specific node alternately.
- `tx_antenna_delay` / rx_antenna_delay: Hardcoded transceiver antenna internal delay factors (in microseconds).
- `rx_timeout` / preamble_timeout: Low-level hardware registration constraints applied to message receiving loops.
- `nodes`: Complete inventory of operating nodes mapped to their sequential OS hardware ports (/dev/ttyACMX).
- `node_pairs`: Array mapping physical ground-truth distance parameters (in meters) between active node nodes.

Example File
```yaml
message_interval: 2e-3 # s
message_measure: # how many messages sent per node alternatively
- 1 # node 0x00
- 1 # node 0x01
- 1 # node 0x02
- 1 # node 0x03
tx_antenna_delay: 0 # us
rx_antenna_delay: 0 # us
rx_timeout: 5000 # us
preamble_timeout: 16000 # us, deactivated if commented
nodes:
- 0x00 # /dev/ttyACM0
- 0x01 # /dev/ttyACM1
- 0x02 # /dev/ttyACM2
- 0x03 # /dev/ttyACM3
node_pairs:
  - nodes:
    - 0x00
    - 0x01
    distance: 0.750 # m
  - nodes:
    - 0x00
    - 0x02
    distance: 0.550 # m
  - nodes:
    - 0x00
    - 0x03
    distance: 0.905 # m
  - nodes:
    - 0x01
    - 0x02
    distance: 0.620 # m
  - nodes:
    - 0x01
    - 0x03
    distance: 0.390 # m
  - nodes:
    - 0x02
    - 0x03
    distance: 0.510 # m
K: 1000
preamble_length: 256
```

## Data Processing & Quick-Start

When processing data from a given execution path:

1. Load the corresponding config.yaml parameters to extract the ground-truth physical distance matrices between node sets.
2. Cross-reference files across matching message_id headers to align independent local hardware clock logs.
3. Compute metrics like relative clock-drift or Time-Difference-of-Arrival (TDoA) calculations using the high-precision timestamp_bit value or the scaled timestamp_seconds.

## Citation

If you use this repository for scientific publication, we would appreciate citation of the following paper:

```latex
@inproceedings{Becoy2026,
  author    = {Becoy, Alexander James and Peternel, Luka and Rajan, Raj Thilak},
  title     = {Clock Synchronization and Antenna Delay Estimation for UWB-based Lunar Rover Networks},
  booktitle = {2026 International Conference on Space Robotics (iSpaRo)},
  year      = {2026}
}
```

## Issue

If you come across bugs, unintended functions, or have some points of improvement, please refer to the issues, and fill in your remarks.