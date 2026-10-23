# 🛡️ Secure Digital Investigation System (SDIS)

> *"Every byte of evidence matters. Every action is traced."*

[![C++17](https://img.shields.io/badge/Language-C++17-blue.svg)](https://en.cppreference.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform: CLI](https://img.shields.io/badge/Platform-Command%20Line-lightgrey.svg)](main.cpp)

**SDIS** is a secure, command-line case and evidence management platform built from scratch in **C++17**[cite: 17, 19]. Designed around the core principles of digital forensics and law enforcement operations, it provides a structured, auditable environment to handle criminal cases, track evidence integrity, and enforce strict chain-of-custody protocols[cite: 19].

---

## 🚀 Project Overview & Motivation

In traditional legal and investigative workflows, missing dockets or unverified evidence can cause cases to collapse[cite: 25]. SDIS addresses these real-world challenges by baking core security features directly into the software architecture rather than treating them as an afterthought[cite: 19]:
* **Eliminating Lost Dockets:** Everything is digitized into structured memory states[cite: 19].
* **Tamper Detection:** Custom mathematical hashing ensures any modification to case records or evidence is instantly flagged[cite: 6, 22].
* **Complete Accountability:** Every login and evidence modification leaves a permanent, unskippable audit trail[cite: 23].

---

## 👥 User Roles & Permissions

SDIS divides operational responsibilities into two distinct roles to enforce checks and balances[cite: 20]:

1. **Detectives (Investigators):** 
   * Responsible for day-to-day casework (creating cases, logging evidence, and tracking suspects)[cite: 20].
   * Operate under a **request-and-approval workflow**: detectives start with no permissions and must request access rights (`CREATE CASE` or `REVIEW CASE`) from a Commander[cite: 20, 21].
   * Restricted to viewing and managing *only* the cases they personally created (ownership isolation)[cite: 22].

2. **Commanders (Supervisors):**
   * Oversee system operations, review pending access requests, and individually approve or deny detective permissions[cite: 20, 21].
   * Monitor system-wide activity through dedicated audit logs[cite: 20].

---

## ✨ Key Features

* **Role-Based Access Control (RBAC):** Restricts high-privilege actions using multi-layer validation[cite: 20, 24].
* **Hash-Based Integrity Verification:** Utilizes a custom hashing algorithm (`simpleHash`) to fingerprint case summaries and individual evidence descriptions upon creation[cite: 6, 22]. If text is modified later, reviews trigger an automatic `WARNING: EVIDENCE TAMPERED!` or `CASE TAMPERING DETECTED!` alert[cite: 12, 22].
* **Automated Audit Logs:** 
  * **Login History:** Tracks every successful login with timestamps and roles[cite: 23].
  * **Tamper History:** Automatically records any evidence modifications, capturing who changed what, in which case, and exactly when[cite: 8, 23].
* **Zero External Dependencies:** Built entirely using the native C++ Standard Library (`<iostream>`, `<vector>`, `<string>`, `<ctime>`, `<algorithm>`)[cite: 4, 19].

---

## 🛠️ System Architecture & Code Structure

The application is architected around robust C++ `struct` entities and in-memory vectors:

* **`Evidence`**: Stores item text descriptions alongside their integrity hash fingerprints[cite: 6].
* **`Suspect`**: Tracks suspect names, profiling reasons, and initial suspicion scoring[cite: 7].
* **`Case`**: Manages case identifiers, crime types, narrative summaries, creator tags, and nested vectors of evidence and suspects[cite: 7].
* **`Request`, `LoginRecord`, & `TamperRecord`**: Manage state queues and permanent chronological logs[cite: 7, 8].

---

## ⚙️ Getting Started & Compilation

### Prerequisites
* A C++ compiler supporting **C++17** (e.g., `g++`, `clang++`, or MSVC).

### Cloning and Compiling
1. Clone the repository:
   ```bash
   git clone [https://github.com/Mco-Logic/SDIS-.git](https://github.com/Mco-Logic/SDIS-.git)
   cd SDIS-
