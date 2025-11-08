# 💻 BadCalcReactHidden - Proyecto de Calidad de Código y Seguridad

Este proyecto fue proporcionado como un ejercicio centrado en la **detección y corrección de antipatrones** de código, vulnerabilidades de seguridad (Inyección de Prompt en LLM), y el uso de herramientas de análisis estático como SonarQube.

Se realizaron correcciones para elevar el estándar de calidad del código y, más importante aún, para **eliminar el riesgo de seguridad funcional** introducido intencionalmente.

---

## 🚨 Reporte de SonarQube y Correcciones de Calidad (App.jsx & hidden.js)

Se abordaron todas las advertencias de calidad y "código muerto" reportadas por SonarQube:

### 1. Robustez y Manejo de Errores

| Advertencia | Problema Corregido | Archivo(s) |
| :--- | :--- | :--- |
| **S2486** | Manejo de excepciones silencioso (`try...catch` que traga errores). | `App.jsx`, `hidden.js` |
| **S2681** | Ambigüedad en la lógica de control debido a múltiples `if`s consecutivos sin llaves. | `App.jsx` |
| **Redundancia** | Inicialización redundante de la variable `r` antes de ser asignada en todas las rutas de ejecución. | `App.jsx` |

### 2. Calidad de Código y Estructura

| Advertencia | Problema Corregido | Archivo(s) |
| :--- | :--- | :--- |
| **S6774** | Falta de validación de `props` en el componente LLM. | `App.jsx` |
| **S6651** | Uso de stringificación de objetos por defecto (`[object Object]`) en el registro del historial. | `App.jsx` |
| **S4030** | Detección de código muerto (`GLOBAL_HISTORY`), una colección que se modifica pero no se lee. | `App.jsx` |
| **S4654** | Uso de sintaxis incorrecta (`camelCase`) para la propiedad CSS `minHeight`. | `styles.css` |

---

## 🔒 Corrección de la Vulnerabilidad de Seguridad (Inyección de Prompt)

El proyecto contenía una vulnerabilidad crítica donde el usuario o código oculto podían manipular las instrucciones de la IA (LLM).

### 1. El Riesgo: Inyección de Prompt

* **Piezas Inseguras**: Las funciones **`insecureBuildPrompt`** y el componente **`DangerousLLM`** permitían la concatenación directa de una plantilla de texto (`userTpl`), que podía ser suministrada por un atacante o provenir del secreto oculto (`hidden`).
* **Vulnerabilidad**: Al unir la plantilla con las instrucciones del sistema, un texto malicioso (ej., `IGNORA LAS INSTRUCCIONES ANTERIORES...`) podía anular las reglas de la IA, lo cual constituye una **Inyección de Prompt**.

### 2. Solución Aplicada

Se modificó la arquitectura para eliminar por completo la posibilidad de inyección:

* **Eliminación**: Se eliminaron la función **`insecureBuildPrompt`** y la lógica de estado asociada a la plantilla (`userTpl`). También se eliminó la dependencia de la plantilla oculta (`hidden`).
* **Implementación Segura**: Se creó una nueva función **`secureBuildPrompt`** que define las instrucciones del sistema de manera *fija y segura*.
    * El **input del usuario** es tratado estrictamente como **dato** y se coloca al final del prompt (`User data: ${userInput}`), sin capacidad de modificar la instrucción inicial del sistema.
* **Componente Seguro**: Se renombró y refactorizó **`DangerousLLM`** a **`SecureLLM`** para reflejar que ahora utiliza el método de construcción de prompt seguro.