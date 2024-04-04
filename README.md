# keycloak-adfs

Demo on identity brokering with RHBK and ADFS.

Scenario 1: RHBK as Identity Broker, ADFS as IdP

Scenario 2: RHBK as IdP, ADFS as Identity Broker

## Prerequisites
1. ADFS deployed on a Windows Server
2. ADFS TLS X.509 certificate and private key in PEM format
If needed, convert a PKCS#12 certificate to PEM:
% openssl pkcs12 -in adfs-cert.pfx -out adfs-cert.pem -clcerts -nokeys
% openssl pkcs12 -in adfs-cert.pfx -out adfs-key.pem -nocerts -nodes

## To deploy RHBK on Openshift using the Operator

### Create ephemeral Postgresql DB
% oc apply -f ./postgres/postgres.yml

### Create secret for DB user and password
% oc create secret generic keycloak-db-secret \
  --from-literal=username=[your_database_username] \
  --from-literal=password=[your_database_password]

### Create TLS secret for outbound requests from RHBK
% openssl req -subj '/CN=test.keycloak.org/O=Test Keycloak./C=US' -newkey rsa:2048 -nodes -keyout keycloak-key.pem -x509 -days 365 -out keycloak-cert.pem
% oc create secret tls keycloak-tls-secret --cert keycloak-cert.pem --key keycloak-key.pem

### Create a TLS secret from the ADFS cert for inbound requests to RHBK from ADFS
% oc create secret tls adfs-tls-secret --cert adfs-cert.pem --key adfs-key.pem

### Deploy Keycloak
% oc apply -f ./keycloak/keycloak.yaml




