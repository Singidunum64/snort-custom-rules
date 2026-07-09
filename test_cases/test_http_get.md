# Test Case: HTTP GET Request Detection

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 80 (msg:"HTTP GET Request Detected"; content:"GET /"; sid:1000005; rev:2;)
```

**Description:**  
Detects basic HTTP GET requests sent to port 80, commonly used by browsers and tools like `curl`.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Web Server:** Python built-in HTTP server  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_http_get.txt`

---

## Steps to Reproduce

1. **Start a simple HTTP server** on the VM:

    ```bash
    sudo python3 -m http.server 80
    ```

2. **Run Snort** in a separate terminal on the VM:

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **From the host machine**, send a basic GET request:

    ```bash
    curl http://<VM_IP>
    ```

4. **View the alert log**:

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log**:

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_http_get.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Snort should log an alert like:

```
[**] [1:1000005:2] HTTP GET Request Detected [**] [Priority: 0] {TCP} 192.168.84.1:38626 -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/http_get/http_get_test_host.png` – Host sending the GET request  
- `screenshots/http_get/http_get_test_VM.png` – Snort terminal capturing the alert

---

## Notes

- This rule is very general — it can be customized further to match specific URI patterns or hosts.
