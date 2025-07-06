# Ansible Configuration Management Learning Project

A comprehensive hands-on project demonstrating Ansible fundamentals through automated configuration of multi-server environments with different roles and configurations.

## 🎯 Project Overview

This project creates a complete Ansible-managed infrastructure using Docker containers to simulate real-world server environments. It demonstrates core Ansible concepts, best practices, and advanced features through practical implementation.

### Managed Infrastructure

- **1 Ansible Control Node** with Ansible installed
- **2 Web Development Servers** (Ubuntu-based)
- **2 Analytics Servers** (Debian-based)
- **Automated SSH key distribution and passwordless authentication**

## 🧠 Core Ansible Concepts Learned

### 1. **Inventory Management**

**Key concepts covered:**
- Creating YAML-based inventory files (`inventory/hosts.yml`)
- Organizing hosts into logical groups (`webdev`, `analytics`, `ubuntu`, `debian`)
- Using nested groups for hierarchical organization
- Setting host-specific and group-specific variables
- Cross-platform inventory management

**Key insights:**
```yaml
all:
  children:
    webdev:
      hosts:
        webdev-01:
          ansible_host: 172.20.0.11
          server_role: web
    analytics:
      hosts:
        analytics-01:
          ansible_host: 172.20.0.21
          server_role: analytics
```

### 2. **Playbook Architecture**

**Technical areas explored:**
- Writing declarative playbooks that define desired state
- Using multiple plays in a single playbook (`site.yml`)
- Implementing proper play targeting with `hosts` directive
- Managing execution flow with `tags` and conditional execution
- Separating concerns across different plays

**Key insights:**
- Playbooks should be idempotent (safe to run multiple times)
- Use descriptive names for plays and tasks
- Tag everything for selective execution
- Always use `become: true` when elevated privileges are needed

### 3. **Role-Based Architecture**

**Implementation details:**
- Creating reusable roles with standard directory structure
- Managing role dependencies through `meta/main.yml`
- Building a common base role that other roles can depend on
- Separating role logic into tasks, handlers, templates, and variables
- Creating specialized roles for different server functions

**Role structure learned:**
```
roles/
├── common/           # Base configuration for all servers
│   ├── tasks/
│   ├── meta/
├── webdev/          # NGINX web server configuration
│   ├── tasks/
│   ├── templates/
│   ├── handlers/
├── analytics/       # Python/Jupyter environment
    ├── tasks/
    ├── meta/
```

### 4. **Variable Management and Precedence**

**Concepts demonstrated:**
- Using `group_vars/` for group-specific variables
- Creating encrypted variables with Ansible Vault
- Understanding variable precedence (host vars > group vars > role defaults)
- Using `{{ variable }}` syntax for templating
- Setting up vault password files for automation

**Variable organization:**
```
group_vars/
├── all/
│   └── vault.yml          # Encrypted sensitive data
├── webdev.yml             # Web server specific vars
└── analytics.yml          # Analytics server specific vars
```

### 5. **Ansible Modules Mastery**

**Skills developed:**

**Package Management:**
- `apt` module for Debian/Ubuntu package installation
- Using package lists and state management
- Handling package cache updates and retries

**Service Management:**
- `service` module for controlling systemd services
- Starting, stopping, enabling, and restarting services
- Using handlers for service restart triggers

**File Operations:**
- `copy` module for static file deployment
- `template` module for dynamic file generation with Jinja2
- `file` module for directory creation and permissions

**Python Environment:**
- `pip` module for Python package installation
- Managing virtual environments
- Installing packages in specific virtualenvs

### 6. **Template Engine (Jinja2)**

**What I learned:**
- Creating dynamic configuration files with `.j2` templates
- Using Ansible facts in templates (`{{ ansible_hostname }}`)
- Implementing conditional logic in templates
- Accessing inventory variables in templates
- Creating responsive HTML templates with server information

**Template example insights:**
```jinja2
<title>{{ webdev_page_title | default('Test Server') }} - {{ inventory_hostname }}</title>
<span class="label">Server IP:</span> {{ ansible_host }}
{% if webdev_custom_message is defined %}
    <div>{{ webdev_custom_message }}</div>
{% endif %}
```

### 7. **Handlers and Notifications**

**What I learned:**
- Creating handlers for triggered actions (like service restarts)
- Using `notify` directive to trigger handlers
- Handlers only run once, even if notified multiple times
- Handlers run at the end of play execution
- Naming handlers descriptively for clarity

**Handler pattern:**
```yaml
# In tasks
- name: "Deploy nginx config"
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: restart nginx

# In handlers  
- name: restart nginx
  service:
    name: nginx
    state: restarted
```

### 8. **Error Handling and Resilience**

**What I learned:**
- Using `register` to capture task results
- Implementing retry logic with `retries` and `delay`
- Using `failed_when` and `changed_when` for custom logic
- Implementing `ignore_errors` for non-critical tasks
- Creating connectivity tests and validation tasks

**Error handling patterns:**
```yaml
- name: "Update package cache"
  apt:
    update_cache: true
  register: apt_update_result
  retries: 3
  delay: 10
  until: apt_update_result is succeeded
```

### 9. **Configuration Management**

**What I learned:**
- Creating comprehensive `ansible.cfg` configuration
- Configuring SSH connection optimization
- Setting up fact caching for performance
- Implementing proper logging
- Managing privilege escalation settings

**Key configuration insights:**
- Disable host key checking for lab environments
- Use SSH connection multiplexing for performance
- Enable pipelining for faster execution
- Set appropriate timeouts for network operations

### 10. **Security and Vault Management**

**What I learned:**
- Encrypting sensitive data with `ansible-vault`
- Setting up vault password files for automation
- Managing SSH keys and passwordless authentication
- Implementing secure variable storage patterns
- Using proper file permissions for security

**Security workflow:**
```bash
# Create vault password file
echo "your-vault-password" > .vault_password
chmod 400 .vault_password

# Encrypt sensitive variables
ansible-vault encrypt inventory/group_vars/all/vault.yml
```

### 11. **Tagging and Selective Execution**

**What I learned:**
- Using tags for organizing and filtering tasks
- Creating semantic tag hierarchies (`install`, `config`, `services`)
- Special tags like `always` and `never`
- Running specific tags with `--tags`
- Skipping tags with `--skip-tags`

**Tagging strategy:**
```yaml
- name: "Install packages"
  apt:
    name: nginx
  tags:
    - packages
    - nginx
    - install
```

### 12. **Facts and System Information**

**What I learned:**
- Gathering system facts automatically
- Using facts in templates and conditionals
- Understanding fact namespaces (`ansible_distribution`, `ansible_hostname`)
- Implementing fact caching for performance
- Creating custom facts when needed

## 🛠️ Advanced Ansible Techniques Discovered

### Conditional Execution
```yaml
- name: "Install packages (Ubuntu/Debian only)"
  apt:
    name: nginx
  when: ansible_distribution in ["Ubuntu", "Debian"]
```

### Task Registration and Validation
```yaml
- name: "Test connectivity"
  ping:
  register: ping_result
  failed_when: false

- name: "Fail if no connectivity"
  fail:
    msg: "Cannot reach {{ inventory_hostname }}"
  when: ping_result.ping is not defined
```

### Virtual Environment Management
```yaml
- name: "Create Python virtual environment"
  command: "python3 -m venv {{ venv_path }}"
  args:
    creates: "{{ venv_path }}"

- name: "Install packages in virtualenv"
  pip:
    name: "{{ packages }}"
    virtualenv: "{{ venv_path }}"
```

## 📁 Ansible Project Structure

```
ansible/
├── ansible.cfg                     # Ansible configuration
├── .vault_password                 # Vault password file (gitignored)
├── inventory/
│   ├── hosts.yml                  # Main inventory
│   └── group_vars/
│       ├── all/
│       │   └── vault.yml          # Encrypted variables
│       ├── webdev.yml             # Web server variables
│       └── analytics.yml          # Analytics variables
├── playbooks/
│   └── site.yml                   # Main orchestration playbook
└── roles/
    ├── common/                    # Base configuration role
    │   ├── tasks/main.yml
    │   └── meta/main.yml
    ├── webdev/                    # Web development role
    │   ├── tasks/main.yml
    │   ├── templates/index.html.j2
    │   ├── handlers/main.yml
    │   └── meta/main.yml
    └── analytics/                 # Analytics environment role
        ├── tasks/main.yml
        └── meta/main.yml
```

## 🎯 Key Ansible Learning Outcomes

### Core Principles Mastered
- **Idempotency**: Tasks produce the same result regardless of how many times they're run
- **Declarative Configuration**: Defining the desired end state rather than step-by-step procedures
- **Infrastructure as Code**: Managing infrastructure through version-controlled code
- **Convergence**: Bringing systems to a desired state from any starting point

### Best Practices Learned
- **Role Dependencies**: Using the common role as a dependency for specialized roles
- **Variable Organization**: Logical separation of variables by scope and sensitivity
- **Template Strategy**: Dynamic configuration generation with proper escaping
- **Error Resilience**: Implementing retry logic and graceful failure handling
- **Security First**: Encrypting sensitive data and managing SSH keys properly

### Real-World Applications
- **Multi-Environment Management**: Different configurations for dev/staging/prod
- **Service Orchestration**: Coordinated deployment of related services
- **Configuration Drift Prevention**: Ensuring servers maintain desired configuration
- **Scalable Automation**: Patterns that work from 2 servers to 200+ servers

## 🚀 Running the Ansible Project

```bash
# Start the infrastructure
docker-compose up -d

# Connect to Ansible control node
docker exec -it ansible-control bash

# Navigate to Ansible directory
cd /ansible

# Change rights on .vault_password file (create one next to ansible.cfg if it is not already the case ! password is : myVaultPassword123)
chmod 400 .vault_password

# Test connectivity
ansible all -m ping

# Run the complete playbook
ansible-playbook playbooks/site.yml

# Run specific roles only
ansible-playbook playbooks/site.yml --tags webdev
ansible-playbook playbooks/site.yml --tags analytics

# Run with increased verbosity for debugging
ansible-playbook playbooks/site.yml -vvv
```

## 💡 Ansible Mastery Achieved

This project demonstrates proficiency in:

- **Inventory Design**: Multi-group, hierarchical host organization
- **Playbook Engineering**: Maintainable, role-based automation code
- **Variable Architecture**: Secure, organized, and scalable variable management
- **Template Mastery**: Dynamic configuration with Jinja2
- **Module Expertise**: Effective use of core Ansible modules
- **Security Implementation**: Vault encryption and SSH key management
- **Error Handling**: Robust automation with proper error recovery
- **Performance Optimization**: SSH multiplexing, fact caching, and pipelining

The project showcases how Ansible transforms manual server management into automated, repeatable, and scalable infrastructure operations.