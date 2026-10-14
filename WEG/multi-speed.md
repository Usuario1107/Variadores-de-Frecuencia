# 📋 ANEXO – Multispeed, Relé de Salida y Control Avance/Retroceso

Este anexo complementa la guía principal. Aquí se explica cómo configurar el modo **Multispeed** (varias velocidades fijas), cómo usar el **relé de salida (DO1)** para señalizar el estado del motor, y cómo **combinar Multispeed con el control de Avance/Retroceso** usando dos selectores.

---

## 🎛️ MODO MULTISPEED (8 Velocidades Fijas)

El modo Multispeed permite predefinir hasta **8 velocidades** y seleccionarlas con combinaciones de entradas digitales (pulsadores, selectores o interruptores). Las velocidades se guardan en los parámetros **P124 a P131**.

### ⚙️ Configuración básica

1.  **Activar la referencia Multispeed**:
    - En modo Local: `P221 = 8 (Multispeed)`.
    - En modo Remoto: `P222 = 8 (Multispeed)`.
2.  **Asignar entradas digitales**: Necesitas de 1 a 3 entradas digitales para seleccionar la velocidad. Por ejemplo, para usar 4 velocidades (2 entradas):
    - `P263 = 13` (DI1 = Multispeed)
    - `P264 = 13` (DI2 = Multispeed)
3.  **Programar las frecuencias**: Ajusta los parámetros **P124, P125, P126, P127** con las velocidades deseadas.

### 📊 Tabla de selección (2 entradas → 4 velocidades)

| DI1 (P263) | DI2 (P264) |  Velocidad  | Parámetro |
| :--------: | :--------: | :---------: | :-------: |
|  Inactiva  |  Inactiva  | Velocidad 1 |   P124    |
|  Inactiva  |   Activa   | Velocidad 2 |   P125    |
|   Activa   |  Inactiva  | Velocidad 3 |   P126    |
|   Activa   |   Activa   | Velocidad 4 |   P127    |

**Ejemplo práctico**:

- `P124 = 10 Hz` (velocidad mínima)
- `P125 = 25 Hz`
- `P126 = 40 Hz`
- `P127 = 50 Hz` (velocidad máxima)

> ⚠️ **Importante**: La entrada más significativa debe ser DI1 o DI2. No puedes usar ambas como "más significativas" simultáneamente; de lo contrario, el variador mostrará "CONF" (configuración incompatible). [reference:0]

---

## 🔌 RELÉ DE SALIDA (DO1) – Configuración y Uso

El CFW300 tiene una salida a relé (DO1) que puedes usar para señalizar estados a una luz piloto, PLC o sistema de control. Se configura con el parámetro **P275**.

### 🎯 Funciones útiles para el relé

| Valor P275 | Función       | ¿Qué hace?                                                                                     |
| :--------: | :------------ | :--------------------------------------------------------------------------------------------- |
|   **11**   | **Run**       | Se activa cuando el motor está girando (pulsos PWM activos). [reference:1]                     |
|   **4**    | **F = F\***   | Se activa cuando la frecuencia de salida alcanza la referencia (final de rampa). [reference:2] |
|   **12**   | **Ready**     | Se activa cuando el variador está listo para ser habilitado.                                   |
|   **13**   | **Sin Falla** | Se activa cuando no hay fallas.                                                                |
|   **26**   | **Con Falla** | Se activa cuando hay una falla.                                                                |

### 🔧 Cómo configurarlo

1.  Navega hasta el parámetro **P275**.
2.  Cambia su valor según la función deseada:
    - **Para saber si el motor está en marcha** → `P275 = 11`.
    - **Para saber si el arranque se estabilizó** (llegó a la velocidad de referencia) → `P275 = 4`.
3.  Presiona **P** para confirmar.

**Conexión física del relé**:

- El relé tiene contactos **NA (Normalmente Abierto)**, **NC (Normalmente Cerrado)** y **Común (C)**.
- Terminales en el CFW300: **DO1-RL-NO** (10), **DO1-RL-C** (11), **DO1-RL-NC** (12).
- Para una luz piloto: conecta la alimentación en serie con el contacto **NA** y el **Común**.

> 💡 **Truco**: Si quieres que el relé se active **solo cuando el motor ya está girando estable** (no durante la rampa de aceleración), usa `P275 = 4 (F = F*)`. Si quieres que se active **en cuanto el motor empieza a girar**, usa `P275 = 11 (Run)`.

---

## 🔄 COMBINAR MULTISPEED CON AVANCE/RETROCESO (Dos Selectores)

Puedes combinar el control de dirección (Avance/Retroceso) con la selección de velocidades Multispeed. Aquí tienes un ejemplo con **dos selectores**:

- **Selector 1 (3 posiciones)**: Avance – Off – Retroceso.
- **Selector 2 (2 posiciones)**: Velocidad Baja – Velocidad Alta.

### 🔌 Conexiones

| Elemento                        | Terminal variador    | Función                  |
| :------------------------------ | :------------------- | :----------------------- |
| Selector 1 – Posición Avance    | **DI1** (terminal 1) | Avance (Forward)         |
| Selector 1 – Posición Retroceso | **DI2** (terminal 2) | Retroceso (Reverse)      |
| Selector 1 – Común              | **GND** (terminal 5) | Común                    |
| Selector 2 – Posición 1         | **DI3** (terminal 3) | Selección velocidad baja |
| Selector 2 – Posición 2         | **DI4** (terminal 4) | Selección velocidad alta |
| Selector 2 – Común              | **GND** (terminal 5) | Común                    |

### ⚙️ Parámetros a programar

| Parámetro |   Valor   | ¿Para qué sirve?                             |
| :-------- | :-------: | :------------------------------------------- |
| **P221**  |   **8**   | Referencia de velocidad = Multispeed         |
| **P223**  |   **4**   | Sentido de giro por entradas digitales (Dlx) |
| **P224**  |   **1**   | Gira/Para por entradas digitales (Dlx)       |
| **P263**  |   **4**   | DI1 = Avance (Forward)                       |
| **P264**  |   **5**   | DI2 = Retroceso (Reverse)                    |
| **P265**  |  **13**   | DI3 = Multispeed (selección de velocidad)    |
| **P266**  |  **13**   | DI4 = Multispeed (selección de velocidad)    |
| **P124**  | **10 Hz** | Velocidad 1 (con DI3 y DI4 inactivas)        |
| **P125**  | **25 Hz** | Velocidad 2 (solo DI4 activa)                |
| **P126**  | **40 Hz** | Velocidad 3 (solo DI3 activa)                |
| **P127**  | **50 Hz** | Velocidad 4 (DI3 y DI4 activas)              |

### 📊 Tabla de operación

| Selector 1 | Selector 2     | Resultado                           |
| :--------- | :------------- | :---------------------------------- |
| Avance     | Velocidad Baja | Motor gira en Avance a **10 Hz**    |
| Avance     | Velocidad Alta | Motor gira en Avance a **40 Hz**    |
| Retroceso  | Velocidad Baja | Motor gira en Retroceso a **10 Hz** |
| Retroceso  | Velocidad Alta | Motor gira en Retroceso a **40 Hz** |
| Off        | Cualquiera     | Motor detenido                      |

> 📌 **Nota**: La combinación exacta de velocidades depende de qué entradas estén activas. Puedes ajustar los valores de P124 a P131 según tus necesidades.

### ⚠️ Ajuste del sentido físico real

Si al probar notas que el sentido real no coincide con lo que esperas (ej. "Avance" gira al revés), **apaga el variador** e intercambia **dos cables cualesquiera** de salida al motor (por ejemplo, U y V). Esto invierte el campo magnético y corrige la dirección sin tocar la programación.

---

## 📌 RESUMEN DE PARÁMETROS CLAVE

| Parámetro     | Función                    | Valor típico           |
| :------------ | :------------------------- | :--------------------- |
| **P221**      | Referencia de velocidad    | 8 (Multispeed)         |
| **P223**      | Fuente del sentido de giro | 4 (Dlx)                |
| **P224**      | Fuente de Gira/Para        | 1 (Dlx)                |
| **P263**      | Función DI1                | 4 (Avance)             |
| **P264**      | Función DI2                | 5 (Retroceso)          |
| **P265**      | Función DI3                | 13 (Multispeed)        |
| **P266**      | Función DI4                | 13 (Multispeed)        |
| **P275**      | Función del relé DO1       | 11 (Run) o 4 (F = F\*) |
| **P124-P131** | Velocidades Multispeed     | Según aplicación       |

Con esta configuración, tendrás un control completo de dos sentidos de giro y varias velocidades fijas, además de poder señalizar el estado del motor con el relé. Si algo no funciona como esperas, puedes consultarme de nuevo.
