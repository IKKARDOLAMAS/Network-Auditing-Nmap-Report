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
