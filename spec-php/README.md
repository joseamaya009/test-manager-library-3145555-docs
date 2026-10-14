# Manager Library — especificación PHP/Laravel

**Estado: borrador de traducción técnica para revisión.** Las historias, reglas de negocio, modelo de datos, API y ADR se comparten con las referencias Python y .NET. Esta carpeta adapta su organización técnica al stack confirmado del reto: PHP con Laravel. Las decisiones marcadas **pendiente** no se deben convertir en requisitos por suposición.

## Lectura recomendada

1. [`constitution.md`](constitution.md): límites de arquitectura y principios verificables.
2. [`spec.md`](spec.md): alcance, actores, historias y criterios de aceptación.
3. [`architecture.md`](architecture.md) y [`arquitectura-panoramica.md`](arquitectura-panoramica.md): capas y recorrido principal.
4. [`data-model.md`](data-model.md) y [`api-contract.md`](api-contract.md): fuentes únicas del modelo y del contrato HTTP.
5. [`plan.md`](plan.md): traducción técnica y decisiones pendientes.
6. [`tasks.md`](tasks.md): orden de trabajo y evidencia.
7. [`adr/`](adr/): decisiones aceptadas compartidas por las versiones de referencia.

## Qué queda definido

- PHP con Laravel para el backend, React para el frontend y PostgreSQL 16 como motor de referencia.
- Los requisitos funcionales y las rutas/cuerpos HTTP de [`spec.md`](spec.md) y [`api-contract.md`](api-contract.md).
- La arquitectura hexagonal y la propiedad del esquema en migraciones de `api`, según [`constitution.md`](constitution.md) y [ADR-001](adr/adr-001-propiedad-del-esquema.md).
- El sembrador de `tool` opera por la API; no ejecuta SQL directo.

## Qué no se decide aquí

Las referencias discrepan en el algoritmo de hash (Argon2id frente a BCrypt), y no fijan para Laravel la versión de PHP/Laravel, el ORM de consultas, la librería JWT, las herramientas de pruebas ni la organización exacta del contenedor de dependencias. La estructura de migraciones Liquibase queda en `api/database`, siguiendo el ejemplo indicado y la propiedad del esquema asignada a `api`. [`plan.md`](plan.md) registra las decisiones pendientes sin presentarlas como hechos.

También se conservan asuntos abiertos heredados de las referencias: `DocumentNumber` se menciona como objeto de valor en los planes pero no está especificado con ese detalle en el modelo; y la secuencia entre arranque, Liquibase, base vacía y creación del administrador debe quedar reproducible. El testigo `book.row_version` ya está incluido en el modelo y la migración; falta conectarlo al adaptador de persistencia.

## Fuentes

- El [README del reto](../README.md) confirma el stack PHP/Laravel y señala que el negocio y los contratos de las referencias se respetan.
- [`spec-python/`](../spec-python/) y [`spec-.net/`](../spec-.net/) contienen dos traducciones técnicas del mismo producto. Los archivos funcionales, el modelo y los cuatro ADR son equivalentes; las herramientas y decisiones de implementación varían.
- Cuando una decisión de tecnología no está respaldada por estas fuentes, se identifica como pendiente. Esta carpeta no introduce alcance funcional adicional.
