# Snort XSS Detection Lab

## Repository Structure

```text
Snort-XSS-Detection-Lab/
├── README.md
├── rules/
│   └── local.rules
├── webapp/
│   └── test.php
├── report/
│   └── snort-xss-detection-report.md
└── video/
    
```

## README.md

# Snort XSS Detection Lab

## Overview
This project demonstrates a Snort-based detection lab built in VirtualBox to identify Cross-Site Scripting (XSS) attack patterns in HTTP traffic. The lab uses Kali Linux as the attacker machine and Ubuntu as the target web server and Snort sensor. Apache and PHP were configured on port 8080, and custom Snort rules were created to detect suspicious XSS-related payloads.

## Objective
- Build a web attack detection lab for XSS testing
- Write custom Snort rules for malicious HTTP payload detection
- Simulate benign and malicious traffic from Kali Linux
- Validate Snort alert generation on Ubuntu
- Document the results for blue-team portfolio use

## Lab Environment
- Hypervisor: VirtualBox
- Attacker VM: Kali Linux
- Target VM: Ubuntu
- IDS: Snort
- Web Server: Apache2
- PHP Processing: PHP
- Port: 8080
- Protocol: HTTP

## Network Configuration
- Kali Linux: 192.168.100.10
- Ubuntu Internal IP: 192.168.100.20
- Ubuntu NAT IP: 10.0.3.15
- Internal Network: SOC-LAB

Traffic path monitored during testing:

`Kali (192.168.100.10) → Ubuntu Apache/Snort (192.168.100.20:8080)`

## Web Application
A simple PHP page was created to reflect user-supplied input through the `q` parameter.

### webapp/test.php
```php
<?php
$q = $_GET['q'] ?? '';
echo "<html><body>";
echo "<h1>Search Result</h1>";
echo "You searched for: " . $q;
echo "</body></html>";
?>
````

## Snort Rules

Custom Snort rules were created to detect common XSS-related strings in HTTP requests to port 8080.

### rules/local.rules

```snort
alert tcp any any -> any 8080 (msg:"XSS Attack - Script Tag Detected"; content:"<script>"; sid:3000001; rev:1;)
alert tcp any any -> any 8080 (msg:"XSS Attack - alert() detected"; content:"alert("; sid:3000002; rev:1;)
alert tcp any any -> any 8080 (msg:"XSS Attack - onerror event detected"; content:"onerror"; sid:3000003; rev:2;)
```

## Testing Process

### 1. Baseline Test

A normal HTTP request was sent first to confirm benign traffic did not trigger alerts.

```bash
curl http://192.168.100.20:8080/index.html
```

Result:

* No alert generated
* Clean baseline confirmed

### 2. XSS Payload Test 1

```bash
curl -G --data-urlencode 'q=<script>alert(1)</script>' "http://192.168.100.20:8080/test.php"
```

Expected detection:

* XSS Attack - Script Tag Detected
* XSS Attack - alert() detected

### 3. XSS Payload Test 2

```bash
curl -G --data-urlencode 'q=<img src=x onerror=alert(1)>' "http://192.168.100.20:8080/test.php"
```

Expected detection:

* XSS Attack - onerror event detected
* May also trigger alert() depending on payload processing

### 4. XSS Payload Test 3

```bash
curl -G --data-urlencode 'q=<script>document.cookie</script>' "http://192.168.100.20:8080/test.php"
```

Expected detection:

* XSS Attack - Script Tag Detected

## Validation Steps

Snort configuration was tested before live monitoring:

```bash
sudo snort -T -c /etc/snort/snort.conf
```

Snort was then started in live console mode:

```bash
sudo snort -A console -c /etc/snort/snort.conf -i enp0s3
```

## Key Results

* Verified communication between Kali and Ubuntu over the internal SOC-LAB network
* Configured Apache and PHP successfully on port 8080
* Created a test PHP page to reflect input in HTTP requests
* Built and validated custom Snort rules for XSS-style payloads
* Confirmed benign traffic remained quiet
* Confirmed malicious payloads triggered Snort alerts
* Demonstrated iterative rule tuning for the `onerror` pattern

## MITRE ATT&CK Mapping

* **T1190 – Exploit Public-Facing Application**

  * **Tactic:** Initial Access

This lab simulates malicious web requests targeting an exposed application and demonstrates signature-based detection of suspicious payloads.

## Skills Demonstrated

* Network traffic inspection
* Snort rule creation and validation
* Apache and PHP configuration
* HTTP request testing with curl
* Detection engineering basics
* Alert validation and troubleshooting
* Blue-team documentation

## Demo Video

A full walkthrough of the lab setup, rule validation, traffic generation, and alert detection is included here:

**Demo Video:** `PASTE_YOUR_VIDEO_LINK_HERE`

## Why This Project Matters

This project demonstrates practical blue-team skills by combining attack simulation, web traffic monitoring, custom IDS rule creation, and alert validation in a controlled lab environment. It shows the ability to build and document a small but realistic detection workflow that aligns with SOC analyst responsibilities.

````

## rules/local.rules

```snort
alert tcp any any -> any 8080 (msg:"XSS Attack - Script Tag Detected"; content:"<script>"; sid:3000001; rev:1;)
alert tcp any any -> any 8080 (msg:"XSS Attack - alert() detected"; content:"alert("; sid:3000002; rev:1;)
alert tcp any any -> any 8080 (msg:"XSS Attack - onerror event detected"; content:"onerror"; sid:3000003; rev:2;)
````

## webapp/test.php

```php
<?php
$q = $_GET['q'] ?? '';
echo "<html><body>";
echo "<h1>Search Result</h1>";
echo "You searched for: " . $q;
echo "</body></html>";
?>
```

## report/snort-xss-detection-report.md

```md
# Snort XSS Detection Report

## Summary
A controlled XSS detection lab was built using Kali Linux and Ubuntu in VirtualBox. Snort was configured on the Ubuntu target to inspect HTTP traffic on port 8080. Custom rules were written to detect script tags, JavaScript alert calls, and onerror-based event injection patterns.

## Findings
- Baseline traffic generated no alert
- Script-tag payloads triggered detection successfully
- alert() payloads triggered detection successfully
- onerror-based detection required rule tuning, which reflects a realistic signature-development workflow

## Conclusion
The lab successfully demonstrated end-to-end detection of XSS-style payloads using Snort and reinforced practical skills in IDS rule development, testing, and alert validation.
```

## video/demo-link.txt

```text
Paste your YouTube or GitHub-hosted video link here.
```

## Suggested GitHub Repo Name

`Snort-XSS-Detection-Lab`

## Suggested Commit Message

`Add Snort XSS detection lab with custom rules, PHP test page, and report`
