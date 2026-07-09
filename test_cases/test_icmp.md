# Test Case: ICMP Ping Detection

## Rule Information

**Snort Rule:**
```snort
alert icmp any any -> any any (msg:"ICMP Ping detected"; sid:1000001; rev:1;)
```

**Description:**  
Detects ICMP echo requests (ping). Useful for identifying ping sweeps or simple host discovery attempts.

---

## Test Environment

- **OS:** Kali Linux (VM)
- **Snort Version:** 2.9.20
- **Interface:** `lo` (loopback)
- **Traffic Tool:** `ping`, `tcpdump`
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_icmp.txt`

---

## Steps to Reproduce

1. **Capture ICMP traffic** in Terminal 1:

    ```bash
    sudo tcpdump -i lo icmp -w /home/kali/snort-custom-rules/pcaps/icmp_test.pcap -s 0 -U -y EN10MB
    ```

2. **Send 5 ping requests** in Terminal 2:

    ```bash
    ping -c 5 127.0.0.1
    ```

3. **Stop tcpdump** using `Ctrl + C`.

4. **Run Snort** on the captured traffic:

    ```bash
    ./run_snort.sh
    ```

5. **View the alert output:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

6. **Archive the alert log:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_icmp.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Snort should generate about 10 alerts (5 requests + 5 replies):

```
[**] [1:1000001:1] ICMP Ping detected [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
```

---

## 📸 Screenshots

- `screenshots/icmp/icmp_test.png` – Snort alert shown in terminal
