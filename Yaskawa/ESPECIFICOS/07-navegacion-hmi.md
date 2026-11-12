# Guía Técnica de Navegación: Interfaz HMI (Digital Operator JVOP-130)

Este documento describe la estructura, configuración y lógica de navegación de la consola de operación (HMI) del variador de frecuencia Yaskawa GPD 515. La información se presenta en tercera persona y está basada en las secciones 4.2, 4.3 y 4.5 del manual técnico.

---

## 1. Descripción del Hardware de la Interfaz
La consola de operación (modelo JVOP-130) es el medio principal de interacción entre el usuario y el variador. Sus componentes principales son:

*   **Pantalla LCD:** Consta de 2 líneas de 16 caracteres alfanuméricos, utilizada para mostrar datos en tiempo real, códigos de falla o parámetros de configuración.
*   **Indicadores LED:** Ubicados en la parte superior, informan sobre el estado operativo (DRIVE, FWD, REV, REMOTE SEQ, REMOTE REF).
*   **Teclado (Keypad):** Conjunto de botones divididos en funciones de control (RUN, STOP), navegación (MENU, ESC, flechas) y edición (DATA/ENTER, RESET).

---

## 2. Configuración de Idioma
El equipo se suministra de fábrica en idioma inglés. Para configurar la interfaz en español, es necesario acceder al menú de inicialización y modificar el siguiente parámetro:

*   **Parámetro `A1-00` (Select Language):** Debe ajustarse al valor **`5`** (Spanish). Una vez aplicado el cambio, la interfaz mostrará los menús en español.

---

## 3. Lógica de Navegación y Modos de Operación
El variador organiza su software en diferentes "Modos" o niveles de menú. El indicador LED **DRIVE** es el principal referente visual:
*   **LED DRIVE Encendido:** El equipo se encuentra en Modo Operación (listo para arrancar o mostrar datos de monitoreo).
*   **LED DRIVE Apagado:** El equipo se encuentra en Modo Programación o menús internos.

Para ingresar a la configuración, se debe presionar la tecla **MENU**. Esto desplegará el Menú Principal, el cual consta de las siguientes opciones principales (dependiendo del nivel de acceso configurado):

1.  **Operation (Operación):** Pantalla principal de datos (frecuencia, corriente).
2.  **Initialize (Inicializar):** Agrupa los parámetros de configuración global (Grupo A), incluyendo idioma, nivel de acceso y contraseña.
3.  **Programming (Programación):** Agrupa los parámetros de control y operación (Grupos b, C, d, E, H, L, o).
4.  **Auto-Tuning (Sintonización Automática):** Rutina guiada para ajustar el variador al motor.
5.  **Modified Constants (Parámetros Modificados):** Muestra únicamente los parámetros que han sido alterados de su valor de fábrica.

---

## 4. Procedimiento de Navegación y Edición de Parámetros

El siguiente procedimiento detalla el flujo estándar para navegar y modificar un parámetro:

1.  Presionar la tecla **MENU** para acceder al Menú Principal.
2.  Utilizar las teclas de flecha (**▲ / ▼**) para seleccionar el modo deseado (ej. *Programming*) y presionar **DATA/ENTER**.
3.  Utilizar las flechas para seleccionar el grupo de parámetros (ej. Grupo *E*) y presionar **DATA/ENTER**.
4.  Utilizar las flechas para seleccionar el parámetro específico (ej. `E2-01`) y presionar **DATA/ENTER**.
5.  En la pantalla se mostrará el valor actual del parámetro. Para modificarlo, presionar **DATA/ENTER** nuevamente. El primer dígito del valor comenzará a parpadear.
6.  Utilizar las teclas de flecha para cambiar el número. Presionar la tecla **RESET** (◄) para mover el cursor hacia la izquierda si es necesario.
7.  Una vez ingresado el nuevo valor, presionar **DATA/ENTER** para guardarlo en memoria. La pantalla mostrará brevemente "Entry Accepted" (Entrada Aceptada).
8.  Para regresar a la pantalla principal de operación, presionar **MENU** repetidamente hasta llegar a *Operation*, o presionar **ESC** varias veces.

---

## 5. Niveles de Acceso (Parameter Access Level)
La visibilidad de los parámetros en el menú de navegación está determinada por el parámetro **`A1-01`**. Si el usuario no logra encontrar un parámetro específico en el menú, se debe verificar este ajuste:

*   `0` (Monitor Only): Solo visualización de datos.
*   `1` (User Program): Muestra solo parámetros previamente seleccionados en la lista `A2` (máximo 32).
*   `2` (Quick-start): Nivel de fábrica. Muestra 32 parámetros básicos.
*   `3` (Basic): Muestra parámetros de nivel intermedio.
*   `4` (Advanced): Muestra la totalidad de los parámetros disponibles en el equipo.
