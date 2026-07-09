# Test Case: Nmap SYN Scan Detection

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any any (flags:S; msg:"Nmap TCP SYN scan detected"; sid:1000002; rev:1;)
```

**Description:**  
Detects TCP SYN packets typically used in stealthy Nmap scans.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `lo` (loopback)  
- **Traffic Tool:** `nmap`  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_nmap.txt`

---

## Steps to Reproduce

1. **Start Snort** in Terminal 1:

    ```bash
    sudo snort -c /home/kali/snort-custom-rules/snort.conf -i lo -l /home/kali/snort-custom-rules/logs -A fast
    ```

2. **Run an Nmap SYN scan** in Terminal 2:

    ```bash
    sudo nmap -sS 127.0.0.1
    ```

3. **Stop Snort** in Terminal 1 (use `Ctrl + C` or `sudo kill -9 <PID>` if needed).

4. **View Snort alerts:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_nmap.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Snort should generate alerts like:

```
[**] [1:1000002:1] Nmap TCP SYN scan detected [**] [Priority: 0] {TCP} 127.0.0.1:63401 -> 127.0.0.1:1025
```

---

## Screenshot

- `screenshots/nmap/nmap_test.png` – Nmap scan alert from Snort
