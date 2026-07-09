# Test Case: Detect PowerShell Command in HTTP

## Rule Details

```snort
alert tcp any any -> any 80 (msg:"Powershell Command in HTTP"; content:"powershell"; sid:1000010; rev:2;)
```

- **SID:** 1000010  
- **Description:** Detects the keyword `powershell` in HTTP traffic, which may indicate a malicious PowerShell command being sent to a web server.  
- **Trigger:** Any HTTP packet containing the string `powershell`

---

## Test Environment

- **OS:** Kali Linux VM  
- **Snort Version:** 2.9.20  
- **Interface:** `eth0`  
- **Snort Config Path:** `/home/kali/snort-custom-rules/snort.conf`  
- **Log Path:** `/home/kali/snort-custom-rules/logs/alerts_powershell.txt`  
- **Traffic Source:** Host machine using `curl`  
- **HTTP Listener:** Custom Python server

---

## Steps to Reproduce

### 1. Create and Run an HTTP POST Listener on the VM

Create a file called `http_post_server.py` with the following content:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class MyHandler(BaseHTTPRequestHandler):
    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_body = self.rfile.read(content_length)
        print("Received POST data:", post_body.decode())
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK")

httpd = HTTPServer(("0.0.0.0", 80), MyHandler)
print("Listening on port 80 for POST requests...")
httpd.serve_forever()
```

Run it with root privileges:

```bash
sudo python3 http_post_server.py
```

---

### 2. Run Snort on the VM

```bash
sudo snort -i eth0 -c /home/kali/snort-custom-rules/snort.conf \
-l /home/kali/snort-custom-rules/logs -A fast
```

---

### 3. From the Host, Simulate Malicious POST Request

```bash
curl -X POST -d "powershell -EncodedCommand evil" http://<VM_IP>
```

---

### 4. View Alert Log

```bash
cat /home/kali/snort-custom-rules/logs/alert
```

Then archive:

```bash
cp /home/kali/snort-custom-rules/logs/alert /home/kali/snort-custom-rules/logs/archived_logs/alerts_powershell.txt
rm /home/kali/snort-custom-rules/logs/alert
```

---

## Expected Output

```
[**] [1:1000010:2] Powershell Command in HTTP [**] [Priority: 0] {TCP} 192.168.84.1:<port> -> 192.168.84.130:80
```

---

## Screenshots

- `screenshots/powershell/powershell_test_host.png` — Host running `curl`  
- `screenshots/powershell/powershell_test_VM.png` — Snort terminal output

---

## Notes

- No decoding is required—this matches the literal string `powershell` in the payload.
- Useful for detecting obfuscated payloads in attack chains involving encoded PowerShell.
