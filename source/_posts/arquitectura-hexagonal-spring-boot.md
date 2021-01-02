---
title: arquitectura-hexagonal-spring-boot
date: 2021-01-02 23:21:56
tags: arquitectura
category: arquitectura
---

En esta primera entrada del blog tengo la intención de tratar de plasmar un poco todo lo aprendido recientemente sobre la Arquitectura Hexagonal aplicada a proyectos de microservicios con Spring Boot.

Partimos de la base de que el lector de este artículo ya posee conocimientos en arquitectura MVC (Modelo, vista y controlador) y está algo familiarizado con el framework SprinBoot (Aunque esta arquitectura sería perfectamente aplicable a otros frameworks, lenguajes de programación).

# Toma de contacto
Vamos al lío, lo primero de todo es cómo llega esta arquitectura a mis oídos. Hace 3 años me encontraba en un proyecto utilizando arquitectura de microservicios, y llegó un buen día un arquitectura y nos explicó la idea de aislar el Dominio, de crear un servicio por caso de uso, nos habló de adaptadores, de puertos... un lío que no veíamos a donde llegaba. La cosa no llegó a nada, y seguimos haciendo nuestros microservicios siguiendo MVC como hasta ahora, y la vida siguió...

Hace unos meses me reencontré con la idea de plantear esta arquitectura a varios proyectos, y decidí investigar un poco sobre el tema. Realicé un curso, investigué un poco sobre un tema (partiendo de la Wikipedia, por supuesto, y siguiendo por fuentes mas amplias). Y bueno a día de hoy doy mis primeros pasos en esta angulosa arquitectura. 

Así que, cómo no quiero que se me olvide nada, dejo constancia en este escrito.

# Idea fundamental

La arquitectura hexagonal (O patrón de puertos&adaptadores) no es algo nuevo, ni novedoso, ni revolucionario. Lleva años ahí, y a mi entender, no es algo que vaya revolucionar el mundo del desarrollo, pero si trae algunas cosas interesantes.

Parte de ideas vistas en la Clean Architectura y complementadas por Alister Cockburn, en su web encontrareis una amplia información.

La idea fundamental de esta arquitectura es aislar la lógica de negocio , o como lo denominan el Dominio (¡Ojo! No confundir con Dominio web, o de base de datos) o lógica de negocia del resto de la infraestructura de forma que cualquier cambio en la forma en la que se llama al servicio, repositorios de datos, cambios en el framework, etc no afecto a la lógica de negocio. Y tengas por un lado limpiamente la lógica de negocio, el core, el núcleo de nuestra aplicación separado de todo lo demás.

Por otro lado se persigue conseguir una alta cambiabilidad, es decir, que si el día de mañana queremos realizar cambios en la forma en la que se nos llama, llamadas que hacemos, framework que utilizamos, repositorios de datos, etc... esto no afecte al Dominio.

// Comentario

Como se puede ver el diagrama superior, existe una separación de capas entre el dominio y los adaptares.

En la capa de infraestructura tendremos todo lo que no es logica de negocio, entradas como podrían ser los controladores con endpoints de tipo REST o SOAP; y salidas como podrían ser repositorios, llamadas a otros servicios, a otros servidores, etc.

Para comunicar esta capa de infraestructura con el dominio se utilizarán interfaces, denominadas Puertos.

# Estructura de carpetas
## Baeldung
La primera estructura de carpetas que vamos a exponer es una que he visto en una demostración de Baelding y que se puede ver en el siguiente enlace.

```
application/
    rest/           # el controller
    request/        # aquí irán todos los objetos dto para la petición
    response /      # los dto de la response
domain/
    repository/     # interfaz del repository          
    service/         
    Exception.java
    Foo.java              
infrastructure/
    configuration/  # La configuración de las distintas bases de datos (MongoDB, Cassandra...). 
                    # También configuración de los Beans (Services en este caso).
    repository/     # Implementación de la interfaz repository para los distintos repositorios, también mappers, Modelo de datos, etc.
Application.java
```
De la estructura de baeldung decir que me gusta mucho el uso que le da al paquete application como un escaparate de todo lo que ofrecemos de nuestra aplicacion a los usuarios que consumirán nuestra aplicación.

## Curso de Openwebinars
Esta estructura de carpetas la vi en un curso que recibí de Openwebinars y se puede encontrar en el siguiente enlace .

```
adapters/
    primary/
        Controller.java
    ecundary/
        RepositoryImp.java
converters/             # Clases para conversión entre Model y Dto
domain/
    Model.js: Objetos de modelo de datos utilizados por repository
ports/
    primary/
        Service.java        # servicio con su implementación
    secundary/
        Repository.java     # implementacion del repository
Request.java
Application.java
```

## Estructura de carpeta del actual proyecto
Tras visualizar varios repositorios de código, ejemplos, ver otros proyectos y conversar del tema con varios compañeros. Esta es la estructura de carpetas que más me ha convencido:
```
config\
domain\
    ports\
        primary\    # Interfaces de service
        secundary\  # Interfaces de infrascturctura outbound
    service\        # Lógica de negocio
    model\        
infrastructure\
    inbound\
        api\
            adapter\    # Controladores
            mapper\
            model\
    outbound\
        adapter\        # Implementación del puerto secundario
        repository\     # Aqui irá el Repository
            dto\
            mapper\     
            model\
```
De esta estructura de carpetas destacar la enorme existencia de mappers, tres para un flujo normal, uno para la capa inbound, otro para el domain y finalmente otro para la capa outbound.

# Interesante crítica y reflexión
Hay un artículo muy interesante de Javier Vélez Reyes llamado Ni Nueva Ni Arquitectura Ni Hexagonal cuya lectura es muy recomendable