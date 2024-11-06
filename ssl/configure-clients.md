# Configuring Kafka Clients for SSL

## Set Up Client Environment
```bash
export CLIPASS=clientpass
cd ~
mkdir ssl
cd ssl
```

## Import CA Certificate
```bash
# Copy CA cert from server
scp -i ~/kafka-security.pem ubuntu@your-public-DNS:/home/ubuntu/ssl/ca-cert .

# Create client truststore
keytool -keystore kafka.client.truststore.jks \
    -alias CARoot \
    -import -file ca-cert \
    -storepass $CLIPASS \
    -keypass $CLIPASS \
    -noprompt

# Verify truststore
keytool -list -v -keystore kafka.client.truststore.jks
```

## Test SSL Connection

### Producer Test
```bash
# Test with SSL config
~/kafka/bin/kafka-console-producer.sh \
    --broker-list your-public-DNS:9093 \
    --topic kafka-security-topic \
    --producer.config ~/ssl/client.properties

# Test without SSL (should fail)
~/kafka/bin/kafka-console-producer.sh \
    --broker-list your-public-DNS:9093 \
    --topic kafka-security-topic
```

### Consumer Test
```bash
~/kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server your-public-DNS:9093 \
    --topic kafka-security-topic \
    --consumer.config ~/ssl/client.properties
```