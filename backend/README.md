# Backend — Sistema de Gestión de Incidencias (FastAPI)

Este backend está diseñado para gestionar el ciclo completo de incidencias técnicas sobre equipos e infraestructura: reporte, asignación, atención, evidencias, historial de cambios, mantenimiento y reportes.

La base del proyecto sigue una arquitectura por capas para mantener separación de responsabilidades, facilitar pruebas y permitir que el sistema escale de forma ordenada.

---

## Tabla de contenido

1. [Objetivo del proyecto](#objetivo-del-proyecto)
2. [Arquitectura](#arquitectura)
3. [Estructura de carpetas](#estructura-de-carpetas)
4. [Dominio funcional](#dominio-funcional)
5. [Stack tecnológico](#stack-tecnológico)
6. [Requisitos previos](#requisitos-previos)
7. [Configuración local](#configuración-local)
8. [Variables de entorno](#variables-de-entorno)
9. [Ejecución del proyecto](#ejecución-del-proyecto)
10. [Migraciones (Alembic)](#migraciones-alembic)
11. [Pruebas](#pruebas)
12. [Estándares de desarrollo](#estándares-de-desarrollo)
13. [Flujo de trabajo sugerido](#flujo-de-trabajo-sugerido)
14. [Roadmap sugerido](#roadmap-sugerido)

---

## Objetivo del proyecto

Construir un backend robusto que permita:

- Registrar incidencias reportadas por usuarios.
- Asignar incidencias a técnicos responsables.
- Registrar atenciones y soluciones aplicadas.
- Adjuntar evidencias (imágenes, PDF, etc.).
- Mantener historial/auditoría de cambios relevantes.
- Gestionar mantenimientos preventivos/correctivos de equipos.
- Generar reportes para seguimiento operativo.

---

## Arquitectura

El proyecto usa una arquitectura por capas inspirada en Clean Architecture / DDD ligero:

- **Presentation**: endpoints HTTP (FastAPI), validación de entrada/salida y dependencias.
- **Application**: casos de uso y orquestación del negocio.
- **Domain**: entidades y contratos de repositorios (reglas de negocio puras).
- **Infrastructure**: persistencia SQLAlchemy, adaptadores externos (correo, PDF, ML, etc.).
- **Core**: configuración global, seguridad y conexión base.

Ventajas:

- Menor acoplamiento.
- Mayor mantenibilidad.
- Facilidad para test unitario/integración.
- Mejor evolución del proyecto en el tiempo.

> Referencia extendida: `backend/docs/arquitectura.md`.

---

## Estructura de carpetas

```text
backend/
├── app/
│   ├── main.py
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py
│   │   └── database.py
│   ├── domain/
│   │   ├── entities/
│   │   └── repositories/
│   ├── application/
│   │   └── services/
│   ├── infrastructure/
│   │   ├── db/
│   │   │   ├── models/
│   │   │   ├── repositories/
│   │   │   └── session.py
│   │   └── external/
│   ├── presentation/
│   │   ├── api/v1/
│   │   │   ├── deps.py
│   │   │   └── routers/
│   │   └── schemas/
│   └── tests/
│       ├── unit/
│       └── integration/
├── scripts/
├── docs/
├── alembic/
├── requirements.txt
└── .env
```

---

## Dominio funcional

Entidades clave previstas en el sistema:

- **Usuario**: autenticación, roles y responsables.
- **Incidencia**: reporte principal del problema.
- **Atención**: acciones técnicas para resolver la incidencia.
- **Evidencia**: archivos de respaldo del proceso.
- **HistorialIncidencia**: auditoría de cambios.
- **Equipo**: activos tecnológicos sobre los que recaen incidencias.
- **Mantenimiento**: tareas preventivas/correctivas.
- **Ubicación / Marca**: catálogos de soporte.

Estados sugeridos de incidencia:

- `ABIERTA`
- `ASIGNADA`
- `EN_PROGRESO`
- `RESUELTA`
- `CERRADA`
- `CANCELADA`

---

## Stack tecnológico

- **Python 3.11+**
- **FastAPI**
- **Uvicorn**
- **SQLAlchemy 2.x**
- **Alembic**
- **Pydantic**
- **PostgreSQL** (recomendado)
- **Pytest** para pruebas

---

## Requisitos previos

Antes de iniciar, asegúrate de tener instalado:

- Python 3.11 o superior
- pip
- virtualenv (opcional, recomendado)
- PostgreSQL (local o remoto)

---

## Configuración local

1. Entrar a la carpeta backend:

```bash
cd backend
```

2. Crear entorno virtual:

```bash
python -m venv .venv
```

3. Activar entorno virtual:

```bash
# Linux/macOS
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

4. Instalar dependencias:

```bash
pip install -r requirements.txt
```

---

## Variables de entorno

Configura `backend/.env` (valores de ejemplo):

```env
APP_NAME=gestion-incidencias-backend
APP_ENV=development
APP_DEBUG=true
APP_PORT=8000

DATABASE_URL=postgresql+psycopg://usuario:password@localhost:5432/gestion_incidencias

JWT_SECRET_KEY=CAMBIAR_EN_PRODUCCION
JWT_ALGORITHM=HS256
JWT_EXPIRE_MINUTES=60

CORS_ORIGINS=http://localhost:3000,http://localhost:5173
```

> Nunca subas secretos reales al repositorio.

---

## Ejecución del proyecto

Desde `backend/`:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Documentación interactiva (cuando `main.py` esté configurado):

- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

---

## Migraciones (Alembic)

Flujo típico:

1. Crear migración:

```bash
alembic revision --autogenerate -m "init schema"
```

2. Aplicar migraciones:

```bash
alembic upgrade head
```

3. Revertir última migración (si aplica):

```bash
alembic downgrade -1
```

---

## Pruebas

Estructura de pruebas:

- `app/tests/unit`: lógica de dominio y aplicación (sin depender de DB real).
- `app/tests/integration`: repositorios, DB y API.

Ejecutar pruebas:

```bash
pytest -q
```

Con cobertura (si está configurado):

```bash
pytest --cov=app --cov-report=term-missing
```

---

## Estándares de desarrollo

- Mantener `domain` sin dependencias de FastAPI/SQLAlchemy.
- Validar entrada/salida en `presentation/schemas`.
- Evitar lógica de negocio en routers.
- Registrar cambios relevantes en historial (auditoría).
- Usar nombres consistentes y tipado explícito.

---

## Flujo de trabajo sugerido

1. Definir entidad/regla en `domain`.
2. Crear contrato de repositorio (`domain/repositories`).
3. Implementar caso de uso en `application/services`.
4. Implementar persistencia en `infrastructure/db/repositories`.
5. Exponer endpoint en `presentation/api/v1/routers`.
6. Definir `schemas` request/response.
7. Agregar pruebas unitarias e integración.

---

## Roadmap sugerido

### Fase 1 — Base técnica

- Configuración global.
- Conexión DB + sesión.
- Seguridad JWT + hash de contraseña.
- CRUD básico de usuarios.

### Fase 2 — Incidencias

- Registro y listado de incidencias.
- Asignación a técnico.
- Flujo de estados con validaciones.
- Auditoría en historial.

### Fase 3 — Operación

- Gestión de equipos.
- Mantenimiento preventivo/correctivo.
- Carga de evidencias.

### Fase 4 — Reportes y calidad

- Reportes operativos.
- Optimización con índices.
- Pruebas end-to-end.
- Pipeline CI/CD.

---

Si necesitas, en el siguiente paso puedo dejarte también:

- un `requirements.txt` inicial completo,
- un `main.py` funcional con routers y CORS,
- y el `config.py` con `pydantic-settings` listo para producción.
