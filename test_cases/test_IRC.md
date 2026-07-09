# Test Case: Detect Outbound IRC Traffic

## Rule Details

```snort
alert tcp any any -> any 6667 (msg:"IRC Traffic Detected - Possible Bot"; sid:1000014; rev:1;)
```

- **SID:** 1000014  
- **Description:** Detects TCP traffic to port 6667 (IRC)  
- **Purpose:** Identify potential IRC-based bot communication  
- **Alternative (tighter):** Match IRC NICK command:
  ```snort
  alert tcp any any -> any 6667 (msg:"IRC NICK Command Detected"; content:"NICK "; sid:1000015; rev:1;)
  ```

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.x  
- **Interface:** `eth0`  
- **Snort Config Path:** `/home/kali/snort-custom-rules/snort.conf`  
- **Log Directory:** `/home/kali/snort-custom-rules/logs`  
- **Tools:** `nc` (netcat) for simulating IRC server and traffic

---

## Steps to Reproduce

### 1. Start Snort on the VM

```bash
sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
-l /home/kali/snort-custom-rules/logs -A fast
```

---

### 2. Open a Listener on IRC Port (Simulated Server)

```bash
sudo nc -lvp 6667
```

---

### 3. Simulate IRC Join from Host

```bash
echo -e "NICK botuser\r\nUSER botuser 0 * :Bot\r\nJOIN #channel\r\n" | nc <VM_IP> 6667
```

---

## Expected Output

```
[**] [1:1000014:1] IRC Traffic Detected - Possible Bot [**] [Priority: 0] {TCP} 192.168.84.1:xxxxx -> 192.168.84.130:6667
```

> If using the NICK-specific rule, the message will read:
> `"IRC NICK Command Detected"`

---

## Screenshots

- `screenshots/irc/IRC_test_host.png` — Host sending IRC-like payload  
- `screenshots/irc/IRC_test_VM.png` — VM showing Snort alert

---

## Notes

- IRC traffic may indicate botnet control channels in real attacks.  
- Use content-based refinement (`NICK`, `PRIVMSG`, etc.) to reduce false positives.  
- Port 6667 is standard but IRC can use other ports; modify rule if needed.
