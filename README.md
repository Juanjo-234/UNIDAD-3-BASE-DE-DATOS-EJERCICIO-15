# UNIDAD-3-BASE-DE-DATOS-EJERCICIO-15
Este diagrama entidad-relación (o modelo relacional) representa la estructura lógica de una **base de datos para la gestión de torneos de eSports** (competiciones de videojuegos), diseñada para administrar los videojuegos, los torneos, los partidos, los equipos, los jugadores y las estadísticas de rendimiento detalladas por cada encuentro.

A continuación se detalla qué representa cada tabla y cómo se relacionan entre sí:

### 1. Entidades Principales (Tablas)

* **`VIDEOJUEGO`**: Almacena el catálogo de títulos o juegos electrónicos disponibles para los torneos.
* *Campos:* `ID_JUEGO` (Clave Primaria), `NOMBRE`, `GEENRO` (Género) y `DESARROLLADOR`.




* **`TORNEO`**: Registra los campeonatos organizados para los diferentes juegos.
* *Campos:* `ID_TORNEO` (Clave Primaria), `NOMBRE`, `FECHA_INICIO`, `FECHA_FIN` e `ID_JUEGO` (Clave Foránea).




* **`EQUIPOS`**: Contiene la información de las organizaciones u escuadras de eSports.
* *Campos:* `TAG_EQUIPO` (Clave Primaria, por ejemplo, "NAVI", "FUR"), `FECHA_FUNDACION`, `RAZON_SOCIAL`, `PAIS_ORIGEN` y `CANTIDAD_JUGAADORES`.




* **`JUGADORES`**: Almacena los perfiles de los competidores individuales.
* *Campos:* `ID_JUGADOR` (Clave Primaria), `NOMBRE_REAL`, `NICKNAME`, `NACIONALIDAD` y `EQUIPO` (Clave Foránea que indica a qué organización pertenece).




* **`PARTIDO`**: Registra los encuentros programados dentro de un torneo.
* *Campos:* `ID_PARTIDO` (Clave Primaria), `EQUIPO_GANADOR`, `FECHA`, `HORA` e `ID_TORNEO` (Clave Foránea).




* **`DETALLES_PARTIDO`**: Tabla intermedia que desglosa los enfrentamientos específicos entre equipos.
* *Campos:* `ID_PARTIDO`, `TAG_EQUIPO_1` y `TAG_EQUIPO_2` (forman una clave primaria compuesta y actúan como claves foráneas hacia los equipos y el partido).




* **`ESTADISTICAS_JUGADOR`**: Almacena el rendimiento individual de cada competidor durante un partido específico.
* *Campos:* `ID_PARTIDO`, `ID_JUGADOR` y `TAG_EQUIPO` (clave primaria compuesta/foránea), junto con métricas como `BAJAS`, `MUERTES`, `ASISTENCIAS` y `DAÑO`.





---

### 2. Relaciones Principales

* **Videojuegos y Torneos:** Un videojuego puede tener múltiples torneos asociados a lo largo del tiempo, mientras que cada torneo pertenece a un único juego (relación de 1 a N).


* **Torneos y Partidos:** Un torneo se compone de múltiples partidos programados (relación de 1 a N).


* **Equipos y Jugadores:** Una organización (`EQUIPOS`) cuenta con varios integrantes (`JUGADORES`), vinculados mediante una clave foránea.


* **Enfrentamientos y Rendimiento:** La tabla `DETALLES_PARTIDO` conecta los encuentros con los equipos contendientes, y la tabla `ESTADISTICAS_JUGADOR` permite registrar de manera precisa la participación y desempeño de cada jugador en dicho partido.
<img width="1267" height="922" alt="EJERCICIO 15-MR drawio" src="https://github.com/user-attachments/assets/54664ad8-091f-4671-a1ba-029e96088faf" />

Este diagrama muestra el **proceso completo de normalización de bases de datos** aplicado a un sistema de gestión de torneos de videojuegos (*eSports*), transformando una tabla inicial desnormalizada (Forma Normal 0) hasta llegar al diseño relacional optimizado en Tercera Forma Normal (3FN).

A continuación se detalla cada una de las fases representadas:

### 1. Fase Normal 0 (FN0) — Tabla No Normalizada

* **Qué se hace:** Se parte de una **única tabla masiva y plana** que concentra toda la información mezclada: datos del torneo, videojuego, partidas disputadas y grupos repetitivos anidados en una misma celda (como los equipos participantes y las estadísticas individuales de cada jugador).
* **Problema:** Contiene colecciones de datos multivaluados y anidados dentro de las celdas, lo que provoca una gran redundancia (repitiendo constantemente los datos del torneo y del juego en cada fila) e impide realizar consultas eficientes o mantener la integridad de los datos.

---

### 2. Primera Forma Normal (1FN)

* **Qué se hace:** Se eliminan los grupos repetitivos y los valores anidados, asegurando que **todos los atributos sean atómicos** (cada celda contiene un único dato indivisible). Se separan los equipos enfrentados en columnas independientes (`Tag_Eq1`, `Tag_Eq2`) y se definen claves primarias compuestas (`ID_TORNEO`, `ID_PARTIDO`, `ID_JUGADOR`).
* **Resultado:** Se obtiene una tabla tabular plana (`TABLA PLANTILLA`) donde cada fila representa el desempeño de un jugador en un partido específico, aunque sigue arrastrando mucha duplicidad de datos generales.

---

### 3. Segunda Forma Normal (2FN)

* **Qué se hace:** Se parte de la 1FN y se eliminan las **dependencias parciales**, separando aquellos atributos que no dependen de la clave primaria completa sino únicamente de una parte de ella:
* **`TABLA VIDEOJUEGO`**: Aísla los datos propios del juego (`ID_JUEGO`, nombre, género, desarrollador).
* **`TABLA TORNEO`**: Registra los campeonatos y sus fechas, vinculándolos al videojuego correspondiente mediante clave foránea.
* **`TABLA PARTIDO`**: Contiene la programación de cada encuentro, fecha, hora y equipo ganador, asociado a un torneo.
* **`TABLA JUGADORES`**: Centraliza el perfil de cada competidor (`ID_JUGADOR`, nombre real, *nickname*, nacionalidad).
* **`TABLA ESTADISTICAS_JUGADOR`**: Registra las métricas de rendimiento (bajas, muertes, asistencias, daño) vinculadas directamente a la clave compuesta del partido y el jugador.



---

### 4. Tercera Forma Normal (3FN)

* **Qué se hace:** Se eliminan las **dependencias transitivas**, asegurando que ningún atributo no clave dependa de otro atributo no clave, y resolviendo las relaciones de muchos a muchos. El sistema queda completamente desglosado en siete tablas especializadas e interconectadas:
* **`TABLA EQUIPOS`**: Se crea una entidad propia para las organizaciones de *eSports* (`TAG_EQUIPO`, razón social, país, fecha de fundación, cantidad de jugadores), eliminando la repetición de sus datos en los partidos y jugadores.
* **`TABLA JUGADORES`**: Mantiene los datos personales del competidor y lo vincula a su organización mediante la clave foránea `EQUIPO (FK)`.
* **`TABLA DETALLES_PARTIDO`**: Resuelve de forma limpia qué equipos se enfrentaron en cada encuentro (`ID_PARTIDO`, `TAG_EQUIPO_1`, `TAG_EQUIPO_2`), reemplazando los campos sueltos de las fases anteriores.
* **`VIDEOJUEGO`**, **`TORNEO`**, **`TABLA PARTIDO`** y **`TABLA ESTADISTICAS_JUGADOR`**: Consolidan la estructura relacional definitiva mediante claves foráneas (FK), garantizando la integridad referencial y evitando anomalías de actualización.
<img width="4632" height="2907" alt="EJERCICIO 15-FASES DE NORMALIZACION drawio" src="https://github.com/user-attachments/assets/0d09d8ea-ce23-434a-9880-577d38656ac5" />


