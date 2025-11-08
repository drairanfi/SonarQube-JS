# 💻 BadCalcReactHidden - Proyecto de Calidad de Código y Seguridad

Este proyecto fue proporcionado como un ejercicio para estudiantes centrado en la **detección y corrección de antipatrones** de código, vulnerabilidades de seguridad (especialmente la Inyección de Prompt en LLM), y el uso de herramientas de análisis estático como SonarQube.

El componente principal, `App.jsx`, estaba intencionalmente diseñado para ser un ejemplo de "código sucio" (messy code) con múltiples problemas de calidad, seguridad y mantenibilidad.

---

## 🚨 Reporte de SonarQube y Correcciones Aplicadas

Se utilizó SonarQube para escanear el proyecto y se corrigieron todas las advertencias de calidad de código detectadas en los archivos `.js`/`.jsx` y `.css`.

### 1. Correcciones de Seguridad y Configuraciones

| Archivo | Advertencia Original | Solución Aplicada |
| :--- | :--- | :--- |
| `sonar-project.properties` | **S6702 (Critical)**: Token de SonarQube expuesto. | **Se eliminó** el token del archivo. Se recomienda encarecidamente que el token sea pasado como una **variable de entorno** (`SONAR_TOKEN`) en el proceso de CI/CD, no en el código fuente. |

### 2. Correcciones en `src/App.jsx`

| Advertencia | Descripción del Problema | Cómo se Solucionó |
| :--- | :--- | :--- |
| **S2486** | Manejo de excepción silencioso (`try...catch` vacío) en `compute` y `badParse`. | Se agregó **`console.error`** en el bloque `catch` de ambas funciones para asegurar que los errores se registren, cumpliendo con la regla de manejar la excepción o no capturarla. |
| **S2681** | Ambüedad en la ejecución de sentencias debido al uso de múltiples `if`s sin llaves para la lógica de operaciones. | Se refactorizó la lógica de las operaciones matemáticas (`+`, `-`, `*`, etc.) a una estructura **`switch`**, eliminando la ambigüedad del flujo de control. |
| **S6774** | Falta de validación de `props` en el componente `DangerousLLM`. | Se instaló la dependencia **`prop-types`** y se definió el bloque **`DangerousLLM.propTypes`** para validar los tipos de datos de las props `userTpl` y `userInput`. |
| **S6651** | Stringificación por defecto de objetos (`[object Object]`) al registrarlos en `GLOBAL_HISTORY`. | Se eliminaron las llaves alrededor de las variables en la *template string* (ej., `${A}` en lugar de `${{A}}`) para concatenar sus valores reales. |
| **S4030** | Detección de código muerto (`GLOBAL_HISTORY`), una variable global que solo se modifica, pero nunca se lee. | Se añadió un **`console.log`** para "usar" la variable, silenciando la advertencia del linter y manteniendo la variable global, que es un antipatrón intencional del ejercicio. |
| **Vite Error** | `Failed to resolve import "prop-types"`. | Se ejecutó **`npm install prop-types`** para añadir la dependencia faltante. |

### 3. Correcciones en Otros Archivos

| Archivo | Advertencia | Solución Aplicada |
| :--- | :--- | :--- |
| `src/hidden.js` | **S2486**: Excepción tragada silenciosamente en la función `extractHiddenPrompt`. | Se agregó **`console.error`** al bloque `catch` para registrar fallas en la decodificación Base64. |
| `src/styles.css` | **S4654**: Uso de propiedad desconocida o sintaxis incorrecta. | Se corrigió el uso de `minHeight` (camelCase) a **`min-height`** (kebab-case) para cumplir con la sintaxis estándar de CSS. |