# Test Case: FTP Login Attempt Detection

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 21 (msg:"FTP login attempt detected"; content:"USER "; nocase; sid:1000004; rev:1;)
```

**Description:**  
Triggers when the `USER` keyword (used in FTP authentication) is detected in traffic to port 21. Case-insensitive due to `nocase`.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **FTP Server:** `vsftpd`  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_ftp.txt`

---

## Steps to Reproduce

1. **Start the FTP service** on the VM:

    ```bash
    sudo systemctl start vsftpd
    ```

2. **Run Snort** in IDS mode on the VM:

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **Initiate FTP login** from the host machine:

    ```bash
    ftp <VM_IP>
    ```

4. **Enter a username** like:

    ```
    Name (<VM_IP>:anonymous): anonymous
    ```

5. **Check Snort alert log:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

6. **Archive the alert:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_ftp.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Example Snort alert:

```
[**] [1:1000004:1] FTP login attempt detected [**] [Priority: 0] {TCP} 192.168.84.1:53044 -> 192.168.84.130:21
```

---

## Screenshots

- `screenshots/ftp/ftp_test_host.png` – FTP login attempt from host  
- `screenshots/ftp/ftp_test_VM.png` – Snort terminal detecting the alert

---

## Notes

- You can change the `USER` keyword to `PASS` or others to detect different stages of the FTP login sequence.
