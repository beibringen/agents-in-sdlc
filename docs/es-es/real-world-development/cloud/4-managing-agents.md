---
title: "Ejercicio 4 - Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los últimos ejercicios has pedido al agente de Copilot en la nube que realice tres tareas distintas para mejorar la experiencia de usuario y añadir funciones. Aunque trabaja de forma autónoma y asíncrona, es importante poder supervisar las tareas.

Hay varias herramientas para gestionar las tareas asignadas al agente, incluida la [página de agentes][agents-page] de GitHub.com. Desde este centro de control puedes ver las tareas de los agentes que tienen solicitudes de incorporación de cambios abiertas, consultar las operaciones realizadas y orientar las sesiones en curso.

En esta lección:

- explorarás la página de agentes para supervisar las tareas.
- orientarás una sesión en curso para solicitar una función adicional.

## Escenario

Después de asignar al agente la implementación del modo de alto contraste, el equipo ha decidido que también convendría añadir un modo claro. Como ya se están actualizando los estilos y se está añadiendo un selector, quieres orientar el trabajo del agente para que incluya ambos modos.

## Revisa las tareas del agente de Copilot en la nube

Veamos el estado de las tareas asignadas al agente.

1. Abre la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Revisa las tareas que aparecen en el panel principal y en el panel de la izquierda. Deberías ver las incidencias asignadas, como:
   - añadir documentación al código.
   - generar API para modificar juegos.
   - añadir el modo de alto contraste al sitio.
3. Selecciona una tarea activa y revisa las acciones que ha realizado Copilot, por ejemplo, descargar el código, preparar el entorno, configurar servidores MCP y completar tareas.

> [!NOTE]
> Los pasos concretos dependerán del estado de la tarea y del enfoque que siga Copilot.

4. Revisa también el panel de solicitudes de incorporación de cambios de la derecha, que permite consultar la PR y los archivos modificados.

## Orienta al agente en la nube

Ahora que has visto las tareas activas, pídele al agente que añada el selector de modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con el modo de alto contraste. El título exacto dependerá de Copilot y del estado de la tarea.

    ![Sesión de accesibilidad en mission control](../../../_images/ex6-accessibility-session.png)

2. Espera unos minutos, hasta que el agente termine la configuración y empiece a trabajar. Verás mensajes que indican que ha iniciado la tarea.
3. En el diálogo **Steer active session while Copilot is working**, escribe:

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Tarea del agente en la nube con el diálogo para orientar la sesión activa resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar la indicación.
5. Observa cómo Copilot reconoce la indicación y la incorpora al flujo de trabajo.

## Deja trabajar a Copilot

Copilot incorporará la nueva solicitud al terminar el paso que estaba realizando cuando recibió el mensaje. El proceso tardará varios minutos; puedes aprovechar ese tiempo para repasar lo aprendido.

## Resumen y pasos siguientes

Has explorado la página de agentes de Copilot, el centro de control para supervisar y orientar las tareas del agente en la nube. Permite seguir las tareas activas y completadas, revisar el trabajo y cambiar el alcance de las sesiones en curso.

En esta lección:

- has explorado el centro de control y la página de agentes.
- has redirigido una sesión en curso para solicitar una función adicional.

Cuando Copilot termine el trabajo de accesibilidad, podrás [iterar sobre las solicitudes de incorporación de cambios que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Lección anterior: Agentes personalizados][previous-lesson] | [Siguiente lección: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
