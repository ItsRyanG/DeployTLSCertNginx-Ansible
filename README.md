# Configuration Guide for a "Deploy SSL Certificates for Nginx" Ansbile Role

This Ansible role is designed to facilitate the management and verification of SSL certificates for your infrastructure. It automates the process of copying SSL certificate components, such as the certificate itself, private key, intermediate trust certificate, and root certificate, to their designated paths. The playbook also includes steps to verify the certificate chain, concatenate certificate components to form a chain, reload the Nginx service, and validate the certificate using OpenSSL's `s_client` utility.

### Tasks Overview

1. **Ensure OpenSSL is Installed**: This task ensures that OpenSSL is installed on the target host, a prerequisite for working with SSL certificates.

2. **Manage and Verify SSL Certificates Block**: This block contains a series of tasks for managing and verifying SSL certificates.

   - **Copy Certificate Components**: Copies the SSL certificate, private key, intermediate trust certificate, and root certificate to their respective paths on the target host. These components are securely stored and maintained.

   - **Verify Certificate Chain**: Uses OpenSSL to verify the certificate chain integrity by checking the trust relationship between certificates.

   - **Concatenate Certificate Chain**: Concatenates the SSL certificate, intermediate trust certificate, and root certificate to create a full certificate chain. This ensures proper verification by clients.

   - **Reload Nginx**: Restarts the Nginx service to apply the new SSL certificate configuration.

   - **Verify Certificate with OpenSSL s_client**: Uses the `openssl s_client` command to connect to the server and validate the SSL certificate. The return code is checked to ensure successful validation.

3. **Recovery Steps Block (Rescue)**: In case any of the above tasks fail, the rescue block contains recovery steps to restore original certificate files and attempt to restart the Nginx service. This helps maintain service availability even in the face of unexpected issues.

By using this ansible role, you can streamline the process of configuring and verifying SSL certificates within your Ansible roles. It covers essential tasks for maintaining a secure SSL configuration and provides recovery measures to handle potential failures.

Remember to customize variables, paths, and URLs according to your environment when incorporating this playbook into your Ansible workflow.


## Configuration Steps

### 1. Modify group_vars File

In your Ansible project, navigate to the `group_vars` or `group_vars` directory. Open or create a file named `ssl_certificates.yml` within the relevant inventory group (e.g., `webservers`).

Required values for each SSL certificate:

- `name`: Description of the certificate
- `verify`: The 'server_name' used to verify the certificate against a site configured on Nginx.
- `ssl_cert`: SSL Certificate in plain text (Ansible Vault encrypted value)
- `ssl_key`: SSL key in plain text (Ansible Vault encrypted value)
- `ssl_intermediate`: SSL intermediate/trusted certificate in plain text (Ansible Vault encrypted value)
- `ssl_ca`: SSL CA certificate in plain text (Ansible Vault encrypted value)
- `ssl_certificate_path`: Full path to SSL certificate
- `ssl_key_path`: Full path to SSL key (used for cert signing requests)
- `ssl_intermediate_path`: Full path to SSL intermediate certificate
- `ssl_ca_path`: Full path to SSL root certificate/CA

Example `ssl_certificates.yml` content:

```yaml
ssl_certificates:
  - name: example.com
    verify: example.verify.com
    ssl_cert: "{{ ssl_cert_example_com }}"
    ssl_key: "{{ ssl_key_example_com }}"
    ssl_intermediate: "{{ ssl_intermediate_example_com }}"
    ssl_ca: "{{ ssl_ca_example_com }}"
    ssl_certificate_path: /etc/ssl/example.com/wildcard.crt
    ssl_key_path: /etc/ssl/example.com/wildcard.key
    ssl_intermediate_path: /etc/ssl/example.com/trusted.crt
    ssl_ca_path: /etc/ssl/example.com/ca.crt
```

### 2. Example Ansible Playbook

```yaml
---

- hosts: localhost

  tasks:
    # --limit= MUST be defined while running this playbook
    - fail:
        msg: "you must use -l or --limit"
      when: ansible_limit is not defined
      run_once: true

- hosts: all
  gather_facts: no
  tasks:
  - name: run nginx_ssl role
    include_role:
      name: nginx_ssl
    loop: "{{ ssl_certificates }}"
```

### 3. Adding this Role as a Submodule

To incorporate this SSL certificate configuration project into your Ansible roles, you can add it as a submodule. Submodules allow you to keep external repositories within your own repository.

1.  Navigate to the root directory of your Ansible roles repository.

2. Add the SSL certificate configuration project as a submodule using the following command:

```bash
git submodule add https://github.com/your-username/ssl-certificate-config.git roles/DeploySSLNginx
```

3. Replace the URL with the actual URL of the SSL certificate configuration project and adjust the destination path (roles/DeploySSLNginx) as needed.

4. Commit the changes to your repository:

```bash
git commit -m "Add SSL certificate configuration submodule"
```

5. When you clone or update your Ansible roles repository in the future, make sure to also initialize and update the submodule:

```
git submodule update --init --recursive
```
