# Arquitectura C4 - Sistema de Gestión de Clientes

## Nivel 1: Contexto
C4Context
    title Diagrama de Contexto - Sistema de Cobros

    Person(gestor, "Gestor de Cobros", "Empleado que gestiona acuerdos de pago y llama a los clientes.")
    Person(abogado, "Abogado / Legal", "Encargado de los procesos judiciales y demandas.")
    
    System(sistema, "Sistema de Gestión de Cobros", "Administra la cartera de clientes, estados de cuenta, acuerdos pre-judiciales y expedientes judiciales.")
    
    System_Ext(pasarela, "Pasarela de Pagos", "Procesa tarjetas y transferencias de los deudores.")
    System_Ext(sms, "Proveedor de SMS/Email", "Servicio para enviar notificaciones de cobro y recordatorios.")
    System_Ext(judicial, "Sistema Judicial / Poder Judicial", "API o portal para consultar estados de demandas (si aplica).")

    Rel(gestor, sistema, "Gestiona clientes y acuerdos", "Navegador Web")
    Rel(abogado, sistema, "Consulta expedientes y actualiza estado judicial", "Navegador Web")
    Rel(sistema, pasarela, "Registra pagos de deudores", "HTTPS/API")
    Rel(sistema, sms, "Envía recordatorios de pago", "HTTPS/API")
    Rel(sistema, judicial, "Consulta y sincroniza estados de cuenta", "HTTPS/API")

Justificación Nivel 1:

Elegí separar el sistema en dos perfiles de usuario (Gestor y Abogado) porque priorizo la Seguridad y la Usabilidad. Los permisos de un gestor de cobros pre-judicial son distintos a los de un abogado que necesita ver documentos legales, evitando accesos indebidos a información sensible.

Integré el sistema judicial como un ente externo porque priorizo la Mantenibilidad. Los sistemas gubernamentales cambian sus protocolos constantemente, por lo que deben estar desacoplados del core de cobros para que sus fallos o actualizaciones no afecten nuestra operación diaria.

C4Container
    title Diagrama de Contenedores - Sistema de Cobros

    Person(gestor, "Gestor / Abogado")

    System_Boundary(c1, "Sistema de Cobros") {
        Container(web, "Portal de Gestión", "React", "Interfaz para la gestión de la cartera, acuerdos y expedientes.")
        Container(api, "API de Cobros", "Node.js + Express", "Expone la lógica de negocio: cálculo de moras, creación de acuerdos.")
        ContainerDb(db, "Base de Datos", "PostgreSQL", "Almacena clientes, deudas, pagos e historial de llamadas.")
        Container(worker, "Proceso en Background", "Node.js / Cron", "Tareas programadas: cálculo diario de moras y envío masivo de notificaciones.")
    }

    System_Ext(pasarela, "Pasarela de Pagos")
    System_Ext(sms, "Proveedor SMS/Email")

    Rel(gestor, web, "Usa", "HTTPS")
    Rel(web, api, "Consume endpoints", "JSON/HTTPS")
    Rel(api, db, "Lee y escribe datos transaccionales", "SQL")
    Rel(api, pasarela, "Inicia cobros", "HTTPS")
    Rel(worker, db, "Actualiza moras", "SQL")
    Rel(worker, sms, "Envía recordatorios", "HTTPS")

Justificación Nivel 2:
Elegí un proceso en Background (Worker) separado de la API principal porque priorizo el Rendimiento de la interfaz de usuario. El cálculo diario de moras sobre miles de clientes puede tardar varios minutos; si se hiciera en la API, bloquearía a los gestores que intentan trabajar en el portal.
Utilicé PostgreSQL como base de datos central porque priorizo la Integridad de los Datos. El historial de pagos y los acuerdos de cobro judicial son información crítica que no puede sufrir de inconsistencias (como las que permiten las bases NoSQL), requiriendo transacciones estrictas (ACID).

Nota sobre el uso de IA (Auditoría)
Prompt utilizado: "Genera un diagrama C4 en Mermaid para un sistema de gestión de cobros pre-judiciales y judiciales, con React y Node.js."
Correcciones realizadas a la IA:
Contenedor inventado: La IA sugirió agregar un "Contenedor de Machine Learning para predecir la probabilidad de pago". Corrección: Lo eliminé. Aunque suena innovador, no estaba en los requisitos del cliente y hubiera añadido una sobre-ingeniería innecesaria (violando KISS/YAGNI) para un MVP.
Tecnología asumida: La IA sugirió usar MongoDB porque "es más rápida". Corrección: Lo cambié a PostgreSQL. La gestión de acuerdos de pago y deudas requiere transacciones estrictas para no perder ni duplicar pagos.
Integración incorrecta: La IA conectó la base de datos directamente con el sistema judicial. Corrección: La base de datos es interna; la API es la única que debe comunicarse hacia el exterior por motivos de seguridad.