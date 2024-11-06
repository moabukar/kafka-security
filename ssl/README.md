# Kafka SSL Security Setup Guide

This guide walks through the process of setting up SSL security for Apache Kafka. The setup is divided into three main steps:

1. Creating the Certificate Authority (CA)
2. Configuring Kafka with SSL
3. Configuring Clients to use SSL

## Prerequisites

- Apache Kafka installed
- OpenSSL and Keytool (comes with Java) installed
- Access to both server and client machines
- AWS EC2 instance (if following the exact setup)

## Quick Start

1. First, create the Certificate Authority:
   ```bash
   cd ssl
   ./1-create-ca-certs.md
   ```

2. Configure Kafka with SSL:
   ```bash
   ./2-configure-kafka-ssl.md
   ```

3. Set up client configuration:
   ```bash
   ./3-configure-clients.md
   ```

## File Structure

- `1-create-ca-certs.md`: Instructions for creating the Certificate Authority
- `2-configure-kafka-ssl.md`: Kafka SSL configuration steps
- `3-configure-clients.md`: Client-side SSL setup
- `server.properties`: Kafka broker SSL configuration template
- `client.properties`: Client SSL configuration template

## Security Notes

- Keep all private keys secure
- Use strong passwords for keystores and truststores
- Update the CN (Common Name) in certificates with your actual domain
- Remember to open port 9093 in your security group for SSL connections

## Testing

After setup, you can verify the SSL configuration using:
```bash
openssl s_client -connect your-kafka-server:9093
```