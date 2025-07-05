# Ansible
## Project Overview
This project demonstrates advanced Ansible automation skills by managing heterogeneous infrastructure using Docker Compose. You'll orchestrate 6 target machines (3 Ubuntu for web development, 3 CentOS for data analytics) from a central Ansible control node, showcasing cross-platform automation, advanced playbook techniques, and enterprise-grade configuration management.

## Architecture
- **1 Ansible Control Node** (Ubuntu-based)
- **3 Ubuntu Web Development Servers** (LAMP/LEMP stack with development tools)
- **3 CentOS Data Analytics Servers** (Python data science stack with Jupyter, Apache Spark, and monitoring)

## Learning Objectives & Advanced Skills Demonstrated

### Core Ansible Concepts
- Multi-platform inventory management (Ubuntu/CentOS)
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

## Required Implementations

### 1. Docker Compose Infrastructure Setup
Create a `docker-compose.yml` that spins up:
- 1 Ansible control node with SSH access to all targets
- 3 Ubuntu containers (webdev-01, webdev-02, webdev-03)
- 3 CentOS containers (analytics-01, analytics-02, analytics-03)
- Proper networking and volume mounts
- SSH key distribution for passwordless access

### 2. Advanced Inventory Management
**Dynamic Inventory Features:**
- Group-based organization (webdev, analytics, ubuntu, centos)
- Host-specific variables for different server roles
- Connection parameters and SSH configuration
- Custom groups for different deployment scenarios

### 3. Ubuntu Web Development Environment
**Must Configure:**
- **Web Stack**: Nginx + PHP-FPM + MySQL/MariaDB
- **Development Tools**: Git, Composer, Node.js, npm, VS Code Server
- **Security**: UFW firewall, SSL certificates, fail2ban
- **Monitoring**: Prometheus node exporter, custom metrics
- **Load Balancing**: Nginx upstream configuration across 3 servers

**Advanced Requirements:**
- Blue-green deployment capability
- Database replication setup between servers
- Automated SSL certificate management
- Custom PHP modules and extensions
- Development environment isolation with Docker

### 4. CentOS Data Analytics Environment
**Must Configure:**
- **Python Stack**: Python 3.9+, pip, virtualenv, conda
- **Data Science Tools**: Jupyter Lab, pandas, numpy, scikit-learn
- **Big Data**: Apache Spark cluster (1 master, 2 workers)
- **Databases**: PostgreSQL with TimescaleDB, Redis
- **Monitoring**: Grafana, InfluxDB, custom dashboards
- **Security**: SELinux configuration, firewalld

**Advanced Requirements:**
- Spark cluster auto-discovery and scaling
- Jupyter multi-user configuration with authentication
- Data pipeline automation
- Custom fact modules for cluster health
- Automated backup and recovery procedures

### 5. Cross-Platform Common Configuration
**Unified Management:**
- User management with sudo privileges
- SSH hardening and key management
- Log aggregation (rsyslog/journald to central location)
- NTP synchronization
- Security updates automation
- Custom motd and system branding

### 6. Advanced Playbook Requirements

#### Site.yml (Master Playbook)
```yaml
# Must demonstrate:
- Pre-flight checks and validation
- Conditional execution based on target groups
- Error handling and rollback procedures
- Notification systems (Slack/email integration)
- Execution time optimization
```

#### Role-Based Architecture
Each role must include:
- `tasks/main.yml` with imported task files
- `handlers/main.yml` with restart/reload handlers
- `templates/` with Jinja2 templates
- `files/` with static configuration files
- `vars/main.yml` and `defaults/main.yml`
- `meta/main.yml` with dependencies
- `molecule/` testing scenarios (advanced)

#### Variable Management Strategy
- Environment-specific variables (dev/staging/prod)
- Encrypted secrets using Ansible Vault
- Custom facts for dynamic configuration
- Variable precedence demonstration
- Group and host-specific overrides

### 7. Advanced Automation Scenarios

#### Rolling Updates
- Implement rolling updates for web servers
- Database migration automation
- Zero-downtime deployment strategies
- Rollback procedures

#### Health Checks and Monitoring
- Custom health check modules
- Automated alerting configuration
- Performance monitoring setup
- Log analysis and alerting

#### Backup and Recovery
- Automated backup schedules
- Database backup and restoration
- Configuration backup and versioning
- Disaster recovery procedures

### 8. Security Implementation
- Ansible Vault for all sensitive data
- SSL/TLS certificate management
- Firewall configuration (UFW/firewalld)
- Security scanning and compliance checks
- User access control and privilege escalation

## Deliverables & Evaluation Criteria

### Required Deliverables
1. **Complete Docker Compose setup** with all 7 containers
2. **Fully functional playbooks** that can provision from scratch
3. **Comprehensive inventory** with proper grouping and variables
4. **Role-based architecture** with reusable components
5. **Documentation** of all configurations and procedures
6. **Testing procedures** and validation scripts

### Advanced Features (Bonus Points)
- **Molecule testing** for role validation
- **CI/CD integration** with GitLab/Jenkins
- **Custom modules** for specific tasks
- **Prometheus monitoring** with custom metrics
- **Grafana dashboards** for infrastructure visualization
- **Ansible Tower/AWX** integration examples

### Evaluation Criteria
1. **Playbook Quality** (25%)
   - Idempotency and error handling
   - Code organization and reusability
   - Variable management and templating

2. **Technical Complexity** (25%)
   - Multi-platform automation
   - Advanced Ansible features usage
   - Integration complexity

3. **Security Implementation** (20%)
   - Vault usage and secret management
   - System hardening
   - Access control

4. **Documentation & Testing** (15%)
   - Clear documentation
   - Testing procedures
   - Troubleshooting guides

5. **Innovation & Best Practices** (15%)
   - Creative solutions
   - Industry best practices
   - Performance optimization

## Getting Started

### Prerequisites
- Docker and Docker Compose installed
- Basic understanding of Linux administration
- Git for version control
- SSH client configured

### Quick Start
1. Clone the repository
2. Run `docker-compose up -d` to start all containers
3. Configure SSH access between control node and targets
4. Execute `ansible-playbook -i inventory/hosts.yml playbooks/site.yml`
5. Verify all services are running and accessible

### Testing Your Implementation
- All web servers should serve content through load balancer
- Jupyter Lab should be accessible on all analytics servers
- Spark cluster should be functional with job submission
- Monitoring dashboards should show all system metrics
- All security measures should be properly configured

## Success Metrics
- **Zero manual configuration** required after playbook execution
- **Sub-10 minute** complete environment provisioning
- **100% idempotent** playbook runs
- **All services operational** with proper monitoring
- **Security compliance** with industry standards

This project will demonstrate enterprise-level Ansible skills and showcase your ability to manage complex, heterogeneous infrastructure using infrastructure as code principles.
