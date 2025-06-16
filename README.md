# Sistema de Gestión de Archivos Seguros

Este proyecto implementa un sistema de gestión de archivos que se comunica mediante conexiones seguras utilizando TLS. Está compuesto por tres módulos principales: `tls-client.js`, `web-server.js` y `main.js`.

---

## 📦 Componentes del Proyecto

### `tls-client.js`

Cliente especializado que se comunica de forma segura con un servidor de archivos. Pensado para ser usado por un backend web, no directamente por usuarios finales.

**Características:**

1. **Seguridad:**  
   Usa TLS y un certificado (`CA_PATH`) para asegurar comunicación cifrada y autenticada.

2. **Asincronía:**  
   Todas las funciones devuelven *Promises* para manejar operaciones de red sin bloquear.

3. **Manejo de Datos:**
   - Comandos simples como texto: `sendCommand`
   - Descarga de archivos: buffers unidos en `getFile`
   - Subida de archivos: `stream` con protocolo de *handshake* (READY / ACK) en `putFile`

4. **Robustez en `putFile`:**
   - Espera `READY` del servidor antes de enviar
   - Espera `ACK` para confirmar éxito
   - Solo elimina archivo local si la subida fue confirmada

5. **Modularidad:**  
   Todas las funciones están exportadas para ser reutilizadas por otros módulos.

---

### `web-server.js`

Servidor web que actúa como intermediario entre el navegador y el `tls-client`. Expone una API RESTful y sirve la UI.

**Características:**

1. **HTTPS:**  
   Sirve UI y API de forma segura.

2. **Uso de Express.js:**  
   Facilita la creación de rutas, manejo de peticiones HTTP y middlewares.

3. **Middlewares Importantes:**
   - `express.static`: sirve archivos HTML/CSS/JS
   - `cors`: permite peticiones cross-origin
   - `express.json`: parsea cuerpo JSON
   - `multer`: gestiona subida de archivos a carpeta temporal (`uploads/`)

4. **Delegación de funciones:**  
   Todas las operaciones se delegan a funciones del módulo `tls-client`.

5. **Manejo de Errores:**  
   Cada endpoint usa `try...catch` y responde con `HTTP 500` ante fallos.

6. **Seguridad en capas:**

   ```
   Navegador <--HTTPS--> web-server.js <--TLS--> tls-client.js <--TLS--> secure-server.js
   ```

---

### `main.js`

Script de frontend que interactúa con el DOM y con el backend (`web-server.js`).

**Características:**

1. **Manipulación del DOM:**  
   Muestra datos y captura inputs del usuario.

2. **API Workspace:**  
   Utiliza `fetch` moderno para todas las comunicaciones con el backend.

3. **Programación asíncrona (`async/await`):**  
   Facilita la lectura y mantenimiento del código.

4. **Eventos del Usuario:**  
   Usa `addEventListener` para responder a eventos como `submit`.

5. **Interacción del Usuario:**  
   Emplea `alert()`, `confirm()` y `prompt()` para mensajes y entradas.

6. **Subida de Archivos:**  
   Utiliza `FormData` para construir solicitudes `multipart/form-data`.

7. **UI Reactiva:**  
   Llama a `loadFiles()` tras acciones importantes para actualizar la tabla de archivos.

8. **Manejo de Errores (básico):**  
   Uso de `try...catch` y `alert()` para mostrar errores del servidor al usuario.

---

## 🚀 Cómo Ejecutar el Proyecto

### 1. Iniciar el servidor TLS (`secure-server.js`)

Este servidor maneja directamente los archivos.

```bash
node secure-server.js
```

**Salida esperada:**

```
[SERVER] Servidor TLS escuchando en puerto 6000
```

---

### 2. Iniciar el servidor web (`web-server.js`)

Este servidor sirve la UI y expone la API RESTful.

```bash
node web-server.js
```

**Salida esperada:**

```
🔐 UI web segura disponible en https://localhost:3000
```

---

### 3. Acceder a la interfaz web

1. Abrir el navegador.
2. Ir a [https://localhost:3000](https://localhost:3000)
3. Aceptar el riesgo del certificado autofirmado.
4. Ver la tabla de archivos y el formulario de subida.

---

## 🧪 Ejemplos de Uso y Pruebas

1. **Listar archivos (LIST):**  
   Archivos en `src/files/` se muestran. Si no hay, aparece "No hay archivos en el servidor".

2. **Subir archivo (PUT):**
   - Seleccionar archivo
   - Clic en “Subir”
   - Aparece en la tabla
   - Verificar que no quede archivo temporal en `src/uploads/`

3. **Descargar archivo (GET):**
   - Clic en “Descargar”
   - El navegador descarga el archivo

4. **Eliminar archivo (DELETE):**
   - Clic en “Eliminar”
   - Confirmar
   - El archivo desaparece y se elimina de `src/files/`

5. **Renombrar archivo (RENAME):**
   - Clic en “Renombrar”
   - Ingresar nuevo nombre (ej. `nuevo.txt`)
   - El nombre cambia en la tabla

---

## ✅ Requisitos

- Node.js
- Certificados TLS válidos o autofirmados
- Navegador moderno

---

## 🛡️ Seguridad

Este sistema aplica seguridad en cada punto de comunicación, desde el navegador hasta el servidor final, usando HTTPS y TLS para garantizar confidencialidad e integridad.

---
