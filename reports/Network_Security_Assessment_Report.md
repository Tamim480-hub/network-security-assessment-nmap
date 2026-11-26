# Network Security Assessment Using Nmap and Kali Linux

**Project Type:** Educational Cybersecurity Lab  
**Assessment Date:** 26 November 2026  
**Operating Environment:** Kali Linux  
**Target:** Localhost (127.0.0.1)  
**Assessment Tool:** Nmap 7.991  
**Project Level:** Beginner

---

## 1. Executive Summary

This project demonstrates a basic network security assessment performed in a Kali Linux environment using Nmap and other Linux command-line tools.

The assessment focused on identifying open TCP ports, detecting running services, investigating listening processes, analyzing HTTP responses, checking installed software versions, and applying available security updates.

During the detailed scan, one open TCP port (45131) was identified. Further investigation associated this port with a Golang-based HTTP server running under the containerd process.

The service was found listening on the localhost interface (127.0.0.1). The installed containerd package was also found to be behind the available Kali repository version and was subsequently upgraded.

The project provided practical experience in network reconnaissance, service identification, software version analysis, and basic security remediation.

## 2. Project Objectives

The main objectives were:

- Identify active TCP ports on the local system.
- Detect running services and service versions.
- Investigate processes associated with open ports.
- Examine HTTP server responses.
- Identify outdated software packages.
- Apply available software updates.
- Verify package remediation.

## 3. Tools and Technologies

| Tool | Purpose |
|---|---|
| Kali Linux | Security testing environment |
| Nmap 7.991 | Network and port scanning |
| ss | Listening port investigation |
| curl | HTTP response testing |
| dpkg | Installed package verification |
| apt | Package management and updates |
| containerd | Identified container runtime |

## 4. Scope of Assessment

The assessment was restricted to the local machine.

**Target IP:** 127.0.0.1

**Target Type:** Localhost

**Scan Type:** TCP port scanning and service detection

**Authorization:** Personal educational lab environment.

No external systems or unauthorized networks were targeted.

## 5. Methodology

The assessment was performed in the following stages:

1. Initial port scanning.
2. Full TCP port scanning.
3. Service and version detection.
4. Listening process investigation.
5. HTTP response analysis.
6. Software package investigation.
7. Package update and remediation.
8. Verification of the updated package.

## 6. Initial Network Scan

The initial scan was performed using:

```bash
nmap -sV 127.0.0.1
```

### Observed Results

- The localhost host was reachable.
- Nmap scanned its default 1,000 TCP ports.
- All 1,000 scanned ports were reported closed.
- No open TCP ports were identified in this initial scan.

This established an initial baseline of the local system.

## 7. Detailed Network Scan

A full TCP port scan was performed using:

```bash
sudo nmap -sS -sV -O -p- -oN scans/detailed_scan.txt 127.0.0.1
```

### Scan Parameters

| Parameter | Description |
|---|---|
| -sS | TCP SYN scan |
| -sV | Service version detection |
| -O | Operating system detection |
| -p- | Scan all TCP ports |
| -oN | Save output in normal format |

### Observed Results

| Port | State | Service | Detection |
|---|---|---|---|
| 45131/tcp | Open | HTTP | Golang net/http server |

Nmap identified one open TCP port.

The operating system detection did not produce an exact OS match.

The service fingerprint was not fully recognized by Nmap.

## 8. Open Port Investigation

The following command was used to investigate the process associated with port 45131:

```bash
sudo ss -ltnp | grep 45131
```

### Observed Output

```text
LISTEN 0 4096 127.0.0.1:45131
users:(("containerd",pid=738,fd=11))
```

### Analysis

The investigation revealed:

- Port number: 45131
- Protocol: TCP
- State: LISTEN
- Listening address: 127.0.0.1
- Associated process: containerd
- Process ID: 738

The service was bound to the localhost interface rather than all network interfaces.

This indicates that the listening socket was configured for local access.

## 9. HTTP Response Analysis

The HTTP server was tested using:

```bash
curl -i http://127.0.0.1:45131/
```

### Observed Response

```http
HTTP/1.1 404 Not Found
Date: Thu, 26 Nov 2026 14:03:22 GMT
Content-Length: 19
Content-Type: text/plain; charset=utf-8

404: Page Not Found
```

### Analysis

The HTTP response confirmed that the server was responding to HTTP requests.

The 404 status indicates that the requested root path was not found.

A 404 response does not independently indicate a security vulnerability.

## 10. Software Version Investigation

The associated software was investigated using:

```bash
containerd --version
```

Observed output:

```text
containerd github.com/containerd/containerd/v2 2.1.9+unknown
```

The installed Debian package was checked using:

```bash
dpkg -l | grep containerd
```

Initial package version:

```text
containerd 2.1.9+ds1-1 amd64
```

The available repository version was checked using:

```bash
apt-cache policy containerd
```

Initial result:

```text
Installed: 2.1.9+ds1-1
Candidate: 2.1.9+ds1-5
```

### Analysis

The installed package version was older than the candidate version available in the Kali Linux rolling repository.

This indicated that a package update was available.

An outdated package should be investigated against relevant security advisories before determining whether it is vulnerable.

## 11. Security Remediation

The containerd package was updated using:

```bash
sudo apt update
sudo apt install containerd
```

The package update completed successfully.

The installed package was checked again:

```bash
dpkg -l | grep containerd
```

Updated package:

```text
containerd 2.1.9+ds1-5 amd64
```

Repository verification:

```bash
apt-cache policy containerd
```

Final result:

```text
Installed: 2.1.9+ds1-5
Candidate: 2.1.9+ds1-5
```

### Remediation Analysis

The installed package version now matches the available repository candidate version.

This confirms that the package update was successfully applied.

The `containerd --version` command continued to display `2.1.9+unknown`. This can happen because the binary version string and distribution package revision are different.

For Debian package verification, the `dpkg` and `apt-cache` outputs are the relevant evidence.

## 12. Security Findings Summary

| Finding | Observation | Assessment |
|---|---|---|
| Open TCP port | 45131 | Service identified |
| HTTP service | Golang net/http | Responding |
| Process | containerd | Identified |
| Network binding | 127.0.0.1 | Localhost-only |
| HTTP response | 404 Not Found | No vulnerability established |
| Package version | 2.1.9+ds1-1 | Update available |
| Package remediation | 2.1.9+ds1-5 | Update verified |

### Overall Assessment

The assessment identified a locally accessible HTTP service associated with containerd.

An available package update was identified and successfully installed.

No confirmed exploitable vulnerability was established through the performed tests.

## 13. Security Recommendations

The following recommendations are suggested:

1. Keep containerd updated through trusted repositories.
2. Regularly review listening TCP ports.
3. Disable unnecessary services when they are not required.
4. Keep internal services bound to localhost when external access is unnecessary.
5. Monitor container runtime processes and logs.
6. Review container runtime configurations periodically.
7. Perform regular vulnerability assessments.

## 14. Limitations

This was a basic educational assessment performed on a single local machine.

The assessment did not include:

- External network penetration testing.
- Authenticated vulnerability scanning.
- Container escape testing.
- Exploit validation.
- Comprehensive container configuration auditing.
- Full operating system security auditing.

Therefore, the results should not be interpreted as a complete security certification.

## 15. Conclusion

This project provided practical experience in network security assessment using Kali Linux and Nmap.

The assessment included TCP port scanning, service identification, process investigation, HTTP response analysis, software package investigation, and security remediation.

One open TCP port was identified and associated with the containerd process. The service was found listening on the localhost interface.

An available containerd package update was successfully installed and verified.

Overall, this project strengthened practical knowledge of network reconnaissance, Linux security tools, service investigation, and basic vulnerability assessment workflows.

## 16. Evidence and Project Files

The following evidence files are included in the project repository:

```text
Network-Security-Assessment/
│
├── scans/
│   ├── localhost.txt
│   └── detailed_scan.txt
│
├── reports/
│   └── Network_Security_Assessment_Report.md
│
├── screenshots/
│
└── README.md
```

Screenshots of the scan results, service investigation, and package update verification can be added to the screenshots directory.

---

**Project:** Network Security Assessment Using Nmap and Kali Linux  
**Environment:** Personal Kali Linux Lab  
**Purpose:** Educational Cybersecurity Portfolio  
**Status:** Package update verified; final service verification pending.
