# Investigación: Arquitectura Web, Frontend, Backend y Hospedaje

## 1. Frontend y Backend: Separación de Responsabilidades y Ejecución
En la ingeniería de software moderna, las aplicaciones web se estructuran bajo una arquitectura cliente-servidor desacoplada. 

El **frontend** representa la capa de presentación y la interfaz con la que interactúa el usuario final. Este componente se ejecuta estrictamente en la **máquina cliente**, concretamente dentro del entorno de ejecución (sandbox) del navegador web (como Google Chrome, Mozilla Firefox o Safari). Los lenguajes fundamentales que componen esta capa son HTML (para estructuración semántica), CSS (para el diseño y presentación visual) y JavaScript o TypeScript (para el dinamismo, reactividad y manipulación del DOM). En sistemas contemporáneos que utilizamos a diario, como Spotify Web o YouTube, el frontend se encarga de pintar las tarjetas de reproducción, capturar los clics del usuario y gestionar las animaciones visuales locales sin recargar la página.

Por otro lado, el **backend** corresponde a la capa lógica y de procesamiento de negocio. Se ejecuta en **máquinas servidoras** (servidores locales, instancias virtuales o contenedores) protegidas dentro de redes privadas. Los lenguajes predominantes en este entorno abarcan Python, Go, Java, C#, PHP y JavaScript/TypeScript (bajo entornos de ejecución de servidor como Node.js y Bun). El backend se encarga de validar reglas de negocio, procesar transacciones críticas, aplicar algoritmos de autenticación/autorización y comunicarse con los motores de bases de datos relacionales o NoSQL. En el ejemplo de YouTube, mientras el frontend renderiza la interfaz, el backend procesa la codificación de video, verifica los permisos de suscripción y consulta los registros transaccionales en la base de datos distribuida.

---

## 2. Ciclo de Vida de una Petición Web: Del Navegador a la Pantalla
Desde el instante en que un usuario escribe una dirección en la barra de búsqueda (por ejemplo, \https://ipn.mx\) y presiona Enter, ocurren los siguientes pasos:

1. **Resolución de Nombres (DNS Lookup):** El navegador consulta la caché local del sistema operativo; si no existe el registro, envía una petición recursiva al servidor DNS configurado para traducir el nombre de dominio en una dirección IP enrutable (IPv4 o IPv6).
2. **Establecimiento de Conexión (TCP Handshake y TLS):** Una vez obtenida la IP, el cliente inicia el protocolo de enlace de tres vías (TCP 3-Way Handshake) mediante el intercambio de paquetes \SYN\, \SYN-ACK\ y \ACK\. Al tratarse de HTTPS, se efectúa inmediatamente la negociación criptográfica TLS (Transport Layer Security) para validar el certificado del servidor, negociar el cifrado simétrico y establecer un canal seguro.
3. **Envío de la Petición HTTP:** El navegador formula y envía un paquete \GET / HTTP/2\ (o HTTP/3) con las cabeceras correspondientes (User-Agent, Accept, cookies de sesión).
4. **Procesamiento en Servidor:** El servidor web (Nginx, Apache o el proxy inverso del hospedaje) recibe el paquete, determina si solicita un activo estático o si debe pasarlo a un servidor de aplicaciones, procesa la lógica y emite una respuesta HTTP (típicamente \200 OK\) con el cuerpo del documento HTML.
5. **Renderizado en Cliente (Critical Rendering Path):** El motor del navegador analiza el HTML entrante construyendo el DOM (Document Object Model). Al encontrar etiquetas \<link>\ y \<script>\, emite peticiones adicionales para descargar CSS y JavaScript, construyendo en paralelo el CSSOM (CSS Object Model). Con ambos árboles genera el *Render Tree*, calcula las geometrías (Layout/Reflow) y finalmente pinta los píxeles en pantalla (Painting).

---

## 3. Interfaces de Programación de Aplicaciones (API) y Seguridad de Datos
Una **API (Application Programming Interface)** es un conjunto formalizado de reglas, especificaciones y protocolos de comunicación que permite a dos sistemas informáticos independientes intercambiar datos y ejecutar procedimientos de manera estandarizada y segura. En el desarrollo web, las APIs más comunes operan bajo arquitecturas REST o GraphQL transmitiendo información serializada en formato JSON sobre el protocolo HTTP.

Una página estática (compuesta únicamente por archivos HTML, CSS y JavaScript alojados en un servidor de archivos como GitHub Pages) **no puede ni debe conectarse directamente a una base de datos** por dos razones estructurales:

* **Exposición Crítica de Credenciales:** Cualquier código JavaScript ejecutado en el navegador es completamente público y visible a través de las herramientas de desarrollador (\F12\). Si una página web alojada en un cliente intentara autenticarse contra PostgreSQL, la cadena de conexión, el usuario, la contraseña y la IP de la base de datos quedarían expuestos, permitiendo a cualquier tercero tomar control del servidor de base de datos.
* **Incompatibilidad de Protocolos:** Los navegadores web únicamente admiten peticiones orientadas a HTTP/HTTPS o WebSockets. Los sistemas gestores de bases de datos relacionales (como PostgreSQL o MySQL) se comunican mediante protocolos TCP propietarios binarios de bajo nivel que requieren sockets nativos, los cuales el entorno del navegador restringe por diseño. Por ello, la API actúa como una compuerta intermedia obligatoria: el frontend solicita \/api/pacientes\, la API valida la identidad del usuario, ejecuta la consulta interna hacia la base de datos mediante una conexión protegida y regresa únicamente los datos necesarios al cliente.

---

## 4. Tipos de Hospedaje Web
Un servicio de hospedaje web es una infraestructura de hardware y software conectada permanentemente a Internet que almacena los recursos de un sistema informático para servirlos ante peticiones remotas.

* **Hospedaje Estático vs. Servidor de Aplicaciones:** Un hospedaje estático (como GitHub Pages o Netlify) solo almacena y entrega archivos planos (HTML, imágenes, CSS, bundles de JS); no ejecuta ningún intérprete de backend (como Python, PHP o Node.js). En contraste, un servidor de aplicaciones mantiene entornos en ejecución persistentes capaces de compilar código, ejecutar procesos concurrentes, escuchar puertos TCP y conectarse a bases de datos.
* **Hospedaje Compartido (Shared Hosting):** Múltiples usuarios comparten un único servidor físico y su sistema operativo. Los recursos (CPU, memoria RAM, ancho de banda) se dividen entre decenas o cientos de sitios. Aunque es económico y fácil de usar, carece de aislamiento de rendimiento y limita la instalación de software especializado.
* **Servidor Virtual Privado (VPS):** Un servidor físico se divide mediante tecnología de hipervisor en múltiples máquinas virtuales aisladas. Cada inquilino dispone de recursos de cómputo dedicados (vCPU, memoria asignada) y acceso como superusuario (\oot\), permitiendo instalar cualquier distribución Linux, contenedores Docker y configuraciones avanzadas.
* **Cómputo en la Nube (Cloud Infrastructure):** Plataformas elásticas (como AWS, Google Cloud, Microsoft Azure) donde la infraestructura no depende de una sola máquina física sino de un grupo virtualizado distribuido. Permite escalabilidad horizontal instantánea, almacenamiento distribuido y modelos de cobro basados estrictamente en el consumo de recursos.

---

## 5. Dominios, DNS y Titularidad de Dominios
Un **dominio** es un identificador alfanumérico legible para los humanos (por ejemplo, \unam.mx\ o \github.com\) diseñado para sustituir las direcciones IP numéricas complejas asociadas a las máquinas de la red.

El **Sistema de Nombres de Dominio (DNS)** funciona como la libreta telefónica distribuida de Internet. Su función principal es la traducción bidireccional entre nombres de dominio y direcciones IP mediante una jerarquía organizada de servidores raíz (Root Servers), servidores de dominio de nivel superior (TLD Servers) y servidores de nombres autoritativos.

En el caso del sitio desplegado en esta práctica (\https://ferverdz18.github.io/Practica3.1/\), el dominio raíz pertenece formalmente a **GitHub Inc. (subsidiaria de Microsoft Corporation)**. Los proyectos de los estudiantes quedan alojados bajo un subdominio de tercer nivel (\erverdz18.github.io\), donde GitHub mantiene el control total de la delegación de zonas DNS, el enrutamiento de tráfico y la titularidad legal del registro.

---

## 6. Protocolo HTTPS y Limitaciones de Planes Gratuitos
**HTTPS (Hypertext Transfer Protocol Secure)** es la extensión segura del protocolo HTTP estándar. Incorpora un esquema de cifrado basado en TLS mediante el uso de criptografía asimétrica y simétrica. Esto garantiza tres pilares esenciales:
1. **Confidencialidad:** La información intercambiada entre el cliente y el servidor viaja cifrada y no puede ser leída por intermediarios.
2. **Integridad:** Los paquetes transmitidos no pueden ser interceptados ni alterados en tránsito sin ser detectados.
3. **Autenticación:** Certifica que el servidor con el que se comunica el cliente es legítimo y posee un certificado digital emitido por una Autoridad de Certificación (CA) válida.

### Limitaciones Típicas de Planes Gratuitos de Hospedaje:
* **Suspensión por Inactividad ("Cold Starts"):** En plataformas con soporte de backend (como Render o Fly.io), los servidores entran en hibernación tras periodos de inactividad, tardando entre 30 y 60 segundos en responder a la primera petición.
* **Límites de Ancho de Banda y Transferencia:** Restricciones estrictas de datos salientes (habitualmente entre 50 GB y 100 GB mensuales).
* **Capacidad de Cómputo Reducida:** Límite de 512 MB de memoria RAM y fracciones de núcleo de CPU compartidas.
* **Ausencia de SLA (Service Level Agreement):** No ofrecen garantías de disponibilidad ni soporte técnico directo ante fallos del servicio.
* **Retención de Certificados y Dominios Propios:** Algunos proveedores restringen el uso de certificados personalizados o aplican renovaciones con limitaciones operativas.

---

## Referencias (Formato APA 7.ª Edición)

* Fielding, R. T., & Taylor, R. N. (2002). Principled design of the modern Web architecture. *ACM Transactions on Internet Technology (TOIT)*, 2(2), 115-150. https://doi.org/10.1145/514183.514185
* Kurose, J. F., & Ross, K. W. (2021). *Computer networking: A top-down approach* (8.ª ed.). Pearson Education.
* Mozilla Developer Network. (2024). *How the Web works: The web standards guide*. MDN Web Docs. https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/How_the_Web_works
* Silberschatz, A., Korth, H. F., & Sudarshan, S. (2020). *Database system concepts* (7.ª ed.). McGraw-Hill Education.
* Tanenbaum, A. S., & Wetherall, D. J. (2014). *Computer networks* (5.ª ed.). Pearson.
