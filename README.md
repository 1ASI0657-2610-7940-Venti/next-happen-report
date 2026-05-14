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

# Capítulo V: Product Implementation, Validation & Deployment

## 5.1 Testing Suites & General Patterns

### 5.1.1 Backend Application Core Testing Suite

En esta sección se presentan las funciones de testing planteadas en Xunit y moq para el servicio 
web del proyecto.

Pruebas unitarias: 
Se encuentran primerio las pruebas unitarias de nuestros microservicios. 

Engagement service

<img src="assets/img/cap5/unittest/unittest1.png" width="800"/>

Event service

<img src="assets/img/cap5/unittest/unittest2.png" width="800"/>

Iam service

<img src="assets/img/cap5/unittest/unittest3.png" width="800"/>

Notification service
<img src="assets/img/cap5/unittest/unittest4.png" width="800"/>

Ticket service

<img src="assets/img/cap5/unittest/unittest5.png" width="800"/>

Pruebas integrales: 
Se encuentran las pruebas integrales de nuestros microservicios.

Engagement service
<img src="assets/img/cap5/integraltest/intest1.png" width="800"/>
Event service
<img src="assets/img/cap5/integraltest/intest2.png" width="800"/>
Iam service
<img src="assets/img/cap5/integraltest/intest3.png" width="800"/>
Notification service
<img src="assets/img/cap5/integraltest/intest4.png" width="800"/>
Ticket service
<img src="assets/img/cap5/integraltest/intest5.png" width="800"/>

### 5.1.2 Pattern Based Backend Application(s)

En esta sección se detallan los patrones de software y de arquitectura utilizados en los microservicios de la plataforma NextHappen, desarrollados con el framework .NET 9 (ASP.NET Core):

#### Arquitectura en Capas (N-Tier Architecture):

La aplicación se divide en capas con responsabilidades bien definidas: API/Controllers (presentación), Application (lógica de negocio y casos de uso), Domain (entidades y reglas core) e Infrastructure (acceso a datos y persistencia).
Esto garantiza una separación de preocupaciones (Separation of Concerns), permitiendo que cada microservicio sea mantenible y escalable de forma independiente.

#### Patrón de Repositorio (Repository Pattern):

Abstrae la lógica de acceso a datos mediante interfaces, permitiendo que la capa de aplicación interactúe con la persistencia sin conocer los detalles de Entity Framework Core.
En NextHappen, cada microservicio define interfaces en la capa de dominio que son implementadas en la capa de infraestructura, facilitando también la realización de pruebas unitarias mediante mocks.

#### Modelo-Controlador para Web API:

Aunque el proyecto es una API REST, sigue la estructura fundamental de separación de componentes:
Model: Representado por las entidades de dominio que contienen el estado y las reglas del negocio.
DTOs (Data Transfer Objects): Objetos que definen qué información se expone o recibe a través de la API.
Controller: Gestiona las peticiones HTTP, coordina la ejecución de servicios y devuelve respuestas estandarizadas (JSON).
Los controladores en ASP.NET Core actúan como el orquestador de entrada para las peticiones de los clientes.

#### Inyección de Dependencias (Dependency Injection):

Se utiliza el contenedor de dependencias nativo de .NET para gestionar el ciclo de vida de los objetos.
Permite un manejo eficiente de las instancias:
Scoped: Para repositorios y servicios que duran lo que dura una petición HTTP.
Singleton: Para componentes compartidos en toda la ejecución, como generadores de tokens JWT.
Esto reduce el acoplamiento y mejora significativamente la testabilidad del sistema.

#### Assembler / Mapping Pattern:

Se implementa la lógica de conversión entre Entidades de Dominio y DTOs.
Este patrón evita exponer las entidades de la base de datos directamente al cliente, protegiendo la integridad del modelo y permitiendo que la API evolucione independientemente del esquema de datos.

#### Microservices Architectural Style:

El sistema está descompuesto en servicios autónomos (Event, IAM, Ticket, Engagement, Notification) que se comunican de forma síncrona (HTTP/REST) y asíncrona (RabbitMQ/MassTransit).
Cada microservicio posee su propia base de datos (Database-per-Service), cumpliendo con el patrón de aislamiento de datos.

### 5.1.3 Pattern Based Custom Software Library


En esta sección se detallan las dependencias clave del proyecto y su relación con los patrones de diseño de software en el ecosistema .NET:

#### Entity Framework Core (EF Core):

Es el ORM (Object-Relational Mapper) principal que simplifica la persistencia de datos mediante la abstracción del acceso a la base de datos SQL (MySQL en este caso).
Relación con patrones: Implementa el Patrón de Repositorio y Unit of Work, permitiendo que los microservicios manejen transacciones de forma segura y mantengan la Arquitectura en Capas al separar el esquema de datos de la lógica de negocio.

#### MassTransit (RabbitMQ):

Librería de mensajería distribuida que facilita la comunicación asíncrona entre microservicios (por ejemplo, entre Engagement y Notification).
Relación con patrones: Implementa los patrones Publisher/Subscriber (Pub-Sub) y Message Broker, permitiendo un desacoplamiento total entre los servicios y mejorando la resiliencia del sistema.
#### Microsoft.AspNetCore.Authentication.JwtBearer:

Biblioteca estándar para la implementación de seguridad basada en tokens JWT en aplicaciones ASP.NET Core.
Relación con patrones: Se integra con el middleware de la aplicación siguiendo el patrón Interceptor (Middleware), validando las credenciales en cada petición antes de llegar al controlador. La generación del token se centraliza en una clase JwtTokenGenerator registrada como Singleton.
#### BCrypt.Net-Next:

Librería especializada en el hashing seguro de contraseñas utilizando el algoritmo BCrypt.
Relación con patrones: Se utiliza bajo el Patrón de Estrategia (Strategy) a través de una interfaz IPasswordHasher, lo que permite que el microservicio de IAM delegue la responsabilidad de seguridad a un componente especializado y fácilmente intercambiable.
#### xUnit & Moq:

Frameworks utilizados para la creación y ejecución de pruebas automatizadas y la generación de objetos simulados.
Relación con patrones: Facilita la aplicación del patrón Triple A (Arrange, Act, Assert) y el uso de Mocking, permitiendo validar la integridad de la lógica de negocio en aislamiento sin depender de recursos externos como la base de datos real.

### 5.1.4 Framework Pattern Driven Refactoring Report

En esta sección se detallan las mejoras aplicadas a la arquitectura de los microservicios de NextHappen a partir de la refactorización guiada por patrones de diseño y las mejores prácticas de ASP.NET Core:

#### Arquitectura en Capas (N-Tier):

Se reorganizó el código en capas de API, Application, Domain e Infrastructure. Esto garantiza una separación clara de responsabilidades, facilitando el mantenimiento y permitiendo que cada capa evolucione de forma independiente.

#### Abstracción mediante Repositorios:

Se implementaron interfaces para todos los repositorios, desacoplando la lógica de negocio de la persistencia física en MySQL. Esto permite cambiar el motor de base de datos o realizar pruebas unitarias sin afectar el núcleo del sistema.

#### Implementación de DTOs (Data Transfer Objects):

Se introdujeron DTOs para todas las entradas y salidas de la API. Esto evita la exposición de las entidades de dominio y protege la integridad de los datos, controlando exactamente qué campos se envían al cliente.

#### Gestión Centralizada de Dependencias (DI):

Se refactorizó el registro de servicios para utilizar el contenedor de Inyección de Dependencias nativo de .NET. Se definieron ciclos de vida adecuados (Scoped para servicios de aplicación y Singleton para utilitarios de seguridad), optimizando el uso de memoria y recursos.

#### Simplificación de Controladores (Clean Controllers):

Siguiendo el patrón de controladores delgados (Thin Controllers), se delegó toda la lógica compleja de negocio a la capa de Servicios o Casos de Uso. Los controladores ahora solo se encargan de la validación básica de la petición y de retornar el código de estado HTTP adecuado.

#### Lógica de Mapeo (Assembler):

Se incorporaron métodos de mapeo para la transformación entre entidades y DTOs. Esto redujo la duplicación de código en los controladores y mejoró la legibilidad al centralizar la lógica de transformación.
Infraestructura de Pruebas (Test-Driven Refactoring):

Se añadió una suite de Pruebas Unitarias e Integrales utilizando xUnit y Moq. La refactorización incluyó la adopción del patrón Triple A (Arrange, Act, Assert), asegurando que cada funcionalidad nueva o existente esté respaldada por pruebas automatizadas confiables.

## 5.2 Software Configuration Management

La gestión de configuración de software en NextHappen tiene como objetivo garantizar el control, seguimiento y estabilidad del desarrollo de la aplicación web y sus microservicios. Para ello, el equipo definió herramientas, estándares y procesos que permiten mantener consistencia en el código, facilitar el trabajo colaborativo y asegurar despliegues confiables utilizando contenedores Docker.

La estrategia de Software Configuration Management (SCM) aplicada contempla la configuración del entorno de desarrollo, la administración del código fuente mediante Git y GitHub, el uso de convenciones de codificación para mantener calidad y mantenibilidad, y la configuración automatizada del despliegue de los servicios.

---

### 5.2.1 Software Development Environment Configuration

Para el desarrollo del proyecto NextHappen se configuró un entorno moderno orientado al desarrollo web basado en microservicios utilizando tecnologías .NET y Vue.js.

### Herramientas utilizadas

| Herramienta | Propósito |
|---|---|
| Visual Studio 2022 | Desarrollo Backend en C# |
| Visual Studio Code | Desarrollo Frontend |
| Vue.js | Desarrollo de interfaz web |
| .NET 8 | Desarrollo de APIs REST |
| MySQL | Persistencia de datos |
| Docker | Contenerización de servicios |
| Git | Control de versiones |
| GitHub | Repositorio remoto |
| Trello | Gestión ágil del proyecto |
| Postman | Pruebas de APIs |

### Configuración del entorno

El entorno fue configurado siguiendo los siguientes pasos:

1. Instalación de Visual Studio 2022 con soporte para ASP.NET Core.
2. Instalación de Node.js y Vue CLI.
3. Configuración de Docker Desktop.
4. Instalación y configuración de MySQL Server.
5. Clonación del repositorio desde GitHub.
6. Restauración de dependencias mediante NuGet y npm.
7. Configuración de variables de entorno mediante archivos `.env`.
8. Ejecución local de frontend y backend.

### Configuración del backend

El backend fue desarrollado utilizando ASP.NET Core Web API en .NET 8.

### Dependencias principales

```bash
Microsoft.EntityFrameworkCore
Pomelo.EntityFrameworkCore.MySql
Swashbuckle.AspNetCore
AutoMapper
```

### Configuración del frontend

El frontend utiliza Vue.js como framework principal.

### Dependencias principales

```bash
vue
vue-router
pinia
axios
```

### Beneficios del entorno configurado

- Compatibilidad entre todos los integrantes.
- Desarrollo modular y escalable.
- Facilidad de pruebas locales.
- Integración rápida mediante Docker.
- Separación clara entre frontend y backend.

---

## 5.2.2 Source Code Management

La gestión del código fuente se realizó utilizando Git como sistema de control de versiones distribuido y GitHub como plataforma colaborativa.

### Estrategia de ramas

El equipo utilizó una estrategia basada en Git Flow simplificado.

| Rama | Propósito |
|---|---|
| `main` | Versión estable del proyecto |
| `develop` | Integración de funcionalidades |
| `feature/*` | Nuevas funcionalidades |
| `hotfix/*` | Correcciones urgentes |

### Flujo de trabajo

1. Cada integrante creaba una rama `feature`.
2. Se desarrollaban funcionalidades localmente.
3. Los cambios eran registrados mediante commits descriptivos.
4. Se realizaban Pull Requests hacia `develop`.
5. El código era revisado antes de aprobarse.
6. Las versiones estables eran integradas en `main`.

### Convenciones de commits

El equipo utilizó commits descriptivos para mantener trazabilidad.

```bash
feat: agregar gestión de eventos
fix: corregir autenticación JWT
docs: actualizar README
refactor: optimizar servicio de usuarios
```

### Gestión ágil del proyecto

El seguimiento de tareas y avances fue realizado mediante Trello.

### Uso de Trello

- Gestión del Product Backlog.
- Organización de tareas por Sprint.
- Seguimiento del progreso del equipo.
- Control visual de actividades pendientes y completadas.

## Beneficios obtenidos

- Mejor organización del trabajo colaborativo.
- Historial claro de cambios.
- Reducción de conflictos entre desarrolladores.
- Mayor control sobre versiones del software.

---

## 5.2.3 Source Code Style Guide & Conventions

Con el objetivo de mantener calidad, legibilidad y mantenibilidad, el equipo estableció convenciones de codificación para frontend y backend.

## Convenciones generales

- Uso de nombres descriptivos.
- Código modular y reutilizable.
- Separación de responsabilidades.
- Evitar duplicidad de código.
- Documentación básica en métodos importantes.

---

## Backend (.NET / C#)

### Convenciones aplicadas

| Elemento | Convención |
|---|---|
| Clases | PascalCase |
| Variables | camelCase |
| Interfaces | Prefijo `I` |
| Métodos | PascalCase |
| Constantes | UPPER_CASE |

### Ejemplo

```csharp
public class EventService : IEventService
{
    public async Task<EventDto> GetEventById(int id)
    {
        return await _repository.FindById(id);
    }
}
```

---

## Frontend (Vue.js)

### Convenciones aplicadas

| Elemento | Convención |
|---|---|
| Componentes | PascalCase |
| Variables | camelCase |
| Archivos Vue | PascalCase |
| Rutas | kebab-case |

### Ejemplo

```javascript
const fetchEvents = async () => {
  const response = await api.get('/events');
  return response.data;
};
```

---

## Herramientas de calidad

| Herramienta | Función |
|---|---|
| ESLint | Validación de código JavaScript |
| Prettier | Formateo automático |
| StyleCop | Convenciones C# |
| Swagger | Documentación de APIs |

## Beneficios

- Código uniforme y fácil de entender.
- Mayor mantenibilidad.
- Facilidad para realizar revisiones de código.
- Reducción de errores de desarrollo.

---

## 5.2.4 Software Deployment Configuration

La configuración de despliegue de NextHappen fue diseñada para facilitar la ejecución y publicación de los microservicios utilizando Docker.

### Arquitectura de despliegue

La solución está compuesta por:

| Componente | Tecnología |
|---|---|
| Frontend | Vue.js |
| Backend | ASP.NET Core Web API |
| Base de datos | MySQL |
| Contenedores | Docker |

---

### Dockerización

Cada servicio cuenta con su propio archivo `Dockerfile`.

### Dockerfile Backend

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY publish .
EXPOSE 8080
ENTRYPOINT ["dotnet", "NextHappen.Api.dll"]
```

### Dockerfile Frontend

```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build
EXPOSE 5173
CMD ["npm", "run", "dev"]
```

---

## Docker Compose

Se utilizó Docker Compose para ejecutar todos los servicios localmente.

### Servicios incluidos

- Frontend Vue.js
- Backend .NET API
- Base de datos MySQL

### Ejemplo básico

```yaml
version: '3.9'

services:
  backend:
    build: ./backend
    ports:
      - "8080:8080"

  frontend:
    build: ./frontend
    ports:
      - "5173:5173"

  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
```

---

### Variables de entorno

Las configuraciones sensibles fueron gestionadas mediante variables de entorno.

```env
DB_HOST=mysql
DB_PORT=3306
DB_NAME=nexthappen
DB_USER=root
DB_PASSWORD=root
JWT_SECRET=secret_key
```

---

### Beneficios del despliegue configurado

- Facilidad para levantar entornos completos.
- Compatibilidad entre máquinas del equipo.
- Despliegues rápidos y reproducibles.
- Separación de servicios.
- Escalabilidad futura basada en contenedores Docker.
## 5.3 Microservices Implementation

La implementación de microservicios en NextHappen fue realizada siguiendo una arquitectura desacoplada basada en servicios independientes desarrollados con ASP.NET Core Web API. Cada microservicio fue diseñado para cumplir responsabilidades específicas del negocio, permitiendo escalabilidad, mantenibilidad y despliegue independiente.

La solución fue desarrollada utilizando .NET 8, Vue.js, MySQL y Docker, aplicando principios de arquitectura limpia y comunicación mediante APIs REST.

Durante el Sprint 1 se desarrollaron las funcionalidades iniciales relacionadas con autenticación, gestión de eventos y configuración base del sistema.

---

## 5.3.1 Sprint 1

Durante el Sprint 1 el equipo se enfocó en la configuración inicial del proyecto, implementación de la arquitectura base y desarrollo de los primeros microservicios necesarios para la plataforma.

La duración del Sprint fue de 2 semanas bajo metodología Scrum.

---

## 5.3.1.1 Sprint Backlog 1

El Sprint Backlog estuvo compuesto por las siguientes User Stories priorizadas:

| User Story ID | Título | Descripción | Responsable | Estado |
|---|---|---|---|---|
| US01 | Configuración inicial del proyecto | Como desarrollador, deseo configurar el entorno base para iniciar el desarrollo del sistema | Equipo | Completado |
| US02 | Gestión de autenticación | Como usuario, deseo iniciar sesión para acceder a funcionalidades personalizadas | Backend | Completado |
| US03 | Registro de usuarios | Como visitante, deseo registrarme para utilizar la plataforma | Backend | Completado |
| US04 | Visualización de eventos | Como usuario, deseo visualizar eventos disponibles | Frontend | Completado |
| US05 | Configuración Docker | Como desarrollador, deseo contenerizar los servicios para facilitar despliegues | DevOps | Completado |

---

## 5.3.1.2 Development Evidence for Sprint Review

Durante el Sprint 1 se desarrollaron los siguientes componentes:

### Backend

- Creación de solución ASP.NET Core.
- Implementación de arquitectura por capas.
- Configuración de Entity Framework Core.
- Implementación de endpoints REST.
- Configuración de autenticación JWT.
- Conexión con base de datos MySQL.

### Frontend

- Inicialización del proyecto Vue.js.
- Configuración de Vue Router.
- Creación de vistas iniciales.
- Integración con APIs REST.
- Implementación de componentes reutilizables.

### Microservicios implementados

| Microservicio | Responsabilidad |
|---|---|
| Auth Service | Gestión de autenticación y JWT |
| User Service | Gestión de usuarios |
| Event Service | Gestión y consulta de eventos |

---

## 5.3.1.3 Testing Suite Evidence for Sprint Review

Se realizaron pruebas funcionales y de integración para validar el correcto funcionamiento de los servicios implementados.

### Herramientas utilizadas

| Herramienta | Uso |
|---|---|
| Postman | Pruebas de APIs |
| Swagger | Validación de endpoints |
| Docker Logs | Verificación de ejecución |
| MySQL Workbench | Validación de persistencia |

### Pruebas realizadas

#### Auth Service

- Registro de usuarios.
- Inicio de sesión.
- Generación de JWT.
- Validación de credenciales inválidas.

#### Event Service

- Consulta de eventos.
- Validación de respuestas HTTP.
- Persistencia de eventos en base de datos.

### Resultados

Las pruebas realizadas permitieron verificar:

- Correcta comunicación entre frontend y backend.
- Persistencia adecuada en MySQL.
- Funcionamiento de autenticación JWT.
- Respuestas REST exitosas.

---

## 5.3.1.4 Execution Evidence for Sprint Review

La ejecución del sistema fue validada mediante despliegue local utilizando Docker Compose.

### Evidencias de ejecución

- Contenedores ejecutándose correctamente.
- Frontend accesible desde navegador.
- APIs respondiendo mediante Swagger.
- Conexión exitosa con MySQL.

### Servicios desplegados

| Servicio | Puerto |
|---|---|
| Frontend Vue.js | 5173 |
| Auth Service | 8081 |
| User Service | 8082 |
| Event Service | 8083 |
| MySQL | 3306 |

### Validaciones realizadas

- Inicio de sesión exitoso.
- Registro de nuevos usuarios.
- Consulta de eventos desde frontend.
- Persistencia de información en base de datos.

---

## 5.3.1.5 Microservices Documentation Evidence for Sprint Review

La documentación técnica de los microservicios fue realizada utilizando Swagger/OpenAPI.

### Contenido documentado

- Endpoints disponibles.
- Métodos HTTP.
- Parámetros requeridos.
- Respuestas esperadas.
- Códigos de estado HTTP.

### Microservicios documentados

| Microservicio | Documentación |
|---|---|
| Auth Service | Swagger |
| User Service | Swagger |
| Event Service | Swagger |

### Beneficios

- Facilita integración frontend-backend.
- Simplifica pruebas de APIs.
- Mejora mantenibilidad del sistema.
- Permite escalabilidad futura.

---

## 5.3.1.6 Software Deployment Evidence for Sprint Review

El despliegue inicial fue realizado utilizando Docker y Docker Compose.

### Componentes desplegados

- Frontend Vue.js.
- APIs ASP.NET Core.
- Base de datos MySQL.

### Evidencias del despliegue

- Construcción correcta de imágenes Docker.
- Ejecución de contenedores.
- Comunicación entre servicios.
- Persistencia de datos funcional.

### Docker Compose

```yaml
version: '3.9'

services:
  auth-service:
    build: ./AuthService
    ports:
      - "8081:8080"

  user-service:
    build: ./UserService
    ports:
      - "8082:8080"

  event-service:
    build: ./EventService
    ports:
      - "8083:8080"

  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
```

---

## 5.3.1.7 Team Collaboration Insights during Sprint

Durante el Sprint 1 el equipo trabajó bajo metodología Scrum utilizando reuniones periódicas y herramientas colaborativas.

### Herramientas utilizadas

| Herramienta | Propósito |
|---|---|
| Trello | Gestión de tareas |
| GitHub | Control de versiones |
| Discord / Meet | Comunicación |
| Docker | Integración de entornos |

### Dinámica de trabajo

- Reuniones de planificación al inicio del Sprint.
- Daily meetings para seguimiento.
- Revisión de Pull Requests.
- Trabajo paralelo entre frontend y backend.

### Aprendizajes obtenidos

- Mejor organización mediante Trello.
- Importancia de ramas feature en Git.
- Coordinación efectiva entre servicios.
- Beneficios de Docker para integración.

---

## 5.3.1.8 Kanban Board --> TP1

El tablero Kanban fue gestionado mediante Trello para visualizar el avance de las tareas del Sprint 1.

### Columnas utilizadas

- Backlog
- To Do
- In Progress
- Review
- Done

### Funcionalidades del tablero

- Asignación de tareas por integrante.
- Seguimiento del estado de User Stories.
- Priorización de actividades.
- Control visual del avance del Sprint.

### Resultados obtenidos

- Mejor organización del equipo.
- Mayor visibilidad del progreso.
- Reducción de tareas bloqueadas.
- Seguimiento eficiente del Sprint.
