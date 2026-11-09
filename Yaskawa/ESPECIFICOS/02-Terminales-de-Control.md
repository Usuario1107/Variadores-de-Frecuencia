# Documentación Técnica: Terminales de Control del Variador Yaskawa GPD 515
**Modelo:** CIMR-G5M4018 (GPD515C-B041)

Este documento detalla la función, configuración de fábrica y niveles de señal de todos los terminales de la tarjeta de control, conforme a las especificaciones del manual técnico del fabricante.

---

## 1. Terminales de Alimentación y Comunes
Estos terminales proporcionan la referencia eléctrica y la energía necesaria para el funcionamiento de los circuitos de control.

*   **Terminal 11 (Común Digital):** De la fuente de +24 Vdc (máx. 8 mA). Es el común (0V) para los terminales de entrada digital 1 al 8. La lógica de activación es conectando estos terminales al 11 (Lógica "Low = True").
*   **Terminal 17 (Común Analógico):** Es el 0V de referencia para todas las señales analógicas (Terminales 13, 14 y 16).
*   **Terminal 15 (+15 Vdc):** Fuente de alimentación de +15 Vdc (máx. 20 mA). Se utiliza exclusivamente para alimentar potenciómetros externos de velocidad.
*   **Terminal 33 (-15 Vdc):** Fuente de alimentación de -15 Vdc (máx. 20 mA). Se utiliza para potenciómetros que requieren control bidireccional.
*   **Terminal 12 (Blindaje / Shield):** Terminal de tierra para la malla (shielding) de los cables de control. El manual exige que la malla se conecte **únicamente** en este extremo y se deje aislada en el otro extremo del cable.

---

## 2. Entradas Digitales (Secuencia y Comandos)
Configurables mediante los parámetros `H1-01` hasta `H1-06`. Operan mediante contactos secos (relés o interruptores).

*   **Terminal 1 (FWD / Marcha Adelante):** 
    *   *En control 2 hilos:* Cierra para correr en sentido horario.
    *   *En control 3 hilos:* Actúa como botón de START (cierre momentáneo).
*   **Terminal 2 (REV / Marcha Atrás):**
    *   *En control 2 hilos:* Cierra para correr en sentido antihorario.
    *   *En control 3 hilos:* Actúa como STOP (debe estar cerrado para permitir el arranque, se abre para detener).
*   **Terminal 3 (Falla Externa 1 / EF3):** Configuración de fábrica. Detiene el variador y muestra "EF3" si se activa.
*   **Terminal 4 (Reset de Falla):** Configuración de fábrica. Cierra el circuito para reiniciar el variador tras una falla mayor (siempre que la orden de marcha esté abierta).
*   **Terminal 5 (Selección Multi-step 1):** Configuración de fábrica. Se usa junto al Terminal 6 para seleccionar velocidades preestablecidas.
*   **Terminal 6 (Selección Multi-step 2):** Configuración de fábrica. Ver Terminal 5.
*   **Terminal 7 (Referencia Jog):** Configuración de fábrica. Al cerrarse, el motor gira a una frecuencia muy baja (por defecto 6.00 Hz) para posicionamiento.
*   **Terminal 8 (Base Block / Paro por inercia):** Configuración de fábrica. Al abrirse (contacto N.C.), el variador corta la salida inmediatamente y el motor se detiene por inercia.

---

## 3. Entradas Analógicas (Referencia de Frecuencia)
Utilizadas para controlar la velocidad del motor de forma proporcional (voltaje o corriente).

*   **Terminal 13 (Referencia Automática):** Entrada de voltaje de 0 a 10 Vdc (impedancia 20 kΩ). Es la entrada principal para control de velocidad desde un PLC o controlador. Se configura con el parámetro `H3-01`.
*   **Terminal 14 (Referencia Multifunción):** Por defecto, configurada para recibir una señal de corriente de 4 a 20 mA (impedancia 250 Ω). Se configura con el parámetro `H3-08`. *(Nota: Si se requiere usar como voltaje, se debe cortar el puente J1 en la tarjeta).*
*   **Terminal 16 (Referencia Manual):** Entrada de voltaje de 0 a 10 Vdc (impedancia 20 kΩ). Diseñada para recibir la señal de un potenciómetro local. Se habilita configurando el parámetro `H3-05 = 0`.

---

## 4. Salidas Digitales (Contactos y Colector Abierto)
Informan al exterior (PLC, lámparas, relés) sobre el estado del variador.

*   **Terminales 9 y 10 (Relé de Contacto Seco):** Capacidad de 250 Vac / 1 A o 30 Vdc / 1 A. 
    *   *Configuración de fábrica:* Cierra el contacto cuando el variador está en operación (RUN). Se configura con `H2-01`.
*   **Terminales 18, 19 y 20 (Relé de Falla):** Contacto seco para indicar falla (Terminal 18-20 N.O., 19-20 N.C.). Se activa ante cualquier falla mayor.
*   **Terminales 25, 26 y 27 (Salidas de Colector Abierto):** Capacidad de 48 Vdc / 50 mA máx. Requieren diodo de protección si se conectan a relés DC.
    *   *Terminal 25:* Configurado de fábrica como "Velocidad Cero" (`H2-02 = 1`).
    *   *Terminal 26:* Configurado de fábrica como "Velocidad Alcanzada" (`H2-03 = 2`).
    *   *Terminal 27:* Común para los terminales 25 y 26.

---

## 5. Salidas Analógicas (Monitorización)
Proporcionan una señal de voltaje proporcional a las variables internas del variador.

*   **Terminales 21 (+), 22 (-) y 23 (+):** Salida de 0 a ±11 Vdc (máx. 2 mA). No deben usarse para control externo, solo para medidores o instrumentación (paneles).
    *   *Terminal 21 (Monitor 1):* Configurado de fábrica para mostrar la frecuencia de salida (`H4-01 = 2`). Escala: 10V = Frecuencia Máxima.
    *   *Terminal 23 (Monitor 2):* Configurado de fábrica para mostrar la corriente de salida (`H4-04 = 3`). Escala: 5V = Corriente nominal continua.
