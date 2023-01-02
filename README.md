Self-Signed CA
=========

Ansible role to generate self-signed Root CA, Intermediate CA, Server(s), and client (user) certificates.

Intermediate CA
--------------

Using only the **Root CA** to create **Intermediate CA(s)** is a best practice. After generating the intermediate CA, you can remove it's private key from the {{ cert_dir }} and store it separately for additional security. Only when creating additional CA certificates, should the **Root CA** private key be needed.

A CA bundle must be created since a intermediate CA is in use. You can do this by the concatenation of the intermediate certificate and the root certificate.

    # cat ${cert_dir}/intermediate/certs/intermediate.cert.pem ${cert_dir}/certs/ca.cert.pem > ${cert_dir}/intermediate/certs/ca_bundle.pem

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

---
    - hosts: localhost
      vars:
        server_hostnames:
          - servera.example.com
          - serverb.example.com
          - web1.example.com
          - example.com
        client_hostnames:
          - client1.example.com
          - client2.example.com
        dn:
          email_address: "admin@example.com"
          locality_name: "SF"
          state_or_province_name: "CA"
          country_name: "US"
          organization_name: "Example Org"
          organizational_unit_name: "Example OU"
      tasks:
        - name: "Include ss_ca"
          include_role:
            name: "ansible-role-ss_ca"


## OpenSSL Commands

Each command below can take additional arguments such as `-noout`, `-text`, or even `-modulus | openssl md5` to check certification/key match.

### Check private key

    $ openssl rsa -in ${cert_dir}/private/ca.key.pem

### Check certificate request

    $ openssl req -in ${cert_dir}/csr/ca.csr.pem

### Check certificate

    $ openssl x509 -in ${cert_dir}/certs/ca.cert.pem

### Create CA Chain (bundle)

    $ cat ${cert_dir}/intermediate/certs/intermediate.cert.pem ${cert_dir}/certs/ca.cert.pem >  ${cert_dir}/intermediate/certs/ca_bundle.pem

### Check certificate was signed by ca (ca_chain)

    $ openssl verify -verbose -CAfile ${cert_dir}/intermediate/certs/ca_bundle.pem ${cert_dir}/intermediate/certs/<certificate>