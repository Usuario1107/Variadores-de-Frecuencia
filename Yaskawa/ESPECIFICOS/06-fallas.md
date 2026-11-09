# Guía Completa de Fallas, Alarmas y Diagnóstico
**Equipo:** Variador de Frecuencia Yaskawa GPD 515 (CIMR-G5M4018)

Este documento detalla cómo el variador notifica los problemas, cómo leer el historial de fallas y un listado completo de los códigos de error más comunes, incluyendo sus causas probables y soluciones basadas en el manual técnico.

---

## 1. ¿Cómo avisa el equipo? (Clasificación de Fallas)
Según la Sección 6.1 (Página 204) del manual, el variador clasifica los problemas en tres niveles. El motor reaccionará de forma distinta según el nivel:

1.  **Alarma (Alarm):** El código **parpadea** en la pantalla. El variador **sigue funcionando** y el motor no se detiene. Es una advertencia temprana (ej. temperatura elevada, pero no crítica).
2.  **Falla Menor (Minor Fault):** El código **parpadea** en la pantalla. El variador **sigue funcionando**, pero activa un relé de salida (si está configurado) para avisar a un PLC o panel de control que algo anda mal externamente (ej. un sensor externo se activó).
3.  **Falla Mayor (Major Fault):** El código aparece **fijo** en la pantalla. El variador **se detiene inmediatamente** (el motor queda libre por inercia). Requiere presionar el botón **RESET** para reiniciar.

### ¿Dónde puedo ver qué pasó? (Herramienta de Diagnóstico)
Antes de buscar la falla, el variador guarda la información en su "caja negra":
*   **`U3` (Fault History):** Muestra las últimas 4 fallas. (Sección 6.3, Pág. 210).
*   **`U2` (Fault Trace):** Muestra la corriente, voltaje y frecuencia exactos en el momento del disparo. (Sección 6.3, Pág. 210).

---

## 2. Tabla de Fallas Comunes, Causas y Soluciones

| Código | Nombre de la Falla | Tipo | Posibles Causas | Solución Sugerida | Ref. Manual |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`OH`** | **Sobrecalentamiento del Disipador** | Falla Mayor | Ventilador trabado, polvo en el disipador, temperatura ambiente > 45°C. | Limpiar disipador, revisar que el ventilador gire libremente, mejorar ventilación del tablero. | Sec. 6.4, Pág. 224 (Chart 6.10) |
| **`OL1`** | **Sobrecarga del Motor** | Falla Mayor | Carga mecánica muy pesada, motor atascado, parámetro `E2-01` mal configurado. | Revisar si la máquina está trabada. Verificar que `E2-01` tenga la corriente de placa exacta. | Sec. 6.4, Pág. 222 (Chart 6.8) |
| **`OC`** | **Sobrecorriente** | Falla Mayor | Cortocircuito en cables del motor, rampa de aceleración muy corta (`C1-01`), carga con mucha inercia. | Revisar aislamiento del motor. Aumentar tiempo de aceleración `C1-01`. | Sec. 6.4, Pág. 221 (Chart 6.7) |
| **`OV`** | **Sobretensión en Bus DC** | Falla Mayor | Rampa de desaceleración muy corta (`C1-02`), motor devuelve energía al frenar (carga con inercia). | Aumentar tiempo de desaceleración `C1-02`. Instalar resistencia de frenado si es necesario. | Sec. 6.4, Pág. 219 (Chart 6.5) |
| **`UV1`** | **Subtensión en Bus DC** | Alarma/Falla | Caída de tensión en la red, falso contacto en L1/L2/L3, condensadores agotados. | Medir voltaje de entrada, apretar terminales. Si es viejo, evaluar cambio de condensadores. | Sec. 6.4, Pág. 223 (Chart 6.9) |
| **`PF`** | **Pérdida de Fase de Entrada** | Falla Mayor | Falta una fase en L1, L2 o L3, fusible de entrada quemado. | Revisar fusibles y continuidad en las 3 fases de entrada. Activar `L8-05=1`. | Sec. 6.1, Pág. 208 |
| **`LF`** | **Pérdida de Fase de Salida** | Falla Mayor | Cable roto hacia el motor, mal contacto en T1/T2/T3. | Revisar continuidad de los cables del motor (U, V, W). | Sec. 6.1, Pág. 206 |
| **`GF`** | **Falla a Tierra** | Falla Mayor | Cable del motor derivado a tierra, humedad en el motor. | Medir aislamiento del motor (Megger). Revisar cables pelados. | Sec. 6.1, Pág. 206 |

---

## 3. Enfoque en "Funciona un rato y falla" (Fallas Intermitentes)

Si el equipo arranca bien, trabaja 10, 20 o 30 minutos y de repente se apaga, las causas suelen ser físicas o térmicas. Aquí el análisis detallado:

### A. Falla `OH` (Sobrecalentamiento) - *El sospechoso #1*
*   **¿Por qué pasa?** El variador está trabajando normal, pero los transistores de potencia (IGBTs) generan calor. Si el ventilador inferior está fallando (rodamientos secos) o el disipador de aluminio está tapado de polvo, el calor no se disipa. Al llegar a ~95°C (parámetro `L8-02`), el sensor dispara la falla.
*   **Cómo solucionarlo:** Con el equipo apagado y el LED "CHARGE" apagado, gira el ventilador con la mano. Si está duro, cámbialo. Sopla el disipador con aire comprimido seco.

### B. Falla `OL1` (Sobrecarga del Motor) - *El sospechoso #2*
*   **¿Por qué pasa?** El variador calcula internamente la temperatura del motor basándose en la corriente (`E2-01`) y el tiempo (`L1-02`, por defecto 8 minutos). Si la máquina empieza a exigir más fuerza (ej. la banda transportadora se va llenando de material, o un rodamiento se calienta), el motor consume más corriente. Después de 8 minutos de exceso, el variador se apaga para no quemar el motor.
*   **Cómo solucionarlo:** Entra a `U2-05` (Corriente de la falla) y compara con `E2-01`. Si la corriente es muy alta, el problema es mecánico (carga pesada). Si la corriente es normal (ej. 20A) pero el equipo falla, es porque `E2-01` está mal configurado (ej. pusieron 15A en lugar de 41A).

### C. Falla `UV` (Subtensión) - *El sospechoso #3*
*   **¿Por qué pasa?** Al calentarse, los condensadores internos del Bus DC pierden capacidad. Cuando el motor exige fuerza, el voltaje DC cae por debajo del nivel seguro (380V DC) y el equipo se apaga para no descontrolarse. También puede ser un contactor de entrada que se calienta y se abre.
*   **Cómo solucionarlo:** Entra a `U1-07` (Voltaje Bus DC) mientras el equipo está trabajando. Si ves que el voltaje empieza a bajar drásticamente después de un rato, el variador está en las últimas (condensadores agotados por la edad) y debe reemplazarse.

### D. Falla `OC` (Sobrecorriente)
*   **¿Por qué pasa?** Ocurre casi instantáneamente cuando la corriente supera el 200% del valor nominal (aprox. 82 Amperios). Si pasa "después de un rato" puede ser porque la carga mecánica se atasca de repente, o porque hay un cortocircuito intermitente en los cables del motor que solo se manifiesta cuando el cable se calienta y se dilata.
*   **Cómo solucionarlo:** Revisar las conexiones del motor y el aislamiento de los cables. Aumentar el tiempo de aceleración si es que falla al arrancar una carga pesada.

---

## 4. Recomendaciones Generales de Seguridad
1.  **Nunca** hacer un puente entre los terminales de potencia para "saltarse" una falla. Si el equipo se apaga, es porque detectó un peligro real.
2.  Antes de tocar cualquier terminal, verificar que el LED **"CHARGE"** esté totalmente apagado.
3.  Si el equipo requiere un reset constante porque se dispara, documentar el código de falla (U3) antes de resetear. Una vez reseteado, la falla actual desaparece, pero queda en el historial.
