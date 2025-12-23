# Fast Port Scanner Scripts - Quick Reference

Three fast port scanning scripts using `nc` for different use cases.

---

## 1. fast_port_scan.sh - Balanced Scanner

**Best for:** General purpose scanning with good speed/accuracy balance

```bash
./fast_port_scan.sh <IP> [max_parallel_jobs]
```

**Features:**
- Scans top 1000 ports
- Default 50 parallel jobs
- 1 second timeout per port
- ~11 seconds scan time

**Examples:**
```bash
./fast_port_scan.sh 38.3.233.204          # Default settings
./fast_port_scan.sh 192.168.1.1 100       # 100 parallel jobs
```

---

## 2. ultra_fast_scan.sh - Speed Optimized

**Best for:** Quick reconnaissance when speed is critical

```bash
./ultra_fast_scan.sh <IP> [max_parallel_jobs]
```

**Features:**
- Scans top 1000 ports
- Default 200 parallel jobs
- 0.5 second timeout per port
- Uses `-z` flag for faster scanning
- ~5 seconds scan time
- Nice progress indicators

**Examples:**
```bash
./ultra_fast_scan.sh 38.3.233.204         # Default ultra-fast
./ultra_fast_scan.sh 192.168.1.1 300      # Maximum speed
```

---

## 3. custom_port_scan.sh - Flexible Scanner

**Best for:** Custom port ranges, comprehensive scans, or specific ports

```bash
./custom_port_scan.sh <IP> [mode] [max_parallel]
```

**Modes:**
- `top1000` - Scan top 1000 ports (default)
- `all` - Scan ALL 65535 ports
- `custom` - Scan custom ports

**Features:**
- Supports port ranges (e.g., 1-10000)
- Supports comma-separated lists (e.g., 80,443,8080)
- Supports mixed (e.g., 21-25,80,443,8000-9000)
- Progress percentage display
- Auto-increases parallelism for large scans

**Examples:**
```bash
# Top 1000 ports (default)
./custom_port_scan.sh 38.3.233.204

# Scan ALL ports (takes several minutes)
./custom_port_scan.sh 38.3.233.204 all 500

# Scan specific range
CUSTOM_PORTS="1-10000" ./custom_port_scan.sh 38.3.233.204 custom 300

# Scan specific ports
CUSTOM_PORTS="21,22,80,443,3389,8080" ./custom_port_scan.sh 38.3.233.204 custom

# Scan web ports
CUSTOM_PORTS="80,443,8000-8100,8443,9443" ./custom_port_scan.sh 192.168.1.1 custom
```

---

## Comparison Table

| Script              | Ports        | Speed     | Time (1000 ports) | Best For                    |
|---------------------|--------------|-----------|-------------------|----------------------------|
| fast_port_scan.sh   | Top 1000     | Medium    | ~11s              | Balanced scanning          |
| ultra_fast_scan.sh  | Top 1000     | Very Fast | ~5s               | Quick reconnaissance       |
| custom_port_scan.sh | Configurable | Variable  | ~90s (10k ports)  | Custom/comprehensive scans |

---

## Performance Tips

1. **Increase parallelism** for faster scans on stable networks:
   ```bash
   ./ultra_fast_scan.sh 192.168.1.1 500
   ```

2. **Reduce parallelism** if getting inconsistent results:
   ```bash
   ./fast_port_scan.sh 192.168.1.1 25
   ```

3. **For local networks**, you can go very high:
   ```bash
   CUSTOM_PORTS="1-65535" ./custom_port_scan.sh 192.168.1.1 custom 1000
   ```

4. **For slow/remote targets**, reduce parallelism:
   ```bash
   ./ultra_fast_scan.sh remote-server.com 50
   ```

---

## Results from Test Scan (38.3.233.204)

```
Open ports found:
  2000/tcp → cisco-sccp
  5060/tcp → sip
  8008/tcp → http
  8015/tcp → cfg-cloud
  8020/tcp → intu-ec-svcdisc

Total: 5 open port(s)
```

**Note:** Ports 8015 and 8020 are NOT in the top 1000, so they won't be detected by fast_port_scan.sh or ultra_fast_scan.sh unless you use custom_port_scan.sh with an appropriate range.

---

## When to Use Each Script

### Use `ultra_fast_scan.sh` when:
- You need quick results
- Scanning for common services
- Doing initial reconnaissance
- Time is critical

### Use `fast_port_scan.sh` when:
- You want reliable results
- Network might be unstable
- Balanced approach needed

### Use `custom_port_scan.sh` when:
- Scanning non-standard ports (like 8015, 8020)
- Need to scan all ports
- Looking for specific services
- Want to scan custom ranges
- Ultra-fast scan missed ports

---

## Technical Details

All scripts use:
- `nc -nv -z` for connection testing
- Background jobs for parallelism
- `timeout` command to prevent hanging
- Temporary files for result collection
- Service name resolution

**Timeout values:**
- fast_port_scan.sh: 1 second
- ultra_fast_scan.sh: 0.5 seconds
- custom_port_scan.sh: 0.5 seconds

**Parallel job limits:**
- Prevents system overload
- Can be adjusted via command line
- Higher = faster but more resource intensive
