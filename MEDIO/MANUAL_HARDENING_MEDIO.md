# GUÍA DE VERIFICACIÓN MANUAL: ENS NIVEL MEDIO (CCN-STIC 811)

Este documento detalla los controles y directivas para el nivel **ENS MEDIO** que requieren validación operativa o supervisión manual por parte del equipo de ciberseguridad o administración de sistemas.

---

## 1. Directivas de Control de Acceso (OP.ACC)

### 1.1 Doble Factor de Autenticación (MFA)
* **Requisito ENS:** Exigido para acceso a recursos de administración y conexiones remotas (RDP / VPN).
* **Verificación:** Asegurar que todo acceso administrativo al host requiera llave de seguridad FIDO2, smartcard corporativa o autenticador TOTP integrado en el IdP.

### 1.2 Gestión de Cuentas Privilegiadas (OP.ACC.1)
* **Requisito ENS:** Ningún usuario debe operar en tareas diarias con privilegios de Administrador local o de dominio.
* **Verificación:** Ejecutar `net localgroup Administrators` y verificar que solo figuren cuentas nominativas de administración técnica.

---

## 2. Gestión de Incidentes y Copias de Seguridad (OP.EXP / MP.SI)

### 2.1 Política de Respaldos Periódicos
* **Verificación:** Validar que las copias de seguridad de la base de datos y configuraciones se realicen diariamente y se conserven en una ubicación segregada de red.

### 2.2 Registro de Eventos en SIEM
* **Verificación:** Confirmar que el agente de telemetría o Sysmon reenvía los eventos de seguridad (`Security Event Log`) hacia el colector central.
