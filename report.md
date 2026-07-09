# Final Report: Snort Custom Rules Project

## Objective
Design and test a suite of custom Snort IDS rules in a Kali Linux environment to detect various types of potentially malicious network behavior. This project demonstrates practical understanding of intrusion detection logic, traffic analysis, and rule development.

## Environment
- **OS:** Kali Linux (VM)
- **Snort Version:** 2.9.20
- **Tools Used:** Nmap, Ping, curl, netcat, browser, Python HTTP server, FTP server
- **Testing Mode:** IDS using `-i lo` or `-i eth0`, `-A fast`, and alert logging to custom paths

## Rule Summary

| SID       | Description                                   | Protocol | Purpose                                      |
|-----------|-----------------------------------------------|----------|----------------------------------------------|
| 1000001   | ICMP Ping detection                           | ICMP     | Detect ping sweeps or host discovery         |
| 1000002   | Nmap TCP SYN scan detection                   | TCP      | Detect stealthy port scans                   |
| 1000003   | SSH brute-force attempt                       | TCP      | Detect rapid SSH login attempts              |
| 1000004   | FTP login attempt detection                   | TCP      | Flag initial `USER` commands to port 21      |
| 1000005   | HTTP GET request detection                    | TCP      | Identify basic web requests                  |
| 1000006   | Suspicious `.exe` download over HTTP          | TCP      | Detect potential malware downloads           |
| 1000007   | SQL Injection attempt detection               | TCP      | Detect common SQLi patterns in URLs          |
| 1000008   | RDP connection attempt                        | TCP      | Detect connections to port 3389              |
| 1000009   | Telnet usage detection                        | TCP      | Flag insecure Telnet usage                   |
| 1000010   | Powershell command inside HTTP POST body      | TCP      | Detect encoded Powershell payloads           |
| 1000011   | NOP sled shellcode detection                  | IP       | Detect signs of shellcode attacks            |
| 1000012   | Access to `admin.php`                         | TCP      | Detect brute-force or admin panel access     |
| 1000013   | C2 beaconing via `keep-alive` HTTP header     | TCP      | Detect command-and-control behavior          |
| 1000014   | IRC traffic on port 6667                      | TCP      | Flag legacy IRC-based botnet communication   |

## Screenshots
Screenshots for each alert and simulation are located in the `/screenshots/` folder and referenced from their respective test cases.

Example:
![Nmap TCP SYN Scan](screenshots/nmap_test.png)

## Test Cases
Each rule is documented and tested in its own Markdown file inside the `/test_cases/` directory. Test steps, simulation methods, and Snort output are included.

Examples:
- `test_icmp.md`
- `test_nmap.md`
- `test_ssh_bruteforce.md`
- `test_http_get.md`
- …

## Results
- **All 14 rules** successfully triggered alerts in controlled test conditions.
- Logs saved under `/logs/alerts_<name>.txt`
- Each test is reproducible with clear, repeatable instructions.

## Lessons Learned
- Developed deep understanding of Snort rule writing and syntax
- Gained hands-on experience simulating network attacks across multiple protocols
- Practiced tuning detection rules to avoid false positives and ensure hits
- Built a functional and reusable Snort test lab

## Future Improvements
- Expand to IPS testing (inline mode)
- Add Python automation for log parsing and rule validation
- Integrate Wireshark for dual-layer traffic analysis
- Build dashboards for alert visualization using ELK or Splunk
