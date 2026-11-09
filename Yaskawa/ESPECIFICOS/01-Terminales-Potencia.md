# Guía de Terminales y Protecciones: Variador GPD 515

Este documento detalla la función de los terminales de potencia, las entradas de control y los elementos de protección del variador de frecuencia Yaskawa GPD 515 (CIMR-G5M4018).

---

## Sección 1: Terminales de Potencia (Circuito Principal)

El variador recibe energía alterna (AC) de la red, la convierte en continua (DC) para almacenarla, y finalmente la vuelve a convertir en alterna (AC) con frecuencia y voltaje variables para controlar el motor.

### 1.1. Entrada de Red (L1, L2, L3)
*   **¿Qué son?** Son los terminales donde se conecta la alimentación trifásica principal de la planta (380V - 460V AC).
*   **Importancia:** De aquí se toma la energía para todo el equipo. Es vital que los cables estén bien apretados. Un falso contacto en estos terminales puede causar caídas de tensión bruscas y generar fallas intermitentes de subtensión (`UV1`) cuando el motor exige fuerza.

### 1.2. Salida al Motor (T1, T2, T3)
*   **¿Qué son?** Son los terminales que entregan la energía ya procesada y controlada al motor trifásico.
*   **Regla de oro:** El cable que va desde el variador hasta el motor **no debe exceder los 50 metros**. Si es más largo, se generan picos de voltaje por capacitancia que pueden dañar los transistores de potencia. Además, este cable de potencia nunca debe ir por la misma tubería que los cables de control, para evitar interferencias electromagnéticas.

### 1.3. El Bus DC y Frenado (+1, +2, +3, -)
*   **¿Qué son?** Es el "tanque de energía" interno de corriente directa (DC). Aquí es donde se almacena la energía en grandes condensadores.
*   **Terminales +1 y +2:** Están **puenteados de fábrica** con una barra metálica. Este puente es totalmente normal. Están diseñados para conectar un Reactor DC externo (un filtro de bobina). Si no usas ese filtro, el puente debe permanecer puesto para que la energía fluya.
*   **Terminales +3 y -:** Son el polo positivo y negativo puro del Bus DC. Se usan si se necesita conectar una Unidad de Frenado Externa (para cuando el motor tiene mucha inercia y debe detenerse rápido). 
*   **Voltaje:** Si la red es de 380V AC, aquí habrá aproximadamente **537V DC** (380 x 1.414).

### 1.4. Alimentación del Circuito de Control (Terminales r y s)
*   **¿Qué son?** Son los terminales que alimentan el transformador interno que enciende el "cerebro" del variador (pantalla, tarjeta de control y ventiladores).
*   **Uso normal:** De fábrica, el variador toma un poco de energía de los terminales L1 y L2 (mediante puentes) y la envía a `r` y `s` para encenderse solo.
*   **Uso con Entrada DC:** Si la potencia principal se llegara a alimentar con Corriente Directa en (+3 y -), los terminales L1 y L2 no tendrán voltaje. En ese caso, es **obligatorio** quitar los puentes y alimentar `r` y `s` con 380V AC externos, de lo contrario, el equipo no encenderá.

---

## Sección 2: Elementos de Protección y Seguridad

### 2.1. El Fusible de Control (FU2)
*   **¿Qué es?** Es un fusible cerámico de **600V, 1.5A AC** ubicado en la tarjeta principal. (El manual solo da el código de parte, el amperaje se lee directo en la pieza).
*   **Función:** Protege la electrónica de la tarjeta de control y los ventiladores contra cortocircuitos. 
*   **Diagnóstico:** Si el variador de repente no enciende (pantalla muerta) pero sí llega voltaje a L1 y L2, este fusible es el primer sospechoso. Nunca debe reemplazarse por uno de mayor amperaje, ya que se arriesgaría a quemar toda la tarjeta.

### 2.2. El Selector de Voltaje (Jumper)
*   **¿Qué es?** Es un conector de plástico (jumper) ubicado en la placa, que tiene varias posiciones: 380V, 400/415V, 440V y 460V.
*   **Función:** Le dice al transformador interno del variador qué voltaje va a recibir para que no se queme. 
*   **Importancia:** Debe estar configurado exactamente en la posición que coincida con el voltaje real de la planta (por ejemplo, 380V). Si el voltaje de la planta es 380V pero el jumper está en 460V, el equipo trabajará forzado y podría fallar.

### 2.3. El LED de "CHARGE" (Peligro de Descarga)
*   **¿Qué es?** Es una pequeña luz roja (LED) ubicada en la placa de potencia.
*   **Función de Seguridad:** Indica que los condensadores del Bus DC (+3 y -) todavía tienen energía almacenada. 
*   **Advertencia:** Al apagar el equipo, este LED puede tardar entre 5 y 15 minutos en apagarse. **Nunca** se debe abrir la tapa ni tocar los terminales de potencia (especialmente +3 y -) hasta que esta luz esté completamente apagada, ya que el voltaje residual de DC es letal.
