# Renascor Hub

Un solo sitio donde veo en qué quedó cada proyecto. No escribo nada nuevo ahí: el Hub lee lo que ya existe en disco (repositorios, checkpoints, bitácoras y el tablero de tareas) y lo pone en orden.

![Inicio](media/01-inicio.png)

## El problema que me harté de resolver a mano

Dieciocho proyectos y 643 documentos repartidos en carpetas. Cada vez que volvía a uno tenía que contestar la misma pregunta: ¿en qué quedó esto? La respuesta salía de `grep`, de abrir tres archivos y de mi memoria, que es el peor de los tres.

GitHub no lo resuelve, porque un repo privado guarda código, no la historia de las decisiones. Y montar un Notion en paralelo obliga a mantener todo a mano, con lo que en una semana tienes dos fuentes de verdad y ninguna confiable.

Así que la decisión fue la contraria a la obvia: **no crear contenido nuevo, indexar el que ya está.**

## Qué hace

Lo primero es un buscador de verdad. Uso FTS5, el índice de texto completo que ya trae SQLite: indexa las palabras de cada documento y encuentra en un instante lo que escaneando archivos tarda segundos. 643 documentos, una consulta, sin depender de ninguna API externa.

Lo segundo es cómo decide en qué estado está cada proyecto. No se lo inventa: lee señales reales, o sea si hay carpeta en disco, si hay un `.git` con historial, si hay servicios desplegados y si hay documentación asociada. Cuando no encuentra evidencia suficiente lo marca como `UNKNOWN` o `LAB` y se queda así. Un tablero que dice "en progreso" porque no sabe es peor que no tener tablero.

También detecta documentos duplicados, lee las fechas del encabezado de cada archivo, y en el mismo sitio muestra checkpoints por proyecto, decisiones registradas, actividad reciente y las tareas abiertas contra las hechas. Todo eso tiene API JSON, con endpoints para búsqueda, estadísticas, contenido crudo y favoritos.

![Proyectos](media/02-proyectos.png)

## Cuatro decisiones que explican por qué el código es así

**Es de solo lectura.** El Hub no edita, no crea, no mueve archivos. Escribe únicamente en su propia base de datos. Es un visor, y esa restricción es lo que hace que no pueda corromper la fuente de verdad.

**Revisa secretos antes de mostrar nada.** Antes de exponer un documento busca dentro de su contenido: llaves privadas, tokens de GitHub, JWT, credenciales de AWS o de Slack. Si encuentra algo, excluye el archivo entero y solo anota la ruta y el motivo, nunca el valor. Un portal que indexa mi carpeta personal no puede ser la puerta por la que se filtren las llaves.

**FTS5 en lugar de `grep`.** Arriba lo dije: buscar escaneando archivos tarda segundos, y con el índice invertido es instantáneo y se puede ordenar por relevancia.

**Un proceso, sin colas.** Corre como servicio `systemd` con Flask en modo producción, sin workers ni Redis. Para un equipo de una persona, la complejidad operativa es el enemigo; cada pieza que agregas es una pieza más que se cae a las tres de la mañana.

## Stack

Python 3, Flask, SQLite FTS5, Jinja2, JavaScript sin framework y CSS propio. Alrededor de 1,100 líneas de Python y 10 plantillas, con dos suites de comprobación: una validación funcional de punta a punta y QA visual con Playwright en escritorio y móvil.

![Productividad](media/03-productividad.png)

## Estado

Funcional y en uso diario desde septiembre de 2026. Indexa los 18 proyectos y actualiza el índice sobre la marcha. Los endpoints `/health` y `/api/stats` dicen cómo va el índice, cuántos duplicados encontró y cuántos documentos quedaron excluidos por seguridad.

## Sobre el código

Este repositorio es documentación de exhibición: describe el sistema, sus decisiones y su interfaz. El código fuente no se distribuye.

© 2026 Javier Cuevas. Todos los derechos reservados. Disponible bajo licencia por contacto.
