# Comparación de Servicios de Hospedaje Web Estático

**Unidad de Aprendizaje:** Bases de Datos  
**Estudiante:** Fernando García Verduzco (\FERVERDZ18\)  
**Proyecto:** Sistema de Gestión Clínico Veterinario (Landing Page)

---

## 1. Tabla Comparativa de Plataformas

| Parámetro | GitHub Pages | Vercel | Netlify |
| :--- | :--- | :--- | :--- |
| **URL de Despliegue** | \https://ferverdz18.github.io/Practica3.1/\ | \https://practica31-docs.vercel.app\ | \https://silver-blancmange-2bddb6.netlify.app\ |
| **Pasos para Configurar** | 1. Ir a Settings > Pages.<br>2. Elegir rama (\practica31\) y carpeta \/docs\.<br>3. Guardar. | 1. Importar repositorio desde GitHub.<br>2. Definir Root Directory en \docs\.<br>3. Clic en Deploy. | 1. Conectar con GitHub.<br>2. Seleccionar repositorio.<br>3. Configurar Publish directory en \docs\. |
| **Tiempo de Despliegue** | ~45 a 90 segundos | ~15 a 25 segundos | ~20 a 30 segundos |
| **Soporte HTTPS** | Automático (Let's Encrypt / DigiCert) | Automático (Let's Encrypt con renovación periódica) | Automático (Let's Encrypt con renovación periódica) |
| **CI/CD Automático** | Integrado vía GitHub Actions nativo | Automático con cada push o Pull Request (Preview Deployments) | Automático con cada push (Deploy Previews inmediatos) |
| **Límites Declarados (Gratis)** | - 1 GB de almacenamiento por repo.<br>- 100 GB de transferencia mensual.<br>- 10 compilaciones por hora. | - 100 GB de ancho de banda al mes.<br>- Límites de ejecución Serverless (Edge Network). | - 100 GB de transferencia mensual.<br>- 300 minutos de compilación al mes. |

---

## 2. Análisis Crítico y Elección para Backend y Base de Datos

Aunque **GitHub Pages**, **Vercel** y **Netlify** destacan por su rapidez y simplicidad al distribuir archivos estáticos (HTML, CSS y JS) a través de redes de entrega de contenido (CDN), **ninguno de los tres es adecuado como solución completa para alojar una base de datos relacional tradicional (PostgreSQL 17) junto con un backend transaccional de larga duración (como una API en FastAPI con pools de conexiones persistentes)**:

1. **GitHub Pages** es estrictamente un servidor de archivos planos. No cuenta con entorno de ejecución para lenguajes de servidor ni gestión de puertos TCP.
2. **Vercel y Netlify** ofrecen funciones Serverless / Edge Functions sin estado (*stateless*). Estas funciones tienen tiempos máximos de ejecución cortos y manejan mal las conexiones persistentes hacia un motor de bases de datos relacional, pudiendo saturar el límite de conexiones de PostgreSQL en picos de tráfico.

### Elección Justificada:
Para la fase de producción con backend y base de datos, la plataforma recomendada es un **Servidor Virtual Privado (VPS) o Plataforma como Servicio en Contenedores (PaaS/IaaS) como Render, Railway o una instancia Linux en la nube (AWS EC2 / DigitalOcean)**:
- Permite ejecutar contenedores **Docker / Docker Compose** aislados.
- Admite procesos persistentes escuchando en puertos dedicados para el servidor FastAPI.
- Ofrece volúmenes de almacenamiento persistente montados para PostgreSQL 17, garantizando que los datos clínicos, citas y tratamientos no se pierdan al reiniciar el contenedor o el servicio.
