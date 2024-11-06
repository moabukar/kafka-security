# Configuring Kafka with SSL

## Set Environment Variables
```bash
export SRVPASS=serversecret
cd ssl
```

## Create Server Certificate
```bash
# Generate keystore (Replace with your EC2 DNS)
keytool -genkey \
    -keystore kafka.server.keystore.jks \
    -validity 365 \
    -storepass $SRVPASS \
    -keypass $SRVPASS \
    -dname "CN=ec2-18-196-169-2.eu-central-1.compute.amazonaws.com" \
    -storetype pkcs12

# Verify keystore
keytool -list -v -keystore kafka.server.keystore.jks
```

## Create and Sign Certificate Request
```bash
# Create certificate request
keytool -keystore kafka.server.keystore.jks \
    -certreq -file cert-file \
    -storepass $SRVPASS \
    -keypass $SRVPASS

# Sign the certificate
openssl x509 -req \
    -CA ca-cert \
    -CAkey ca-key \
    -in cert-file \
    -out cert-signed \
    -days 365 \
    -CAcreateserial \
    -passin pass:$SRVPASS
```

## Create Truststore and Import Certificates
```bash
# Create truststore and import CA cert
keytool -keystore kafka.server.truststore.jks \
    -alias CARoot \
    -import -file ca-cert \
    -storepass $SRVPASS \
    -keypass $SRVPASS \
    -noprompt

# Import CA and signed cert into keystore
keytool -keystore kafka.server.keystore.jks \
    -alias CARoot \
    -import -file ca-cert \
    -storepass $SRVPASS \
    -keypass $SRVPASS \
    -noprompt

keytool -keystore kafka.server.keystore.jks \
    -import -file cert-signed \
    -storepass $SRVPASS \
    -keypass $SRVPASS \
    -noprompt
```

## Configure and Restart Kafka
1. Update server.properties with SSL configurations
2. Restart Kafka:
```bash
sudo systemctl restart kafka
sudo systemctl status kafka

# Verify SSL endpoint
sudo grep "EndPoint" /home/ubuntu/kafka/logs/server.log
```

## Verify SSL Setup
```bash
openssl s_client -connect your-public-DNS:9093
```