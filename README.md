# Linux Commands Cheat Sheet

## Certificate Management

### Check that private key matches the certificate

```shell
openssl x509 -noout -modulus -in certificate.crt | openssl sha256 -c
35:55:d1:ef:ea:35:65:83:ed:35:80:32:1d:d1:b0:bc:87:5f:2d:2e:0b:43:55:d8:3b:e5:ec:d1:a3:2e:a3:dd
openssl rsa -noout -modulus -in private.key | openssl sha256 -c
35:55:d1:ef:ea:35:65:83:ed:35:80:32:1d:d1:b0:bc:87:5f:2d:2e:0b:43:55:d8:3b:e5:ec:d1:a3:2e:a3:dd
```

## Process Management That Actually Works

```shell
### Find what's using port 3000
lsof -ti:3000 | xargs kill -9

# Or make it a function
killport() {
    lsof -ti:$1 | xargs kill -9
}
# Usage: killport 3000
```

## Memory and CPU investigation

```shell
# Memory usage by process
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head
```

```shell
# Find memory leaks in running processes (refresh every 5s)
PID=12345  # example
watch -n 5 ps -p $PID -o %mem,rss,vsz
```

## Performance Monitoring and System Info

```shell
# Real-time disk usage (refresh every 1s)
watch -n 1 df -h

# Find what's eating your disk space (current directory, human-readable and reverse sorted)
du -h --max-depth=1 | sort -hr

# Process monitoring with context
ps -aux --sort=-%cpu | head -20  # List 20 processes sorted by cpu usage
ps -aux --sort=-%mem | head -20  # List 20 processes sorted by memory usage

# Network
ss -lntp | grep -i listen  # Listen ports
ss -entp | grep -i estab   # Established ports

# Uptime and System load
uptime && top -b | head -n 5
```
