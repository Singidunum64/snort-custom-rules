# Test Case: SQL Injection Attempt in HTTP URI

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 80 (msg:"SQL Injection Attempt Detected"; content:"or+1=1"; nocase; sid:1000007; rev:2;)
```

**Description:**  
Detects a common SQL injection string (`or 1=1`) within the URI of an HTTP request. The `+` symbol matches a space in URL-encoded format.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Web Server:** Python HTTP server  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_SQL_injection.txt`

---

## Steps to Reproduce

1. **Set up a fake endpoint** to simulate a search form:

    ```bash
    cd /var/www/html
    sudo touch search
    sudo python3 -m http.server 80
    ```

2. **Run Snort** in another terminal:

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
    -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **Simulate SQL Injection attempt from host**:

    ```bash
    curl "http://<VM_IP>/search?q=or+1=1"
    ```

4. **View Snort alert output**:

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log**:

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_sql_injection.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Snort should generate an alert like:

```
[**] [1:1000007:2] SQL Injection Attempt Detected [**] [Priority: 0] {TCP} 192.168.84.1:45520 -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/sqli/SQL_injection_test_host.png` – Host curl command  
- `screenshots/sqli/SQL_injection_test_VM.png` – VM showing triggered Snort alert

---

## Notes

- Without `+`, the rule may not trigger if Snort doesn’t decode spaces (`%20`).
- This rule can be expanded to detect more complex SQLi patterns like `UNION SELECT`, `'1'='1`, etc.
