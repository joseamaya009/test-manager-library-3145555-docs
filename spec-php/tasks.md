# Tareas — Manager Library (PHP/Laravel)

Cada tarea termina cuando existe la evidencia indicada (artículo XII). Estado inicial: **pendiente**. Se conservan tareas y alcance de las referencias; los nombres de herramientas específicos de Python y .NET se sustituyen por verificaciones equivalentes para PHP, aún por elegir cuando corresponda.

| # | Tarea | Depende de | Evidencia |
|---|---|---|---|
| T-01 | Esqueleto Laravel y `GET /health` | — | `GET /health` responde `{"status":"ok"}`; el contenedor arranca con un comando |
| T-02 | Migración inicial: seis tablas, FK, índices únicos y las cinco categorías sembradas | T-01 | Las consultas de [`data-model.md` §10](data-model.md) devuelven lo documentado; ninguna columna tiene `DEFAULT` |
| T-03 | Dominio: `Isbn`, `Quantity`, `DateRange` (objetos de valor) | T-01 | Pruebas unitarias: ISBN de 13 dígitos, cantidad > 0, fin ≥ inicio. Resolver si `DocumentNumber` también requiere objeto de valor |
| T-04 | Dominio: `Book` (retirar, reponer, baja lógica) | T-03 | Pruebas: disponibles nunca < 0 ni > total |
| T-05 | Dominio: `Loan` (añadir/devolver línea, estado calculado, 14 días, máximo 3) | T-04 | Pruebas con reloj falso: `open`, `overdue`, `closed` |
| T-06 | Dominio: `Member` y `User` (normalización del nombre, rol válido) | T-03 | Pruebas de normalización y rol; la API rechaza crear un usuario `admin` |
| T-07 | Puertos y casos de uso de autenticación (`Login`, `CreateLibrarian`) con fakes | T-06 | HU-01 y HU-02 pasan contra fakes |
| T-08 | Puertos y casos de uso de libros (crear, buscar, editar, baja, portada) con fakes | T-04 | HU-03 y HU-04 pasan contra fakes |
| T-09 | Caso de uso de préstamo con fakes | T-05, T-08 | HU-06 completa: límites, vencidos y falta de ejemplares |
| T-10 | Casos de uso de devolución y vencidos | T-09 | HU-07 y HU-09; devolver dos veces produce `ITEM_ALREADY_RETURNED` |
| T-11 | Caso de uso del reporte y su puerto de lectura | T-09 | HU-10; el título congelado se conserva después de renombrar el libro (ADR-004) |
| T-12 | Adaptador de persistencia para libros, socios y usuarios | T-02, T-08 | Pruebas de integración contra PostgreSQL real |
| T-13 | Adaptador de persistencia de préstamos y control de concurrencia | T-12, T-09 | Dos solicitudes concurrentes sobre el último ejemplar: una 201 y otra 409 (RNF-2); el testigo debe estar definido en el modelo |
| T-14 | Adaptadores de hash de claves y emisión/verificación de JWT | T-07 | La clave nunca aparece en logs ni respuestas (RNF-3); algoritmo y librería definidos en el plan |
| T-15 | Adaptador de portadas en disco | T-08 | Subir PNG de 2 MB funciona; 3 MB da 422; sin portada `cover_key` queda nulo |
| T-16 | Adaptador HTTP Laravel: rutas de [`api-contract.md`](api-contract.md), cuerpo único de error y autorización por rol | T-10, T-11, T-14, T-15 | Pruebas de contrato recorren las rutas y códigos de [`api-contract.md` §2](api-contract.md) |
| T-17 | Composición y arranque; crear admin inicial desde entorno | T-16 | Arrancar según la secuencia documentada e iniciar sesión como admin |
| T-18 | Añadir a la base las invariantes marcadas **pendiente (T-18)** | T-12 | Un `INSERT` directo que viole `ck_book_total_positive` o `ck_user_role` falla; actualizar las marcas de `data-model.md` |
| T-19 | Comprobaciones automatizadas de arquitectura (artículos I, II y III) | T-17 | La verificación elegida para PHP pasa y falla ante una dependencia prohibida |
| T-20 | `infra`: `docker compose up` levanta motor vacío, API y frontend | T-17 | Un comando; documentar explícitamente cuándo se ejecutan las migraciones y cuándo deja de estar vacía la base |
| T-21 | `tool`: sembrador de demostración **por la API** (≥ 40 libros, 10 socios, 15 préstamos, 3 vencidos) | T-17 | Tras correrlo, `/loans/overdue` devuelve 3 líneas y el reporte tiene filas |
| T-22 | `app`: login, catálogo, socios, prestar, devolver, vencidos y reporte | T-16 | Recorrido manual de punta a punta en la feria |
| T-23 | `page`: sitio público estático de presentación | — | Se abre sin la API; correcto en móvil y escritorio |

## Definición de terminado de la prueba

Las referencias consideran terminado el núcleo al llegar a T-17 con evidencia; T-18 a T-23 distinguen un resultado sobresaliente. Confirmar que esa clasificación también aplica al stack PHP/Laravel antes de usarla como criterio de evaluación. Se evalúa el desempeño con SDD, no la cantidad de tareas: una tarea con evidencia vale más que tres a medias.
