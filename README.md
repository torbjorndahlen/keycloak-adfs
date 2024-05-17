# keycloak-adfs

Demo on identity brokering with RHBK and ADFS.

RHBK as Identity Broker, ADFS as IdP



## Prerequisites
1. ADFS deployed on a Windows Server
2. ADFS TLS X.509 certificate and private key in PEM format

If needed, convert a PKCS#12 certificate to PEM:

% openssl pkcs12 -in adfs-cert.pfx -out adfs-cert.pem -clcerts -nokeys

% openssl pkcs12 -in adfs-cert.pfx -out adfs-key.pem -nocerts -nodes

## To deploy RHBK on Openshift using the Operator

% oc new-project rhbk

### Create TLS secret containing the ADFS certificate and private key
% oc create secret tls adfs-tls-secret --cert adfs-cert.pem --key adfs-key.pem


### Create TLS secret for outbound requests from RHBK
% openssl req -verbose -subj '/CN=keycloak' -addext subjectAltName=DNS:rhbk-keycloak.apps.cluster-qtbgp.dynamic.redhatworkshops.io -newkey rsa:2048 -nodes -keyout keycloak-key.pem -x509 -days 365 -out keycloak-cert.pem

Note: Change the Subject Alt Name to your Keycloak host.

% oc create secret tls keycloak-tls-secret --cert keycloak-cert.pem --key keycloak-key.pem


### Create secret for DB user and password
% oc create secret generic keycloak-db-secret \
  --from-literal=username=[your_database_username] \
  --from-literal=password=[your_database_password]

### Deploy ephemeral Postgresql DB
% oc apply -f ./postgres/postgres.yml

### Deploy Keycloak
% oc apply -f ./keycloak/keycloak.yaml





