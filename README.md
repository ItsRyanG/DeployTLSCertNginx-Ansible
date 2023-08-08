
## Configuring your group_vars file

Required values:

- name: description
- verify: the 'server_name' used to verify the the certificate against a site configured on nginx. Ansible will use the IP from the host inventory to connect and use the host header defined by 'server_name'. 
- ssl_cert: SSL Certificate in plain text (ansible vault encrypted value)
- ssl_key: SSL key in plain text (ansible vault encrypted value)
- ssl_intermediate: SSL intermediate/trusted certificate in plain text (ansible vault encrypted value)
- ssl_ca: SSL CA certificate in plain text (ansible vault encrypted value)
- ssl_certificate_path: full path to ssl certificate
- ssl_key_path: full path to SSL key (used for cert signing requests)
- ssl_intermediate_path: full path to ssl intermediate certificate
- ssl_ca_path: full path to ssl root certificate/CA

Example: 

file: `ansible/inventories/group_vars/yw-dr/yw-dr-webservers/ssl_certificates.yml`

```
ssl_certificates:
  - name: bigvikinggames.com
    # verify is used to test the new cerificate 
    # use the server_name definition in the nginx config which uses this certificate.
    verify: fw.bigvikinggames.com
    ssl_cert: "{{ ssl_cert_bigvikinggames_com }}"
    ssl_key: "{{ ssl_key_bigvikinggames_com }}"
    ssl_intermediate: "{{ ssl_intermediate_bigvikinggames_com }}"
    ssl_ca: "{{ ssl_ca_bigvikinggames.com }}"
    ssl_certificate_path: /etc/ssl/bigvikinggames.com/wildcard.crt
    ssl_key_path: /etc/ssl/bigvikinggames.com/wildcard.key
    ssl_intermediate_path: /etc/ssl/bigvikinggames.com/trusted.crt
    ssl_ca_path: /etc/ssl/bigvikinggames.com/ca.crt
```

file: `ansible/inventories/group_vars/all/ssl/ssl_bigvikinggames_com.yml`

```
---
# SSL Certificate, Private Key, and Trusted CA
ssl_cert_bigvikinggames_com: !vault |
  $ANSIBLE_VAULT;1.2;AES256;bvg-devops
  346339383065663631336439 ...

ssl_key_bigvikinggames_com: !vault |
  $ANSIBLE_VAULT;1.2;AES256;bvg-devops
  663439366462323230623231 ...

ssl_intermediate_bigvikinggames_com: !vault |
  $ANSIBLE_VAULT;1.2;AES256;bvg-devops
  316232363266656239366536 ...

ssl_ca_bigvikinggames_com: !vault |
  $ANSIBLE_VAULT;1.2;AES256;bvg-devops
  616439623937316331383863 ...
```

## Encrypt Values in Ansible Vault
  Example:    
  ```
  ansible-vault encrypt_string --vault-id vault-password-file --encrypt-vault-id bvg-devops "-----BEGIN CERTIFICATE-----    
  MIIDrzCCApegAwIBAgIQCDvgVpBCRrGhdWrJWZHHSjANBgkqhkiG9w0BAQUFADBh
  -----END CERTIFICATE-----
  "
  ```

  Note:
  - Include a new space on the end of the encrypted certificate/key value (as seen above).

## Generate a new CSR.

Example: 
`openssl req -new -key /etc/ssl/yoworld.com/wildcard.key -out star_yoworld_com.csr -subj "/C=CA/ST=Ontario/L=London/O=Big Viking Games Inc./CN=*.yoworld.com"`