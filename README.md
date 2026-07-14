# Cívica - Nuevas Incorporaciones (Onboarding System)

Este proyecto corresponde al desarrollo de la aplicación web de gestión de incorporaciones (Onboarding) de nuevos empleados en **Cívica**. Permite digitalizar, automatizar y validar la recopilación de datos y documentación obligatoria requerida para la contratación.

---

## 🚀 Propósito del Proyecto
Facilitar un canal seguro e interactivo para que los nuevos empleados externos (quienes aún no poseen cuenta corporativa o acceso al Directorio Activo de Cívica) completen su ficha personal y suban documentos de carácter sensible en los formatos requeridos. Recursos Humanos (Talento y People Care) cuenta con una interfaz administrativa para definir dinámicamente los campos a solicitar, monitorizar el avance del proceso, validar individualmente cada registro y gestionar subsanaciones de datos erróneos.

---

## 📂 Estructura de la Documentación (Fase 1)

El análisis conceptual, diseño de datos y arquitectura de seguridad se encuentran detallados en los siguientes documentos técnicos:

1. 📑 **[Análisis de Requisitos y Casos de Uso](01_analisis_requisitos_casos_uso.md):** Especificación formal de los actores del sistema (`Nuevo Empleado` y `Administración`), reglas de validación en frontend y backend, flujos del sistema y el diagrama de casos de uso (Mermaid.js).
2. 🗄️ **[Modelo de Datos Relacional](02_modelo_datos_relacional.md):** Diseño conceptual y físico orientado a PostgreSQL. Detalla el diccionario de datos de las tablas físicas y la justificación del modelo híbrido relacional/dinámico utilizado para campos dinámicos y la carga de ficheros.
3. 🔒 **[Diseño de Arquitectura y Seguridad](03_diseno_arquitectura_seguridad.md):** Justificación técnica del flujo de autenticación passwordless basado en OTP/Magic Links temporales de un solo uso, estrategia de almacenamiento y descarga segura de ficheros de carácter personal y la máquina de estados del expediente.

---

## 🛠️ Stack Tecnológico Propuesto (Fase 2)

* **Backend:** Java Spring Boot (REST API, persistencia con Spring Data JPA, seguridad JWT y cookies efímeras, gestión de archivos y envío de notificaciones).
* **Frontend:** Angular (Interfaz interactiva responsiva, formularios dinámicos y validación de cliente en tiempo real).
* **Base de Datos:** PostgreSQL (almacenamiento de metadatos, tablas relacionales e históricos de validación).

---

## 📈 Flujo de Estados del Onboarding

El expediente del nuevo empleado avanza de forma lógica a través de los siguientes estados:

```
Creado ──► Enviado_Enlace ──► En_Progreso ──► Completado_Pendiente_Validacion ──► Validado (Fin)
                                 ▲                          │
                                 │                          ▼
                                 └───────────────── Rechazado_Subsanacion (rrhh rechaza datos)
```
