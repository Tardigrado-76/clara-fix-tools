# MANUAL DE ACCIONES MANUALES DE HARDENING: CLARAfix_ALTO_MANUAL

Este documento consolida y detalla aquellas directivas analizadas en la auditoria de **CLARA** sobre el host de BunkerOS que no pueden ser automatizadas completamente mediante el script `CLARAfix_ALTO.ps1` debido a requerimientos de evaluacion manual, politicas organizacionales externas o dependencias de infraestructura (Directiva NIS2 Art. 21 / CRA).

---

## 1. Directivas de Revision y Verificacion Manual (No Automatizables)

### 1.1 MP.EXP.5 - Otro Firewall (Estado: Inconclusivo en CLARA)
*   **Descripcion de CLARA:** CLARA realiza un escaneo buscando suites comerciales de seguridad de terceros de marcas como McAfee, Norton, Trend Micro, F-Secure y Microsoft.
*   **Problema:** Al no detectar un software antivirus o cortafuegos corporativo de terceros instalado en el host, la herramienta reporta el cumplimiento como **Inconcluso**.
*   **Acciones Manuales:**
    1.  Verifique si existe un agente **EDR / XDR** corporativo gestionado centralizadamente en la red.
    2.  Si la maquina opera como una VM aislada (Air-Gap), mantenga activo el **Windows Defender Firewall** nativo.
    3.  Asegurese de documentar en el Plan de Seguridad del Sistema que el host esta securizado localmente sin firewalls de terceros redundantes para mitigar la superficie de ataque y el consumo de recursos en entornos virtualizados.

### 1.2 Restriccion de NTLM en el Dominio (OP.EXP.2)
*   **Directivas Afectadas:**
    *   *Seguridad de red: restringir NTLM: auditar la autenticación NTLM en este dominio* (Esperado: Habilitar todo / `7`).
    *   *Seguridad de red: restringir NTLM: autenticación NTLM en este dominio* (Esperado: Denegar para cuentas de dominio en servidores de dominio / `7`).
*   **Problema:** El script automatizado `CLARAfix_ALTO.ps1` inyecta las claves de registro locales en el host. Sin embargo, estas directivas **solo entran en vigor si el servidor esta unido a un dominio de Active Directory (AD)**.
*   **Acciones Manuales (Administrador de AD):**
    1.  Si el host es miembro de un dominio, estas politicas deben ser empujadas a nivel corporativo desde el controlador de dominio (DC) mediante una **GPO de Dominio**.
    2.  Verifique en el Visor de Eventos del host (fichero de logs de seguridad) que no haya trafico de autenticacion legacy NTLMv1 o NTLM sin encapsular.

---

## 2. Acciones que Requieren Privilegios Elevados (Administrador Local)

Todas las demas directivas aplicadas por `CLARAfix_ALTO.ps1` (Registro, DCOM SDDL, protector de pantalla de Default User, permisos del filesystem y politicas de secedit) se realizan automaticamente **pero requieren ejecutar PowerShell como Administrador**.

### Tabla de Auditoria de Privilegios Locales Forzados

| Componente Hardening | Nombre de la Politica / Registro | Metodo de Aplicacion Automatizada | Requisito de Elevacion |
| :--- | :--- | :--- | :--- |
| **DCOM SDDL Limits** | `MachineAccessRestrictionLimits` y `MachineLaunchRestrictionLimits` | Conversion de SDDL string a bytes binarios y escritura en `HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DCOM`. | **Administrador del Host** (Acceso de escritura en HKLM). |
| **Derechos de Usuario** | Asignaciones de inicio (Interactive Logon, Network Logon, Deny lists) | Escritura de plantilla de politicas e importacion mediante el comando `secedit /configure`. | **Administrador del Host** (Derecho `SeSecurityPrivilege`). |
| **Bloqueo de Puesto (MP.EQ.2)** | Activacion y contraseña de protector de pantalla para nuevos perfiles | Carga temporal de la colmena del registro (`reg load HKU\DefaultUser C:\Users\Default\NTUSER.DAT`) y modificacion de politicas de usuario. | **Administrador del Host** (Derecho de montaje de archivos del sistema). |
| **Bastionamiento de Servicios** | Configuracion de arranque del servicio de mantenimiento `WaaSMedicSvc` | Escritura directa en registro para bypass de restriccion de TrustedInstaller (`HKLM:\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc`). | **Administrador del Host** (Escritura en base de datos del Administrador de Control de Servicios). |
