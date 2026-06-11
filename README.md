# Govind_G_Major_Project_SecureBoot-BBB
Lightweight Secure Boot implementation and integrity verification for BeagleBone Black using HMAC and hash validation.
---

# Overview

This project demonstrates the implementation of a lightweight Secure Boot mechanism on the BeagleBone Black platform using U-Boot FIT image verification, SHA256 integrity checking, and HMAC-based authentication.

The system verifies the integrity and authenticity of kernel images before booting, helping prevent unauthorized or tampered kernel execution in embedded systems.

The project also demonstrates tampered kernel detection using modified FIT images.

---

# Principle of Secure Boot

Secure Boot is a security mechanism used to ensure that only trusted and verified software components are allowed to boot on a device.

The main objective is to:

* prevent unauthorized firmware or kernel execution
* detect tampered boot images
* maintain system integrity during the boot process

In this project, Secure Boot works using:

1. FIT Image Verification
2. SHA256 Hash Integrity Checking
3. HMAC-Based Authentication

---

# Working Principle

## 1. FIT Image Creation

The Linux kernel image is packed into a FIT (Flattened Image Tree) image using U-Boot tools.

The FIT image contains:

* kernel image
* metadata
* SHA256 hash information

---

## 2. SHA256 Integrity Verification

During boot, U-Boot calculates the SHA256 hash of the loaded kernel image and compares it with the trusted hash stored inside the FIT image.

If:

* hashes match → boot continues
* hashes mismatch → boot is denied

This helps detect tampered kernel images.

---

## 3. HMAC-Based Authentication

In addition to SHA256 verification, HMAC authentication is implemented using a secret key.

HMAC improves security by adding authentication along with integrity verification.

Even if an attacker modifies the kernel image, they cannot generate a valid HMAC without the secret key.

---

# Hardware Components Used

* BeagleBone Black
* FTDI USB-to-Serial Cable
* UART Interface
* MicroSD Card
* Ubuntu Linux Host System

---

# Software and Tools Used

* U-Boot
* Linux Kernel
* OpenSSL
* Ubuntu Linux
* FIT Image Tools (`mkimage`)
* Bash Scripting

---

# System Architecture

```text
Host System
   ↓
Generate FIT Image
   ↓
SHA256 Hash Generation
   ↓
HMAC Generation
   ↓
Copy to SD Card
   ↓
BeagleBone Black Boot Process
   ↓
U-Boot Verification
   ↓
Kernel Boot
```

---

# Implementation Steps

## Step 1: Create FIT Image

Kernel image was converted into a FIT image using U-Boot image tools.

Example:

```bash
mkimage -f kernel.its kernel.itb
```

---

## Step 2: Load FIT Image in U-Boot

```bash
mmc dev 0
fatload mmc 0:1 0x82000000 kernel.itb
```

---

## Step 3: Verify FIT Image Integrity

```bash
iminfo 0x82000000
```

Successful verification output:

```text
Verifying Hash Integrity ... sha256+ OK
```

---

## Step 4: Boot Verified Kernel

```bash
bootm 0x82000000
```

---

## Step 5: Tampered Kernel Detection

A modified kernel image (`tampered_kernel.itb`) was created by corrupting raw bytes.

When the tampered image was loaded:

```text
Bad Data Hash
Hash Integrity failed
```

The kernel boot process was blocked successfully.

---

# HMAC Authentication Implementation

## Generate HMAC Key

```bash
echo "securebootsecretkey123" > hmac.key
```

---

## Generate Trusted HMAC

```bash
openssl dgst -sha256 -hmac "$(cat hmac.key)" kernel.itb > kernel.hmac
```

---

## Run HMAC Verification

```bash
./verify_hmac.sh
```

---

# HMAC Verification Results

## Original Kernel

```text
HMAC Verification SUCCESS
Kernel image is authentic.
```

---

## Tampered Kernel

```text
HMAC Verification FAILED
Tampered kernel detected!
```

---

# Security Features Implemented

* Secure Boot using U-Boot
* SHA256 FIT image verification
* Tampered kernel detection
* HMAC-based authentication
* Embedded Linux security validation
* UART boot monitoring

---

# UART Connection Setup

The FTDI USB-to-Serial cable was used for UART communication between the Ubuntu host system and the BeagleBone Black.

UART settings:

* Baud Rate: 115200
* Data Bits: 8
* Stop Bits: 1
* Parity: None

Connection command:

```bash
screen /dev/ttyUSB0 115200
```

---

# Results

The project successfully demonstrated:

* Secure Boot verification on BeagleBone Black
* SHA256 integrity checking
* HMAC authentication
* detection of tampered kernel images
* prevention of unauthorized boot execution

---

# Future Enhancements

* RSA signature verification
* TPM integration
* Secure key storage
* Full chain-of-trust implementation
* Hardware-backed authentication

---

# Author

Govind G

M.Tech Cybersecurity

Embedded Systems and Secure Boot Research

