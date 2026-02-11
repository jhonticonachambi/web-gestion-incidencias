# Arquitectura del Backend

El backend sigue una arquitectura en capas para separar reglas de negocio, aplicación, infraestructura y presentación.

## Capas

1. **Domain**: entidades y repositorios abstractos.
2. **Application**: servicios/casos de uso.
3. **Infrastructure**: SQLAlchemy, repositorios concretos y adaptadores externos.
4. **Presentation**: FastAPI (routers, dependencias y schemas).

## Objetivo

Mantener bajo acoplamiento, facilitar pruebas y permitir crecimiento del proyecto.
