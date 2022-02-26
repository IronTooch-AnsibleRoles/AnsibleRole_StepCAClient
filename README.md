smallstep_leafclient
=========

A role to utilize a Smallstep CA for self-hosted services, install leaf certificates, and start a scheduled job to copy those leaf certificates to the appropriate place on certificate renewal. This is meant principly to serve internal requests. 

Requirements
------------

None

Role Variables
--------------

- **smallstep_top_level_domain**: The top level domain that owns the certificates
  - Defaults to *example.com*, MUST BE CHANGED
- **smallstep_ca_url**: The FQDN to the Certificate Authority
  - Defaults to *ca.{{ smallstep_top_level_domain }}*, MUST BE CHANGED
- **smallstep_ca_footprint**: The footprint of the Certificate Authority Root CA to install
  - Defaults to *aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa*, MUST BE CHANGED
- **smallstep_provisioner_password**: The provisioner password for the Root CA JWK provisioner
  - Defaults to *aaaaaaa*, MUST BE CHANGED
- **smallstep_subject_names**: All of the subject names for the new leaf certificate
  - Defaults to *a list containing HOSTNAME.TOP_LEVEL_DOMAIN, HOSTNAME.PROD.TOP_LEVEL_DOMAIN, and IP_ADDRESS*
- **smallstep_certificate_name**: The overall name of the leaf certificate
  - Defaults to *{{ smallstep_subject_names[0] }}*
- **smallstep_certificate_filename**: The full filename of the certificate to be requested
  - Defaults to *{{ smallstep_certificate_name }}.crt*
- **smallstep_postrenewal_cert_path**: The full path to put a copy of the certificates after a renewal operation, other then the default
  - Defaults to *blank* (Or don't move certs)
- **smallstep_postrenewal_cert_owner**: The user that should own the copied version of the certificate
  - Defaults to *blank* (Or don't change the owner of the certs)
- **smallstep_postrenewal_key_path**: EXPLANATION
  - Defaults to *blank* (Or don't move certs)
- **smallstep_postrenewal_key_owner**: The user that should own the copied version of the key
  - Defaults to *{{ smallstep_postrenewal_cert_owner }}*
- **smallstep_install_root_certificate**: A flag to indicate that the Root CA needs to be installed
  - Defaults to *true*
- **smallstep_request_leaf_certificates**: A flag to indicate that leaf certificates should be installed
  - Defaults to *true*
- **smallstep_install_renewal_procedure**: A flag to indicate that the script files to do renewals should be deployed
  - Defaults to *true*
- **smallstep_turn_on_renewal**: A flag to indicate that a Cron job should be started to renew certificates
  - Defaults to *true*
- **smallstep_services_to_restart**: A list of which services should be restarted once the certificates are renewed 
  - Defaults to *No services*

Dependencies
------------

No role dependencies.

Notes
------------

The default is to place all the renewal certificate items in */etc/step-ca/cert_management*. All certificates requested are renewed in accordance with the schema. To stop renewing a certificate, it needs to be removed from */etc/step-ca*

Example Playbooks
----------------

```yaml
# Deploys a default leaf certificate, with the hostname and IP address as the SAN. No renewal to external locations
    - role: 'irontooch.smallstep_leafclient'
      vars:
        smallstep_top_level_domain: "myapp.com"
        smallstep_ca_url: "ca.example.com"
        smallstep_ca_footprint: "08713e48e24e420dbf6a51bc0a8ad608"
        smallstep_provisioner_password: "766a4bffc4c34f9eafd203cd02bb0d70"
```

```yaml
# Deploys a leaf certificate, with the two specific hostnames and an IP address
    - role: 'irontooch.smallstep_leafclient'
      vars:
        smallstep_top_level_domain: "myapp.com"
        smallstep_ca_url: "ca.example.com"
        smallstep_ca_footprint: "08713e48e24e420dbf6a51bc0a8ad608"
        smallstep_provisioner_password: "766a4bffc4c34f9eafd203cd02bb0d70"
        smallstep_subject_names:
          - "my-first-hostname.mydomain.com"
          - "my-second-hostname.mydomain.com"
          - "192.168.1.10"
```

```yaml
# Deploys a default certififcate, and sets up a renewer to redeploy to a given location. Restarts nginx after renewal
    - role: 'irontooch.smallstep_leafclient'
      vars:
        smallstep_top_level_domain: "myapp.com"
        smallstep_ca_url: "ca.example.com"
        smallstep_ca_footprint: "08713e48e24e420dbf6a51bc0a8ad608"
        smallstep_provisioner_password: "766a4bffc4c34f9eafd203cd02bb0d70"
        smallstep_postrenewal_cert_path: "/tmp/mycert.pem"
        smallstep_postrenewal_cert_owner: "local_admin"
        smallstep_postrenewal_key_path: "/tmp/mykey.key"
        smallstep_postrenewal_key_owner: "local_admin"
        smallstep_services_to_restart:
          - nginx
```

License
-------

GPL v3.0

Author Information
------------------

Author is IronTooch
