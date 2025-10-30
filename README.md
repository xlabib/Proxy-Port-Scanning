# IP & Port Scanner — README

## Overview
A fast, flexible scanner that tests IP addresses for open TCP ports using simple text lists.  
Place target IPs in `Ips.txt` and ports or port ranges in `ports.txt`, configure performance options if needed, then run the tool.

> **WARNING:** This tool performs active network scanning. Scanning systems you do **not** own or do not have explicit permission to test may be illegal and/or result in network bans or other consequences. Read **Security & Legal** below before use.

---

## Repository contents (recommended)
- `README.md` — this file.  
- `Ips.example.txt` — example IP list.  
- `ports.example.txt` — example ports list.  
- `config.example.json` — optional example configuration (threads, timeouts, etc.).  
- `LICENSE` — add an open-source license (MIT/Apache-2.0) if you intend to publish.  
- `CHANGELOG.md` — optional: record of changes and release notes.

---

## Quick start

1. Create `Ips.txt` and `ports.txt` (or copy from the provided `*.example.txt` files).  
2. Adjust the configuration if you use `config.json` or change constants inside the source.  
3. Build & run the scanner.

Example (dotnet):
```bash
dotnet run --project ScannerProject
# or after building
./scanner.exe
```

Typical outputs are written to a results folder (configurable): e.g. `results/YYYYMMDD_HHMMSS/open_ports.json` and `results/YYYYMMDD_HHMMSS/open_ports_raw.log`.

---

## Input file formats

### `Ips.txt`
One entry per line. Accepted values:
- Single IPv4 address:
```
36.110.143.55
185.41.152.110
```
- Special token (use with caution):
```
ALL_IPV4
```

**Notes**
- `ALL_IPV4` instructs the scanner to iterate over the IPv4 address space. **Extremely heavy, risky, and potentially illegal** on public networks — do not use without explicit authorization and proper infrastructure.
- By default the tool skips local/private/reserved address ranges (RFC1918) and other protected prefixes. Check the source if you need to override this behavior.

### `ports.txt`
One entry per line. Each line may be:
- Single port:
```
80
443
```
- Port range (inclusive):
```
8000-9000
1-65535   # whole range — extremely heavy
```

---

## Example files

**`Ips.example.txt`**
```
# Single IPs (one per line)
36.110.143.55
185.41.152.110

# Danger: FULL IPv4 scan (do NOT run on public networks)
# ALL_IPV4
```

**`ports.example.txt`**
```
80
443
8080
8000-8005
# 1-65535
```

---

## Configuration (example)
You can expose tunable parameters in a `config.json` or adapt constants in the source.

`config.example.json`
```json
{
  "Threads": 800,
  "ConnectTimeoutMs": 3500,
  "HeaderReadTimeoutMs": 3000,
  "TlsHandshakeTimeoutMs": 3500,
  "HttpReadTimeoutMs": 5000,
  "MaxRetries": 3,
  "PerTargetTotalTimeoutMs": 30000,
  "SkipPrivateRanges": true,
  "OutputFolder": "results"
}
```

**Common options**
- `Threads` — number of worker threads/tasks. Higher = faster but more CPU/network usage.  
- `ConnectTimeoutMs` — TCP connect timeout (ms).  
- `MaxRetries` — attempts per port if failures occur.  
- `SkipPrivateRanges` — whether to auto-skip RFC1918 and reserved prefixes.

---

## Behavior & features
- Reads `Ips.txt` (targets) and `ports.txt` (ports / ranges).  
- Performs TCP connection attempts to each port for each target.  
- Skips local/private/reserved ranges by default.  
- Configurable concurrency (threads/tasks) and timeouts.  
- Writes structured output (JSON/CSV), raw logs, and per-run result folders.  
- Optional proxy support (if implemented): supply `proxies.txt` in `ip:port` or `ip:port:user:pass` format.

---

## Output suggestions
- Save run outputs in a timestamped folder, e.g. `results/20251030_050400/`.  
- Produce:
  - `open_ports.json` — machine-readable JSON with all open ports found.  
  - `open_ports.csv` — simple CSV export.  
  - `open_ports_raw.log` — raw attempt logs for debugging.  
- Keep both summarized and raw logs so you can audit scanner behavior.

---

## Usage examples

Scan a small list on common ports:
- `Ips.txt`:
```
36.110.143.55
185.41.152.110
```
- `ports.txt`:
```
80
443
8080
```

Scan a single IP across a range:
- `Ips.txt`:
```
36.110.143.55
```
- `ports.txt`:
```
8000-9000
```

---

## Safety & legal (READ THIS BEFORE USING)
- **Do not scan systems you do not own or do not have explicit written permission to test.** Unauthorized scanning can be considered intrusive activity and may be illegal in many jurisdictions.  
- Internet service providers, hosting providers, and network operators often treat unsolicited scanning as abuse — your IPs may be blocked, you may receive abuse reports, or you may face legal actions.  
- `ALL_IPV4` or full-range scans are dangerous on public networks and should only be used with signed authorization and appropriate infrastructure.  
- When testing, prefer:
  - isolated lab environments (private networks, VMs), or  
  - clearly scoped, signed penetration-testing engagements.  
- Add a prominent legal disclaimer and contact information in your repo if you intend to share the tool publicly.

Suggested short disclaimer to include near the top of the repo:
> **LEGAL NOTICE:** This tool is provided for educational and authorized testing use only. The author is not responsible for misuse. Do not scan networks/systems without explicit permission.

---

## Logging, monitoring & performance tips
- Use moderate `Threads` initially and increase only after testing resource usage.  
- Monitor CPU, RAM, and network IO during large scans.  
- Persist intermediate results frequently to avoid losing progress on long runs.  
- If you implement proxy support, validate proxies for correctness and latency first.

---

## Contributing
If you accept contributions, add a `CONTRIBUTING.md` with:
- coding style and formatting rules,  
- how to run tests,  
- required config for local development, and  
- how to file issues/PRs.

---

## License
Choose a license that matches your intentions. Common choices:
- MIT — permissive, allows reuse with attribution.  
- Apache 2.0 — permissive and includes patent protection language.

Add a `LICENSE` file to the repository root.

---

## Contact / Support
Include author contact or CVE/abuse contact details if you plan to publish publicly. Consider adding a `SECURITY.md` describing responsible disclosure.

---

## Final notes
- Keep example files (`Ips.example.txt`, `ports.example.txt`, `config.example.json`) in the repo so users immediately see required formats.  
- Make the legal warning and scanning limitations clearly visible (top of README and as a header comment in code).  
- If you want, I can also generate the `Ips.example.txt`, `ports.example.txt`, and a ready-to-copy `config.example.json` content — tell me and I will produce them in plain text now.

