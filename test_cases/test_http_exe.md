# Test Case: Suspicious EXE File Download Detection

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 80 (msg:"Suspicious EXE file download"; content:".exe"; sid:1000006; rev:3;)
```

**Description:**  
Detects the presence of `.exe` in HTTP traffic, which may indicate an attempted download of an executable.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Web Server:** Python HTTP server  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_http_exe.txt`

---

## Steps to Reproduce

1. **Create a dummy `.exe` file** and start a web server on the VM:

    ```bash
    echo "Dummy EXE" > trojan.exe
    sudo python3 -m http.server 80
    ```

2. **Run Snort** in a separate terminal on the VM:

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **From the host machine**, simulate downloading the file:

    ```bash
    curl http://<VM_IP>/trojan.exe
    ```

4. **Check the Snort alert log**:

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log**:

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_http_exe.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Snort should trigger an alert when `.exe` is detected in the HTTP traffic:

```
[**] [1:1000006:3] Suspicious EXE file download [**] [Priority: 0] {TCP} 192.168.84.1:42472 -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/http_exe/http_exe_test_host.png` – Host machine sending the curl request  
- `screenshots/http_exe/http_exe_test_VM.png` – Snort and web server on the VM

---

## Notes

- This rule is based on payload inspection, so it works without `http_uri` or HTTP-specific preprocessors.
- You can improve precision by using `http_uri` if you later enable `http_inspect`.

