# Clasificación y Listado de Parámetros del Variador GPD 515

El manual técnico del fabricante organiza los parámetros de programación mediante una estructura de tablas (Apéndice 1, páginas 236 a 267). Para poder configurar el equipo de manera segura y eficiente, es fundamental comprender cómo se clasifica esta información.

---

## 1. Estructura de las Tablas de Parámetros (El manual)
Cada vez que el manual lista un parámetro, lo presenta en una tabla con las siguientes columnas, las cuales dictan cómo debes programarlo en la consola (HMI):

*   **Número (Parameter Number):** El código exacto (Ej. `E2-01`).
*   **Nombre (Function/Name):** Qué hace ese parámetro.
*   **Incremento (Setting Increment):** La unidad mínima de ajuste (Ej. 0.1 Hz, 1 segundo, 1 Amperio).
*   **Rango (Setting Range):** Los límites permitidos. **El variador no aceptará un valor fuera de este rango.**
*   **Valor de Fábrica (Factory Setting):** El valor por defecto con el que sale el equipo. Si no sabes qué poner, este es el valor seguro.
*   **Nivel de Acceso (Access Level):** Indica si el parámetro es visible en el menú Rápido (Q), Básico (B) o Avanzado (A).

---

## 2. Clasificación General (Los Grupos de Letras)
El equipo divide los parámetros por la letra inicial para agrupar funciones lógicas:

*   **Grupo A (Initialize):** Entorno y configuración global (método de control, idioma).
*   **Grupo b (Application):** Operación básica (fuentes de marcha/paro, frenado, PID).
*   **Grupo C (Tuning):** Rampas de aceleración/desaceleración y sintonización.
*   **Grupo d (Reference):** Velocidades preestablecidas, límites y frecuencias de rechazo.
*   **Grupo E (Motor):** Datos de la placa del motor y curva V/f. *(Crítico para seguridad).*
*   **Grupo H (Terminals):** Configuración del hardware (entradas digitales, analógicas y relés).
*   **Grupo L (Protection):** Protecciones eléctricas (térmica, sobrecarga, pérdida de fase).
*   **Grupo o (Operator):** Configuración de la pantalla (display).
*   **Grupo U (Monitors):** Solo lectura. Datos en vivo (corriente, voltaje, historial de fallas).

---

## 3. Listado Resumen de Parámetros Críticos (Cheat Sheet)
Este es un resumen de los parámetros más importantes para dejar el equipo operando de forma segura, extraídos de la lista principal del manual, con sus rangos y valores por defecto:

| Código | Nombre / Función | Rango de Ajuste Permitido | Valor de Fábrica | Importancia |
| :--- | :--- | :--- | :--- | :--- |
| **A1-02** | Método de Control | 0 a 3 | **2** (Vector Lazo Abierto) | Define cómo el variador controla el motor. |
| **b1-01** | Fuente de Referencia de Velocidad | 0 a 4 | **1** (Terminales) | 0=Teclado, 1=Terminales (Potenciómetro/PLC). |
| **b1-02** | Fuente de Comando de Marcha | 0 a 4 | **1** (Terminales) | 0=Teclado (RUN/STOP), 1=Cables (PLC/Botones). |
| **C1-01** | Tiempo de Aceleración 1 | 0.0 a 6000.0 seg | **10.0 seg** | Tiempo que tarda en ir de 0 a velocidad máxima. |
| **C1-02** | Tiempo de Desaceleración 1 | 0.0 a 6000.0 seg | **10.0 seg** | Tiempo que tarda en detenerse por completo. |
| **E1-01** | Voltaje de Entrada de Red | 155 a 510 V | **460 V** | Debe coincidir con el voltaje de la planta (380V o 460V). |
| **E1-03** | Selección de Curva V/f | 0 a F | **F** | Curva de rendimiento (F = personalizada). |
| **E2-01** | Corriente Nominal del Motor | 0.01 a 1500.0 A | *Varía según placa* | **CRÍTICO.** Evita que el motor se queme. |
| **E2-04** | Número de Polos del Motor | 2 a 48 | **4** | Define el cálculo de las RPM del motor. |
| **L1-01** | Protección Térmica del Motor | 0 (Off) o 1 (On) | **1** | Habilita el seguro térmico interno. |
| **L1-02** | Tiempo de Protección Térmica | 0.1 a 20.0 min | **8.0 min** | Tiempo que tolera sobrecarga antes de fallar (`OL1`). |

---

## 4. Nota sobre los Niveles de Acceso (`A1-01`)
El manual clasifica los parámetros según su nivel de acceso. De fábrica, el equipo solo muestra los parámetros "Quick-start" (32 básicos). 
*   Si necesitas ver todos los parámetros (Avanzados), debes cambiar el parámetro **`A1-01`** a **`4`** (Advanced Level).
*   Esto es útil para diagnóstico, ya que permite ver ajustes ocultos que podrían estar causando fallas intermitentes.
