# Gestión reservas de Cine

## Miembros del grupo L7-XXX-X (sustituir)

1. Benítez Villarejo, David
1. Núñez Muñoz, Iker
1. Donaire Minguet, Juan
1. Fung Chirinos, Steven

## 1. Introducción al problema

- Actualmente, en la industria del cine se requieren sistemas de informacion dinamicos, eficientes y para los usuarios. Con este proyecto vamos a integrar un sistema de gestion para el cine moderno.
Los problemas principales es resolver la digitalizacion y automatizacion de procesos comerciales y administrativos de dicha industria. Con esto abarcamos administración de cartelera, la programacion de la sesion por sala, reparto de artista y la venta de entradas interactiva con el usuario. Los usuarios podrán ver la cartelera, reservar butacas de forma segura y online, y valorar las peliculas vistas.
Para garantizar la viabilidad del sistema en un entorno real, es crítico evitar errores como la sobreventa de entradas (asentar dos reservas sobre la misma butaca en la misma sesión) o el solapamiento de horarios (programar dos sesiones simultáneas en una misma sala). Por tanto, se requiere un modelo conceptual riguroso apoyado por restricciones declarativas y triggers en la capa de datos.

## 2. Glosario de términos

- Términos específicos del dominio del problema, ordenados alfabéticamente. Se valorará la presencia de información multimedia.

## 3. Visión general del sistema
- El Sistema de Gestión de Cine permite organizar las salas, las películas y las sesiones, y que los espectadores reserven sus butacas en tiempo real sin que se solapen horarios. Además, los espectadores pueden valorar las películas que han visto.

### 3.1. Requisitos generales

### 3.2. Usuarios del sistema
A continuación, se definen los distintos perfiles de usuario (actores) que interactúan con el sistema, especificando sus responsabilidades, nivel de acceso y las funciones principales que pueden realizar dentro de la plataforma:

| Perfil / Actor | Descripción | Permisos y Responsabilidades |
| :--- | :--- | :--- |
| **Visitante**<br>*(Cliente No Registrado)* | Usuario casual que accede a la plataforma web o móvil sin autenticarse. | • Consultar la cartelera de películas en proyección.<br>• Visualizar detalles de las fichas técnicas, sinopsis, géneros y repartos.<br>• Consultar los horarios y el aforo disponible de las sesiones. |
| **Cliente Registrado**<br>*(Usuario)* | Espectador que cuenta con una cuenta personal autenticada en la plataforma. | • Todos los permisos del rol **Visitante**.<br>• Seleccionar asientos en el mapa interactivo de la sala.<br>• Realizar la reserva y compra online de entradas.<br>• Consultar su historial personal de compras y descargar entradas.<br>• Solicitar la cancelación de reservas dentro del plazo permitido.<br>• Registrar puntuaciones (0 a 5 estrellas) y comentarios en las películas. |
| **Gestor del Cine**<br>*(Administrador)* | Personal interno autorizado responsable de la gestión comercial y operativa del cine. | • Dar de alta, editar o retirar películas del catálogo general.<br>• Configurar las salas, su capacidad total y el tipo de sus asientos.<br>• Programar las sesiones de proyección (asociando película, sala, fecha, hora y precio base).<br>• Supervisar la ocupación global de las salas y los reportes de recaudación. |


## 4. Catálogo de requisitos

### 4.1. Requisitos funcionales

#### R.F.01. Consultar sesiones y butacas disponibles
**Historia de usuario:** Como espectador quiero ver las sesiones de una película y las butacas libres de cada una para elegir cuándo ir y dónde sentarme.

**Prueba de aceptación:**
- Se muestran fecha, sala, tipo de pantalla, precio y número de butacas disponibles de cada sesión.
- Solo se listan sesiones futuras.
- Se muestran fila y número de las butacas.
- Las butacas con una reserva Pendiente o Pagada para esa sesión aparecen como no seleccionables.
- Las butacas con una reserva Cancelada aparecen como libres.

#### R.F.02. Reservar butacas
**Historia de usuario:** Como espectador quiero seleccionar una o varias butacas de una sesión y reservarlas para asegurar mi asiento.

**Prueba de aceptación:**
- Se genera un código de reserva único, la fecha de compra y el estado Pendiente.
- Se rechaza una butaca ya reservada para esa sesión.
- Se rechaza una butaca que no pertenece a la sala de la sesión.
- Se rechaza una reserva sin ninguna butaca.
- Con 3 butacas o menos no se aplica descuento.
- Se debe aplicar la regla de negocio R.N.04.

#### R.F.03. Cancelar reserva
**Historia de usuario:** Como espectador quiero cancelar una reserva para liberar las butacas.

**Prueba de aceptación:**
- Con 2 horas o más de antelación respecto al inicio de la sesión, la reserva pasa a Cancelada y sus butacas vuelven a estar libres.
- Con exactamente 2 horas de antelación, la cancelación se permite.
- Con menos de 2 horas de antelación, la cancelación se rechaza indicando el motivo.
- Se pueden cancelar reservas Pendientes y Pagadas.
- No se puede cancelar una reserva que ya figure como Cancelada.
- Se debe aplicar la regla de negocio R.N.03.

#### R.F.04. Pagar reserva
**Historia de usuario:** Como espectador quiero pagar una reserva pendiente para confirmar mis entradas.

**Prueba de aceptación:**
- La reserva pasa de Pendiente a Pagada.
- Se rechaza el pago de una reserva que ya figure como Pagada o Cancelada.

#### R.F.05. Valorar película
**Historia de usuario:** Como espectador quiero puntuar y comentar una película para compartir mi opinión y que se actualice su nota media.

**Prueba de aceptación:**
- Se almacena la puntuación (entero de 0 a 5) y un comentario opcional.
- Se rechaza la valoración si el espectador ya ha valorado esa misma película.
- Tras guardar la valoración, se recalcula la nota media de la película.

#### R.F.06. Gestionar sesiones
**Historia de usuario:** Como administrador quiero programar sesiones asignando película, sala, fecha y precio para ofrecerlas a los espectadores.

**Prueba de aceptación:**
- La película y la sala asignadas deben existir.
- Se rechaza una sesión que coincida en el tiempo con otra de la misma sala (R.N.01).
- Se permiten hasta 4 sesiones de la misma película en el mismo día; la quinta se rechaza (R.N.02).
- Al modificar la fecha u hora de una sesión se vuelven a comprobar ambas condiciones.
- No se puede modificar ni eliminar una sesión que tenga reservas Pendientes o Pagadas.
- Se deben aplicar las reglas de negocio R.N.01 y R.N.02.

#### 4.1.1. Requisitos de información

#### R.I.01. Información de usuarios
**Historia de usuario:** Como administrador quiero que el sistema almacene los datos de los usuarios para identificarlos y gestionar su acceso.

**Prueba de aceptación:**
- Se almacenan email, contraseña, nombre, apellido y fecha de nacimiento, todos obligatorios.
- El email es único.
- Cada usuario es espectador o administrador.

#### R.I.02. Información de películas
**Historia de usuario:** Como administrador quiero que el sistema almacene los datos de las películas para publicarlas en la cartelera.

**Prueba de aceptación:**
- Se almacenan título, argumento, duración (minutos, mayor que 0), clasificación de edad y géneros.
- Cada película tiene al menos un género.
- Los géneros se toman de una lista cerrada: Acción, Comedia, Drama, Ciencia ficción, Terror, Animación.
- La nota media no se almacena: se calcula como la media de las puntuaciones recibidas, o nula si no hay ninguna.

#### R.I.03. Información de artistas
**Historia de usuario:** Como administrador quiero que el sistema almacene los artistas para indicar quién participa en cada película.

**Prueba de aceptación:**
- Se almacenan nombre, apellido y nacionalidad.
- Cada película tiene al menos un artista; un artista puede participar en varias películas.

#### R.I.04. Información de salas
**Historia de usuario:** Como administrador quiero que el sistema almacene las salas para programar sesiones en ellas.

**Prueba de aceptación:**
- Se almacenan número, capacidad (mayor que 0) y tipo de pantalla.
- Cada sala contiene al menos una butaca.

#### R.I.05. Información de butacas
**Historia de usuario:** Como administrador quiero que el sistema almacene las butacas de cada sala para poder reservarlas.

**Prueba de aceptación:**
- Cada butaca pertenece a una única sala y no se repite la pareja de fila y número dentro de ella.

#### R.I.06. Información de sesiones
**Historia de usuario:** Como administrador quiero que el sistema almacene las sesiones para ofrecer proyecciones concretas.

**Prueba de aceptación:**
- Se almacenan fecha y hora, y precio (mayor o igual que 0).
- Cada sesión corresponde a una película y a una sala.
- Las butacas disponibles no se almacenan: se derivan de las reservas.

#### R.I.07. Información de reservas
**Historia de usuario:** Como administrador quiero que el sistema almacene las reservas para controlar la ocupación y las ventas.

**Prueba de aceptación:**
- Se almacenan código de reserva (único), fecha de compra y estado (Pendiente, Pagada o Cancelada).
- El precio no se almacena: se calcula a partir del precio de la sesión y el descuento de R.N.04 si corresponde.
- Cada reserva pertenece a un espectador, corresponde a una sesión e incluye una o más butacas.

#### R.I.08. Información de valoraciones
**Historia de usuario:** Como administrador quiero que el sistema almacene las valoraciones para calcular la nota media de cada película.

**Prueba de aceptación:**
- Se almacenan puntuación (entero de 0 a 5), comentario (opcional) y fecha.
- Cada valoración pertenece a un espectador y a una película, y no puede haber dos del mismo espectador para la misma película.

#### 4.1.2. Reglas de negocio

#### R.N.01. No solapamiento de sesiones en una sala
En una sala no puede haber dos sesiones al mismo tiempo. El intervalo ocupado por una sesión es su hora de inicio más la duración de la película más el tiempo de limpieza.

#### R.N.02. Máximo de sesiones diarias por película
Una película no puede tener más de 4 sesiones en el mismo día (según la fecha de inicio de la sesión).

#### R.N.03. Antelación mínima de cancelación
Una reserva puede cancelarse con una antelación mínima de 2 horas respecto al inicio de la sesión.

#### R.N.04. Descuento por reserva de grupo
Si una reserva incluye 4 o más butacas, se aplica automáticamente un descuento del 10% sobre el precio total.

#### R.N.05. Notas para la valoración
Las valoraciones deben ser enteros de 0 a 5.

### 4.2. Mapa de historias de usuario (opcional)
| 1. Exploración y Ficha | 2. Reserva y Compras | 3. Feedback | 4. Administración |
| :--- | :--- | :--- | :--- |
| • **[RI01]** Consultar cartelera y sesiones<br><br>• **[RI02]** Consultar detalle reparto y película | • **[RF01]** Reservar y comprar entradas<br><br>• **[RI03]** Consultar historial de reservas<br><br>• **[RF03]** Solicitar cancelación | • **[RF04]** Registrar valoración | • **[RF02]** Programar sesiones en salas |

### 4.3. Requisitos no funcionales (opcional)

#### RNF 01. Tiempo de respuesta del mapa de la sala
**Historia de usuario:** Como cliente del cine quiero que el plano visual interactivo de la sala cargue en menos de 2 segundos para poder seleccionar mis butacas de forma fluida y sin interrupciones.

#### RNF 02. Cifrado y seguridad en las transacciones
**Historia de usuario:** Como cliente registrado quiero que mis credenciales de acceso y mis datos de pago se procesen mediante protocolos de comunicación segura (HTTPS/TLS) y algoritmos de cifrado para garantizar la confidencialidad y la protección de mi información personal y bancaria.

#### RNF 03. Alta disponibilidad de la plataforma
**Historia de usuario:** Como cliente del cine quiero que la plataforma web presente una disponibilidad del 99.9% del tiempo para poder adquirir entradas a cualquier hora del día, especialmente durante días de gran afluencia o preventa de estrenos.

#### RNF 04. Diseños adaptativos (Responsive Design)
**Historia de usuario:** Como usuario móvil quiero disponer de una interfaz gráfica que se adapte automáticamente al tamaño de pantalla de cualquier dispositivo (Smartphone, Tablet o PC) para navegar y comprar entradas de forma intuitiva independientemente del equipo utilizado.

#### RNF 05. Escalabilidad ante picos de demanda
**Historia de usuario:** Como gestor del cine quiero que la infraestructura de software sea capaz de soportar hasta 1.000 usuarios concurrentes de forma simultánea para prevenir caídas o degradaciones del servicio durante el lanzamiento de películas muy esperadas.

## 5. Modelo conceptual

### 5.1. Diagramas de clases UML

- con restricciones.

### 5.2. Escenarios de prueba

- con descripción textual y diagrama de objetos UML.

## 6. Matrices de trazabilidad

- Matriz de trazabilidad entre los elementos del modelo conceptual y los requisitos.

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:------|:-----------|:-----------|:-----------|:-----------|
| RI-1  | X          | X          | X          | X          |
| RI-2  |            | X          |            | X          |
| RF-1  |            | X          |            | X          |
| RF-2  | X          |            | X          | X          |
| RN-1  |            | X          |            |            |
| RN-2  | X          | X          | X          |            |
| ...   |            |            |            |            |

-- fin entregable 2 --

## 7. Modelo relacional en 3FN

- Relaciones obtenidas al aplicar la transformación del modelo conceptual.

### 7.1.  Justificación de la estrategia de transformación de jerarquías

- si se identificaron jerarquías en el MC.


### 8. Matriz de trazabilidad MC/SQL (opcional):

- Restricciones sobre el MC / Elementos del modelo tecnológico (SQL) (Triggers, checks, etc.)
- Incluir Reglas de negocio — Constraints/Triggers en las matrices de trazabilidad para el entregable 3

|       | EntidadX   | AsociaciónX  | RestricciónX  | Entidad2 ...   | 
|:-------|:-------|:-------|:-------|:-------|
| TABLA-1 |        |        |        |        |
| TABLA-2 |        |        |        |        |
| TABLA-3 |        |        |        |        |
| TABLA-4 |        |        |        |        |
| TRIG-1 |        |        |        |        |
| TRIG-2 | X      | X      |        | X      |
| TRIG-3 |        | X      |        | X      |
| TRIG-4 |        |        | X      |        |
| CONST-1 |        |        |        |        |
| CONST-2 | X      | X      |        | X      |
| CONST-3 |        | X      |        | X      |
| CONST-4 |        |        | X      |        |

Se consideran todo tipo de constraints declarativas (aquellas definidas durante el CREATE TABLE).
-- fin entregable 3 --

## Referencias


