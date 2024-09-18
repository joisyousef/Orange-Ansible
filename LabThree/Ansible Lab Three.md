#### Write the playbook to add a task to start httpd service on all nodes defined in inventory file. hint: Use the service module.
![](2ae3cb0ff54489534896e0cab2a1fda7.png)
![](64eb74d0a21dd21efc36391cbb9268af.png)
then run
![](c39c1b0612ba53ec7c1594cf82db177b.png)
Success! 

#### Create a playbook that uses the file module to create a directory structure on multiple hosts. Ensure the directories have specific permissions, ownership, and that certain files are absent in these directories.
![](309a8a1aad6d45251d3c1c206cbdd24b.png)
![](f68be9b2512dfae46337b9ad85a94878.png)
Success!\

#### Use the lineinfile module to ensure that a specific line exists in a configuration file on all hosts. For example, add a line to /etc/hosts only if it’s not already there.

![](2e212f825f67ceb5a3713354a5d3fee1.png)

![](0a18a48f021858fd357eb7fbe265a017.png)Success!

#### Write a playbook that uses the docker_container module to start a container with a specific image (e.g., nginx) and ensures the container is always running.

![](c65a55c1bd6d658992c196780ff54502.png)

![](c96b46affc9ede866078ae09c85c56f1.png)
Success!

#### Create a role named nginx_setup that installs and configures Nginx on any system. The role should ensure that the nginx.conf file is properly set up, the service is running, and the firewall allows traffic on port 80.

To create an Ansible role named `nginx_setup` that installs and configures Nginx, follows these steps:

1. **Create the Role Directory Structure**

Run the following command to create the directory structure for the role:

```bash
ansible-galaxy init nginx_setup
```

This will create a directory named `nginx_setup` with the following structure:

```
nginx_setup/
├── defaults
│   └── main.yml
├── files
│   └── nginx.conf
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
│   └── nginx.conf.j2
└── vars
    └── main.yml
```

2. **Define Default Variables**

Edit `nginx_setup/defaults/main.yml` to set default variables for your Nginx configuration:

```yaml
---
# Default variables for nginx_setup role

nginx_conf_file: "/etc/nginx/nginx.conf"
nginx_port: 80
nginx_service_name: nginx
```

3. **Create the Nginx Configuration File**

You can either provide a static configuration file or use a template. Here we use a template. Create the file `nginx_setup/templates/nginx.conf.j2` with the following content:

```nginx
worker_processes 1;

events {
    worker_connections 1024;
}

http {
    server {
        listen {{ nginx_port }};
        server_name localhost;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }

        # Add other configuration directives here as needed
    }
}
```

4. **Define Tasks**

Edit `nginx_setup/tasks/main.yml` to define tasks for installing and configuring Nginx:

```yaml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present
  become: yes

- name: Configure Nginx
  template:
    src: nginx.conf.j2
    dest: "{{ nginx_conf_file }}"
  become: yes
  notify: Restart Nginx

- name: Ensure Nginx service is running
  service:
    name: "{{ nginx_service_name }}"
    state: started
    enabled: yes
  become: yes

- name: Allow traffic on port 80
  ufw:
    rule: allow
    name: 'Nginx Full'
    port: "{{ nginx_port }}"
    proto: tcp
  become: yes
```

5. **Define Handlers**

Edit `nginx_setup/handlers/main.yml` to define a handler for restarting Nginx:

```yaml
---
- name: Restart Nginx
  service:
    name: "{{ nginx_service_name }}"
    state: restarted
  become: yes
```

6. **Add Role Dependencies (Optional)**

If your role depends on other roles, specify them in `nginx_setup/meta/main.yml`. For example:

```yaml
---
dependencies: []
```

7. **Use the Role in a Playbook**

Create a playbook to use the `nginx_setup` role. Save this as `site.yml` or another appropriate name:

```yaml
---
- name: Configure Nginx on all servers
  hosts: all
  become: yes
  roles:
    - nginx_setup
```

8. **Run the Playbook**

Execute the playbook using the `ansible-playbook` command:

```bash
ansible-playbook site.yml -i inventory.ini
```


### - Create a role that configures a basic LAMP stack (Linux, Apache, MySQL, PHP). Ensure each service is installed and properly configured. For example, MySQL should have a database and user created, and Apache should serve a test PHP file.



1. **Create a Role for LAMP Stack Configuration**

Here’s how to create an Ansible role that configures a basic LAMP stack (Linux, Apache, MySQL, PHP).

#### Directory Structure of the Role:
```bash
lamp_stack/
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
├── templates/
│   └── test.php.j2
├── vars/
│   └── main.yml
```

#### **tasks/main.yml**:
This file contains the main tasks to install and configure the LAMP stack.

```yaml
---
# Install Apache
- name: Install Apache
  apt:
    name: apache2
    state: present
    update_cache: yes
  notify: Restart Apache

# Install MySQL
- name: Install MySQL
  apt:
    name: mysql-server
    state: present
  notify: Restart MySQL

# Install PHP
- name: Install PHP and necessary modules
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - php
    - libapache2-mod-php
    - php-mysql
  notify: Restart Apache

# Create a MySQL database
- name: Create MySQL database
  mysql_db:
    name: my_database
    state: present

# Create a MySQL user
- name: Create MySQL user
  mysql_user:
    name: my_user
    password: "my_password"
    priv: "my_database.*:ALL"
    state: present

# Copy test PHP file to the web directory
- name: Deploy test PHP file
  template:
    src: test.php.j2
    dest: /var/www/html/test.php
  notify: Restart Apache
```

#### **handlers/main.yml**:
This file defines handlers that restart services when they are notified of changes.

```yaml
---
# Handler to restart Apache
- name: Restart Apache
  service:
    name: apache2
    state: restarted

# Handler to restart MySQL
- name: Restart MySQL
  service:
    name: mysql
    state: restarted
```

#### **templates/test.php.j2**:
This is a simple PHP test file template.

```php
<?php
phpinfo();
?>
```

#### **vars/main.yml**:
Optional variables that can be used to define default settings for the role.

```yaml
mysql_root_password: "root_password"
```

---

### Given the following sample Ansible playbook, write handler will correctly restart the web server when the configuration file is changed?
- name: Update web server configuration
  hosts: webservers
     tasks:
     - name: Copy web server configuration file
       copy:
	     src: /path/to/webserver.conf
	     dest: /etc/webserver/webserver.conf
	   notify: Restart web server

-

To correctly restart the web server when the configuration file is changed, you should define a handler. Here’s the updated playbook with the handler definition:

#### Playbook:
```yaml
---
- name: Update web server configuration
  hosts: webservers
  tasks:
    - name: Copy web server configuration file
      copy:
        src: /path/to/webserver.conf
        dest: /etc/webserver/webserver.conf
      notify: Restart web server

  handlers:
    - name: Restart web server
      service:
        name: apache2  # Or the appropriate service name for your web server
        state: restarted
```

In this playbook:
- The `copy` task copies the web server configuration file.
- The `notify` directive triggers the handler to restart the web server only if the configuration file is changed.
- The handler named `Restart web server` uses the `service` module to restart the Apache service.

   ```bash
   ansible-playbook -i inventory playbook.yml
   ```

This setup will install Apache, MySQL, PHP, configure MySQL, and ensure Apache is serving a test PHP file.