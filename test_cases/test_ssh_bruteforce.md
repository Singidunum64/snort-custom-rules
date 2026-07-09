# Test Case: SSH Brute Force Attempt Detection

## Rule Information

**Snort Rule:**
```snort
alert tcp any any -> any 22 (msg:"Possible SSH brute-force"; flags:S; detection_filter:track by_src, count 5, seconds 60; sid:1000003; rev:2;)
```

**Description:**  
Detects multiple TCP SYN packets to port 22 from the same IP within 60 seconds, indicating a brute-force login attempt.

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Service:** SSH (`sudo systemctl start ssh`)  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_ssh_bruteforce.txt`

---

## Steps to Reproduce

1. **Ensure SSH is running** on the VM:

    ```bash
    sudo systemctl start ssh
    ```

2. **Start Snort** on the VM:

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **Simulate brute-force attempts** from the host machine:

    ```bash
    for i in {1..6}; do nc -zv <VM_IP> 22; done
    ```

4. **Check Snort logs:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_ssh_bruteforce.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Example Snort alert:

```
[**] [1:1000003:2] Possible SSH brute-force [**] [Priority: 0] {TCP} 192.168.84.1:44908 -> 192.168.84.130:22
```

---

## Screenshots

- `screenshots/ssh_bruteforce/ssh_bruteforce_test_host.png` – Host sending 6 probes  
- `screenshots/ssh_bruteforce/ssh_bruteforce_test_VM.png` – Snort detects brute-force attempt

---

## 🔍 Notes

- You can adjust the detection threshold by modifying `count` and `seconds` in the rule.
