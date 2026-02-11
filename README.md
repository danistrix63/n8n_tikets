# 🧾 Analizador de Tickets Inteligente con n8n (Template)

Este repositorio contiene un flujo de trabajo de automatización avanzado construido con **n8n**. El sistema está diseñado para procesar imágenes de tickets y facturas de manera profesional, integrando Inteligencia Artificial para la extracción de datos y una infraestructura robusta para el almacenamiento y organización.

> [!IMPORTANT]
> **Nota sobre el estado del proyecto:** 
> Este repositorio sirve como una **plantilla profesional** y una demostración de arquitectura. El archivo JSON incluido ha sido **sanitizado para proteger la privacidad**. Los IDs de carpetas de Google Drive, webhooks y credenciales reales han sido reemplazados por placeholders genéricos (ej: `YOUR_FOLDER_ID`, `YOUR_WEBHOOK_ID`). 
>
> **Para que el flujo sea funcional** en tu propia instancia de n8n, deberás:
> - Re-configurar los conectores con tus propios IDs de Google Drive
> - Actualizar las credenciales de MySQL, OpenAI y Gmail
> - Reemplazar los placeholders con tus valores reales

## 🚀 Características Principales

*   **Extracción de Datos con IA (Visión)**: Utiliza modelos como GPT-4o o Gemini 1.5 Pro para "leer" la imagen y extraer:
    *   Precio Total (limpiando símbolos de moneda)
    *   Desglose de IVA (múltiples tipos soportados)
    *   Nombre de la empresa (normalizado a mayúsculas)
    *   Fecha de emisión (conversión automática de formatos)
*   **Gestión de Datos Multicapa**:
    1.  **MySQL**: Registro histórico para auditoría y búsqueda rápida.
    2.  **Google Sheets**: Reporte dinámico para contabilidad y gestión visual.
    3.  **Google Drive**: Almacenamiento organizado jerárquicamente.
*   **Lógica de Negocio Avanzada**:
    *   **Detección de Duplicados**: Antes de guardar, el sistema genera un hash único y consulta la base de datos para evitar registros repetidos.
    *   **Clasificación Dinámica**: Crea automáticamente carpetas por `Año-Mes` (ej: `2025-02`) basándose en la fecha del ticket, no en la fecha de subida.
    *   **Control de Errores**: Sistema de captura de errores que mueve archivos problemáticos a una carpeta de "Error" y notifica vía Gmail con el stack trace técnico.

## 🏗️ Infraestructura y Despliegue

Este sistema está diseñado para ejecutarse en un entorno de producción real:

*   **Entorno**: VPS (Servidor Privado Virtual).
*   **Containerización**: Desplegado mediante **Docker** y Docker Compose para asegurar que el entorno sea idéntico en desarrollo y producción.
*   **Seguridad**: Configurado detrás de un proxy inverso con certificados **Let's Encrypt (SSL)**, permitiendo Webhooks seguros (HTTPS).
*   **Backups**: El diseño original incluye copias de seguridad automáticas del volumen de datos de n8n y la base de datos MySQL.

### Stack Tecnológico de Producción
*   **VPS**: Servidor Linux alojado en la nube
*   **Docker**: Contenedor n8n (`n8nio/n8n:latest`)
*   **Dominio**: Dominio personalizado con HTTPS
*   **SSL/TLS**: Certificados Let's Encrypt gestionados automáticamente
*   **Protocolo**: HTTPS con webhooks seguros
*   **Zona Horaria**: Europe/Madrid
*   **Volúmenes Persistentes**:
    *   `./n8n_data_local` → Datos de n8n (workflows, credenciales)
    *   `./local-files` → Archivos procesados

### Configuración Docker (Ejemplo)

```yaml
version: '3'

services:
  n8n:
    container_name: n8n
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=your-domain.com
      - N8N_PROTOCOL=https
      - WEBHOOK_URL=https://your-domain.com/
      - GENERIC_TIMEZONE=Europe/Madrid
    volumes:
      - ./n8n_data_local:/home/node/.n8n
      - ./local-files:/files
```

> [!NOTE]
> Por razones de seguridad y privacidad, los archivos de backup reales (`.tar.gz`) que contienen datos de producción, certificados SSL y configuraciones privadas **no se incluyen en este repositorio público**. El `.gitignore` está configurado para prevenir la subida accidental de estos archivos sensibles.

## 🛠️ Tecnologías del Flujo

*   [n8n](https://n8n.io/) - Plataforma de automatización de flujos de trabajo
*   **MySQL** - Base de datos relacional
*   **Google Drive API** - Gestión de archivos
*   **Google Sheets API** - Reportes dinámicos
*   **OpenAI API / Gemini** - Procesamiento de imágenes con visión artificial
*   **Gmail API** - Notificaciones de errores

## ⚙️ Configuración y Uso

### 1. Importación del Workflow
1.  Importa el archivo `Tikets (1).json` en tu instancia de n8n.
2.  Verifica los nodos marcados con placeholders como `YOUR_FOLDER_ID` o `YOUR_WEBHOOK_ID`.
3.  Actualiza todas las credenciales (Google Drive, MySQL, OpenAI, Gmail).

### 2. Base de Datos MySQL
Crea la tabla necesaria en tu base de datos:

```sql
CREATE TABLE processed_files (
    id INT AUTO_INCREMENT PRIMARY KEY,
    file_id VARCHAR(255),
    empresa_emisora VARCHAR(255),
    fecha DATE,
    precio_total DECIMAL(10,2),
    tipo_documento VARCHAR(50),
    iva_porcentaje VARCHAR(50),
    iva_importe VARCHAR(50),
    identificador_unico VARCHAR(255) UNIQUE,
    webContentLink TEXT,
    thumbnailLink TEXT,
    processed_at DATETIME
);
```

### 3. Variables de Entorno

Consulta el archivo `.env.example` para ver todas las variables necesarias. Las principales son:

*   `DB_HOST`, `DB_USER`, `DB_PASS`, `DB_NAME` - Conexión MySQL
*   `OPENAI_API_KEY` - Clave de API para procesamiento IA
*   IDs de carpetas de Google Drive (configurados directamente en los nodos)

## 📦 Contenido del Repositorio

*   `Tikets (1).json`: Workflow de n8n (Template sanitizado)
*   `DEMO.md`: Guía visual y diagrama de flujo con Mermaid
*   `.env.example`: Guía de variables de entorno necesarias
*   `.gitignore`: Configuración para evitar la subida accidental de datos sensibles
*   `README.md`: Este archivo

## 🔒 Privacidad y Seguridad

Este proyecto ha sido cuidadosamente sanitizado para su publicación:
- ✅ Todos los IDs de carpetas de Google Drive reemplazados por placeholders
- ✅ Webhooks IDs anonimizados
- ✅ Emails corporativos eliminados
- ✅ Credenciales y tokens excluidos mediante `.gitignore`
- ✅ Backups de producción excluidos del repositorio

El objetivo es demostrar la arquitectura y lógica del sistema sin comprometer ninguna información sensible.

---
**Daniel Dans** | [LinkedIn](https://www.linkedin.com/in/danieldans/) | [GitHub](https://github.com/danistrix63)