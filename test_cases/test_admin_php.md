# Test Case: Detect Access to admin.php

## Rule Details

```snort
alert tcp any any -> any 80 (msg:"Access to admin.php"; content:"admin.php"; http_uri; sid:1000012; rev:1;)
```

- **SID:** 1000012  
- **Description:** Detects HTTP requests targeting `admin.php`  
- **Purpose:** Identify potential brute-force or admin panel access attempts  
- **Preprocessor Dependency:** Requires `http_inspect` to be enabled in `snort.conf`

---

## Test Environment

- **VM OS:** Kali Linux  
- **Snort Version:** 2.9.20  
- **Monitored Interface:** `eth0`  
- **Snort Config Path:** `/home/kali/snort-custom-rules/snort.conf`  
- **Log Directory:** `/home/kali/snort-custom-rules/logs`  
- **Web Server:** Python HTTP server serving static files  
- **Client:** Host machine using `curl`

---

## Steps to Reproduce

### 1. Create `admin.php` File on the VM

```bash
cd /var/www/html
sudo touch admin.php
```

---

### 2. Start HTTP Server on the VM

```bash
sudo python3 -m http.server 80
```

---

### 3. Launch Snort in IDS Mode

```bash
sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
-l /home/kali/snort-custom-rules/logs -A fast
```

---

### 4. From the Host Machine, Trigger the Rule

```bash
curl http://<VM_IP>/admin.php
```

---

## Expected Output

```
[**] [1:1000012:1] Access to admin.php [**] [Priority: 0] {TCP} 192.168.84.1:xxxxx -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/admin_php/admin_php_test_host.png` – Host terminal issuing curl command  
- `screenshots/admin_php/admin_php_test_VM.png` – Snort terminal showing triggered alert

---

## Notes

- If the alert does not fire, verify that `http_inspect` is enabled in `snort.conf`.
- For troubleshooting, temporarily remove `http_uri` to confirm Snort is receiving the packet and parsing the content.
- This rule is helpful for detecting unauthorized admin panel access attempts during scanning and enumeration phases.
