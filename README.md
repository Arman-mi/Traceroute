# Traceroute (Python)

An educational implementation of **traceroute** in Python. It sends UDP probes with increasing IP TTL values and listens for ICMP replies to discover the layer-3 path to a destination.


## How it works

- For each TTL from `1..30`, send up to `3` UDP probes to the destination.
- Receive ICMP responses on a separate socket and parse packet headers:
  - Outer IPv4 header (the router that generated the ICMP message)
  - ICMP header (`Time Exceeded` / `Destination Unreachable`)
  - The quoted inner IPv4 + UDP headers inside the ICMP payload (used to match replies to our probes)
- Collect unique responding router IPs for each TTL and print progress as results arrive.
- Stop early when the destination is reached (ICMP “port unreachable” from the target).

## Requirements

- Python **3.9+**
- Elevated privileges are usually required to open an ICMP socket:
  - Linux: run with `sudo`
  - Windows: run from an **Administrator** shell
  - macOS: raw ICMP may fail; the helper code attempts a non-privileged fallback

No third-party dependencies.

## Usage

Run traceroute to a host:

```bash
python traceroute.py example.com
```

If you see a permission error, try:

- Linux:

```bash
sudo python traceroute.py example.com
```

## Output

The program prints one line per TTL. If no routers respond for a TTL, it prints `*`.

Example shape:

```text
 1: 192.168.1.1
 2: 10.0.0.1
 3: *
 4: some-router.example.net (203.0.113.10)
```

## Repo layout

- `traceroute.py` — main implementation (IPv4/ICMP/UDP parsing + traceroute loop)
- `util.py` — socket helpers, argument parsing, and printing utilities
- `tests.py` — placeholder for tests (not currently implemented)

## Configuration

Key constants live in `traceroute.py`:

- `TRACEROUTE_MAX_TTL` (default `30`)
- `PROBE_ATTEMPT_COUNT` (default `3`)

Receive timeout is controlled by `util.SELECT_TIMEOUT` (default `2` seconds).

## Reference

- Project spec: https://cs168.io/proj1/
