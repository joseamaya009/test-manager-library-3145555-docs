# Plan técnico — Manager Library (PHP/Laravel)

Traducción técnica de [`spec.md`](spec.md) y [`constitution.md`](constitution.md). Los requisitos y el esquema están definidos solo en [`spec.md`](spec.md), [`api-contract.md`](api-contract.md) y [`data-model.md`](data-model.md). Este plan separa decisiones confirmadas por el reto de las propuestas o asuntos que todavía requieren validación.

## 1. Decisiones técnicas

| # | Decisión | Traducción PHP/Laravel | Estado y motivo |
|---|---|---|---|
| D-01 | Lenguaje y runtime | PHP con Laravel | Stack confirmado; versiones de PHP y Laravel pendientes |
| D-02 | HTTP | Laravel implementa el adaptador de entrada | Framework confirmado; estilo de rutas/controladores no especificado |
| D-03 | Persistencia | PostgreSQL 16; todo el DDL pertenece a migraciones de `api` | Motor y propiedad definidos por las fuentes; ORM/mecanismo concreto de migración por validar |
| D-04 | Concurrencia | Testigo de versión por libro más `CHECK` de rango, según [ADR-002](adr/adr-002-concurrencia-optimista.md) | Decisión heredada; el testigo no figura en [`data-model.md`](data-model.md), así que el modelo debe resolverse antes de implementar |
| D-05 | Fechas | Instantes UTC (`timestamptz`); la aplicación consume un puerto `Clock` | Compartido con las referencias; permite probar vencimientos sin esperar 14 días |
| D-06 | Reporte | Una consulta agregada de lectura, agrupada por valores congelados | Definido en [ADR-004](adr/adr-004-reporte-agregado-y-congelado.md) |
| D-07 | Objetos de valor | `Isbn`, `Quantity`, `DocumentNumber`, `DateRange`; sin tabla propia | Heredado del plan de referencia; `DocumentNumber` y sus validaciones requieren completar el modelo/tareas |
| D-08 | Portadas | Binario fuera de la base; PostgreSQL guarda solo `cover_key` opaca | Regla compartida; proveedor y configuración local/producción por validar |
| D-09 | Contraseñas | Adaptador detrás de `PasswordHasher` | Pendiente: la referencia Python usa Argon2id y la .NET BCrypt factor 12 |
| D-10 | Administrador inicial | El arranque crea el admin con credenciales de entorno | Requisito heredado; hash generado por el adaptador, nunca por SQL |
| D-11 | Autenticación | JWT HS256 con vigencia de 8 horas y secreto fuera del repositorio | Decisión compartida por ambos planes; librería PHP concreta por validar |
| D-12 | Pruebas | Pruebas unitarias con fakes y pruebas de integración contra PostgreSQL real | Niveles definidos; runner y herramientas de arquitectura PHP pendientes |

## 2. Configuración por entorno

Los nombres de la columna izquierda se heredan de los planes Python y .NET como propuesta de interfaz de configuración. La forma de mapearlos a Laravel debe acordarse en el repositorio `api`. Ningún valor secreto se versiona.

| Variable propuesta | Uso | Ejemplo no secreto |
|---|---|---|
| `DATABASE_URL` | Conexión a PostgreSQL | `postgresql://library:library@db:5432/library` |
| `JWT_SECRET` | Firma de tokens | Se genera fuera del repositorio |
| `ADMIN_USERNAME` / `ADMIN_PASSWORD` | Creación del admin inicial | Se definen en entorno local/seguro |
| `COVERS_DIR` | Almacenamiento local de portadas | `./var/covers` |
| `CORS_ORIGIN` | Origen autorizado del frontend | `http://localhost:5173` |

## 3. Estrategia de pruebas

| Nivel | Qué prueba | Contra qué |
|---|---|---|
| Dominio | Invariantes de ISBN, cantidades, máximo de préstamos y fechas | Objetos puros, sin Laravel ni base de datos |
| Aplicación | Casos de uso de autenticación, catálogo, préstamos, devoluciones y reporte | Fakes de los puertos |
| Integración | Repositorios, restricciones y consulta del reporte | PostgreSQL real |
| Contrato | Rutas, cuerpos de error, códigos y autorización por rol | API levantada |
| Arquitectura | Límites de dependencia de los artículos I, II y III | Comprobación automatizada; herramienta pendiente |
| Concurrencia | Dos solicitudes prestando el último ejemplar | PostgreSQL real; exactamente una debe obtener éxito y la otra 409 |

## 4. Orden de construcción

Esqueleto y salud → migraciones iniciales y categorías → dominio → casos de uso con fakes → adaptadores de persistencia, seguridad y portadas → rutas Laravel y contrato HTTP → composición y administrador inicial → pruebas de integración, contrato y concurrencia.

El detalle y la evidencia están en [`tasks.md`](tasks.md). Antes de cerrar la tarea de esquema inicial, resuelve el testigo de concurrencia que falta en el modelo. Antes de cerrar la autenticación, valida la selección de hash y la librería JWT.

## 5. Decisiones que bloquean una especificación técnica cerrada

1. Versiones compatibles de PHP y Laravel para el entorno de evaluación.
2. ORM y mecanismo de migraciones concretos, conservando la propiedad del esquema en `api`.
3. Campo/estrategia de testigo de concurrencia coherente entre ADR-002 y el modelo físico.
4. Algoritmo de hash de contraseñas y librería JWT.
5. Runner de pruebas y mecanismo automatizado para verificar dependencias.
6. Mapeo de configuración, almacenamiento de portadas, migraciones y creación del admin para un arranque reproducible.
