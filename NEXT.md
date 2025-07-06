# Ansible Multi-Environment Project - Advanced Ansible Features Roadmap

## 📊 Current Project Status

### ✅ Completed (Foundation Phase)
- **Infrastructure Setup**: Docker Compose with 5 containers (1 control + 4 targets)
- **Basic Networking**: Custom bridge network with static IPs (172.20.0.0/24)
- **SSH Connectivity**: Password-based SSH access between control node and targets
- **Ansible Configuration**: Basic ansible.cfg with optimized settings
- **Inventory Management**: YAML inventory with proper host grouping
- **Basic Playbooks**: Simple site.yml with connectivity tests and basic package installation
- **Web Foundation**: NGINX installation and basic test pages
- **Analytics Foundation**: Python virtual environment with Jupyter Lab setup

### 🔄 Current Capabilities
Your project demonstrates basic Ansible functionality but lacks the advanced features that showcase enterprise-level automation skills.

---

## 🎯 Phase 1: Role-Based Architecture & Design Patterns

### Why This Matters for Ansible Mastery
Roles are the cornerstone of maintainable, scalable Ansible automation. This phase demonstrates understanding of:
- Code organization and reusability
- Dependency management
- Role composition patterns
- Galaxy-compatible role structure

### Tasks to Complete:

#### 1.1 Create Advanced Role Structure
```bash
# Complete role hierarchy
ansible/roles/
├── common/
│   ├── tasks/main.yml
│   ├── handlers/main.yml
│   ├── templates/
│   ├── vars/main.yml
│   ├── defaults/main.yml
│   ├── meta/main.yml
│   └── README.md
├── security/
├── webdev/
├── analytics/
├── monitoring/
└── backup/
```

#### 1.2 Implement Role Dependencies
- [ ] **Create `ansible/roles/common/meta/main.yml`**
  ```yaml
  galaxy_info:
    author: your_name
    description: Common system configuration
    min_ansible_version: 2.14
    platforms:
      - name: Ubuntu
        versions: [22.04]
      - name: Debian
        versions: [12]
  dependencies: []
  ```

- [ ] **Create role dependencies chain**
  ```yaml
  # webdev/meta/main.yml
  dependencies:
    - role: common
    - role: security
  
  # analytics/meta/main.yml
  dependencies:
    - role: common
    - role: security
  ```

#### 1.3 Advanced Role Design Patterns
- [ ] **Implement Role Composition**
  - Parent roles that include child roles
  - Conditional role inclusion based on variables
  - Role inheritance patterns

- [ ] **Create Parameterized Roles**
  - Roles that adapt behavior based on input variables
  - Default configurations with override capabilities
  - Multi-environment role usage

**Deliverables:**
- Galaxy-compatible role structure
- Working role dependency chain
- Role composition examples
- Complete role documentation

---

## 🎯 Phase 2: Advanced Variable Management & Precedence

### Why This Demonstrates Ansible Expertise
Variable management is critical for enterprise automation. This showcases:
- Understanding of Ansible's 22 levels of variable precedence
- Environment-specific configuration management
- Scalable variable organization
- Dynamic variable generation

### Tasks to Complete:

#### 2.1 Comprehensive Variable Architecture
```bash
ansible/
├── inventory/
│   ├── group_vars/
│   │   ├── all/
│   │   │   ├── common.yml
│   │   │   └── versions.yml
│   │   ├── webdev/
│   │   │   ├── main.yml
│   │   │   ├── nginx.yml
│   │   │   └── database.yml
│   │   ├── analytics/
│   │   │   ├── main.yml
│   │   │   ├── python.yml
│   │   │   └── spark.yml
│   │   ├── production.yml
│   │   ├── staging.yml
│   │   └── development.yml
│   └── host_vars/
│       ├── webdev-01/
│       │   └── main.yml
│       ├── analytics-01/
│       │   └── main.yml
│       └── ansible-control/
│           └── main.yml
```

#### 2.2 Variable Precedence Demonstration
- [ ] **Create Variable Override Examples**
  ```yaml
  # group_vars/all/common.yml
  app_environment: development
  log_level: info
  
  # group_vars/webdev/main.yml
  app_environment: web_dev
  log_level: debug
  
  # host_vars/webdev-01/main.yml
  log_level: trace
  hostname_suffix: primary
  ```

- [ ] **Implement Environment-Specific Variables**
  ```yaml
  # group_vars/development.yml
  database_host: localhost
  ssl_enabled: false
  
  # group_vars/production.yml
  database_host: "{{ vault_database_host }}"
  ssl_enabled: true
  ```

#### 2.3 Dynamic Variable Generation
- [ ] **Fact-Based Variables**
  ```yaml
  # Use gathered facts to set variables
  nginx_worker_processes: "{{ ansible_processor_vcpus }}"
  max_connections: "{{ (ansible_memtotal_mb / 10) | int }}"
  ```

- [ ] **Cross-Host Variable References**
  ```yaml
  # Reference variables from other hosts
  web_servers: "{{ groups['webdev'] | map('extract', hostvars, 'ansible_host') | list }}"
  analytics_endpoints: "{{ groups['analytics'] | map('extract', hostvars, 'jupyter_port') | list }}"
  ```

#### 2.4 Variable Validation & Documentation
- [ ] **Implement Variable Validation**
  ```yaml
  - name: Validate required variables
    assert:
      that:
        - app_environment is defined
        - app_environment in ['development', 'staging', 'production']
        - nginx_port is number
        - nginx_port > 1024
      fail_msg: "Invalid configuration detected"
  ```

**Deliverables:**
- Comprehensive variable hierarchy
- Environment-specific variable examples
- Dynamic variable generation examples
- Variable validation implementations

---

## 🎯 Phase 3: Ansible Vault & Secrets Management

### Why This Is Critical for Enterprise Ansible
Secrets management demonstrates security-conscious automation and production readiness.

### Tasks to Complete:

#### 3.1 Vault Structure Implementation
```bash
ansible/vault/
├── group_vars/
│   ├── all/
│   │   └── vault.yml
│   ├── webdev/
│   │   └── vault.yml
│   └── analytics/
│       └── vault.yml
├── host_vars/
│   ├── webdev-01/
│   │   └── vault.yml
│   └── analytics-01/
│       └── vault.yml
└── passwords/
    ├── database_passwords.yml
    ├── api_keys.yml
    └── certificates.yml
```

#### 3.2 Create Encrypted Variables
- [ ] **Database Credentials**
  ```bash
  # Create encrypted database passwords
  ansible-vault create ansible/vault/group_vars/webdev/vault.yml
  ```
  ```yaml
  # Contents (encrypted)
  vault_mysql_root_password: "super_secure_password"
  vault_mysql_app_password: "app_secure_password"
  vault_database_host: "172.20.0.100"
  ```

- [ ] **API Keys and Certificates**
  ```bash
  ansible-vault create ansible/vault/passwords/api_keys.yml
  ```
  ```yaml
  vault_github_token: "ghp_xxxxxxxxxxxx"
  vault_aws_access_key: "AKIAIOSFODNN7EXAMPLE"
  vault_jwt_secret: "your-256-bit-secret"
  ```

#### 3.3 Vault Integration Patterns
- [ ] **Variable Mixing Patterns**
  ```yaml
  # group_vars/webdev/main.yml (unencrypted)
  mysql_user: webapp
  mysql_database: app_db
  mysql_password: "{{ vault_mysql_app_password }}"
  
  # Reference encrypted variables in templates
  database_url: "mysql://{{ mysql_user }}:{{ mysql_password }}@{{ vault_database_host }}/{{ mysql_database }}"
  ```

- [ ] **Conditional Vault Usage**
  ```yaml
  # Use vault only in production
  ssl_certificate_key: "{{ vault_ssl_key if app_environment == 'production' else '/dev/null' }}"
  ```

#### 3.4 Multiple Vault Files
- [ ] **Environment-Specific Vaults**
  ```bash
  # Different vault passwords for different environments
  ansible-vault create --vault-id dev@prompt ansible/vault/dev_secrets.yml
  ansible-vault create --vault-id prod@prompt ansible/vault/prod_secrets.yml
  ```

- [ ] **Vault Password Files**
  ```bash
  # Create vault password scripts
  echo '#!/bin/bash\necho "development_vault_password"' > .vault_dev_pass
  echo '#!/bin/bash\necho "production_vault_password"' > .vault_prod_pass
  chmod +x .vault_*_pass
  ```

**Deliverables:**
- Multi-environment vault structure
- Encrypted secrets for all sensitive data
- Vault integration in roles and playbooks
- Vault password management strategy

---

## 🎯 Phase 4: Advanced Jinja2 Templates & Configuration Management

### Why This Shows Advanced Ansible Skills
Template mastery demonstrates ability to create dynamic, environment-aware configurations.

### Tasks to Complete:

#### 4.1 Advanced Nginx Configuration Templates
- [ ] **Create `ansible/roles/webdev/templates/nginx/nginx.conf.j2`**
  ```jinja2
  # Dynamic worker processes based on CPU cores
  user {{ nginx_user | default('www-data') }};
  worker_processes {{ ansible_processor_vcpus | default(1) }};
  pid /run/nginx.pid;
  
  # Environment-specific settings
  {% if app_environment == 'production' %}
  error_log /var/log/nginx/error.log warn;
  {% else %}
  error_log /var/log/nginx/error.log debug;
  {% endif %}
  
  events {
      worker_connections {{ nginx_worker_connections | default(1024) }};
      {% if ansible_processor_vcpus > 1 %}
      use epoll;
      multi_accept on;
      {% endif %}
  }
  
  http {
      # Security headers for production
      {% if app_environment == 'production' %}
      add_header X-Frame-Options DENY;
      add_header X-Content-Type-Options nosniff;
      add_header X-XSS-Protection "1; mode=block";
      {% endif %}
      
      # Upstream configuration for load balancing
      {% if groups['webdev'] | length > 1 %}
      upstream backend {
          {% for host in groups['webdev'] %}
          server {{ hostvars[host]['ansible_host'] }}:{{ hostvars[host]['app_port'] | default(8080) }};
          {% endfor %}
      }
      {% endif %}
      
      include /etc/nginx/sites-enabled/*;
  }
  ```

#### 4.2 Virtual Host Templates with Conditional Logic
- [ ] **Create `ansible/roles/webdev/templates/nginx/vhost.conf.j2`**
  ```jinja2
  server {
      listen {{ nginx_port | default(80) }};
      server_name {{ inventory_hostname }}.{{ domain_name | default('local') }};
      
      # SSL configuration (production only)
      {% if ssl_enabled | default(false) and app_environment == 'production' %}
      listen 443 ssl http2;
      ssl_certificate {{ ssl_cert_path }}/{{ inventory_hostname }}.crt;
      ssl_certificate_key {{ ssl_cert_path }}/{{ inventory_hostname }}.key;
      ssl_protocols TLSv1.2 TLSv1.3;
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384;
      {% endif %}
      
      root {{ web_root | default('/var/www/html') }};
      index index.html index.php;
      
      # PHP handling
      location ~ \.php$ {
          {% if php_enabled | default(true) %}
          fastcgi_pass unix:/var/run/php/php{{ php_version | default('8.1') }}-fpm.sock;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          include fastcgi_params;
          {% else %}
          return 404;
          {% endif %}
      }
      
      # Environment-specific logging
      {% if app_environment == 'development' %}
      access_log /var/log/nginx/{{ inventory_hostname }}_access.log combined;
      error_log /var/log/nginx/{{ inventory_hostname }}_error.log debug;
      {% else %}
      access_log /var/log/nginx/{{ inventory_hostname }}_access.log;
      error_log /var/log/nginx/{{ inventory_hostname }}_error.log;
      {% endif %}
  }
  ```

#### 4.3 Analytics Environment Templates
- [ ] **Create `ansible/roles/analytics/templates/jupyter/jupyter_lab_config.py.j2`**
  ```jinja2
  # Jupyter Lab Configuration for {{ inventory_hostname }}
  # Generated by Ansible on {{ ansible_date_time.iso8601 }}
  
  c = get_config()
  
  # Server configuration
  c.ServerApp.ip = '{{ jupyter_bind_ip | default("0.0.0.0") }}'
  c.ServerApp.port = {{ jupyter_port | default(8888) }}
  c.ServerApp.open_browser = False
  c.ServerApp.allow_root = True
  
  # Security settings based on environment
  {% if app_environment == 'production' %}
  c.ServerApp.token = '{{ vault_jupyter_token }}'
  c.ServerApp.password = '{{ vault_jupyter_password_hash }}'
  {% else %}
  c.ServerApp.token = ''
  c.ServerApp.password = ''
  {% endif %}
  
  # Resource limits based on available memory
  {% set memory_limit_mb = (ansible_memtotal_mb * 0.8) | int %}
  c.ResourceUseDisplay.mem_limit = {{ memory_limit_mb * 1024 * 1024 }}
  
  # Analytics-specific extensions
  {% for extension in jupyter_extensions | default([]) %}
  c.ExtensionApp.load_extensions = '{{ extension }}'
  {% endfor %}
  
  # Notebook directory structure
  c.ServerApp.notebook_dir = '{{ jupyter_notebook_dir | default("/opt/notebooks") }}'
  
  # Kernel settings
  c.MappingKernelManager.default_kernel_name = 'python3'
  ```

#### 4.4 Dynamic Configuration Generation
- [ ] **Create `ansible/roles/analytics/templates/spark/spark-defaults.conf.j2`**
  ```jinja2
  # Spark Configuration for {{ inventory_hostname }}
  # Dynamically generated based on available resources
  
  # Memory configuration based on available RAM
  {% set total_memory_gb = (ansible_memtotal_mb / 1024) | round(1) %}
  {% set executor_memory = ((total_memory_gb * 0.6) | round(1)) ~ 'g' %}
  {% set driver_memory = ((total_memory_gb * 0.2) | round(1)) ~ 'g' %}
  
  spark.executor.memory              {{ executor_memory }}
  spark.driver.memory                {{ driver_memory }}
  spark.driver.maxResultSize         {{ ((total_memory_gb * 0.1) | round(1)) ~ 'g' }}
  
  # CPU configuration
  spark.executor.cores               {{ ansible_processor_vcpus | default(1) }}
  spark.sql.adaptive.enabled        true
  spark.sql.adaptive.coalescePartitions.enabled true
  
  # Cluster configuration
  {% if groups['analytics'] | length > 1 %}
  spark.master                       spark://{{ groups['analytics'][0] }}:7077
  {% for host in groups['analytics'][1:] %}
  # Worker: {{ hostvars[host]['ansible_host'] }}
  {% endfor %}
  {% else %}
  spark.master                       local[*]
  {% endif %}
  
  # Environment-specific settings
  {% if app_environment == 'development' %}
  spark.sql.warehouse.dir            /opt/spark/warehouse
  spark.eventLog.enabled             true
  spark.eventLog.dir                 /opt/spark/logs
  {% endif %}
  
  # Security settings for production
  {% if app_environment == 'production' %}
  spark.authenticate                 true
  spark.authenticate.secret          {{ vault_spark_secret }}
  spark.network.crypto.enabled       true
  {% endif %}
  ```

#### 4.5 Template Macros and Includes
- [ ] **Create reusable template macros**
  ```jinja2
  {# ansible/roles/common/templates/macros/logging.j2 #}
  {% macro log_config(service_name, log_level='info') %}
  # Logging configuration for {{ service_name }}
  log_level: {{ log_level if app_environment == 'development' else 'warn' }}
  log_file: /var/log/{{ service_name }}/{{ service_name }}.log
  log_rotation: daily
  log_retention: {{ '7' if app_environment == 'development' else '30' }}
  {% endmacro %}
  
  {# Usage in other templates #}
  {% from 'macros/logging.j2' import log_config %}
  {{ log_config('nginx', 'debug') }}
  ```

**Deliverables:**
- Advanced Nginx configuration templates
- Dynamic Jupyter Lab configurations
- Spark cluster configuration templates
- Reusable template macros
- Environment-aware template logic

---

## 🎯 Phase 5: Advanced Playbook Features & Error Handling

### Tasks to Complete:

#### 5.1 Advanced Playbook Patterns
- [ ] **Implement Block/Rescue/Always**
  ```yaml
  - name: Deploy application with rollback
    block:
      - name: Stop application
        service: name=myapp state=stopped
      - name: Deploy new version
        copy: src=app.jar dest=/opt/app/
      - name: Start application
        service: name=myapp state=started
    rescue:
      - name: Rollback on failure
        copy: src=/opt/app/backup/app.jar dest=/opt/app/
      - name: Restart with previous version
        service: name=myapp state=restarted
    always:
      - name: Cleanup temporary files
        file: path=/tmp/deploy state=absent
  ```

#### 5.2 Conditional Execution Patterns
- [ ] **Complex When Conditions**
  ```yaml
  - name: Install production packages
    package:
      name: "{{ item }}"
    loop: "{{ production_packages }}"
    when:
      - app_environment == 'production'
      - ansible_distribution == 'Ubuntu'
      - ansible_distribution_version is version('20.04', '>=')
  ```

#### 5.3 Dynamic Task Generation
- [ ] **Include Tasks Based on Variables**
  ```yaml
  - name: Include OS-specific tasks
    include_tasks: "{{ ansible_distribution | lower }}.yml"
  
  - name: Include role-specific configurations
    include_tasks: "setup_{{ server_role }}.yml"
    when: server_role is defined
  ```

**Deliverables:**
- Error handling and recovery procedures
- Complex conditional logic examples
- Dynamic task inclusion patterns
- Production-ready playbook structure

---

## 📈 Success Metrics & Learning Outcomes

### Phase 1: Role Mastery
- ✅ Galaxy-compatible role structure
- ✅ Role dependencies working correctly
- ✅ Parameterized roles with defaults
- ✅ Role composition patterns implemented

### Phase 2: Variable Expertise
- ✅ 22-level variable precedence demonstration
- ✅ Environment-specific variable management
- ✅ Dynamic variable generation
- ✅ Variable validation and documentation

### Phase 3: Security & Vault
- ✅ Multi-environment vault structure
- ✅ Encrypted sensitive data
- ✅ Vault integration in all components
- ✅ Secure password management

### Phase 4: Template Mastery
- ✅ Complex Jinja2 templates with logic
- ✅ Environment-aware configurations
- ✅ Dynamic resource allocation
- ✅ Reusable template components

### Phase 5: Production Readiness
- ✅ Comprehensive error handling
- ✅ Complex conditional logic
- ✅ Dynamic execution patterns
- ✅ Enterprise-grade playbooks

---

## 💼 Portfolio Value

This roadmap transforms your basic Ansible setup into a showcase of advanced automation skills that demonstrate:

1. **Enterprise Architecture**: Role-based design patterns
2. **Security Consciousness**: Proper secrets management
3. **Scalability**: Environment-aware automation
4. **Maintainability**: Template-driven configuration
5. **Production Readiness**: Error handling and validation

These skills are essential for Senior DevOps Engineer, Site Reliability Engineer, and Infrastructure Automation roles.