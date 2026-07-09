# Test Case: Detect Possible C2 Beaconing (beaconing pattern)

## Rule Details

```snort
alert tcp any any -> any any (msg:"Possible C2 beaconing"; content:"keep-alive"; sid:1000013; rev:1;)
```

- **SID:** 1000013  
- **Description:** Detects HTTP `keep-alive` string often used in persistent C2 connections  
- **Purpose:** Identify potential beaconing behavior in malware traffic  
- **Protocol:** TCP  
- **Note:** `flow` not used to simplify detection without requiring full session tracking

---

## Test Environment

- **OS:** Kali Linux (VM)  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Snort Config Path:** `/home/kali/snort-custom-rules/snort.conf`  
- **Log Output Directory:** `/home/kali/snort-custom-rules/logs`  
- **Traffic Generation:** `curl` request with custom header  
- **Web Server:** Python HTTP server on VM (port 80)

---

## Steps to Reproduce

### 1. Start Web Server on VM

```bash
sudo python3 -m http.server 80
```

---

### 2. Run Snort on VM

```bash
sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
-l /home/kali/snort-custom-rules/logs -A fast
```

---

### 3. Send Beacon Simulation from Host

```bash
curl -H "Connection: keep-alive" http://<VM_IP>
```

---

## Expected Output

```
[**] [1:1000013:1] Possible C2 beaconing [**] [Priority: 0] {TCP} 192.168.84.1:xxxxx -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/c2/C2_beaconing_test_host.png` — Host terminal sending `curl` with `keep-alive` header  
- `screenshots/c2/C2_beaconing_test_VM.png` — Snort alert appearing in real-time

---

## Notes

- This rule is useful in detecting malware that communicates with a remote server using periodic "keep-alive" pings.  
- Avoids complex flow/state inspection to ensure compatibility with minimal Snort configurations.
