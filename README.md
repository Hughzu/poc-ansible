# Ansible Multi-Environment Project
## Project Overview
This project demonstrates advanced Ansible automation skills by managing heterogeneous infrastructure using Docker Compose. You'll orchestrate 4 target machines (2 Ubuntu for web development, 2 Debian for data analytics) from a central Ansible control node, showcasing cross-platform automation, advanced playbook techniques, and enterprise-grade configuration management.

## Architecture
- **1 Ansible Control Node** (Ubuntu 22.04-based)
- **2 Ubuntu Web Development Servers** (Ubuntu 22.04 with NGINX web stack)
- **2 Debian Data Analytics Servers** (Debian 12 with Python data science stack)

## Learning Objectives & Advanced Skills Demonstrated

### Core Ansible Concepts
- Multi-platform inventory management (Ubuntu/Debian)
- Advanced playbook organization with roles and collections
- Variable precedence and group/host-specific configurations
- Conditional logic based on OS families and distributions
- Error handling and rescue blocks
- Ansible Vault for sensitive data management

### Advanced Automation Techniques
- Custom facts and fact gathering optimization
- Handler chains and notification systems
- Template inheritance and Jinja2 advanced templating
- Loop constructs with complex data structures
- Delegation and run_once strategies
- Rolling updates and zero-downtime deployments

### Infrastructure as Code Practices
- Idempotent playbook design
- Environment-specific variable management
- Service discovery and dynamic inventories
- Configuration drift detection and remediation
- Automated testing with Molecule (optional)

## Project Structure
```
ansible-multi-env-project/
├── docker-compose.yml
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── hosts.yml
│   │   ├── group_vars/
│   │   │   ├── all.yml
│   │   │   ├── webdev.yml
│   │   │   └── analytics.yml
│   │   └── host_vars/
│   ├── playbooks/
│   │   ├── site.yml
│   │   ├── webdev-setup.yml
│   │   ├── analytics-setup.yml
│   │   ├── monitoring-setup.yml
│   │   └── maintenance.yml
│   ├── roles/
│   │   ├── common/
│   │   ├── webdev/
│   │   ├── analytics/
│   │   └── monitoring/
│   ├── templates/
│   ├── files/
│   └── vault/
│       └── secrets.yml
└── README.md
```

## Current Implementation Status

### 1. Docker Compose Infrastructure ✅
**Implemented:**
- **Ansible Control Node**: Ubuntu 22.04 with Ansible, SSH client, and Python 3
- **Web Development Servers**: 
  - webdev-01 (172.20.0.11) - Ubuntu 22.04, exposed on port 8080
  - webdev-02 (172.20.0.12) - Ubuntu 22.04, exposed on port 8081
- **Analytics Servers**:
  - analytics-01 (172.20.0.21) - Debian 12, Jupyter on port 8888, Spark UI on port 4040
  - analytics-02 (172.20.0.22) - Debian 12, Jupyter on port 8889, Spark UI on port 4041
- **Networking**: Custom bridge network (172.20.0.0/24)
- **Security**: SSH access configured with root login and password authentication

### 2. Ansible Configuration ✅
**Implemented:**
- **ansible.cfg**: Optimized configuration with fact caching, SSH optimization, and logging
- **Inventory Management**: YAML-based inventory with proper grouping:
  - `webdev` group (Ubuntu servers)
  - `analytics` group (Debian servers)
  - `ubuntu` and `debian` groups for OS-based management
- **Host Variables**: Server-specific configuration including ports, roles, and environment settings

### 3. Basic Playbook Implementation ✅
**Current Features:**
- **Connectivity Testing**: Ping test for all hosts
- **Package Management**: Cross-platform package updates (Ubuntu/Debian)
- **Web Server Setup**: NGINX installation and basic configuration
- **Analytics Environment**: Python virtual environment with Jupyter Lab, pandas, numpy, matplotlib
- **Service Management**: Automated service startup and configuration

## Required Advanced Implementations

### 1. Enhanced Web Development Environment
**To Implement:**
- **Complete Web Stack**: PHP-FPM + MySQL/MariaDB integration
- **Development Tools**: Git, Composer, Node.js, npm, VS Code Server
- **Security**: UFW firewall, SSL certificates, fail2ban
- **Load Balancing**: Nginx upstream configuration across servers
- **Monitoring**: Prometheus node exporter, custom metrics

### 2. Advanced Analytics Environment
**To Implement:**
- **Big Data Stack**: Apache Spark cluster configuration
- **Database Integration**: PostgreSQL with TimescaleDB, Redis
- **Enhanced Python Stack**: Conda environment, additional data science libraries
- **Jupyter Configuration**: Multi-user setup with authentication
- **Monitoring**: Grafana dashboards, InfluxDB integration

### 3. Cross-Platform Common Configuration
**To Implement:**
- **User Management**: Non-root users with sudo privileges
- **SSH Hardening**: Key-based authentication, disable password auth
- **Security**: Firewall configuration, automated security updates
- **Monitoring**: Centralized logging, system metrics collection
- **Backup**: Automated backup procedures

### 4. Advanced Playbook Features
**To Implement:**
- **Role-Based Architecture**: Modular roles for common, webdev, analytics
- **Variable Management**: Environment-specific variables, Ansible Vault integration
- **Error Handling**: Comprehensive error handling and rollback procedures
- **Health Checks**: Automated service validation and monitoring
- **Rolling Updates**: Zero-downtime deployment strategies

## Quick Start Guide

### Prerequisites
- Docker and Docker Compose installed
- Git for version control
- Basic understanding of Linux administration

### Getting Started
1. **Clone and Start Infrastructure**
   ```bash
   git clone <repository-url>
   cd ansible-multi-env-project
   docker-compose up -d
   ```

2. **Access Ansible Control Node**
   ```bash
   docker exec -it ansible-control bash
   cd /ansible
   ```

3. **Test Connectivity**
   ```bash
   ansible all -m ping
   ```

4. **Run Basic Setup**
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/site.yml
   ```

### Accessing Services
- **Web Servers**: 
  - webdev-01: http://localhost:8080
  - webdev-02: http://localhost:8081
- **Analytics Servers**:
  - Jupyter Lab (analytics-01): http://localhost:8888
  - Jupyter Lab (analytics-02): http://localhost:8889
  - Spark UI (analytics-01): http://localhost:4040
  - Spark UI (analytics-02): http://localhost:4041

### Manual Testing
You can SSH directly to any server for manual testing:
```bash
# From your host machine or from ansible-control container
ssh root@172.20.0.11  # webdev-01 (password: ansible123)
ssh root@172.20.0.12  # webdev-02 (password: ansible123)
ssh root@172.20.0.21  # analytics-01 (password: ansible123)
ssh root@172.20.0.22  # analytics-02 (password: ansible123)
```

## Container Details

### Ansible Control Node
- **Base Image**: Ubuntu 22.04
- **IP Address**: 172.20.0.10
- **Installed**: Ansible, Python 3, SSH client, Git
- **SSH Keys**: Auto-generated for passwordless access
- **Configuration**: Optimized ansible.cfg with fact caching

### Web Development Servers
- **Base Image**: Ubuntu 22.04
- **IP Addresses**: 172.20.0.11, 172.20.0.12
- **Ports**: 8080, 8081 (mapped to container port 80)
- **Services**: SSH daemon, Python 3, ready for web stack installation

### Analytics Servers
- **Base Image**: Debian 12
- **IP Addresses**: 172.20.0.21, 172.20.0.22
- **Ports**: 8888/8889 (Jupyter), 4040/4041 (Spark UI)
- **Services**: SSH daemon, Python 3, pip, ready for analytics stack

## Current Limitations & Next Steps

### Security Considerations
- **Current**: Using password authentication for simplicity
- **Production**: Implement SSH key-based authentication
- **Hardening**: Disable root login, create service users
- **Secrets**: Implement Ansible Vault for sensitive data

### Scalability Improvements
- **Load Balancing**: Implement proper load balancer configuration
- **Service Discovery**: Add dynamic inventory management
- **Monitoring**: Implement comprehensive monitoring stack
- **Backup**: Add automated backup and recovery procedures

### Advanced Features to Implement
- **Molecule Testing**: Add role validation and testing
- **CI/CD Integration**: GitHub Actions or Jenkins pipeline
- **Custom Modules**: Develop project-specific Ansible modules
- **Compliance**: Security scanning and compliance checks

## Success Metrics
- **✅ Infrastructure Deployment**: All 5 containers running successfully
- **✅ Basic Connectivity**: SSH access and Ansible ping working
- **✅ Web Services**: NGINX serving test pages
- **✅ Analytics Environment**: Jupyter Lab accessible with Python packages
- **⏳ Advanced Features**: Role-based architecture, security hardening
- **⏳ Monitoring**: Comprehensive monitoring and alerting
- **⏳ Zero-Downtime**: Rolling updates and deployment strategies

This foundation provides a solid starting point for demonstrating enterprise-level Ansible skills and can be extended with additional advanced features as needed.