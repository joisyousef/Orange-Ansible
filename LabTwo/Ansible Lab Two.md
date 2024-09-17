#### 1- If we use the following inventory, on which hosts will Ansible install the httpd package using the given playbook?

```yaml
[webserver]
web1
web2
[appserver]
app1
					app2
app3

- name: Setup apache
  hosts: webserver
  tasks:
    - name: install httpd
      yum:
        name: httpd
        state: installed

- name: Setup tomcat
  hosts: appserver
  tasks:
    - name: install httpd
      yum:
        name: tomcat
        state: installed
```
In the provided Ansible playbook, there are two separate plays, each targeting different host groups and installing different packages:

1. **Play 1: Setup apache**
   - `hosts: webserver`
   - This play installs the `httpd` package on all hosts in the `webserver` group.

2. **Play 2: Setup tomcat**
   - `hosts: appserver`
   - This play installs the `tomcat` package on all hosts in the `appserver` group.

Given your inventory:

```ini
[webserver]
web1
web2
[appserver]
app1
app2
app3
```

Here's what happens:

- For the **"Setup apache"** play:
  - The `httpd` package will be installed on **`web1`** and **`web2`**.

- For the **"Setup tomcat"** play:
  - The `tomcat` package will be installed on **`app1`**, **`app2`**, and **`app3`**.

So, Ansible will install the `httpd` package on **`web1`** and **`web2`**.

#### 2- what commands can you use to run an Ansible playbook named install.yaml?

To run an Ansible playbook named `install.yaml`, you can use the `ansible-playbook` command followed by the playbook file name. Here are a few variations of the command you can use:

1. **Basic Command:**
   ```bash
   ansible-playbook install.yaml
   ```

2. **Specifying the Inventory File:**
   If you need to specify a custom inventory file, use the `-i` option:
   ```bash
   ansible-playbook -i my_inventory_file install.yaml
   ```

3. **Running with Verbosity:**
   To get more detailed output, you can increase the verbosity level. Use `-v` for verbose output, `-vv` for more verbosity, or `-vvv` for even more detailed information:
   ```bash
   ansible-playbook -v install.yaml
   ```

4. **Running as a Specific User:**
   To run the playbook as a specific user, use the `--user` option:
   ```bash
   ansible-playbook --user=myuser install.yaml
   ```

5. **Using Extra Variables:**
   If you need to pass extra variables to the playbook, use the `-e` option:
   ```bash
   ansible-playbook -e "variable1=value1 variable2=value2" install.yaml
   ```

6. **Running with a Specific Limit:**
   To limit the playbook execution to specific hosts or groups, use the `--limit` option:
   ```bash
   ansible-playbook --limit webserver install.yaml
   ```

These commands should cover most common scenarios for running an Ansible playbook.

#### 3- A sample playbook named update_service.yml is shown below, it is supposed to update a service on your servers.
```
- hosts: all
  tasks:
- name: Install a new package
  apt:
    name: new_package
    state: present
                  
- name: Update the service
    service:
        name: my_service
        state: restarted
                  
- name: Check service status
    service:
        name: my_service
        state: started
```
what command would you use to run update_service.yml playbook in check mode?

To run the `update_service.yml` playbook in check mode (also known as dry-run mode), you use the `--check` option with the `ansible-playbook` command. This mode allows you to see what changes would be made without actually applying them.

Here’s the command to run the playbook in check mode:

```bash
ansible-playbook --check update_service.yml
```

In check mode, Ansible simulates the changes and reports what it would do without making any actual changes to the systems. This is useful for validating what will happen when the playbook is executed for real.

### 4- Consider again the same sample playbook named update_service.yml as shown below.

Let's suppose you have already ran this playbook on your server. Now, once you run this playbook in check mode against same server, which tasks would result in changed status?

A. Install a new package
B. Update the service
C. Check service status
D. All of the tasks

When you run the `update_service.yml` playbook in check mode, Ansible simulates the changes but doesn't apply them. Based on the nature of each task:

1. **Install a new package (A)**: 
   - If the package `new_package` is already installed on the server, this task will **not** result in a "changed" status. In check mode, Ansible will verify if the package is present, and if it is, no changes are needed.
   - If the package is not installed, Ansible will simulate installing it and report a "changed" status.

2. **Update the service (B)**: 
   - This task is restarting the service `my_service`. Even in check mode, restarting a service typically results in a "changed" status because restarting a service implies an action is needed.
   - So, this task will likely report a "changed" status.

3. **Check service status (C)**: 
   - Checking the service status ensures the service is started. If the service is already running, this task will **not** result in a "changed" status.
   - If the service is stopped or in a different state, Ansible would simulate starting it and report a "changed" status.

So, the correct answer would most likely be **B. Update the service**, though **A** might also result in a "changed" status if the package is missing.
  
#### 5- There is another sample playbook named configure_database.yml that modifies a configuration file on your database servers. The initial code sample is as follows:

```
    - hosts: all
      tasks:
        - name: Set max connections
          lineinfile:
            path: /etc/postgresql/12/main/postgresql.conf
            line: 'max_connections = 500'

        - name: Set listen addresses
          lineinfile:
            path: /etc/postgresql/12/main/postgresql.conf
            line: 'listen_addresses = "*"'
```
what command would you use to run the configure_database.yml playbook in both check mode and diff mode?

To run the `configure_database.yml` playbook in **both check mode** and **diff mode**, you need to combine the `--check` and `--diff` options with the `ansible-playbook` command. 

Here's the command:

```bash
ansible-playbook --check --diff configure_database.yml
```

- **`--check`**: Runs the playbook in check mode, simulating changes without applying them.
- **`--diff`**: Shows the differences between the current state and the desired state, i.e., it highlights what changes would be made to files.

This combination lets you preview what Ansible would modify and see exactly what changes would be applied to the configuration file without making any actual changes.

#### 6- Consider again the same sample playbook named configure_database.yml as shown below what is the command to check syntax error what command would you use to run ansible-lint on the database_setup.yml playbook?

To check for syntax errors in the `configure_database.yml` playbook, you can use the `--syntax-check` option with the `ansible-playbook` command:

### Command to check for syntax errors:
```bash
ansible-playbook --syntax-check configure_database.yml
```

This will verify the playbook for any syntax-related issues without executing it.

### Command to run `ansible-lint` on the `configure_database.yml` playbook:
To lint the playbook and check for best practices or common mistakes, use `ansible-lint`:

```bash
ansible-lint configure_database.yml
```

This will check the playbook against Ansible best practices and highlight potential improvements or issues.

  
7- Consider again the same sample playbook named database_setup.yml as shown below.

```
      bash
      - name: Database Setup Playbook
        hosts: db_servers
        tasks:
          - name: Ensure PostgreSQL is installed
            apt:
              name: postgresql
              state: latest
              update_cache: yes

          - name: Start PostgreSQL service
            service:
              name: postgresql
              state: started

          - copy:
              src: /path/to/pg_hba.conf
              dest: /etc/postgresql/12/main/pg_hba.conf
            notify:
              - Restart PostgreSQL
 
```

After running ansible-lint on the playbook, which of the following issues might you expect to see [make sure to select two choice]?

 A. Incorrect indentation.
 B. Deprecated 'apt' module.
 C. Missing 'name' attribute for a task.
 D. Use of a blacklisted command.
After running `ansible-lint` on the provided `database_setup.yml` playbook, the two most likely issues you might expect to see are:

When reviewing the given Ansible playbook snippet and considering common issues that `ansible-lint` might raise, here are the two choices that could be expected as warnings or errors:

1. **C. Missing 'name' attribute for a task.**
   - This issue might be flagged because the task using the `copy` module does not have a `name` attribute. In Ansible best practices, each task should have a `name` that describes what the task does, which makes the playbook more readable and easier to maintain.

2. **A. Incorrect indentation.**
   - Although the provided code snippet appears to have correct YAML indentation at first glance, `ansible-lint` might flag subtle issues not visible without detailed inspection or depending on the context. However, if the indentation exactly matches what's shown, this wouldn't normally be an issue. Without indentation problems visible in the snippet, the more likely issue would involve the missing `name`.

the most likely issues `ansible-lint` might flag are **C. Missing 'name' attribute for a task**. The selection of **A. Incorrect indentation** is contingent on potential subtle issues not visible here.
#### 8- You've been given feedback from ansible-lint about potential issues in your hypothetical webserver_setup.yml playbook. The feedback mentions issues with indentation, deprecated modules, and missing name attributes. Which of the following is NOT a recommended action based on the feedback?

 A. Correcting the indentation in the playbook.
 B. Replacing deprecated modules with their newer counterparts.
 C. Ignoring the feedback and proceeding with playbook execution.
 D. Adding 'name' attributes to tasks that are missing them.
 
**Correct answer: C. Ignoring the feedback and proceeding with playbook execution.**

---
#### 9- If ansible-lint provides no output after checking a playbook, what does it indicate?

When `ansible-lint` checks a playbook and provides no output, this indicates the state of the playbook in terms of style and best practices adherence. Here's what each option suggests:

A. The playbook has syntax errors.
B. The playbook is empty.
C. The playbook adheres to best practices and has no style-related issues.
D. ansible-lint failed to check the playbook.

**Correct answer: C. The playbook adheres to best practices and has no style-related issues.**

#### 10-Solve issues in below playbook

```bash
---
- hosts: localhost
  become: yes
  tasks:
    - name: 'Execute a date command on localhost'
      command: date

```

- **Using the `command` module for system commands** like `date` is okay, but in some cases, you might prefer to use the `shell` module if you're working with more complex commands or need to use shell-specific features.
- **YAML formatting**: While the given YAML formatting is mostly fine, ensuring a consistent and clean structure helps readability.

Here’s the improved playbook:

```
---
- hosts: localhost
  become: yes
  tasks:
    - name: Execute the date command on localhost
      command: date

```

#### 11-Create an Ansible playbook to install Nginx on a group of servers. Ensure the service is started and enabled at boot.

```
- hosts: web_servers  # Replace 'web_servers' with the correct group in your inventory
  become: yes
  tasks:
    - name: Ensure Nginx is installed
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start and enable Nginx service
      systemd:
        name: nginx
        state: started
        enabled: yes

```

- **Installing Nginx**: The `apt` module is used to install the `nginx` package. The `update_cache: yes` option ensures the package list is updated before the installation.
- **Starting and enabling Nginx**: The `systemd` module ensures that Nginx is started and set to automatically start at boot (`enabled: yes`).
- **Hosts**: Replace `web_servers` with the actual group name from your Ansible inventory that corresponds to the servers where you want to install Nginx.

