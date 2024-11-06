# Creating the Certificate Authority (CA)

This guide creates the Certificate Authority (CA) files needed for SSL setup.

## Create CA Certificate and Private Key

```bash
# Create SSL directory
mkdir ssl
cd ssl 

# Generate CA certificate and private key
openssl req -new -newkey rsa:4096 -days 365 -x509 \
    -subj "/CN=Kafka-Security-CA" \
    -keyout ca-key \
    -out ca-cert \
    -nodes

# Verify the certificates
cat ca-cert
cat ca-key
keytool -printcert -v -file ca-cert
```

## Verify Certificate (Optional)

You can verify the certificate format using this command:

```bash
echo | openssl s_client -connect www.google.com:443 2>/dev/null | \
    openssl x509 -noout -text \
    -certopt no_header,no_version,no_serial,no_signame,no_pubkey,no_sigdump,no_aux \
    -subject -nameopt multiline -issuer
```