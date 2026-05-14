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

### 5.2.1 Software Development Environment Configuration

### 5.2.2 Source Code Management

### 5.2.3 Source Code Style Guide & Conventions

### 5.2.4 Software Deployment Configuration

## 5.3 Microservices Implementation

### 5.3.1 Sprint 1

#### 5.3.1.1 Sprint Backlog 1

#### 5.3.1.2 Development Evidence for Sprint Review

#### 5.3.1.3 Testing Suite Evidence for Sprint Review

#### 5.3.1.4 Execution Evidence for Sprint Review

#### 5.3.1.5 Microservices Documentation Evidence for Sprint Review

#### 5.3.1.6 Software Deployment Evidence for Sprint Review

#### 5.3.1.7 Team Collaboration Insights during Sprint

#### 5.3.1.8 Kanban Board
