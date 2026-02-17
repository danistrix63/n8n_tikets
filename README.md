# Analizador de Tickets con n8n

Este proyecto consiste en un flujo de trabajo automatizado desarrollado en n8n para la gestión de tickets y facturas. El sistema procesa imágenes de forma automática, extrae la información relevante mediante inteligencia artificial y organiza tanto los datos como los archivos en diferentes plataformas.

Este repositorio sirve como plantilla y demostración de arquitectura. El archivo JSON incluido ha sido editado para eliminar datos sensibles, por lo que deberás configurar tus propias credenciales y IDs de carpetas para que sea funcional.

## Funcionamiento y Características

### Extracción de datos
El sistema utiliza modelos de visión (como GPT-4o o Gemini) para leer las imágenes y extraer los datos clave sin necesidad de plantillas fijas.
- Precio total: Limpieza automática de símbolos de moneda.
- Desglose de IVA: Soporte para múltiples tipos impositivos.
- Datos del emisor: Normalización del nombre de la empresa.
- Fecha: Conversión automática al formato estándar.

### Gestión de la información
Los datos extraídos se envían a varios destinos de forma simultánea:
- MySQL: Registro histórico para consultas y auditoría.
- Google Sheets: Reporte actualizado para contabilidad y gestión visual.
- Google Drive: Almacenamiento organizado de los archivos originales.

### Lógica interna
- Detección de duplicados: El sistema genera un identificador único por ticket para evitar registros repetidos en la base de datos.
- Clasificación dinámica: Los archivos se mueven automáticamente a carpetas organizadas por año y mes basándose en la fecha del ticket.
- Gestión de errores: Si un archivo no se puede procesar, se mueve a una carpeta específica y se envía una notificación por correo con los detalles técnicos.

## Infraestructura y Despliegue

El sistema está preparado para un entorno de producción real con las siguientes características:

- Entorno: VPS (Servidor Privado Virtual).
- Despliegue: Configurado con Docker y Docker Compose para asegurar la consistencia del entorno.
- Seguridad: Uso de proxy inverso con certificados SSL de Let's Encrypt para habilitar webhooks seguros por HTTPS.
- Copias de seguridad: El diseño contempla backups automáticos de la base de datos y la configuración de n8n.

### Stack Tecnológico de Producción
- Servidor: Linux en la nube (VPS).
- Docker: n8nio/n8n:latest.
- Dominio: Dominio propio con HTTPS activo.
- SSL: Gestión automática de certificados con Let's Encrypt.
- Zona Horaria: Europe/Madrid.

### Configuración de Docker (Ejemplo)

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
      - N8N_HOST=tu-dominio.com
      - N8N_PROTOCOL=https
      - WEBHOOK_URL=https://tu-dominio.com/
      - GENERIC_TIMEZONE=Europe/Madrid
    volumes:
      - ./n8n_data_local:/home/node/.n8n
      - ./local-files:/files
```

## Herramientas Utilizadas
- n8n: Orquestación del flujo de trabajo.
- MySQL: Base de datos relacional.
- Google Drive API: Gestión y almacenamiento de archivos.
- Google Sheets API: Generación de reportes.
- OpenAI API / Gemini: Procesamiento de imágenes con visión artificial.
- Gmail API: Notificaciones de sistema.

## Configuración del Entorno

### 1. Importación
Carga el archivo Tikets (1).json en tu instancia de n8n. Asegúrate de revisar los nodos que contienen placeholders y actualizar las credenciales de Google, MySQL, OpenAI y Gmail.

### 2. Base de Datos
Es necesario crear la tabla processed_files en MySQL con la estructura que se detalla a continuación:

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

### 3. Variables de entorno
Utiliza el archivo .env.example como referencia para configurar los accesos a la base de datos y las claves necesarias para las APIs.

## Contenido del Repositorio
- Tikets (1).json: Flujo de n8n (plantilla).
- DEMO.md: Explicación visual del proceso.
- .env.example: Guía para las variables de configuración.
- README.md: Documentación principal.

## Notas sobre privacidad
El flujo ha sido anonimizado para su publicación. Se han eliminado IDs de carpetas, webhooks, correos electrónicos y credenciales reales. Los archivos de backup con datos de producción no están incluidos en el repositorio para proteger la seguridad del sistema original.

Daniel Dans | [LinkedIn](https://www.linkedin.com/in/danieldans/) | [GitHub](https://github.com/danistrix63)
