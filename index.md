# CMPN202 Operating Systems Coursework
**Student ID:** A00026547
**Module:** Operating Systems (CMPN202)

---

## Project Overview
This technical journal documents the deployment, configuration, and analysis of a secure Linux server infrastructure. Over the course of 7 weeks, I have implemented a dual-system architecture consisting of a headless Ubuntu Server and an administrative workstation.

The project covers system hardening (SSH, Firewalls, AppArmor), performance testing with Python and standard Linux tools, automation scripting, and a final security audit using Lynis.

---

## Technical Journal: Weekly Progress

Please navigate to the specific week below to view detailed documentation, screenshots, and command-line evidence.

### Phase 1: Planning & Setup
* **[Week 1: System Planning and Distribution Selection](week1.md)**
   * System Architecture Diagram
   * Choice of Ubuntu Server & Workstation Setup
   * Initial Network Configuration (Host-only Adapter)

### Phase 2: Security & Testing Strategy
* **[Week 2: Security Planning and Testing Methodology](week2.md)**
   * Performance Testing Plan
   * Security Configuration Checklist
   * Threat Modelling

### Phase 3: Application Selection
* **[Week 3: Application Selection for Performance Testing](week3.md)**
   * Workload Selection (CPU, RAM, I/O, Network)
   * Python HTTP Server setup
   * Monitoring Strategy

### Phase 4: Implementation
* **[Week 4: Initial System Configuration & Security](week4.md)**
   * User & Privilege Management
   * SSH Hardening (Key-based Auth)
   * UFW Firewall Implementation

### Phase 5: Advanced Security & Automation
* **[Week 5: Advanced Security and Monitoring Infrastructure](week5.md)**
   * Access Control (AppArmor)
   * Fail2Ban Intrusion Detection
   * Custom Scripts: `security-baseline.sh` & `monitor-server.sh`

### Phase 6: Analysis
* **[Week 6: Performance Evaluation and Analysis](week6.md)**
   * Baseline vs. Load Testing Results
   * Optimisation 1: Tuning Swappiness
   * Optimisation 2: Network Buffer Tuning
   * Quantitative Data Analysis

### Phase 7: Audit
* **[Week 7: Security Audit and System Evaluation](week7.md)**
   * Lynis Security Scan Results (Before/After)
   * Network Security Assessment (nmap)
   * Final Service Inventory & Risk Assessment

---
