# OS-Jackfruit — Multi-Container Runtime

A lightweight Linux-based container runtime implemented in C, designed to demonstrate core Operating System concepts such as process isolation, namespaces, memory monitoring, and container lifecycle management.

This project simulates a minimal container orchestration environment with support for multiple containers, logging, and kernel-level memory monitoring.

---

## 📌 Project Overview

This project implements a **mini container runtime** that allows users to:

* Create and manage multiple containers
* Execute isolated processes using `chroot`
* Monitor container memory usage via a custom kernel module
* Capture logs from container processes
* Enforce soft and hard memory limits
* Run CPU and memory stress workloads inside containers

The system is composed of:

* A **user-space container engine**
* A **kernel module (monitor.ko)** for memory tracking
* Multiple **root filesystem environments (rootfs)**

---

## ⚙️ Features

### ✅ Container Lifecycle Management

* Start, stop, and inspect containers
* View container states: `starting`, `running`, `stopped`, `killed`, `exited`

### ✅ Process Isolation

* Uses `chroot` to isolate filesystem
* Mounts `/proc` inside containers

### ✅ Logging System

* Capture and display container output using:

  ```bash
  ./engine logs <container_id>
  ```

### ✅ Kernel-Level Memory Monitoring

* Custom Linux kernel module (`monitor.ko`)
* Tracks RSS (Resident Set Size)
* Enforces:

  * Soft memory limit
  * Hard memory limit

### ✅ Resource Stress Testing

* `memory_hog` → simulates memory pressure
* `cpu_hog` → simulates CPU load
* `io_pulse` → simulates I/O activity

---

## 📁 Project Structure

```
OS-Jackfruit/
│── boilerplate/
│   ├── engine            # Container runtime binary
│   ├── engine.c          # Core runtime implementation
│   ├── monitor.c         # Kernel module source
│   ├── monitor.ko        # Compiled kernel module
│   ├── memory_hog.c      # Memory stress program
│   ├── cpu_hog.c         # CPU stress program
│   └── io_pulse.c        # I/O stress program
│
│── screenshots/          # Output screenshots (for submission)
│── code/                 # Important code files (optional)
│── README.md
│── project-guide.md
│── run_all.sh
```

---

## 🚀 How to Run

### 1️⃣ Navigate to project

```bash
cd OS-Jackfruit/boilerplate
```

### 2️⃣ Build components

```bash
make
```

### 3️⃣ Load kernel module

```bash
sudo insmod monitor.ko
```

Verify:

```bash
lsmod | grep monitor
ls -l /dev/container_monitor
```

---

### 4️⃣ Start Supervisor

```bash
cd ~/OS-Jackfruit
sudo ./boilerplate/engine supervisor ./rootfs-base
```

Expected Output:

```
Supervisor ready...
mount proc OK
chroot OK
```

---

### 5️⃣ Run Containers

#### 🔹 Start container

```bash
sudo ./engine start c1 ../rootfs-alpha "/bin/sleep 120"
```

#### 🔹 List containers

```bash
sudo ./engine ps
```

---

### 6️⃣ Logging Demo

```bash
sudo ./engine start logtest ../rootfs-alpha "/bin/sh /test.sh"
sudo ./engine logs logtest
```

---

### 7️⃣ CPU Stress Test

```bash
sudo ./engine start cpu1 ../rootfs-alpha "/cpu_hog"
sudo ./engine logs cpu1
```

---

### 8️⃣ Memory Stress Test

```bash
sudo ./engine start memtest ../rootfs-alpha "/memory_hog --soft-mib 2 --hard-mib 10"
sleep 5
sudo dmesg | grep LIMIT
```

---

## 🧠 Memory Monitoring Explanation

The kernel module monitors container processes and logs:

* **SOFT LIMIT**

  * Warning when memory usage exceeds threshold
* **HARD LIMIT**

  * Process is terminated

Example:

```
[container_monitor] SOFT LIMIT container=memtest ...
[container_monitor] HARD LIMIT container=memtest ...
```

---

## 📸 Screenshots

All required outputs are stored in the `screenshots/` folder:

| Screenshot | Description                     |
| ---------- | ------------------------------- |
| SS1        | Kernel module loaded            |
| SS2        | Container creation              |
| SS3        | Container listing (`engine ps`) |
| SS4        | Logging demonstration           |
| SS5        | CPU hog execution               |
| SS6        | Memory limit monitoring         |
| SS7        | Soft & Hard limit logs          |
| SS8        | Supervisor mode                 |
| SS9        | Runtime logs                    |
| SS10       | Cleanup and module unload       |

---

## 🧹 Cleanup

Unload kernel module:

```bash
sudo rmmod monitor
```

Verify:

```bash
dmesg | grep "Module unloaded"
```

---

## ⚠️ Notes

* Root privileges (`sudo`) are required
* Kernel module may show:

  ```
  module verification failed → expected (not an error)
  ```
* Empty rootfs directories may cause execution errors

---

## 🎯 Learning Outcomes

This project demonstrates:

* Linux container fundamentals
* Process isolation using `chroot`
* Kernel module development
* Resource monitoring
* System call interactions
* Logging pipelines

---

## 📚 Technologies Used

* C Programming
* Linux Kernel Modules
* GCC Compiler
* Bash Scripting
* Ubuntu (VM / UTM)

---

## 👨‍💻 Author

**Harsha Kamaraj**
PES University
Operating Systems Project

---

## 📌 Conclusion

This project provides a simplified yet powerful insight into how modern container runtimes (like Docker) function internally, covering both user-space and kernel-space interactions.

---
