# Arquitectura C4 - Sistema de Gestión de Clientes (Agencia de Cobros)
## Nivel 1: Contexto
Este diagrama muestra cómo interactúa el sistema de la agencia con los usuarios internos y los actores o sistemas externos.
Justificación Nivel 1:
Elegí separar el sistema en dos perfiles (Gestor y Abogado) porque priorizo la Seguridad. Los permisos de un gestor pre-judicial son distintos a los de un abogado, evitando accesos indebidos.
Integré el sistema judicial como un ente externo porque priorizo la Mantenibilidad. Los sistemas gubernamentales cambian, por lo que deben estar desacoplados del core de cobros.

```mermaid
C4Context
    title Diagrama de Contexto - Sistema de Cobros
    Person(gestor, "Gestor de Cobros", "Empleado que gestiona acuerdos.")
    Person(abogado, "Abogado / Legal", "Encargado de procesos judiciales.")
    System(sistema, "Sistema de Gestión de Cobros", "Administra la cartera.")
    System_Ext(pasarela, "Pasarela de Pagos", "Procesa pagos.")
    Rel(gestor, sistema, "Gestiona clientes", "Navegador")
    Rel(abogado, sistema, "Consulta expedientes", "Navegador")
    Rel(sistema, pasarela, "Registra pagos", "API")
```

## Nivel 2: Contenedores
Este diagrama muestra las piezas de software desplegables y las tecnologías.
Elegí un proceso en Background (Worker) separado porque priorizo el Rendimiento. El cálculo de moras puede tardar y bloquearía la interfaz si se hiciera en la API.
Utilicé PostgreSQL porque priorizo la Integridad de los Datos. El historial de pagos requiere transacciones estrictas (ACID).

```mermaid
C4Container
    title Diagrama de Contenedores - Sistema de Cobros
    Person(gestor, "Gestor / Abogado")
    System_Boundary(c1, "Sistema de Cobros") {
        Container(web, "Portal de Gestión", "React", "Interfaz de usuario.")
        Container(api, "API de Cobros", "Node.js + Express", "Lógica de negocio.")
        ContainerDb(db, "Base de Datos", "PostgreSQL", "Almacena datos.")
    }
    Rel(gestor, web, "Usa", "HTTPS")
    Rel(web, api, "Consume", "JSON")
    Rel(api, db, "Lee/Escribe", "SQL")
```
