# GUÍA DE DIRECTIVAS LOCALES Y GPOs: CCN-CERT ENS ALTO

Esta guía contiene la parametrización técnica y la sintaxis exacta para aplicar directivas mediante la consola de Directiva de Seguridad Local (`secpol.msc`) o la Consola de Administración de Directivas de Grupo (`gpmc.msc`) en Active Directory.

---

## 1. Directivas de Opciones de Seguridad

### 1.1 Deshabilitar Tareas Programadas para Operadores de Servidor
* **Ruta GPO:** `Configuración del equipo > Configuración de Windows > Configuración de seguridad > Directivas locales > Opciones de seguridad`
* **Directiva:** *Controlador de dominio: permitir a los operadores de servidor programar tareas*
* **Valor Requerido:** `Deshabilitado`

### 1.2 Desacoplamiento sin Iniciar Sesión
* **Ruta GPO:** `Configuración del equipo > Configuración de Windows > Configuración de seguridad > Directivas locales > Opciones de seguridad`
* **Directiva:** *Dispositivos: permitir desacoplamiento sin tener que iniciar sesión*
* **Valor Requerido:** `Deshabilitado`

---

## 2. Restricciones DCOM SDDL (DACL y SACL)

### 2.1 Restricciones de Acceso DCOM al Equipo
* **Directiva:** *DCOM: restricciones de acceso al equipo en sintaxis de Lenguaje de definición de descriptores de seguridad (SDDL)*
* **Descriptor de Seguridad SDDL Base:**
  ```text
  O:BAG:BAD:(
    A;CCDC;;;S-1-5-32-562
    A;CCDC;;;S-1-15-2-1
    A;CCDC;LC;;;S-1-5-11
    A;CCDC;LC;;;S-1-5-32-545
  )
  ```
* **Permisos Asignados:**
  - `BUILTIN\Administrators`: Propietario y Grupo.
  - `ALL APPLICATION PACKAGES`: Acceso permitido.
  - `Authenticated Users`: Acceso local y remoto regulado.
  - `BUILTIN\Distributed COM Users`: Acceso específico.

### 2.2 Restricciones de Inicio DCOM
* **Directiva:** *DCOM: restricciones de inicio de equipo en sintaxis de Lenguaje de definición de descriptores de seguridad (SDDL)*
* **Permisos Requeridos:**
  - Acceso de inicio y activación para `BUILTIN\Administrators` y `Authenticated Users`.

---

## 3. Directivas de Bloqueo de Cuenta

* **Ruta GPO:** `Configuración del equipo > Configuración de Windows > Configuración de seguridad > Directivas de cuenta > Directiva de bloqueo de cuenta`
* **Directiva:** *Duración del bloqueo de cuenta*
  - **Valor ENS Alto:** `Reinicio por administrador / -1` o `Al menos 15 minutos`.
* **Directiva:** *Umbral de bloqueo de cuenta*
  - **Valor ENS Alto:** `5 intentos incorrectos`.
* **Directiva:** *Restablecer el bloqueo de cuenta después de*
  - **Valor ENS Alto:** `15 minutos`.
