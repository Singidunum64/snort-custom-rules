# Test Case: Detect RDP Connection Attempt

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 3389 (msg:"RDP Connection Attempt"; flags:S; sid:1000008; rev:1;)
```

**Description:**  
Detects TCP SYN packets targeting port 3389 (used by Remote Desktop Protocol). Useful for spotting scans or unauthorized RDP access attempts.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Source:** Host machine (simulating the scan)  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_rdp.txt`

---

## Steps to Reproduce

1. **Ensure rule is active** in your `custom.rules` file:

    ```snort
    alert tcp any any -> any 3389 (msg:"RDP Connection Attempt"; flags:S; sid:1000008; rev:1;)
    ```

2. **Start Snort on the VM:**

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
    -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **From the host machine, simulate RDP probe:**

    ```bash
    nc -zv <VM_IP> 3389
    ```

4. **View the alert:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_rdp.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Example alert in Snort log:

```
[**] [1:1000008:1] RDP Connection Attempt [**] [Priority: 0] {TCP} 192.168.84.1:58052 -> 192.168.84.130:3389
```

---

## Screenshots

- `screenshots/rdp/rdp_test_host.png` – Host running `nc`
- `screenshots/rdp/rdp_test_VM.png` – VM terminal with Snort alert

---

## 🔍 Notes

- This test works without a real RDP service running.
- Only a SYN packet (`nc -z`) is required to match the rule.
