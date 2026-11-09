# Guía de Puesta en Marcha, Control Local y Diagnóstico de Fallas
**Equipo:** Variador de Frecuencia Yaskawa GPD 515 (CIMR-G5M4018)

Este documento detalla el procedimiento para configurar, operar desde la consola (HMI) y diagnosticar el variador al conectarlo a un motor trifásico de 380V / 50Hz.

---

## 1. Parametrización Completa para Motor 380V / 50Hz

Aunque el variador es de la clase 460V (según su placa), es totalmente compatible con redes y motores de 380V. Para evitar sobrecalentamiento y pérdida de torque, es **obligatorio** ajustar la curva de Voltaje/Frecuencia (V/f) para que coincida exactamente con la placa del motor.

*(Referencia: Sección 2.4, Páginas 65-68 y Sección 5.48, Páginas 195-196 del manual)*

| Parámetro | Nombre | Valor a Configurar | Explicación |
| :--- | :--- | :--- | :--- |
| **`A1-02`** | Método de Control | **`2`** (Open Loop Vector) | Recomendado para mejor torque. Si el motor es viejo o no se puede autotunear, usar `0` (V/f). |
| **`E1-01`** | Voltaje de Entrada | **`380`** V | Debe coincidir con el voltaje de la red eléctrica de la planta. |
| **`E1-04`** | Frecuencia Máxima | **`50.0`** Hz | Límite superior de velocidad del motor. |
| **`E1-05`** | Voltaje Máximo | **`380`** V | **CRÍTICO:** Evita que el variador intente entregar 460V al motor a 50Hz. |
| **`E1-06`** | Frecuencia Base | **`50.0`** Hz | Frecuencia a la cual el motor alcanza su voltaje nominal. |
| **`E2-01`** | Corriente del Motor | ***(Ver placa)*** | Amperaje nominal (FLA) exacto del motor. Protege contra sobrecarga (`OL1`). |
| **`E2-02`** | Deslizamiento | ***(Calculado)*** | Diferencia entre RPM síncronas y nominales. Se calcula con la fórmula del manual. |
| **`E2-03`** | Corriente en Vacío | ***(Calculado)*** | Usualmente 30% a 50% de la corriente nominal. |
| **`E2-04`** | Número de Polos | **`4`** | Asumiendo un motor estándar de 4 polos (ej. 1450 RPM a 50Hz). |
| **`L1-01`** | Protección Térmica | **`1`** (Habilitado) | Activa el relé térmico electrónico interno. |
| **`L1-02`** | Tiempo Protección | **`8.0`** min | Tiempo de tolerancia a sobrecarga antes de disparar la falla `OL1`. |

> **Nota de Seguridad:** Una vez configurados estos parámetros, se recomienda ejecutar el **Auto-Tuning** (Sección 2.2a, Páginas 48-50) con el motor desconectado de la carga mecánica para que el variador calcule automáticamente los parámetros `E2-05` al `E2-08`.

---

## 2. Control por Pantalla (HMI) - Modo Local

Para realizar pruebas de funcionamiento sin necesidad de cables externos (PLC o botones), el variador debe operar en modo "Local".

*(Referencia: Sección 2.5, Páginas 69-71 y Sección 4.2, Páginas 76-77 del manual)*

1.  **Cambiar a Modo Local:** Presionar el botón **LOCAL/REMOTE**. Los LEDs **SEQ** y **REF** (arriba a la derecha) deben apagarse. Esto indica que el teclado tiene el control.
2.  **Ajustar Frecuencia:** Presionar **MENU** > **DATA/ENTER**. En la pantalla aparecerá `U1-01` (Frequency Ref). Usar las flechas **▲/▼** para fijar la velocidad (ej. `30.00 Hz`) y presionar **DATA/ENTER** para guardar.
3.  **Arrancar:** Presionar **RUN** (Botón verde). El motor acelerará según la rampa `C1-01`.
4.  **Cambiar Sentido:** Presionar **FWD/REV** mientras el motor corre para invertir la rotación.
5.  **Detener:** Presionar **STOP** (Botón rojo). El motor se detendrá según la rampa `C1-02`.
6.  **Prueba Jog:** Con el motor detenido, mantener presionado **JOG**. El motor girará a baja velocidad (por defecto 6 Hz) solo mientras se mantenga presionado.

---

## 3. Copia de Seguridad, Restauración y Reset de Fábrica

El variador permite guardar una copia exacta de la configuración para evitar reprogramar todo manualmente en caso de fallas o cambios de tarjeta.

*(Referencia: Sección 5.37, Páginas 170-171 y Sección 5.46, Página 193 del manual)*

*   **Guardar Copia de Seguridad (Backup):**
    Ir al parámetro **`o2-03`** (User Parameter Default Value) y configurarlo en **`1`** (Set default). El equipo guardará hasta 50 parámetros modificados en su memoria no volátil. Una vez guardado, el parámetro vuelve a `0` automáticamente.
*   **Restaurar Copia de Seguridad:**
    Ir al parámetro **`A1-03`** (Initialize) y configurarlo en **`1110`** (User Initialize). El equipo cargará los valores guardados previamente.
*   **Reset a Valores de Fábrica (Borrado Total):**
    *   **`A1-03 = 2220`:** Restablece el equipo a fábrica para control a 2 hilos (interruptor).
    *   **`A1-03 = 3330`:** Restablece el equipo a fábrica para control a 3 hilos (botones Start/Stop).
    *   **⚠️ ADVERTENCIA:** Usar `2220` o `3330` borra permanentemente la copia de usuario guardada con `o2-03`. El parámetro `1110` ya no funcionará después de esto.

---

## 4. Clasificación y Diagnóstico de Fallas

El manual clasifica los problemas en tres categorías principales según el comportamiento del equipo.

*(Referencia: Sección 6.1, Páginas 204-208 del manual)*

### A. Tipos de Indicaciones en Pantalla
1.  **Alarma (Parpadea):** Advertencia. El variador **sigue funcionando**. (Ej. Sobrecalentamiento inminente).
2.  **Falla Menor (Parpadea):** Problema externo o de menor gravedad. El variador **sigue funcionando**, pero activa un relé de advertencia.
3.  **Falla Mayor (Fijo en pantalla):** Problema grave. El variador **se detiene inmediatamente** (el motor queda por inercia) y requiere reset.

### B. Códigos de Falla Más Comunes (Ordenados por Frecuencia)

*   **`OH` (Heatsink Overtemp):** Sobretemperatura del disipador. Causas: Ventilador trabado, polvo en las aletas, temperatura ambiente > 45°C.
*   **`OL1` (Motor Overloaded):** El motor está consumiendo demasiada corriente. Causas: Carga mecánica muy pesada, parámetro `E2-01` mal configurado, o motor atascado.
*   **`OC` (Overcurrent):** Sobrecorriente instantánea (supera el 200%). Causas: Cortocircuito en el motor/cables, rampa de aceleración muy corta, o intento de arrancar una carga con mucha inercia.
*   **`OV` (Overvoltage):** Sobretensión en el bus DC (aprox. 800V). Causas: Rampa de desaceleración muy corta, o motor con mucha inercia devolviendo energía al frenar.
*   **`UV1` (Undervoltage):** Caída de tensión en el bus DC. Causas: Falso contacto en L1/L2/L3, caída de la red eléctrica, o condensadores degradados.
*   **`EF3` / `EF4` (External Fault):** El terminal 3 o 4 se abrió/cerró (por un PLC o sensor externo).
*   **`PF` (Input Phase Loss):** Falla de una de las fases de entrada (L1, L2 o L3).

### C. Herramienta de Diagnóstico: Historial de Fallas
Para saber exactamente qué pasó cuando el equipo falló sin estar presente, ir a:
**`MENU` > `U3` (Fault History) > `DATA/ENTER`**
*   `U3-01`: Última falla.
*   `U3-02`: Penúltima falla.
*   `U3-03` / `U3-04`: Fallas anteriores.
*   *(Usar las flechas para ver la corriente y frecuencia exactas que había al momento del disparo).*
