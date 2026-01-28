## Case 1: Website Not Reachable (when HTTP is Blocked)

### Symptoms
- Browser timeout when accessing EC2 public IP

### Investigation
- Verified EC2 instance was running
- Checked security group inbound rules
- Observed port 80 (HTTP) was missing

### Root Cause
Inbound HTTP traffic was blocked at the security group level.

### Resolution
Added an inbound rule allowing HTTP (port 80).

### Prevention
Validate required inbound ports before deploying public-facing services.
