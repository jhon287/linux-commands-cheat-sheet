# Linux Commands Cheat Sheet

## Certificate Management

### Check that private key matches the certificate

```shell
# Certificate
openssl x509 -noout -modulus -in certificate.crt | openssl sha256 -c
35:55:d1:ef:ea:35:65:83:ed:35:80:32:1d:d1:b0:bc:87:5f:2d:2e:0b:43:55:d8:3b:e5:ec:d1:a3:2e:a3:dd

# Private Key
openssl rsa -noout -modulus -in private.key | openssl sha256 -c
35:55:d1:ef:ea:35:65:83:ed:35:80:32:1d:d1:b0:bc:87:5f:2d:2e:0b:43:55:d8:3b:e5:ec:d1:a3:2e:a3:dd
```

```shell
function private_key_matches_certificate() {
  if [[ -z "$1" || -z "$2" ]]; then
    echo "Usage: private_key_matches_certificate <PRIVATE_KEY_FILE> <CERTIFICATE_FILE>"
    return 1
  fi
  
  PK=$(openssl rsa -in "$1" -noout -modulus | openssl sha256)
  CRT=$(openssl x509 -in "$2" -noout -modulus | openssl sha256)
  
  if [[ "${PK}" == "${CRT}" ]]; then
    echo "[👍] Private key and certificate match."
  else
    echo "[👎] Private key and certificate do not match."
  fi
}
```

### Extract private key or certificate from PCKS12

```shell
# Get private key from PKCS12 file
openssl pkcs12 -in certificate.p12 -out private.key -nodes -nocerts
# Get certificate from PKCS12 file
openssl pkcs12 -in certificate.p12 -out certificate.crt -nodes -nokeys
```

### Create PCKS12 file from private key and certificate

```shell
openssl pkcs12 -export -out certificate.p12 -inkey private.key -in certificate.crt -name myalias
```

### Import PCKS12 file to Java Keystore (PKCS12 format)

```shell
keytool -importkeystore -deststoretype PKCS12 -destkeystore keystore.p12 -srckeystore certificate.p12 -srcstoretype PKCS12 -alias myalias
```

### Display certificate interesting details

```shell
openssl x509 -in certificate.crt -inform PEM -noout -subject -issuer -dates
```

## Private keys

### Get Public Key from Private Key

```shell
openssl rsa -in private.key -pubout -out public.key
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
