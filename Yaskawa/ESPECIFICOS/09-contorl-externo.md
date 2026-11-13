# Guía de Parametrización y Auto-Sintonización: Control Externo
**Equipo:** Variador Yaskawa GPD 515 (CIMR-G5M4018)
**Objetivo:** Configurar el control por selector de 3 posiciones y potenciómetro, calcular el deslizamiento del motor, ejecutar la Auto-Sintonización y guardar la copia de seguridad.

---

## 1. Fórmula para el Deslizamiento del Motor (`E2-02`)
El parámetro `E2-02` (Motor Rated Slip) define la pérdida de velocidad del motor cuando trabaja a plena carga. Se expresa en Hertz (Hz) y se calcula con la siguiente fórmula (Manual, Página 67):

**Fórmula:**
> **`E2-02 = (Ns - Nr) × (Frecuencia Nominal / Ns)`**

**Donde:**
*   **Ns (Velocidad Síncrona):** Es la velocidad teórica del campo magnético. Se calcula como: `(Frecuencia Nominal × 120) / Número de Polos`.
*   **Nr (Velocidad Nominal):** Es la velocidad real del motor a plena carga (leída directamente de la placa del motor en RPM).
*   **Frecuencia Nominal:** Los Hz de la placa (ej. 50 Hz).

**Ejemplo Práctico (Motor de 4 polos, 50 Hz, 1440 RPM):**
1.  Calcular Ns: `(50 × 120) / 4 = 1500 RPM`.
2.  Aplicar la fórmula: `(1500 - 1440) × (50 / 1500)`.
3.  Resultado: `60 × 0.0333 = 2.0 Hz`. 
4.  El valor a ingresar en `E2-02` es **`2.0`**.

---

## 2. Tablas de Parametrización (Orden Secuencial)

### FASE 1: Control a 2 Hilos (Selector de 3 posiciones)
*Objetivo: Habilitar el control por interruptor mantenido.*

| Parámetro | Función | Valor a Configurar | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`A1-03`** | Inicialización a 2 Hilos | **`2220`** | Pág. 170 |

### FASE 2: Control Externo y Límites de Frecuencia
*Objetivo: Leer la velocidad del potenciómetro (Terminal 13) y calibrar la ganancia para que no exceda el límite (debido a que el potenciómetro se alimenta con 15V).*

| Parámetro | Función | Valor a Configurar | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`b1-01`** | Fuente de Referencia de Velocidad | **`1`** (Terminales) | Pág. 237 |
| **`b1-02`** | Fuente de Marcha/Paro | **`1`** (Terminales) | Pág. 237 |
| **`H3-01`** | Tipo de señal en Terminal 13 | **`0`** (0-10 VDC) | Pág. 253 |
| **`d2-01`** | Límite superior de frecuencia | **`100.0`** % | Pág. 245 |
| **`d2-02`** | Límite inferior de frecuencia (Evita que baje a 0 Hz) | **`4.0`** % a **`10.0`** % | Pág. 245 |
| **`H3-02`** | Ganancia (Calibración del Potenciómetro) | **`66.6`** % | Pág. 253 |
| **`H3-03`** | Bias (Punto de inicio de la perilla) | **`0.0`** % | Pág. 253 |

### FASE 3: Datos de la Placa del Motor
*Objetivo: Ingresar los datos reales del motor para protegerlo y preparar la Auto-Sintonización.*

| Parámetro | Función | Valor a Configurar (Ejemplo) | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`A1-02`** | Método de Control | **`2`** (Open Loop Vector) | Pág. 236 |
| **`E1-01`** | Voltaje de Entrada de Red | **`380`** V | Pág. 247 |
| **`E1-04`** | Frecuencia Máxima del Motor | **`50.0`** Hz | Pág. 247 |
| **`E1-05`** | Voltaje Máximo de Salida | **`380`** V | Pág. 247 |
| **`E1-06`** | Frecuencia Base | **`50.0`** Hz | Pág. 247 |
| **`E1-09`** | Frecuencia Mínima de Operación | **`1.5`** Hz (o **`2.0`** Hz) | Pág. 247 |
| **`E2-01`** | Corriente Nominal (FLA) | *(Ver placa del motor)* | Pág. 248 |
| **`E2-02`** | Deslizamiento Nominal (Hz) | *(Calcular con la Fórmula del Punto 1)* | Pág. 248 |
| **`E2-03`** | Corriente en Vacío (A) | *(Aprox. 30% a 50% de la FLA)* | Pág. 248 |
| **`E2-04`** | Número de Polos | **`4`** | Pág. 248 |

### FASE 4: Auto-Sintonización (Auto-Tuning)
*Objetivo: Que el variador calcule automáticamente los parámetros internos del motor (Resistencia, Inductancia).*
*⚠️ Advertencia: El motor debe estar desconectado de la carga mecánica (en vacío).*

| Acción | Descripción | Referencia Manual |
| :--- | :--- | :--- |
| **Menú Auto-Tuning** | Ingresar al menú y cargar: Voltaje (380V), Corriente (Placa), Frecuencia (50Hz), RPM (Placa), Polos (4). Presionar **RUN**. Esperar el mensaje **`Tune Successful`**. | Pág. 48-50 |
| **Si falla (Tune Aborted)** | Verificar que el motor gire libremente. Apretar terminales T1, T2, T3. Revisar los datos ingresados. | Pág. 209 |

### FASE 5: Respaldo de Seguridad (Backup)
*Objetivo: Guardar la configuración exitosa para evitar reprogramar en caso de fallas o cambios de tarjeta.*

| Parámetro | Función | Valor a Configurar | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`o2-03`** | Guardar Copia de Seguridad | **`1`** (Set Default) | Pág. 260 |
| **`A1-03`** | Restaurar Copia (Uso futuro) | **`1110`** (User Initialize) | Pág. 170 |
