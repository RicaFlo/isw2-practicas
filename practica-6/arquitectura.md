# Arquitectura C4 - Sistema de Gestión de Clientes (Agencia de Cobros)

## Nivel 1: Contexto
Este diagrama muestra cómo interactúa el sistema de la agencia con los usuarios internos y los actores o sistemas externos.

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
