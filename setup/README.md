# Apache Kafka Setup Demo

This repository demonstrates the setup of Apache Kafka on an EC2 instance, intended for use in a future security lab.

## Prerequisites

- AWS account
- EC2 instance (Ubuntu 16.04, `t2.large`)
- SSH access configured with your key pair
- Software packages: `wget`, `net-tools`, `netcat`, `tar`, `openjdk-8-jdk`

## Installation Steps

### 1. EC2 Setup
Set up an Ubuntu 16.04 instance with a `t2.large` configuration in AWS, configure SSH access, and connect to your instance.

### 2. Install Kafka
Run the installation script:
```bash
chmod +x scripts/install_kafka.sh
./scripts/install_kafka.sh
```

### 3. Configure Services

1. Copy service files to systemd:
```bash
sudo cp systemd/zookeeper.service /etc/systemd/system/
sudo cp systemd/kafka.service /etc/systemd/system/
sudo systemctl enable zookeeper
sudo systemctl enable kafka
```

2. Update Kafka configuration:
   - Edit `config/server.properties`
   - Set `advertised.listeners` to your instance's public DNS
   - Set `zookeeper.connect` to your instance's public DNS

### 4. Configure AWS Security Group

Add the following inbound rules:
- Port 2181 (Zookeeper)
- Port 9092 (Kafka)

### 5. Start Services
```bash
sudo systemctl start zookeeper
sudo systemctl start kafka
```

Verify services are running:
```bash
sudo systemctl status zookeeper
sudo systemctl status kafka
```

## Testing

### 1. Test Zookeeper
```bash
echo "ruok" | nc localhost 2181
```

### 2. Test Kafka Topics
```bash
# Create topic
~/kafka/bin/kafka-topics.sh --zookeeper localhost:2181 \
    --create --topic kafka-security-topic \
    --replication-factor 1 --partitions 2

# Describe topic
~/kafka/bin/kafka-topics.sh --zookeeper localhost:2181 \
    --describe --topic kafka-security-topic
```

### 3. Test Producer and Consumer
```bash
# Start producer
~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 \
    --topic kafka-security-topic

# Start consumer (in a new terminal)
~/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
    --topic kafka-security-topic
```

## External Access

To connect from external clients:
1. Install Kafka on your local machine
2. Use the EC2 instance's public DNS in your client configuration
3. Ensure your Security Group rules allow incoming connections

## Configuration Files

Relevant configuration files are available in this repository:
- `scripts/install_kafka.sh`: Installation script
- `config/server.properties`: Kafka broker configuration
- `config/zookeeper.properties`: Zookeeper configuration
- `systemd/kafka.service`: Kafka systemd service file
- `systemd/zookeeper.service`: Zookeeper systemd service file