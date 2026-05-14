
<div align="center">

<p align="center">
  <img src="assets/img/logo/upc_logo.png" alt="logo" width="200"/>
</p>

<h3 align="center">
Universidad Peruana de Ciencias Aplicadas
</h3>

<h3 align="center">
Ingeniería de Software  
<br><br>
Periodo: 202610 
<br><br>
1ASI0657 - Fundamentos Arquitectura de Software
<br><br>
NRC: 7940  
<br><br>
Docente: Mori Yzaguirre, Daniel Enrique  
<br><br>
<strong>Informe de TB2</strong>  
<br><br>
Startup: Venti  
<br><br>
Producto: NextHappen  
<br><br>
<br><br>
<strong>Integrantes</strong>  
<br><br>
Cabanillas Meza, Jose Mateo (u202311458)
<br><br>
Nakasone Gomes, Marco Antonio (u202210790)
<br><br>
Carhuancote Domminguez, Gonzalo Alonso (u202210720) 
<br><br>
<br>

**2026**
</h3>
</div>

# Capítulo IV: Product Architecture Design

## 4.1 Design Concepts, ViewPoints & ER Diagrams

### 4.1.1 Principles Statements

El diseño arquitectónico de NextHappen se fundamenta en principios sólidos de ingeniería de software para garantizar la calidad, mantenibilidad y evolución del sistema a lo largo del tiempo:

*   **SOLID Principles:** Cada componente y microservicio en el backend se diseña respetando el principio de responsabilidad única (SRP), de modo que cada servicio (ej. Gestión de Eventos, Gestión de Usuarios) tenga una sola razón para cambiar. Las interfaces estarán claramente definidas (ISP) para la comunicación entre servicios.
*   **Separation of Concerns (SoC):** Separación estricta entre la capa de presentación (Frontend web y móvil), la capa de negocio (Microservicios) y la capa de persistencia (Bases de datos poli-glotas).
*   **Don't Repeat Yourself (DRY):** La lógica de negocio compartida (como la validación de tokens JWT o configuraciones globales) será centralizada en librerías comunes (Custom Software Library) importables por cada microservicio, o delegadas a componentes de infraestructura como un API Gateway.
*   **Keep It Simple, Stupid (KISS):** A pesar de aplicar una arquitectura de microservicios, las soluciones implementadas (como la mensajería asíncrona) se mantendrán lo más simples posibles hasta que la demanda requiera mayor complejidad, garantizando un "Time to Market" acelerado.
*   **Design for Failure:** Dado que la aplicación está orientada a la nube (Cloud Native), se asume que los componentes pueden fallar. Se aplicarán patrones como Circuit Breaker y Retries para manejar la indisponibilidad de microservicios sin afectar la experiencia general del usuario final.

### 4.1.2 Approaches Statements Architectural Styles & Patterns

Para satisfacer los requisitos funcionales y no funcionales de NextHappen, se ha seleccionado un enfoque híbrido de estilos arquitectónicos:

*   **Arquitectura de Microservicios (Microservices Architecture):** El sistema monolítico inicial será refactorizado y desacoplado en unidades de negocio independientes (Bounded Contexts derivados de Domain-Driven Design). Esto permite el despliegue y escalado independiente de servicios de alta demanda, como el catálogo de eventos o el mapa interactivo.
*   **RESTful APIs & API Gateway Pattern:** Toda comunicación externa desde los clientes (Web App en React/Angular, Mobile App) hacia el backend se realizará mediante APIs RESTful. Se utilizará un API Gateway como único punto de entrada para enrutar, balancear carga y autenticar las peticiones.
*   **Domain-Driven Design (DDD) - Layered Architecture:** Internamente, cada microservicio adoptará una arquitectura de capas (o Hexagonal/Clean Architecture), separando Domain, Application, Infrastructure y Presentation (Controladores REST).
*   **Polyglot Persistence:** Se utilizará un enfoque de persistencia políglota, combinando bases de datos relacionales (ej. PostgreSQL) para datos estructurados con garantías transaccionales (Usuarios, Facturación, Entradas) y bases de datos NoSQL (ej. MongoDB) para datos dinámicos, búsquedas espaciales y altos volúmenes de lectura (Catálogo de eventos, Reseñas).

### 4.1.3 Context Diagram

<p align="center">
  <img src="assets/img/cap4/context_diagram.png" alt="Context Diagram" width="600"/>
</p>

El diagrama de contexto presenta a NextHappen como el sistema central que interactúa con tres tipos de usuarios: asistentes, organizadores y administradores. Asimismo, se integra con sistemas externos como servicios de pago, mapas y notificaciones, los cuales permiten soportar funcionalidades clave como compra de entradas, geolocalización de eventos y comunicación en tiempo real.

### 4.1.4 Approach driven ViewPoints Diagrams

*   **Diagrama de Clases**

El diagrama de clases representa las entidades principales del dominio siguiendo principios de Domain Driven Design (DDD), mostrando las relaciones entre usuarios, eventos, tickets, categorías y reseñas.

<p align="center">
  <img src="assets/img/cap4/class_diagram.png" alt="Class Diagram" width="600"/>
</p>

*   **Diagrama de Actividad**

El diagrama de actividad describe el flujo principal de compra de entradas, incluyendo validación de disponibilidad, procesamiento de pagos y notificación al usuario.

<p align="center">
  <img src="assets/img/cap4/activity_diagram.png" alt="Activity Diagram" width="600"/>
</p>

*   **Diagrama de Estados**

El diagrama de estados modela el ciclo de vida de un ticket, desde su creación hasta su uso o cancelación, permitiendo entender los distintos estados posibles dentro del sistema.

<p align="center">
  <img src="assets/img/cap4/status_diagram.png" alt="Status Diagram" width="400"/>
</p>

### 4.1.5 Relational/Non Relational Database Diagram

<p align="center">
  <img src="assets/img/cap4/non_relational_database.png" alt="Database Diagram" width="600"/>
</p>

El diagrama de base de datos representa las entidades principales del sistema NextHappen y sus relaciones. Se identifican entidades como usuarios, eventos, tickets, categorías, reseñas y notificaciones, las cuales permiten soportar las funcionalidades clave del sistema como la gestión de eventos, compra de entradas, personalización y comunicación con los usuarios. Las relaciones entre tablas garantizan la integridad referencial y permiten una correcta organización de la información dentro del sistema.

### 4.1.6 Design Patterns

A lo largo del desarrollo de NextHappen, se aplicarán los siguientes patrones de diseño de software (Design Patterns / GoF) y de arquitectura para asegurar la calidad del código:

*   **Creacionales:** Factory Method y Builder para la instanciación compleja de objetos como las respuestas HTTP (DTOs) estandarizadas y la construcción de peticiones a APIs externas. Singleton para la gestión de conexiones a bases de datos y clientes externos de Cloud.
*   **Estructurales:** Data Transfer Object (DTO) para desacoplar el modelo de dominio interno de las representaciones expuestas en las APIs REST. Adapter / Wrapper para la integración de la pasarela de pagos, aislando la lógica específica del proveedor externo del dominio del sistema.
*   **Comportamiento:** Strategy para implementar múltiples estrategias de filtrado y búsqueda de eventos en el mapa. Observer para el sistema de alertas personalizadas, permitiendo a los suscriptores (Web/Mobile App) reaccionar a cambios de estado en un evento (ej. cambio de horario o cancelación).
*   **Patrones Cloud / Microservicios:** Database per Service, API Gateway, y Circuit Breaker (usando herramientas como Resilience4J o Istio).

### 4.1.7 Tactics

Las tácticas arquitectónicas definidas buscan cumplir los principales atributos de calidad del sistema, como rendimiento, seguridad y disponibilidad. Entre las más relevantes se incluyen el uso de caching para optimizar tiempos de respuesta, autenticación y autorización para el control de acceso, procesamiento asíncrono para mejorar la escalabilidad, y mecanismos como retry y circuit breaker para garantizar la resiliencia del sistema.

Estas decisiones se alinean con la arquitectura de microservicios propuesta, permitiendo un sistema más robusto, escalable y mantenible.

## 4.2 Architectural Drivers

### 4.1.8 Design Purpose

El propósito central del diseño arquitectónico de NextHappen es proveer una plataforma altamente disponible, escalable (capaz de manejar picos de tráfico durante los fines de semana cuando hay eventos concurrentes) y geográficamente responsiva (mapas en tiempo real rápidos y precisos). Asimismo, busca ser un entorno seguro para procesar pagos y datos de los emprendedores culturales.

### 4.1.9 Primary Functionality (Primary User Stories)

Las funcionalidades primarias de mayor impacto arquitectónico (por su volumen de transacciones o complejidad) son:

1.  **Descubrimiento y Mapa:** Como usuario asistente, quiero buscar ferias y eventos en un mapa interactivo con información en tiempo real para decidir a dónde ir hoy. (Alta concurrencia, lecturas masivas).
2.  **Gestión de Eventos:** Como organizador, quiero publicar mi evento indicando ubicación, aforo y cambios de última hora, para que todos los interesados sean notificados. (Escrituras asíncronas, notificación push).
3.  **Compra y Gestión de Entradas:** Como usuario asistente, quiero adquirir y descargar una entrada digital segura (código QR), para acceder al evento fácilmente. (Alta confiabilidad, consistencia ACID).

### 4.1.10 Quality Attribute Scenarios

*   **QA-1 Disponibilidad (Availability):** Si el microservicio de gestión de pagos cae durante un fin de semana, el catálogo y mapa interactivo de eventos debe seguir funcionando de forma degradada (Read-Only) permitiendo a los usuarios al menos ver los eventos gratuitos o revisar la ubicación de los eventos ya comprados, sin que el sistema colapse por completo.
*   **QA-2 Escalabilidad (Scalability):** Durante un anuncio viral de un evento independiente muy esperado, el sistema experimenta un aumento del 500% de peticiones por minuto. El clúster de microservicios en la nube debe escalar horizontalmente (Auto-scaling) en menos de 2 minutos para mantener el tiempo de respuesta por debajo de 1.5 segundos.
*   **QA-3 Rendimiento (Performance):** Cuando un usuario navega por el mapa interactivo de NextHappen, las consultas espaciales (búsqueda por radio geográfico) deben resolverse en la base de datos (MongoDB) y renderizarse en el frontend en un máximo de 2 segundos en conexiones móviles 4G.
*   **QA-4 Seguridad (Security):** Todo el tráfico que ingresa a la plataforma desde la aplicación web o móvil debe ser autenticado en menos de 500ms utilizando un token JWT, asegurando que ninguna petición no autorizada llegue a la red privada de microservicios.

### 4.1.11 Constraints

1.  **Tecnológicas:** El desarrollo se restringirá a frameworks open source modernos y ampliamente adoptados por la industria y la comunidad académica (ej. Node.js, Spring Boot, React/Angular) para asegurar soporte a largo plazo y facilidad de implementación.
2.  **Tiempo (Time to Market):** El proyecto debe desarrollarse e implementarse a nivel funcional en ciclos cortos de 2 a 3 semanas (Sprints), limitando la creación de componentes personalizados en favor de soluciones nativas de la nube administradas (PaaS o SaaS como RDS, MongoDB Atlas).
3.  **Presupuesto Inicial:** La infraestructura debe ser hospedada en proveedores de Cloud Computing (AWS o Azure) utilizando inicialmente la capa gratuita (Free Tier) o créditos académicos, forzando a la arquitectura a ser liviana e eficiente en el uso de recursos de cómputo.

### 4.1.12 Architectural Concerns

*   **DevOps & CI/CD:** El despliegue de microservicios separados requiere un proceso de integración y despliegue continuo automatizado para evitar conflictos de versionamiento (uso de GitFlow y pipelines).
*   **Gestión de Configuración:** Al tener múltiples microservicios, los endpoints externos, credenciales de bases de datos y tokens de pasarelas de pago deben estar centralizados y securizados.
*   **Testing Automatizado (BDD/TDD):** Garantizar la cobertura de pruebas unitarias y de comportamiento en la lógica central (Core Application Suite) para evitar regresiones en cada release.
*   **Observabilidad:** Necesidad de trazabilidad (tracing) y aggregación de logs en todos los microservicios para detectar cuellos de botella y auditar las fallas reportadas por los usuarios.

## 4.3 ADD Iterations

### 4.2.1 Iteration 1: Establishing the Baseline Microservices Architecture

#### 4.3.1.1 Architectural Design Backlog 1

**Objetivo:** Definir la arquitectura base de NextHappen, asegurando que soporte el descubrimiento de eventos y la gestión de identidades con alta disponibilidad y seguridad.

| Id | Tipo | Título/Driver | Descripción | Prioridad |
| :--- | :--- | :--- | :--- | :--- |
| QA-01 | Atributo | Disponibilidad | Garantizar acceso estable durante picos de preventa mediante replicación y balanceo. | Alta |
| QA-02 | Atributo | Escalabilidad | Asegurar la capacidad del sistema para manejar incrementos súbitos en el número de usuarios concurrentes mediante escalado horizontal. | Alta |
| QA-04 | Atributo | Seguridad | Implementar autenticación JWT y cifrado de datos de perfiles y pagos. | Alta |
| US06 | User Story | Búsqueda de Eventos | Como asistente, quiero filtrar ferias por categoría y ubicación para encontrar opciones de interés. | Alta |
| US11 | User Story | Compra de Entradas | Como asistente, quiero realizar la compra de tickets y recibir un comprobante digital. | Alta |
| CON-01 | Restricción | Base Tecnológica | Arquitectura de microservicios desarrollada con Spring Boot. | Alta |

#### 4.3.1.2 Establish Iteration Goal by Selecting Drivers

| Drivers Seleccionados | Resultado Esperado |
| :--- | :--- |
| QA-01 & QA-02 | Microservicios que se pueden replicar (escalar) dinámicamente según la carga de tráfico. |
| QA-04 | Seguridad perimetral en el Gateway para proteger todos los servicios escalados. |
| US06 & US11 | Lógica de negocio distribuida en servicios independientes listos para crecer por separado. |

#### 4.3.1.3 Choose One or More Elements of the System to Refine

En esta fase se seleccionan los elementos de software y de infraestructura que recibirán el diseño detallado para materializar los atributos de calidad de la iteración:

| Elementos del Sistema | Capa / Contexto | Descripción de Refinamiento |
| :--- | :--- | :--- |
| Web App (Angular) | Presentación | Interfaz web para que los Organizadores gestionen ferias y los asistentes busquen eventos desde el navegador. |
| Mobile App (Flutter) | Presentación | Aplicación para Asistentes enfocada en la portabilidad, búsqueda rápida (US06) y acceso a tickets QR (US11). |
| API Gateway (Spring Cloud) | Infraestructura Perímetro | Configurar como punto único de entrada para aplicar Load Balancing (QA-01) y permitir el Escalado Horizontal (QA-02) redirigiendo tráfico. |
| Módulo de Autenticación (Auth Service) | Seguridad Identidad | Implementar la lógica de gestión de identidades y generación de tokens JWT para cumplir con el QA-04. Centraliza el acceso de usuarios (US01). |
| Catálogo de Eventos (Event Service) | Negocio Dominio | Refinar el componente de búsqueda y filtrado de la US06. Se diseña para ser un servicio independiente con su propia persistencia para escalar. |
| Módulo de Compras (Booking Service) | Negocio Persistencia | Diseñar la lógica de reserva y venta de la US11, asegurando la consistencia de datos y la capacidad de respuesta bajo alta concurrencia. |
| Servidor de Descubrimiento (Eureka) | Infraestructura Red | Establecer el registro dinámico de microservicios para permitir que el Gateway detecte nuevas instancias escaladas automáticamente (QA-01 y QA-02). |

#### 4.3.1.4 Choose One or More Design Concepts That Satisfy the Selected Drivers

En esta sección se seleccionan las tácticas y patrones de diseño que permiten cumplir con los atributos de calidad y las historias de usuario priorizadas:

| Driver Seleccionado | Tipo | Concepto de Diseño / Patrón Seleccionado | Justificación |
| :--- | :--- | :--- | :--- |
| QA-01 Disponibilidad | Atributo | Replicación de Servicios + Health Checks | Permite mantener múltiples instancias de los servicios de Eventos y Compras activas; si una falla, el sistema redirige el tráfico a las sanas. |
| QA-02 Escalabilidad | Atributo | Escalado Horizontal + Service Discovery | Facilita la adición dinámica de nuevos nodos de procesamiento mediante Eureka, permitiendo que el sistema soporte incrementos de usuarios concurrentes sin degradarse. |
| QA-04 Seguridad | Atributo | API Gateway + Centralized JWT | Centraliza la validación de identidad en la periferia del sistema, asegurando que solo peticiones autenticadas lleguen a los microservicios de negocio. |
| US11 Compra de Entradas | User Story | Database per Service | Garantiza que el servicio de Booking tenga su propia persistencia, evitando cuellos de botella en la base de datos durante transacciones masivas. |

#### 4.3.1.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces

Se definen las responsabilidades de los componentes instanciados y sus puntos de interacción (APIs):

| Elemento Arquitectónico | Responsabilidades | Interfaces |
| :--- | :--- | :--- |
| NextHappen Web (Angular) | Interfaz administrativa para organizadores y visualización de catálogo para usuarios web. | Browser / REST Consumer |
| NextHappen Mobile (Flutter) | Interfaz móvil optimizada para la experiencia del asistente y validación de entradas mediante QR. | Mobile / REST Consumer |
| API Gateway | Recibir peticiones de Web/Móvil, validar seguridad JWT y redirigir según el directorio de Eureka. | Spring Cloud Gateway API |
| Eureka Server | Mantener el mapa actualizado de qué microservicios están encendidos y en qué dirección IP. | Eureka Discovery API |
| Identity Service | Gestionar el registro de usuarios US01, autenticación y generación de tokens de seguridad. | REST: /api/v1/auth |
| Módulo de Eventos | Proveer la data de las ferias y procesar los filtros de búsqueda de la US06. | REST: /api/v1/events |
| Módulo de Compras | Gestionar el flujo de pago, stock y generación de comprobantes digitales de la US11. | REST: /api/v1/booking |
| PostgreSQL (Instance per service) | Proveer persistencia de datos independiente para cada microservicio para evitar cuellos de botella. | JDBC Connection |

#### 4.3.1.6 Sketch Views (C4 & UML) and Record Design Decisions

<p align="center">
  <img src="assets/img/cap4/sketch_views.png" alt="Sketch Views" width="800"/>
</p>

El diagrama de contenedores presenta una arquitectura de microservicios distribuida, donde la interacción de los usuarios (Asistentes y Organizadores) se canaliza a través de aplicaciones Web (Angular) y Móviles (Flutter) que consumen servicios de forma centralizada mediante un API Gateway. Esta estructura garantiza la Seguridad (QA-04) al validar tokens JWT en el perímetro y promueve la Escalabilidad (QA-02) y Disponibilidad (QA-01) mediante el uso de un Servidor Eureka, que permite el registro y descubrimiento dinámico de instancias para el balanceo de carga. Cada microservicio de negocio —Identity, Event y Booking— gestiona su propia persistencia en bases de datos independientes (PostgreSQL) para evitar acoplamientos y cuellos de botella, integrándose con sistemas externos como Google Maps API para la geolocalización de ferias (US06) y una Pasarela de Pagos para el procesamiento seguro de entradas (US11).

#### 4.3.1.7 Analysis of Current Design and Review Iteration Goal (Kanban Board) (Avance 2)

El Kanban de la primera iteración organizó las actividades de análisis y diseño del sistema. Se avanzó desde el entendimiento del problema hasta la definición de la arquitectura base. Las historias de usuario guiaron las decisiones de diseño.

<p align="center">
  <img src="assets/img/cap4/kanban1.png" alt="Kanban Iteration 1" width="800"/>
</p>

### 4.2.2 Iteration 2: Events Service (Búsqueda Geolocalizada)

**Objetivo:** Refinar el servicio de eventos para asegurar tiempos de respuesta mínimos (Rendimiento) y capacidad de carga masiva durante búsquedas geolocalizadas mediante el uso de caché y optimización de datos.

#### 4.3.2.1 Architectural Design Backlog 2

Seleccionamos los drivers que impactan directamente en la experiencia de búsqueda.

| Id | Tipo | Título / Driver | Descripción | Prioridad |
| :--- | :--- | :--- | :--- | :--- |
| QA-03 | Atributo | Rendimiento | Latencia menor a 200ms en la recuperación de eventos cercanos mediante caché. | Alta |
| QA-02 | Atributo | Escalabilidad | Procesar ráfagas de consultas de geolocalización concurrentes sin degradar el servicio. | Alta |
| US06 | User Story | Búsqueda de Eventos | Búsqueda eficiente de ferias por ubicación y categoría. | Alta |
| US07 | User Story | Geolocalización | Visualización de eventos en un mapa basados en la posición actual del usuario. | Alta |

#### 4.3.2.2 Establish Iteration Goal by Selecting Drivers

| Drivers Seleccionados | Resultado Esperado |
| :--- | :--- |
| QA-03 (Rendimiento) | Consultas de búsqueda que responden casi instantáneamente gracias a la integración de Redis. |
| QA-02 (Escalabilidad) | El servicio de eventos puede manejar múltiples solicitudes de ubicación simultáneas sin aumentar la latencia. |
| US06 & US07 | Experiencia de usuario fluida donde las ferias se cargan en el mapa en tiempo real según la posición del GPS. |

#### 4.3.2.3 Choose One or More Elements of the System to Refine

En esta iteración nos enfocamos exclusivamente en la pieza de visualización y el motor de búsqueda.

| Elementos del Sistema | Capa / Contexto | Descripción de Refinamiento |
| :--- | :--- | :--- |
| Event Service (Backend) | Negocio / Dominio | Lógica de búsqueda geoespacial y filtros avanzados por categorías. |
| Redis Cache | Infraestructura / Datos | Capa de memoria volátil para almacenar resultados de búsquedas frecuentes. |
| Google Maps API | Integración Externa | Comunicación para obtener coordenadas y renderizado de mapas en tiempo real. |

#### 4.3.2.4 Choose One or More Design Concepts That Satisfy the Selected Drivers

Aplicación de tácticas específicas para optimizar la velocidad.

| Driver Seleccionado | Tipo | Concepto de Diseño / Patrón | Justificación |
| :--- | :--- | :--- | :--- |
| QA-03 Rendimiento | Atributo | Cache-Aside Pattern (Redis) | Reduce el acceso a la base de datos entregando resultados desde memoria. |
| QA-02 Escalabilidad | Atributo | CQRS (Read side) | Separa consultas de lectura de actualizaciones para escalar independientemente. |
| US07 Geolocalización | User Story | Spatial Indexing | Uso de índices geográficos (PostGIS) para optimizar cálculos de distancia. |

#### 4.3.2.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces

Definición de responsabilidades y contratos de comunicación.

| Elemento Arquitectónico | Responsabilidades | Interfaces |
| :--- | :--- | :--- |
| EventController | Exponer endpoints de búsqueda y recibir coordenadas (lat/long). | GET /api/v1/events/search |
| Redis Server | Almacenar objetos de "Ferias Cercanas" para evitar re-cálculos costosos. | Redis Sentinel / Jedis |
| Google Maps Integration | Transformar direcciones de texto en coordenadas (Geocoding). | Maps SDK for Mobile/Web |
| Spatial Repository | Consultas SQL optimizadas con operadores geoespaciales (radio de distancia). | Spring Data JPA / PostGIS |

#### 4.3.2.6 Sketch Views (C4 & UML) and Record Design Decisions

<p align="center">
  <img src="assets/img/cap4/sketch_views_2.png" alt="Sketch Views Iteration 2" width="800"/>
</p>

Este diagrama de componentes del Event Service justifica una arquitectura orientada a la eficiencia y escalabilidad geográfica de la siguiente manera:

El EventController actúa como el punto de entrada principal, desacoplando la recepción de peticiones del procesamiento lógico. La integración con Redis (mediante el componente Redis Server) se justifica por la necesidad de reducir la latencia en búsquedas frecuentes, evitando re-cálculos costosos en la base de datos. Por otro lado, la inclusión de Google Maps Integration permite la flexibilidad de buscar por texto (direcciones), mientras que el Spatial Repository centraliza la complejidad de las consultas SQL de proximidad utilizando operadores de PostGIS. Esta estructura permite que cada componente evolucione de forma independiente, garantizando que el sistema soporte una alta demanda de usuarios buscando ferias en tiempo real.

#### 4.3.2.7 Analysis of Current Design and Review Iteration Goal (Kanban Board) (Avance 2)

El Kanban de la segunda iteración refleja el inicio del desarrollo del sistema. Se implementaron funcionalidades clave sobre la arquitectura definida. Esto permitió avanzar hacia una versión funcional del producto.

<p align="center">
  <img src="assets/img/cap4/kanban2.png" alt="Kanban Iteration 2" width="800"/>
</p>
