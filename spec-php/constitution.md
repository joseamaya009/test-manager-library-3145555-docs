# Constitución — Manager Library (PHP/Laravel)

Principios del proyecto traducidos de las referencias Python y .NET al stack del reto. Las reglas de dependencia y de datos son obligatorias; las rutas de carpetas y herramientas de verificación son recomendaciones hasta que se validen en [`plan.md`](plan.md).

---

## Artículo I — El dominio no depende del framework

El dominio contiene las reglas y los objetos de negocio. Puede usar el lenguaje y sus tipos estándar, pero no depende de Laravel, HTTP, persistencia, ORM ni servicios de infraestructura. Las reglas deben poder probarse sin servidor ni base de datos.

**Verificación:** una comprobación automatizada de arquitectura impide dependencias del dominio hacia aplicación, adaptadores o infraestructura. La herramienta concreta queda pendiente.

## Artículo II — La aplicación declara los puertos; los adaptadores los implementan

Los casos de uso dependen del dominio y declaran las interfaces que necesitan para repositorios, reloj, hash de claves, emisión/verificación de tokens y almacenamiento de portadas. Las firmas de esos puertos no exponen clases de Laravel, HTTP ni ORM.

**Verificación:** la comprobación de arquitectura impide que la aplicación dependa de adaptadores. Los casos de uso se prueban con implementaciones falsas de los puertos.

## Artículo III — Un único punto de composición

Los puertos se conectan con sus adaptadores en un único lugar de composición. El arranque de Laravel es responsable de construir la aplicación, pero la ubicación y el mecanismo concretos de registro se definen en el plan técnico.

**Verificación:** las instancias de infraestructura no se construyen desde el dominio ni desde los casos de uso; una comprobación automatizada vigila el límite.

## Artículo IV — SOLID se aplica en el diseño

| Principio | Aplicación concreta | Verificación |
|---|---|---|
| **SRP** | Un caso de uso resuelve una operación, por ejemplo prestar o devolver | Cada caso de uso tiene una responsabilidad identificable |
| **OCP** | Sustituir almacenamiento de portadas no modifica dominio ni casos de uso | El cambio queda en el adaptador y la composición |
| **LSP** | Los adaptadores falsos sustituyen a los reales con el mismo contrato | Las pruebas del caso de uso pasan con fakes y con adaptadores de integración |
| **ISP** | Puertos pequeños: `Clock`, `PasswordHasher`, `TokenIssuer`, `CoverStorage` | Cada implementación usa el contrato completo |
| **DIP** | La capa que necesita una capacidad define su puerto | Los puertos pertenecen a la aplicación |

## Artículo V — El esquema es propiedad de las migraciones de `api`

`infra` levanta un motor vacío. Todo el DDL —tablas, índices, restricciones y semilla de categorías— vive en las migraciones mantenidas por `api`. Laravel se usa como framework del backend; el mecanismo concreto de migraciones debe seguir esta propiedad. Ver [ADR-001](adr/adr-001-propiedad-del-esquema.md).

**Verificación:** `infra` no crea tablas y las consultas de [`data-model.md` §10](data-model.md) coinciden con las migraciones.

## Artículo VI — La base impone las invariantes que puede expresar

Las reglas expresables con `CHECK`, `UNIQUE` o claves foráneas se declaran en la base. Si una regla todavía no está impuesta por el motor, [`data-model.md`](data-model.md) la marca **pendiente** o **solo dominio**; no se presenta como protegida por la base.

**Verificación:** las marcas y consultas de [`data-model.md` §4 y §10](data-model.md) coinciden con las restricciones desplegadas.

## Artículo VII — Lo derivado se calcula

El estado de un préstamo (`open`, `overdue`, `closed`) y los días de retraso se calculan; no se almacenan en columnas.

**Verificación:** [`data-model.md`](data-model.md) no define columnas `status` ni `days_late`.

## Artículo VIII — Los valores congelados no siguen al catálogo

La línea del préstamo conserva el título, ISBN y nombre de categoría que tenía el libro cuando se prestó. Los reportes históricos usan esos valores congelados, no los datos actuales del catálogo.

**Verificación:** una prueba renombra un libro después del préstamo y confirma que el reporte del periodo conserva los valores históricos, como indica [ADR-004](adr/adr-004-reporte-agregado-y-congelado.md).

## Artículo IX — Ningún secreto entra al repositorio ni a un log

Contraseñas, hashes precalculados y secretos de firma no se versionan ni se registran en logs. Los secretos se entregan mediante configuración de entorno; se versiona un ejemplo sin valores secretos, no el `.env` real.

**Verificación:** revisión automatizada de secretos y comprobación de que las respuestas HTTP no incluyen `password` ni `password_hash`.

## Artículo X — Gana el motor

Si la base de datos y [`data-model.md`](data-model.md) discrepan, el documento está desactualizado y se corrige para reflejar las restricciones efectivas.

## Artículo XI — Idioma

Código, rutas, nombres de tablas y columnas en inglés. Documentación y mensajes dirigidos al usuario en español.

## Artículo XII — Nada se da por hecho sin evidencia

Una tarea de [`tasks.md`](tasks.md) está terminada cuando la evidencia indicada existe y se puede ejecutar, no cuando solo está programada.
