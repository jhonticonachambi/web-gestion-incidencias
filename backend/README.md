# Backend - Gestión de Incidencias

Estructura base inicial del backend con FastAPI y arquitectura por capas.

## Estructura

- `app/core`: configuración, seguridad y base de datos.
- `app/domain`: entidades y contratos de repositorio.
- `app/application`: casos de uso/servicios.
- `app/infrastructure`: implementación técnica (DB y servicios externos).
- `app/presentation`: API HTTP (routers y schemas).
- `app/tests`: pruebas unitarias e integración.
- `scripts`: tareas auxiliares.
- `docs`: documentación técnica.
- `alembic`: migraciones.
