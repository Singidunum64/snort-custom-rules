# Test Case: Telnet Connection Attempt Detection

## Rule Details

```snort
alert tcp any any -> any 23 (msg:"Telnet Connection Attempt"; flags:S; sid:1000009; rev:1;)
```

- **SID:** 1000009  
- **Description:** Detects TCP SYN packets sent to port 23 (Telnet), commonly associated with legacy or insecure remote access.  

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Telnet Service:** `telnetd` via `inetd`  
- **Traffic Source:** Host machine  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_telnet.txt`

---

## Steps to Reproduce

1. **Start Telnet Service on VM:**

    Edit the `inetd` config:
    ```bash
    sudo nano /etc/inetd.conf
    ```

    Add or uncomment:
    ```
    telnet stream tcp nowait root /usr/sbin/tcpd /usr/sbin/in.telnetd
    ```

    Restart the service:
    ```bash
    sudo systemctl restart inetd
    ```

    Confirm it’s listening:
    ```bash
    sudo netstat -tulnp | grep :23
    ```

2. **Start Snort on the VM:**

    ```bash
    sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
    -l /home/kali/snort-custom-rules/logs -A fast
    ```

3. **From Host, simulate Telnet connection:**

    ```bash
    nc -zv <VM_IP> 23
    ```

4. **Check Snort alert log:**

    ```bash
    cat /home/kali/snort-custom-rules/logs/alert
    ```

5. **Archive the alert log:**

    ```bash
    cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_telnet.txt
    rm /home/kali/snort-custom-rules/logs/alert
    ```

---

## Expected Output

Example alert:
```
[**] [1:1000009:1] Telnet Connection Attempt [**] [Priority: 0] {TCP} 192.168.84.1:xxxxx -> 192.168.84.130:23
```

---

## Screenshots

- `screenshots/telnet/telnet_test_host.png` – Host running `nc -zv`
- `screenshots/telnet/telnet_test_VM.png` – VM terminal with Snort alert

---

## Post-Test Cleanup

Disable Telnet for security:

```bash
sudo systemctl stop inetd
```

Or comment out the Telnet line in `/etc/inetd.conf` and restart `inetd`.

---

## Notes

- Telnet is insecure and mostly deprecated.
- This test demonstrates Snort's ability to detect connections to legacy services.
