# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

No hay código, build, lint ni tests. Es un repositorio de documentación Markdown (en español) de un trabajo de la asignatura ADA: análisis de requisitos del sistema de facturación interno de **Turbine** (startup que vende suscripciones mensuales de su producto *Brain Operative System*). El equipo sigue Scrum; los roles (PO, Scrum master, equipo) están en `README.md`. Todo el contenido va en español.

## Estructura

- `Scrum/Product Backlog/Product backlog.md` — épicas globales: documento de requisitos (negocio, usuario, funcionales, no funcionales), diagrama UML de casos de uso (actores, casos, PlantUML), especificación de casos de uso.
- `Scrum/sprints/sprintN/` — un directorio por sprint, con `sprint backlog.md` y, según el sprint, entregables (`RequisitosFuncionales.md`, `RequisitosNoFuncionales.md`, `Actores.md`, `sprint retrospective.md`).
- `utilities/` — fuente de verdad del cliente: análisis de la entrevista al CEO de Turbine. **Está en `.gitignore`** (no se versiona); consultarlo antes de inventar o corregir requisitos.

## Convenciones de los requisitos

Los requisitos funcionales (`RequisitosFuncionales.md`) se agrupan en **7 secciones temáticas** (usuarios/roles/asignaciones, clientes/contactos, suscripciones/tarifas/catálogo, presupuestos/facturación/ciclo documental, cobros/conciliación/impagos, comunicaciones/incidencias/trabajo colaborativo, operativa/informes/configuración/API). El sprint 2 reparte los casos de uso con esos mismos 7 grupos, así que mantener esa numeración y títulos coherentes entre ficheros.

Formato de cada requisito:

- IDs jerárquicos `FR<n>` con subrequisitos `FR<n>.<m>` (numeración global continua entre secciones).
- Redacción: "El sistema permitirá / validará / ...".
- Cada subrequisito lleva línea `*Dependencias:* FRx.` con sublista indentada.

Al añadir o renumerar un FR, revisar las referencias en `*Dependencias:*` de todo el fichero.

## Dominio (reglas clave del cliente)

3 roles RBAC (Superadministrador, Administrador, Lector con CRUD limitado a su cartera asignada); facturas recurrentes el día 1 con prorrateo en altas a mitad de mes; facturas bloqueadas tras cierre de trimestre fiscal; app web responsive e independiente de Brain, sin acceso de clientes finales y sin ser pasarela de pago; API externa documentada. Detalle completo en `utilities/`.

## Git

Rama de trabajo habitual `Victor-Branch`; la principal es `main`. Mensajes de commit en español.
