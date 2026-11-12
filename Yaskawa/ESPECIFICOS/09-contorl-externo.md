# Guía de Conexión y Parametrización: Selector de 3 Posiciones (ON-OFF-ON) y Potenciómetro de 10k

**Equipo:** Variador de Frecuencia Yaskawa GPD 515 (CIMR-G5M4018)
**Objetivo:** Controlar la marcha/paro/dirección con un selector de 3 posiciones (2 hilos) y la velocidad con un potenciómetro de 10k.

---

## 1. Conexión Física (Hardware)

Para esta configuración, se utilizará el método de control a **2 Hilos** (contacto mantenido) y una referencia analógica de voltaje.

### A. Conexión del Selector de 3 Posiciones (ON-OFF-ON)
*   **Terminal 11 (Común Digital):** Se conecta al pin central del selector.
*   **Terminal 1 (Marcha Adelante - FWD):** Se conecta a un extremo del selector (ON).
*   **Terminal 2 (Marcha Atrás - REV):** Se conecta al otro extremo del selector (ON).
*   **Lógica:** 
    *   Palanca arriba = Cierra 11 con 1 (Gira Adelante).
    *   Palanca centro (OFF) = Abre ambos (Motor detenido).
    *   Palanca abajo = Cierra 11 con 2 (Gira Atrás).

### B. Conexión del Potenciómetro de 10k
*   **Terminal 15 (+15V DC):** Se conecta a un extremo de la resistencia del potenciómetro.
*   **Terminal 17 (Común Analógico - 0V):** Se conecta al otro extremo de la resistencia.
*   **Terminal 13 (Referencia Automática):** Se conecta al pin central (Wiper) del potenciómetro.
*   *Nota:* El terminal 13 acepta 0-10V. Como el terminal 15 entrega 15V, la perilla podría entregar hasta 15V. El variador se calibrará mediante software (Parámetro `H3-02`) para que 10V (o el voltaje máximo de la perilla) equivalgan a la velocidad máxima deseada.

*(Referencia del Manual: Página 5, "Example 2: Remote Sequence (2-Wire)"; Página 40, "Figure 1-3. Interconnections - 2-Wire Control").*

---

## 2. Parametrización (Software)

Una vez realizado el cableado, se debe configurar el variador para que acepte estas señales.

### Paso 1: Inicialización para Control a 2 Hilos
Para que el equipo entienda que los terminales 1 y 2 son un interruptor mantenido y no botones momentáneos.
1.  Ir al parámetro **`A1-03`** (Initialize Parameters).
2.  Configurar en **`2220`** (2-Wire Control Initialization). Presionar `DATA/ENTER`.
    *   *(Advertencia: Esto reinicia algunos parámetros a fábrica. Si tienes configuraciones previas, anótalas).*

### Paso 2: Selección de la Fuente de Comando y Referencia
1.  Ir al parámetro **`b1-01`** (Reference Source). Configurar en **`1`** (Terminals). Esto le dice al variador que lea la velocidad del potenciómetro.
2.  Ir al parámetro **`b1-02`** (Run Source). Configurar en **`1`** (Terminals). Esto le dice al variador que lea la marcha/paro del selector.

### Paso 3: Configurar la Entrada Analógica (Terminal 13)
1.  Ir al parámetro **`H3-01`** (Auto Speed Reference Signal Level Selection). 
2.  Configurar en **`0`** (0 a 10 VDC). *(Si el equipo leyera voltajes negativos, se pondría 1, pero para un potenciómetro simple se usa 0).*

### Paso 4: Calibración de la Velocidad (Ganancia y Bias)
Debido a que el potenciómetro está alimentado por 15V y no por 10V, es necesario ajustar los parámetros de ganancia y bias para que el rango de la perilla coincida con el rango de frecuencia del motor.
1.  Ir al parámetro **`H3-02`** (Frequency Reference Gain - Term. 13). Por defecto es `100.0%`.
2.  Ir al parámetro **`H3-03`** (Frequency Reference Bias - Term. 13). Por defecto es `0.0%`.

**Procedimiento de Calibración (Sección 5.18, Página 116):**
1.  Poner el potenciómetro al mínimo (0%). El motor debe estar detenido.
2.  Poner el potenciómetro al máximo (100%).
3.  Si la pantalla (U1-01) marca más de la frecuencia máxima deseada (ej. 60 Hz), reducir el valor del parámetro **`H3-02`** (por ejemplo, a 66% o 70%) hasta que la pantalla marque exactamente la frecuencia máxima (ej. 50.0 Hz o 60.0 Hz).

*(Referencia del Manual: Página 116, "5.18 FREQUENCY REFERENCE BIAS/GAIN"; Página 118, "5.19 FREQUENCY REFERENCE INPUT SIGNALS").*

---

## 3. Resumen de Parámetros a Configurar

| Parámetro | Función | Valor a Configurar | Página del Manual |
| :--- | :--- | :--- | :--- |
| **`A1-03`** | Inicialización del equipo | `2220` (2-Wire) | Pág. 170 |
| **`b1-01`** | Fuente de Referencia de Velocidad | `1` (Terminales) | Pág. 237 |
| **`b1-02`** | Fuente de Marcha/Paro | `1` (Terminales) | Pág. 237 |
| **`H3-01`** | Tipo de señal en Terminal 13 | `0` (0-10 VDC) | Pág. 253 |
| **`H3-02`** | Ganancia (Ajuste de Velocidad Máxima) | `100.0` (o ajustar según prueba) | Pág. 253 |
| **`H3-03`** | Bias (Ajuste de Velocidad Mínima) | `0.0` (o ajustar según prueba) | Pág. 253 |

---

## 4. Prueba de Funcionamiento

1.  Energizar el variador.
2.  Verificar que los LEDs **SEQ** y **REF** estén encendidos (Modo Remoto).
3.  Girar el potenciómetro a un 50%.
4.  Mover el selector a la posición "Adelante" (ON). El motor debe arrancar y acelerar hasta la mitad de su velocidad.
5.  Mover el selector al centro (OFF). El motor debe detenerse.
6.  Mover el selector a "Atrás" (ON). El motor debe girar en sentido inverso.
