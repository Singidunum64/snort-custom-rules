# Test Case: Detect Possible Shellcode NOP Sled

## Rule Details

```snort
alert ip any any -> any any (msg:"Possible Shellcode NOP Sled"; content:"|90 90 90 90 90|"; sid:1000011; rev:1;)
```

- **SID:** 1000011  
- **Description:** Detects five consecutive NOP (`0x90`) bytes, often used in shellcode and buffer overflow attacks.  
- **Protocol:** IP (works for both TCP and UDP payloads)

---

## Test Environment

- **VM OS:** Kali Linux  
- **Snort Version:** 2.9.20  
- **Interface Monitored:** `eth0`  
- **Snort Config:** `/home/kali/snort-custom-rules/snort.conf`  
- **Log File:** `/home/kali/snort-custom-rules/logs/alerts_nopsled.txt`  
- **Test Port:** TCP 9999  
- **Tools:** `netcat`, `printf`

---

## Steps to Reproduce

### 1. Start Snort on the VM

```bash
sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
-l /home/kali/snort-custom-rules/logs -A fast
```

---

### 2. (Optional) Listen on TCP port 9999 for visibility

```bash
sudo nc -lvp 9999
```

> Snort will detect the packet whether or not the port is listening.

---

### 3. From the Host Machine, Send the NOP Sled

```bash
printf '\x90\x90\x90\x90\x90\x90\x90\x90\x90\x90' | nc <VM_IP> 9999
```

> Sends 10 NOP bytes (`0x90`) to simulate a shellcode payload.

---

### 4. View and Archive the Alert

```bash
cat /home/kali/snort-custom-rules/logs/alert
cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_nopsled.txt
rm /home/kali/snort-custom-rules/logs/alert
```

---

## Expected Output

```
[**] [1:1000011:1] Possible Shellcode NOP Sled [**] [Priority: 0] {TCP} 192.168.84.1:XXXXX -> 192.168.84.130:9999
```

---

## Screenshots

- `screenshots/nopsled/NOPSled_test_host.png` – Host machine sending the payload  
- `screenshots/nopsled/NOPSled_test_VM.png` – VM terminal with Snort alert

---

## 📝 Notes

- Matching is raw payload-based, not protocol-specific.
- This rule is especially helpful in buffer overflow lab simulations.
- Could trigger false positives on benign `0x90` sequences.
