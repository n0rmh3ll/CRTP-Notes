# 🛡️ CRTP Study Notes

Welcome to my **Certified Red Team Professional (CRTP)** study repository. This space contains a comprehensive collection of notes, cheatsheets, and tactical guides compiled while preparing for the CRTP exam.


## 📖 Overview
The goal of these notes is to document the methodologies used to attack and defend Active Directory environments. From initial enumeration to domain dominance and persistence, these pages cover the core pillars of the CRTP curriculum.



---

## 🛠️ Core Focus Areas

* **Enumeration:** Mapping the attack surface using PowerView and BloodHound.
* **Evasion:** Bypassing AMSI, PowerShell logging, and EDR/MDE solutions.
* **Privilege Escalation:** Exploiting misconfigured GPOs, Kerberoasting, and ADCS.
* **Lateral Movement:** Tradecraft for moving across the domain using WinRM and Mimikatz.
* **Persistence:** Maintaining access through Golden/Silver tickets and ACL abuse.

---

## 🚀 Lab Environment & Tools
Most of the techniques documented here are practiced in a lab environment using the following toolset:
* [PowerView / SharpView](https://github.com/PowerShellMafia/PowerSploit)
* [Mimikatz](https://github.com/gentilkiwi/mimikatz)
* [BloodHound](https://github.com/BloodHoundAD/BloodHound)
* [Rubeus](https://github.com/GhostPack/Rubeus)

---

## 📂 Repository Navigation
Quickly jump into specific attack modules:

* **[🔍 AD Enumeration](./AD%20-%20Enumeration/)** — Mapping the domain.
* **[🛡️ AMSI Evasion](./AMSI/)** — Bypassing security controls.
* **[⚡ Privilege Escalation](./Privilege%20Escalation/)** — Escalating to Domain Admin.
* **[🏃 Lateral Movement](./Lateral%20Movement/)** — Moving through the network.
* **[⚓ Persistence](./Persistence/)** — Maintaining domain access.
* **[🛠️ Other Useful Stuffs](./Other%20Usefull%20Stuffs/)** — Cheatsheets and scripts.

---

## ⚖️ Disclaimer
This repository is for **educational and ethical security testing purposes only**. Unauthorized access to computer systems is illegal. Always ensure you have explicit, written permission before performing any security assessments.

---
*Created and maintained by [n0rmh3ll](https://n0rmh3ll.github.io)*
