VPN
===

The VPN role is able to set up a containerized VPN on the host system. It supports openvpn for now.

For automatically generating new certificates for your users, look into the [Certman](https://github.com/OneOffTech/openvpn-certman) Project.

Requirements
------------
A working docker installation is required.

Configuration Variables
-----------------------

```yaml
openvpn_days_valid: "1825" # In days, maximum age of the master CA certificate.
openvpn_key_size: "2048" # Bits, 2048+ strongly recommended
openvpn_cipher: "AES-256-CBC" # Cipher used, some cyphers are more secure than others, or offer hardware accelleration
openvpn_auth_digest: "SHA512" # Hash digest used, see note above
openvpn_tls_cipher: "TLS-ECDHE-..." # TLS cipher chain, see note above
openvpn_tls_version_min: "1.2" # TLS version, TLS 1.2+ recommended unless you need to support ancient devices
openvpn_path: "/etc/openvpn" # Path where the configuration files will be put
openvpn_ca: "{{ openvpn_path }}/ca" # Path where the CA will be generated
openvpn_dhparam: "{{ openvpn_path }}/dh{{ openvpn_key_size }}.pem"
openvpn_hmac_firewall: "{{ openvpn_path }}/ta.key" # Path for the shared secret file
openvpn_server: "maintenance.oneofftech.xyz" # Hostname that is visible for the clients
openvpn_port: 443 # Port openvpn will be listening on 
openvpn_proto: tcp # Protocol; TCP is more likely to work, UDP is faster
openvpn_dev: tun # tun or tap; determines if this is a switching or bridging net
openvpn_mtu: "1300" # MTU, slightly lower value prevents package segmentation

# Information for your CA certificate, not very important for the functionality
# of the network, but will be displayed in the logs upon connection.
openvpn_key_country:  "DE"
openvpn_key_province: "Berlin"
openvpn_key_city: "Berlin"
openvpn_key_org: "OneOffTech"
openvpn_key_ou: "IT department"

openvpn_request_subject: "/C={{ openvpn_key_country }}/ST={{ openvpn_key_province }}/L={{ openvpn_key_city }}/O={{ openvpn_key_org }}/OU={{ openvpn_key_ou }}"

openvpn_network: 10.207.0.0 # Address space for client IPs

openvpn_route_traffic: false # Use this VPN to tunnel client traffic

# Some clients that should be generated automatically, so you can have
# something to test the fresh network.
openvpn_initial_clients:
  - testclient

```

Example Configuration
---------------------

```yaml
openvpn_days_valid: "1825" # 5 Years.
openvpn_key_size: "2048"
openvpn_cipher: "AES-256-CBC"
openvpn_auth_digest: "SHA512"
openvpn_tls_version_min: "1.2"
openvpn_tls_cipher: "TLS-ECDHE-RSA-WITH-AES-128-GCM-SHA256:TLS-ECDHE-ECDSA-WITH-AES-128-GCM-SHA256:TLS-ECDHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256"
openvpn_path: "/etc/openvpn"
openvpn_ca: "{{ openvpn_path }}/ca"
openvpn_dhparam: "{{ openvpn_path }}/dh{{ openvpn_key_size }}.pem"
openvpn_hmac_firewall: "{{ openvpn_path }}/ta.key"
openvpn_server: "maintenance.oneofftech.xyz"
openvpn_port: 443 # https for firewalls
openvpn_proto: tcp
openvpn_dev: tun
openvpn_mtu: "1300"

openvpn_key_country:  "DE"
openvpn_key_province: "Berlin"
openvpn_key_city: "Berlin"
openvpn_key_org: "OneOffTech"
openvpn_key_ou: "IT department"

openvpn_request_subject: "/C={{ openvpn_key_country }}/ST={{ openvpn_key_province }}/L={{ openvpn_key_city }}/O={{ openvpn_key_org }}/OU={{ openvpn_key_ou }}"

openvpn_network: 10.207.0.0 # Address space for client IPs

openvpn_route_traffic: false

openvpn_initial_clients:
  - testclient
```

Upgrading installation
----------------------

As the server does not store any persistent state, it is possible to simply run this role after changing the parameters. Note that some configuration changes will require the client configuration to be regenerated.
