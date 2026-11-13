# Guía de Sensores Internos, Monitoreo de Temperatura y Tiempo de Operación
**Equipo:** Variador Yaskawa GPD 515 (CIMR-G5M4018)

Este documento detalla cómo el variador mide su temperatura interna, cómo se monitorean sus variables críticas (corriente, voltaje, tiempo), y cómo utilizar esta información para diagnosticar fallas intermitentes.

---

## 1. Sensores de Temperatura y Protección Térmica

El variador no utiliza un termómetro digital tradicional. Utiliza un **Termistor (NTC)** montado directamente sobre el disipador de aluminio (Heatsink), justo debajo de los módulos de potencia (IGBTs).

### ¿Cómo se mide y se configura?
El termistor envía una señal analógica a la tarjeta de control. El variador no te muestra la temperatura en grados Celsius en la pantalla principal, sino que actúa cuando se cruzan los umbrales configurados en los parámetros `L8-02` y `L8-03`.

### Tabla de Parámetros de Temperatura (Grupo L8)
| Parámetro | Función | Valor / Rango | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`L8-02`** | **Nivel de Alarma de Sobrecalentamiento (`OH`)** | Por defecto: **`95°C`** a **`100°C`** (depende de la capacidad). | Pág. 258 |
| **`L8-03`** | **Acción tras la Alarma de Sobrecalentamiento** | `0`=Rampa, `1`=Inercia, `2`=Parada Rápida, `3`=Solo Alarma (Sigue funcionando). | Pág. 259 |
| **`OH`** | **Código de Falla** | Se dispara cuando el disipador supera la temperatura de `L8-02`. | Pág. 206 |
| **`OH1`** | **Código de Falla (Falla del Termistor)** | Ocurre si el sensor se desconecta o si la temperatura supera los **`105°C`**. | Pág. 206 |

*(Nota para el diagnóstico: Si tu equipo falla con `OH` después de un rato, revisa el ventilador de potencia y el polvo en el disipador).*

---

## 2. Monitoreo de Variables en Tiempo Real (Grupo U1)

El variador cuenta con un sistema de monitoreo que puedes revisar en la pantalla sin detener la máquina (Presionar `MENU` > `U1` > `DATA/ENTER`).

### Tabla de Monitores Esenciales (Grupo U1)
| Código | Variable a Medir | Unidad | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`U1-01`** | Referencia de Frecuencia (Velocidad pedida) | Hz | Pág. 262 |
| **`U1-02`** | Frecuencia de Salida (Velocidad real) | Hz | Pág. 262 |
| **`U1-03`** | Corriente de Salida | Amperios (A) | Pág. 262 |
| **`U1-06`** | Voltaje de Salida | Voltios (V) | Pág. 262 |
| **`U1-07`** | **Voltaje del Bus DC** | Voltios DC (V) | Pág. 262 |
| **`U1-08`** | Potencia de Salida | Kilovatios (kW) | Pág. 262 |
| **`U1-13`** | **Tiempo de Operación (Odómetro)** | Horas (H) | Pág. 263 |
| **`U1-14`** | Número de Software (Firmware) | N/A | Pág. 263 |

*(Nota para el diagnóstico: El `U1-07` es vital. Si la falla es `UV`, debes vigilar este parámetro; si baja de 400V DC cuando el motor exige carga, el problema es de alimentación o condensadores agotados).*

---

## 3. Tiempo de Funcionamiento y Mantenimiento (Odómetro)

El variador tiene un contador interno que acumula las horas de funcionamiento. Esto es crucial para saber cuándo hacer mantenimiento preventivo (cambio de ventiladores, revisión de condensadores).

### Tabla de Contadores y Temporizadores (Grupos U y o)
| Parámetro | Función | Descripción / Rango | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`U1-13`** | **Tiempo Total de Operación** | Acumula las horas desde que salió de fábrica. Solo lectura. | Pág. 263 |
| **`o2-07`** | **Ajuste del Temporizador de Mantenimiento** | Permite fijar una meta (ej. `20000` horas) para avisar que toca mantenimiento. | Pág. 260 |
| **`o2-08`** | **Selección de Conteo del Temporizador** | `0` = Cuenta siempre que esté energizado. `1` = Cuenta solo cuando el motor está en RUN. | Pág. 261 |

---

## 4. Diagnóstico de Fallas usando el Historial de Tiempo

Cuando el equipo se apaga por una falla, el variador guarda una "foto" del momento exacto, incluyendo cuántas horas llevaba funcionando.

### Tabla de Historial de Fallas con Tiempo (Grupo U3)
| Parámetro | Función | Descripción | Pág. Manual |
| :--- | :--- | :--- | :--- |
| **`U3-01` a `U3-04`** | **Mensajes de Falla 1 a 4** | Muestra el código de las últimas 4 fallas (ej. `OH`, `OL1`). | Pág. 266 |
| **`U3-05`** | **Tiempo de Operación de la Falla 1** | Cuántas horas totales tenía el equipo cuando ocurrió la última falla. | Pág. 266 |
| **`U3-06` a `U3-08`** | **Tiempo de Fallas 2, 3 y 4** | Horas totales de las fallas anteriores. | Pág. 266 |

*(Nota para el diagnóstico: Si ves que la falla `OH` ocurrió a las `35,000` horas de operación, es un aviso claro de que el ventilador de potencia ya cumplió su vida útil y debe ser reemplazado).*

---
