# 📚 RAG Chatbot: PDF Intelligence System

Este proyecto es una aplicación de **Generación Aumentada por Recuperación (RAG)** que permite a los usuarios cargar archivos PDF, almacenarlos en la nube y realizar consultas inteligentes sobre su contenido. Combina potentes herramientas de IA, bases de datos vectoriales y almacenamiento escalable.



## 🛠️ Stack Tecnológico

* **Frontend:** [Streamlit](https://streamlit.io/)
* **LLM (Generación de texto):** [Google Gemini 1.5 Flash](https://ai.google.dev/)
* **Embeddings (Representación vectorial):** [Cohere Multilingual](https://cohere.com/)
* **Base de Datos Vectorial:** [MongoDB Atlas Vector Search](https://www.mongodb.com/products/platform/atlas-vector-search)
* **Almacenamiento de Archivos:** [Backblaze B2](https://www.backblaze.com/)
* **Procesamiento de PDF:** PyPDF2

---

## 🚀 Características Principales

1.  **Ingesta de Documentos:** Carga de archivos PDF con extracción automática de texto.
2.  **Fragmentación Inteligente:** División del texto en fragmentos (chunks) para una búsqueda precisa.
3.  **Almacenamiento Híbrido:** * Vectores y metadatos guardados en **MongoDB**.
    * Archivos físicos respaldados en **Backblaze B2**.
4.  **Búsqueda Semántica:** Localiza la información relevante mediante similitud de coseno, no solo palabras clave.
5.  **Contexto en Tiempo Real:** Las respuestas de la IA se basan exclusivamente en el contenido de tus documentos.
6.  **Visor Integrado:** Previsualización del PDF almacenado directamente en la interfaz.

---

## ⚙️ Configuración del Entorno

Para ejecutar este proyecto, necesitas crear un archivo `.streamlit/secrets.toml` con las siguientes credenciales:

```toml
[app]
GOOGLE_API_KEY = "tu_google_api_key"
MONGODB_URI = "tu_mongodb_atlas_uri"
COHERE_API_KEY = "tu_cohere_api_key"
USER = "TuNombre"

[b2]
B2_READ_KEY_ID = "tu_key_id_lectura"
B2_READ_APPLICATION_KEY = "tu_application_key_lectura"
B2_WRITE_KEY_ID = "tu_key_id_escritura"
B2_WRITE_APPLICATION_KEY = "tu_application_key_escritura"
B2_BUCKET_NAME = "nombre_de_tu_bucket"
```

### ⚙️ Configuración de MongoDB Atlas

Para que la búsqueda vectorial funcione correctamente, es necesario configurar un índice específico en tu clúster de MongoDB Atlas:

1.  Crea un clúster en [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) (Tier M0 o superior).
2.  En la sección de **Atlas Search**, crea un nuevo **Search Index**.
3.  Selecciona el editor de JSON y utiliza la siguiente configuración para el índice llamado `vector_index`:

```json
{
  "fields": [
    {
      "numDimensions": 768,
      "path": "embedding",
      "similarity": "cosine",
      "type": "vector"
    }
  ]
}
```

> Nota: El nombre del índice debe coincidir exactamente con vector_index tal como está definido en el código.

## 🛠️ Instalación y Preparación

Sigue estos pasos para configurar el entorno de desarrollo y ejecutar la aplicación localmente:

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/eviediaz/cc-pc2
   cd cc-pc2
   ```

2. **Instalar dependencias:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Configurar secretos de Streamlit:**
   Crea la carpeta .streamlit y dentro el archivo secrets.toml con la configuración mencionada previamente.

4. **Ejecutar la aplicación:**
   Una vez configuradas las credenciales, se debe iniciar el servidor de Streamlit:
   ```bash
   streamlit run app.py
   ```

## 🔄 Flujo de Datos del Proyecto

La aplicación tiene el siguiente proceso para garantizar que la información esté disponible y recuperable:

1. **Carga y Backup**  
   Al subir un PDF, el archivo original se almacena de forma persistente en un bucket de **Backblaze B2**.

2. **Extracción y Fragmentación**  
   El texto se extrae del PDF y se divide en fragmentos (*chunks*) de **1000 caracteres** para no exceder los límites de los modelos de lenguaje.

3. **Generación de Embeddings**  
   Cada fragmento se envía a la API de **Cohere**, que devuelve un vector numérico de **768 dimensiones** que representa el significado semántico del texto.

4. **Indexación**  
   El texto original junto con su vector se guarda en **MongoDB Atlas**.

5. **Recuperación (Retrieval)**  
   Cuando el usuario hace una pregunta, esta se convierte en vector y se realiza una búsqueda de **vecinos más cercanos** en MongoDB.

6. **Generación (Augmentation)**  
   Los fragmentos más relevantes se envían a **Google Gemini Flash** como contexto para que genere una respuesta precisa y fundamentada.



