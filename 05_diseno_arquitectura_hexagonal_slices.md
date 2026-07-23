# Diseño de Arquitectura: Hexagonal y Vertical Slices

Este documento describe de manera concisa el diseño arquitectónico del sistema de Onboarding, basado en la combinación de **Vertical Slices** y **Arquitectura Hexagonal**, junto con las justificaciones de cada decisión técnica.

---

## 1. Patrones Arquitectónicos

### 1.1 Vertical Slices (Rebanadas Verticales)
El sistema no se organiza por capas técnicas globales (controladores por un lado, servicios por otro), sino por **características de negocio** o módulos independientes llamados *Slices*.

* **Slices definidos:** `auth`, `configuration`, `onboarding`, `validation`, y `shared`.
* **Justificación:** 
  * **Desacoplamiento funcional:** Cada slice contiene todo lo necesario (desde el controlador hasta la base de datos) para cumplir su caso de uso.
  * **Mantenibilidad:** Si hay que modificar la lógica de "autenticación", solo se toca el slice `auth`, reduciendo el riesgo de romper otras partes del sistema.
  * **Escalabilidad del equipo:** Diferentes desarrolladores pueden trabajar en distintos slices en paralelo sin conflictos.

### 1.2 Arquitectura Hexagonal (Ports & Adapters)
Dentro de cada *Slice*, el código se divide en tres capas concéntricas para aislar la lógica de negocio de los detalles de infraestructura.

* **Domain (Dominio):** Contiene las reglas de negocio, entidades puras y eventos.
* **Application (Aplicación):** Contiene los casos de uso (puertos de entrada) y las interfaces de los servicios externos (puertos de salida).
* **Infrastructure (Infraestructura):** Implementa los adaptadores (Controladores REST, repositorios JPA, acceso a ficheros).
* **Justificación:** 
  * **Aislamiento del Core:** El dominio no sabe que existe Spring Boot, bases de datos o HTTP. Esto lo hace muy fácil de testear de forma unitaria.
  * **Flexibilidad tecnológica:** Se puede cambiar la base de datos o el framework web sin modificar la lógica de negocio, simplemente creando nuevos adaptadores para los puertos.

---

## 2. Decisiones de Diseño y Justificaciones

### A. Dominio Puro (Sin Frameworks)
* **Decisión:** Las entidades de dominio son POJOs puros o Java Records. No contienen anotaciones de JPA (`@Entity`, `@Table`) ni de librerías como Lombok o Spring.
* **Justificación:** Evita que el núcleo del negocio se contamine con detalles de persistencia. Garantiza que la lógica pura pueda sobrevivir a cambios de versión o de framework a largo plazo.

### B. Mapeadores de Entidades (Entity Mapping)
* **Decisión:** Separar las entidades del dominio de las entidades de base de datos (JPA Entities) usando mappers (como MapStruct) en la capa de infraestructura.
* **Justificación:** Las bases de datos relacionales requieren estructuras planas y anotaciones que chocan con un buen diseño orientado a objetos en el dominio. Al separarlas, cada una se optimiza para su propósito (negocio vs almacenamiento).

### C. Comunicación entre Slices
* **Decisión:** Los slices se comunican entre sí de dos maneras:
  1. **Llamadas unidireccionales a Puertos de Entrada:** Un slice llama a la interfaz (Inbound Port) de otro slice, nunca a su repositorio o base de datos.
  2. **Eventos de Dominio asíncronos:** Uso de `ApplicationEventPublisher` para comunicación reactiva (ej. notificar por email cuando se termina un onboarding).
* **Justificación:** Previene el alto acoplamiento y las dependencias cíclicas ("código espagueti"). Permite que módulos como el envío de correos operen sin bloquear el flujo principal del usuario.

### D. Gestión de Archivos Segura
* **Decisión:** El almacenamiento físico delega el guardado a un adaptador local que renombra los archivos con UUIDs y valida los *Magic Bytes* (MIME types reales, no solo extensión).
* **Justificación:** Seguridad. Evita que usuarios suban archivos maliciosos ocultos bajo extensiones falsas o que sobrescriban archivos con nombres idénticos, garantizando la integridad del servidor.
