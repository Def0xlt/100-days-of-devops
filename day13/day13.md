# Day 13: IPtables Installation and Configuration

## Task Description
Install iptables on app hosts running Apache on port 5004, restricting access exclusively to the load balancer at 172.16.238.14, with persistent rules surviving reboots.

## Solution Commands

### 1. Installation:
```bash
sudo yum install -y iptables-services
```

### 2. Flush existing rules (optional):
```bash
sudo iptables -F
```

### 3. Configure firewall rules:
```bash
# Allow loopback traffic
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow established and related connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow load balancer access to port 5004
sudo iptables -A INPUT -p tcp -s 172.16.238.14 --dport 5004 -j ACCEPT

# Deny all other traffic to port 5004
sudo iptables -A INPUT -p tcp --dport 5004 -j DROP
```

### 4. Save and enable persistence:
```bash
sudo service iptables save
sudo systemctl enable iptables
sudo systemctl start iptables
```

### 5. Verification:
```bash
sudo iptables -L -n --line-numbers
```

Expected output should show:
```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
3    ACCEPT     tcp  --  172.16.238.14        0.0.0.0/0            tcp dpt:5004
4    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:5004
```

### 6. Test connectivity:
```bash
# From load balancer (should succeed)
curl http://app-server:5004

# From unauthorized host (should timeout)
curl http://app-server:5004
```

## Understanding IPtables Rules

### Rule breakdown:
- `-A INPUT` - Append to INPUT chain
- `-i lo` - Interface loopback
- `-m state --state` - Connection state matching
- `-s 172.16.238.14` - Source IP address
- `--dport 5004` - Destination port
- `-j ACCEPT/DROP` - Jump to target (action)

### Rule order matters:
Rules are processed top-to-bottom. First match wins!

## Notes
- Substitute the example IP (172.16.238.14) with your actual load balancer address
- Multiple load balancers require additional ACCEPT rules
- The flush command removes all existing rules—use cautiously
- Environments using firewalld should implement rules there instead
- Automation tools like Ansible benefit from managing the rules file directly (`/etc/sysconfig/iptables`)
- Rules establish: loopback traffic, stateful connections, LBR access, and default deny
