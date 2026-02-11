# Diagrama de clases mejorado (Gestión de Incidencias)

Este diseño incorpora mejoras para implementación real con **FastAPI + SQLAlchemy**:

- Cardinalidades corregidas.
- Eliminación de duplicidades (`id_equipo` repetido en `Incidencia`).
- Estados/prioridades como `enum`.
- Auditoría detallada en historial.
- Relación práctica entre incidencias y mantenimientos.

## Diagrama UML (Mermaid)

```mermaid
classDiagram

class Usuario {
  +int id
  +string username
  +string email
  +string password_hash
  +string nombres_completos
  +string cargo
  +RolUsuario rol
  +bool activo
  +datetime created_at
  +datetime updated_at
  +datetime ultimo_login
}

class Incidencia {
  +int id
  +string codigo
  +date fecha_reporte
  +time hora_reporte
  +string asunto
  +text descripcion_problema
  +PrioridadIncidencia prioridad
  +EstadoIncidencia estado
  +CategoriaIncidencia categoria
  +int? sla_horas
  +datetime? fecha_limite_sla
  +datetime? fecha_cierre
  +int id_usuario_reporta
  +int? id_tecnico_asignado
  +int? id_equipo
}

class Atencion {
  +int id
  +datetime fecha_atencion
  +text diagnostico_tecnico
  +text procedimiento_realizado
  +text solucion_aplicada
  +text recomendaciones
  +TipoAtencion tipo_atencion
  +int? tiempo_resolucion_min
  +int id_incidencia
  +int id_usuario_tecnico
}

class Evidencia {
  +int id
  +string url_archivo
  +TipoArchivo tipo_archivo
  +datetime fecha_subida
  +int id_incidencia
  +int id_usuario_creador
}

class HistorialIncidencia {
  +int id
  +datetime fecha
  +string campo_modificado
  +text valor_anterior
  +text valor_nuevo
  +string motivo
  +int id_usuario_modifica
  +int id_incidencia
}

class Equipo {
  +int id
  +string codigo_patrimonial
  +TipoEquipo tipo
  +string serie
  +string condicion
  +EstadoOperativo estado_operativo
  +date? fecha_compra
  +date? garantia_hasta
  +string? proveedor
  +int id_ubicacion
  +int id_marca
}

class Mantenimiento {
  +int id
  +TipoMantenimiento tipo
  +date fecha_realizacion
  +date fecha_proximo_mantenimiento
  +text actividades_realizadas
  +text observaciones
  +int id_equipo
  +int id_usuario_responsable
  +int? id_incidencia_origen
}

class Ubicacion {
  +int id
  +string nombre_sede
  +string piso
  +string nombre_oficina
}

class Marca {
  +int id
  +string nombre
}

class RolUsuario {
  <<enumeration>>
  ADMIN
  TECNICO
  USUARIO
}

class EstadoIncidencia {
  <<enumeration>>
  ABIERTA
  ASIGNADA
  EN_PROGRESO
  RESUELTA
  CERRADA
  CANCELADA
}

class PrioridadIncidencia {
  <<enumeration>>
  BAJA
  MEDIA
  ALTA
  CRITICA
}

class CategoriaIncidencia {
  <<enumeration>>
  HARDWARE
  SOFTWARE
  RED
  SERVICIO
  OTRO
}

class EstadoOperativo {
  <<enumeration>>
  OPERATIVO
  DEGRADADO
  FUERA_SERVICIO
  EN_MANTENIMIENTO
}

class TipoMantenimiento {
  <<enumeration>>
  PREVENTIVO
  CORRECTIVO
  PREDICTIVO
}

class TipoAtencion {
  <<enumeration>>
  REMOTA
  PRESENCIAL
}

class TipoArchivo {
  <<enumeration>>
  IMAGEN
  VIDEO
  PDF
  AUDIO
  OTRO
}

Usuario "1" --> "0..*" Incidencia : reporta
Usuario "1" --> "0..*" Incidencia : asignado_a
Usuario "1" --> "0..*" Atencion : realiza
Usuario "1" --> "0..*" HistorialIncidencia : modifica
Usuario "1" --> "0..*" Evidencia : sube
Usuario "1" --> "0..*" Mantenimiento : responsable

Incidencia "1" --> "0..*" Atencion : recibe
Incidencia "1" --> "0..*" Evidencia : adjunta
Incidencia "1" --> "0..*" HistorialIncidencia : registra
Incidencia "0..*" --> "0..1" Equipo : afecta

Equipo "1" --> "0..*" Incidencia : tiene
Equipo "1" --> "0..*" Mantenimiento : recibe

Ubicacion "1" --> "0..*" Equipo : contiene
Marca "1" --> "0..*" Equipo : clasifica

Mantenimiento "0..*" --> "0..1" Incidencia : originado_por
```

## Reglas clave sugeridas

1. **Transición de estados controlada**: no permitir `ABIERTA -> CERRADA` sin pasar por `RESUELTA`.
2. **Asignación técnica válida**: solo usuarios con rol `TECNICO` pueden ser `id_tecnico_asignado`.
3. **Auditoría obligatoria**: todo cambio de `estado`, `prioridad` o `id_tecnico_asignado` crea fila en `HistorialIncidencia`.
4. **Trazabilidad documental**: cada `Evidencia` registra autor (`id_usuario_creador`) y fecha.
5. **Mantenimiento correctivo opcionalmente ligado a incidencia** vía `id_incidencia_origen`.

## Índices recomendados

- `incidencia(estado, prioridad, fecha_reporte)`
- `incidencia(id_usuario_reporta)`
- `incidencia(id_tecnico_asignado)`
- `atencion(id_incidencia, fecha_atencion)`
- `mantenimiento(id_equipo, fecha_proximo_mantenimiento)`
- `evidencia(id_incidencia, fecha_subida)`

