# Documentación Técnica: Variador de Frecuencia Yaskawa GPD 515 (Serie G5)

## 1. Descripción General
El equipo es un **Variador de Frecuencia (VFD)** de la marca **Yaskawa** (comercializado en su momento por Magnetek bajo la referencia GPD 515). Este dispositivo se utiliza para controlar la velocidad, el par y la dirección de motores de inducción de corriente alterna (CA) trifásicos.

Genera una salida de voltaje y frecuencia ajustable mediante modulación por ancho de pulso (PWM), lo que permite un control preciso del motor y un ahorro energético significativo en aplicaciones como bombas, ventiladores y transportadores.

---

## 2. Especificaciones Técnicas (Placa de Datos)
Basado en la placa del equipo y la Tabla A2-1 del manual técnico:

| Parámetro | Valor |
| :--- | :--- |
| **Fabricante** | Yaskawa Electric |
| **Modelo (Nuevo)** | CIMR-G5M4018 |
| **Modelo (Antiguo)** | GPD515C-B041 |
| **Potencia Nominal** | 30 HP (Aprox. 22 kW) |
| **Entrada (Input)** | Trifásico, 380-460V AC, 50/60Hz, 46 A |
| **Salida (Output)** | Trifásico, 0-460V AC, 0-400Hz, 41 A |
| **Masa** | 32 kg |
| **Consola/Display** | Digital Operator JVOP-130 (Pantalla LCD de 2 líneas) |

---

## 3. Componentes Principales
Según el análisis visual del equipo y el diagrama del manual:

*   **Rectificador:** Convierte la energía AC de la red en DC (Bus DC).
*   **Bus DC y Condensadores:** Almacenan la energía y estabilizan el voltaje. *(Peligro: Retienen alta carga eléctrica incluso apagados).*
*   **Inversor (IGBTs):** Convierte la energía DC de vuelta a AC con frecuencia y voltaje variables para el motor.
*   **Tarjeta de Control (PCB):** Es el "cerebro" del equipo. Procesa las señales y controla los IGBTs. Incluye terminales de control digitales y analógicos.
*   **Tarjeta de Potencia:** Aloja los módulos rectificadores e inversores.
*   **Ventilador de Disipador (Cooling Fan):** Enfría los módulos de potencia (parte inferior trasera).
*   **Ventilador Interno:** Enfría los componentes electrónicos de las tarjetas.
*   **Terminales de Potencia:** 
    *   L1, L2, L3 (Entrada de red).
    *   T1, T2, T3 (Salida al motor).
    *   B1, B2 (Frenado dinámico).

---

## 4. Clasificación de Fallas y Alarmas (Según el Manual)
El variador clasifica los problemas en tres categorías (Sección 6.1 del manual):

1.  **Alarma (Alarm):** Parpadea en la pantalla. El equipo **sigue funcionando**. Es una advertencia (ej. sobrecalentamiento prematuro).
2.  **Falla Menor (Minor Fault):** Parpadea en la pantalla. El equipo **sigue funcionando**, pero activa un relé de advertencia para sistemas externos (ej. fallo en un sensor externo).
3.  **Falla Mayor (Major Fault):** Se muestra fijo en la pantalla. El equipo **se detiene inmediatamente** (el motor queda libre por inercia) y bloquea la salida. Requiere reinicio manual.

### Códigos de Falla Comunes (Troubleshooting)
*   `OH` **(Overheat):** Sobretemperatura del disipador. Ocurre si el ventilador falla, hay mucho polvo o la temperatura ambiente es alta.
*   `OH1` **(Heatsink MAX Temp):** Temperatura extrema (>105°C) o fallo del ventilador interno.
*   `OL1` **(Motor Overloaded):** El motor consume demasiada corriente, activando la protección térmica.
*   `OL2` **(Inv Overloaded):** El propio variador está sufriendo sobrecarga en sus transistores.
*   `OC` **(Overcurrent):** Sobrecorriente brusca (cortocircuito o carga mecánica trabada).
*   `OV` **(Overvoltage):** Sobretensión en el bus DC (suele ocurrir al frenar muy rápido).
*   `UV1` **(DC Bus Undervolt):** Caída de tensión en el bus (red eléctrica inestable o condensadores degradados).
*   `PF` **(Input Pha Loss):** Pérdida de una de las fases de entrada de energía.

---

## 5. Parámetros Clave de Configuración
Para proteger el equipo y el motor, los siguientes parámetros son fundamentales (Sección 5 del manual):

*   **`A1-02` (Control Method):** Define el método de control. Por defecto suele ser `2` (Open Loop Vector - Vector en lazo abierto).
*   **`E2-01` (Motor Rated FLA):** Se debe configurar con la corriente nominal de la placa del motor para una protección térmica correcta.
*   **`L1-01` y `L1-02`:** Configuran la curva de protección térmica del motor y el tiempo de disparo (por defecto 8.0 minutos).
*   **`L8-02`:** Nivel de temperatura del disipador que activa la alarma de sobrecalentamiento.
*   **`C6-01` y `C6-02`:** Frecuencia portadora (PWM). A mayor frecuencia, más silencioso el motor pero más calor genera el variador.

---

## 6. Herramientas de Diagnóstico Integradas
El equipo cuenta con un historial de fallas que es vital para el mantenimiento:
*   **Monitor de fallas actuales (`U2`):** Muestra las condiciones exactas (frecuencia, voltaje, corriente) en el momento de la falla.
*   **Historial de fallas (`U3`):** Almacena las últimas 4 fallas ocurridas en la memoria no volátil. Muy útil para diagnosticar fallas intermitentes (como equipos que funcionan un rato y luego fallan).

---

## 7. Notas de Seguridad (Importante)
> ⚠️ **PELIGRO DE ALTO VOLTAJE** ⚠️
> *   Los condensadores del Bus DC mantienen cargas letales incluso después de desconectar la alimentación principal.
> *   Antes de abrir la tapa o tocar cualquier terminal, **espere al menos 10-15 minutos** y verifique que la luz indicadora **"CHARGE"** (ubicada en la tarjeta principal) esté completamente apagada.

---
**Documentación elaborada para fines de mantenimiento y diagnóstico técnico.**
