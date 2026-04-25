# Auditoría de Red y Descubrimiento de Activos (Nmap)

En este laboratorio se aplicaron técnicas de reconocimiento activo para identificar la superficie de ataque de una estación de trabajo Windows 10 dentro de una infraestructura híbrida.

## 🎯 Objetivos de la Auditoría
1. Identificar activos vivos en el segmento de red local.
2. Detectar servicios y versiones de software mediante escaneos agresivos (`-A`).
3. Analizar la postura de seguridad frente a protocolos críticos (SMB/MSRPC).

## 📊 Resultados del Escaneo (Evidence)
| Puerto | Estado | Servicio | Versión / Información Adicional |
|--------|--------|----------|---------------------------------|
| 135    | Open   | msrpc    | Microsoft Windows RPC          |
| 139    | Open   | netbios  | NetBIOS-SSN                    |
| 445    | Open   | SMB      | microsoft-ds (Firma de mensajes habilitada) |

## 🔍 Hallazgos Críticos para el Sector Financiero

### 1. Exposición de SMB (Puerto 445)
Se detectó el puerto 445 abierto. En entornos de banca, este protocolo es un vector crítico para ataques de movimiento lateral y propagación de Ransomware. 
* **Observación:** El escaneo indica que la firma de mensajes (Message Signing) está habilitada pero no es obligatoria, lo que permitiría ataques de **SMB Relay**.

### 2. Detección de OS (Fingerprinting)
Mediante el análisis de paquetes, se identificó con precisión que el objetivo corre **Windows 10 (versión 1709 - 21H2)**. Esto permite a un analista identificar vulnerabilidades específicas (CVE) no parcheadas en el sistema.

### 3. Invisibilidad de Servicios No-Estándar
Se intentó auditar el puerto personalizado **2222 (SSH)**. El puerto no fue detectado en el escaneo general, lo que confirma la efectividad de la "Seguridad por Oscuridad" y las reglas de filtrado del Firewall local (estado: `filtered`).

## 💡 Conclusiones Técnicas
La auditoría revela una configuración estándar de Windows. Como recomendación de Hardening, se sugiere restringir el acceso a los servicios de NetBIOS y SMB únicamente a direcciones IP autorizadas o mediante el uso de una VPN, especialmente en aplicaciones de manejo de datos sensibles.

---
*Auditado por íkkii - Especialización en Ciberseguridad 2026*

# 🛡️ Auditoría de Red 02: Enumeración de Endpoint Windows 10

## 📌 Resumen Ejecutivo
En esta fase de auditoría (Día 16), se escaló el análisis desde un entorno controlado hacia un **Host Real (Endpoint)**. El objetivo principal fue identificar la superficie de exposición de una estación de trabajo Windows 10 y analizar la seguridad de sus protocolos de comunicación activos en una red local.

---

## 🛠️ Metodología y Herramientas
* **Herramienta:** Nmap 7.98 (Network Mapper).
* **Técnica:** Enumeración profunda de servicios y detección de versiones (`-sV -sC -O`).
* **Protocolo de Privacidad:** Se aplicó **Data Redaction** (censura de datos) en las evidencias para proteger identificadores físicos (MAC Address) y nombres de host, siguiendo estándares éticos de consultoría y manejo de información sensible.

---

## 📊 Resultados de la Exploración (Evidencia Técnica)

| Puerto | Estado | Servicio | Versión / Información Detectada |
| :--- | :--- | :--- | :--- |
| **135/tcp** | Open | msrpc | Microsoft Windows RPC |
| **139/tcp** | Open | netbios-ssn | Microsoft Windows netbios-ssn |
| **445/tcp** | Open | microsoft-ds | SMB (Server Message Block) |
| **5357/tcp** | Open | http | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) |

> **Nota:** Se identificaron 996 puertos adicionales en estado `closed (reset)`, lo que indica que el sistema rechaza activamente conexiones en puertos no autorizados.

---

## 🔍 Hallazgos Críticos y Análisis de Riesgo

### 1. Exposición de SMB y Riesgo de Movimiento Lateral (Puerto 445)
Se detectó el servicio de intercambio de archivos operativo. El script de enumeración reveló que la **firma de mensajes está habilitada pero no es obligatoria** (`Message signing enabled but not required`).
* **Riesgo:** **ALTO**. Esta configuración es vulnerable a ataques de **SMB Relay**, permitiendo a un atacante interceptar y retransmitir peticiones de autenticación para ganar acceso a otros sistemas de la red.

### 2. Detección de OS Fingerprinting (Reconocimiento de Objetivo)
Mediante el análisis de la pila TCP/IP, Nmap identificó con precisión la arquitectura: **Microsoft Windows 10 (Build 1709 - 21H2)**.
* **Impacto:** El perfilado exacto del Sistema Operativo permite a un atacante filtrar vulnerabilidades específicas (CVEs) para ejecutar exploits dirigidos.

### 3. Fuga de Metadatos vía NetBIOS / SSDP
El sistema expone información sobre el nombre del equipo y servicios de descubrimiento de red (UPnP), facilitando el reconocimiento pasivo dentro del segmento de red.

---

## ✅ Recomendaciones de Hardening (Mitigación)
1.  **Segmentación de SMB:** Restringir el acceso al puerto 445 mediante Firewall, permitiendo conexiones solo desde IPs administrativas.
2.  **Firma SMB Obligatoria:** Configurar mediante GPO (Directiva de Grupo) que la firma de mensajes sea **requerida** para mitigar ataques de retransmisión.
3.  **Higiene de Servicios:** Deshabilitar el servicio `Network Discovery` y el puerto 5357 si la estación de trabajo no requiere interactuar con dispositivos multimedia de red.

---

### 🖼️ Evidencia Fotográfica (Reporte Censurado)
!Auditoría de Red 2

---
*Próximo paso: Análisis de vulnerabilidades y detección de CVEs sobre servicios expuestos.*
