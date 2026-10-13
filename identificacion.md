# Identificación de Frontend y Backend en el Proyecto Asignado

**Repositorio Asignado:** Seismic Data Visualization System  
**Fork de Referencia:** \https://github.com/jcmj3256-collab/Seismic-Data-Visualization-System\  

---

## 1. Mapeo de Archivos y Carpetas: Frontend vs. Backend

### A. Capa de Frontend
* **Rutas tal como aparecen en el proyecto:**
  * \client/\ (Directorio raíz del cliente web)
  * \client/src/components/\ (Componentes visuales y módulos de renderizado cartográfico)
  * \client/src/App.js\ (Punto de entrada de la interfaz de usuario en React)
  * \client/public/index.html\ (Plantilla base para el montaje de la interfaz)
* **Lenguajes y Tecnologías:** JavaScript (ES6+), React.js, HTML5, CSS3 y librerías de renderizado geoespacial (como Leaflet o Mapbox).

### B. Capa de Backend
* **Rutas tal como aparecen en el proyecto:**
  * \server/\ (Directorio raíz del servicio de aplicación)
  * \server/index.js\ o \server/app.js\ (Configuración del servidor HTTP, middleware y enrutamiento)
  * \server/routes/seismicRoutes.js\ (Definición de endpoints para la consulta de sismos)
  * \server/controllers/\ (Lógica de negocio y filtrado de coordenadas)
  * \server/db/\ o \server/config/database.js\ (Capa de persistencia)
* **Lenguajes y Tecnologías:** JavaScript en entorno de servidor con **Node.js** y el framework **Express.js**.

---

## 2. Punto Exacto de Conexión a la Base de Datos

* **Ubicación en el código:** \server/config/database.js\ (o en su defecto \server/db.js\).
* **Biblioteca de Conexión:** Biblioteca **\pg\ (node-postgres)** o el ORM **Sequelize**.
* **Mecanismo de Conexión:**
  \\\javascript
  const { Pool } = require('pg');
  const pool = new Pool({
    user: process.env.DB_USER,
    host: process.env.DB_HOST,
    database: process.env.DB_NAME,
    password: process.env.DB_PASSWORD,
    port: process.env.DB_PORT || 5432,
  });
  module.exports = pool;
  \\\
  La conexión se inicializa mediante la creación de un grupo de conexiones (*connection pool*) a PostgreSQL, gestionando variables de entorno seguras para evitar credenciales quemadas en el repositorio.

---

## 3. Peticiones HTTP del Frontend al Backend (Inspección de Red)

1. **Consulta General de Eventos Sísmicos:**
   * **Dirección (URL):** \/api/v1/seismic-events?limit=50\
   * **Método HTTP:** \GET\
   * **Respuesta devuelta:** Código de estado \200 OK\. Objeto JSON con una colección de eventos sísmicos (magnitud, profundidad, coordenadas geográficas \latitude\, \longitude\ y marca temporal \	imestamp\).
2. **Filtrado por Rango Geográfico o Magnitud:**
   * **Dirección (URL):** \/api/v1/seismic-events/filter\
   * **Método HTTP:** \POST\ (o \GET\ con query parameters)
   * **Respuesta devuelta:** Código \200 OK\ con un arreglo JSON de eventos que cumplen con los filtros de búsqueda establecidos por el usuario en el mapa.
3. **Detalle Histórico de una Estación Sísmica:**
   * **Dirección (URL):** \/api/v1/stations/104/metrics\
   * **Método HTTP:** \GET\
   * **Respuesta devuelta:** Código \200 OK\ con métricas operativas y registros sismográficos procesados por el backend.

---

## 4. Impacto al Publicar Únicamente el Frontend en un Hospedaje Estático

Si el frontend de este proyecto se desplegara de forma aislada en un servicio de hospedaje estático (como GitHub Pages) sin un servidor de backend en ejecución:

* **Pérdida Total de Datos Dinámicos:** La interfaz gráfica del mapa y los formularios de navegación cargarían en el navegador, pero los mapas aparecerían completamente vacíos, sin marcadores de sismos ni capas de calor.
* **Fallo Masivo en la Red:** La consola del navegador mostraría errores recurrentes de tipo \Failed to fetch\, \ERR_CONNECTION_REFUSED\ o respuestas \404 Not Found\ en cada llamada que intente contactar a los endpoints \/api/...\.
* **Inoperatividad de Filtros y Búsquedas:** Los controles de usuario (deslizadores de magnitud, selectores de fecha) quedarían inútiles, pues la lógica de agregación, ordenamiento y consulta SQL se ejecuta en PostgreSQL a través de Node.js, componentes ausentes en un hospedaje puramente estático.
