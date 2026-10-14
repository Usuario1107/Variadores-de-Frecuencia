# 📋 README – CFW300 PARA ASCENSOR (VERSIÓN FINAL DE PRODUCCIÓN)

**Modelo:** CFW300A02P6S2NB20 (2.6 A, 220 V monofásico)
**Motor:** 220 V Delta, 50 Hz, 1.93 A

---

## 🔄 PASO 0 – RESET DE FÁBRICA

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P204** | **6** | Carga estándar de fábrica para **50 Hz** |

> Después del reset, **P296** debe quedar en 2 (200-240 V). Este parámetro es de solo lectura en la línea 200 V.

---

## ⚙️ PASO 1 – DATOS BÁSICOS DEL MOTOR

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P400** | **220** | Tensión nominal del motor |
| **P401** | **1.93** | Corriente nominal del motor |
| **P402** | **1450** | RPM del motor (4 polos a 50 Hz; ajusta según placa) |
| **P403** | **50** | Frecuencia nominal del motor |
| **P404** | **2** | Potencia del motor (0.33 HP / 0.25 kW; ajusta según placa) |
| **P202** | **0** | Tipo de control V/f lineal |
| **P133** | **0** | Frecuencia mínima (0 Hz) |
| **P134** | **50** | Frecuencia máxima (igual a la nominal) |

---

## 🔌 PASO 2 – ENTRADAS DIGITALES (4 entradas)

| Parámetro | Valor | Entrada | Función |
| :--- | :---: | :---: | :--- |
| **P263** | **1** | DI1 | **Gira/Para** |
| **P264** | **8** | DI2 | **Sentido de Giro** (una sola entrada) |
| **P265** | **13** | DI3 | **Multispeed** (velocidad lenta) |
| **P266** | **14** | DI4 | **2ª Rampa** (desaceleración suave) |

> ✅ **Optimización clave:** DI4 se conecta **en paralelo con DI1** (puenteados). Así, cuando el selector de marcha está ON, DI1 y DI4 se activan juntos; cuando está OFF, ambos se desactivan. Esto permite usar la 2ª Rampa (larga) durante la marcha y la 1ª Rampa (corta) en la parada final, **sin usar una salida extra del PLC**.

---

## 🧭 PASO 3 – DIRECCIÓN Y COMANDO

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P220** | **0** | Siempre Local |
| **P223** | **4** | Sentido de giro por Dlx (DI2) |
| **P224** | **1** | Gira/Para por Dlx (DI1) |

> Con **P264 = 8** y **P223 = 4**, la DI2 controla la dirección:
> - **DI2 OFF (0 V)** → Giro Horario (Subir)
> - **DI2 ON (GND)** → Giro Antihorario (Bajar)

---

## 🎛️ PASO 4 – REFERENCIA MULTISPEED (UNA SOLA ENTRADA)

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P221** | **8** | Referencia de velocidad por Multispeed |
| **P124** | **50 Hz** | Velocidad de **crucero** (DI3 inactiva) |
| **P126** | **5 Hz** | Velocidad de **aproximación** (DI3 activa) |

> **DI3 OFF** → 50 Hz (crucero)
> **DI3 ON** → 5 Hz (aproximación)
>
> *Nota:* DI3 es el bit del medio, por eso usa **P126** cuando se activa. No necesitas dos selectores; con una sola entrada tienes las 2 velocidades que necesitas.

---

## ⏱️ PASO 5 – RAMPAS (DOS DESACELERACIONES)

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P100** | **2.0 s** | Aceleración 1ª Rampa (arranque suave) |
| **P101** | **0.3 s** | Desaceleración 1ª Rampa (**parada final corta y precisa**) |
| **P102** | **2.0 s** | Aceleración 2ª Rampa |
| **P103** | **2.0 s** | Desaceleración 2ª Rampa (**transición 50→5 Hz suave**) |
| **P104** | **1** | Rampa S activa (suaviza aún más) |
| **P105** | **2** | Selección 2ª Rampa vía DIx (DI4) |

> **Lógica de rampas:**
> - **DI4 OFF** → usa 1ª Rampa (P100/P101). Para crucero y parada final.
> - **DI4 ON** → usa 2ª Rampa (P102/P103). Para transición 50→5 Hz.

---

## 🛑 PASO 6 – FRENADO FINAL (CC)

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P299** | **0.0 s** | Sin frenado CC al arrancar |
| **P300** | **1.0 s** | Tiempo de Frenado CC en la parada (clava el motor) |
| **P301** | **3.0 Hz** | Frecuencia para iniciar el frenado CC |
| **P302** | **20 %** | Corriente de frenado CC (ajusta si el motor se mueve) |

> El frenado CC garantiza que el motor quede **inmóvil** en la posición final, evitando que se deslice.

---

## 🔔 PASO 7 – RELÉ DE SALIDA (OPCIONAL)

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P275** | **4** | Relé se activa cuando F = F* (final de rampa) |

> Si quieres que el relé se active cuando el motor está girando, usa **P275 = 11** (Run).

---

## 🔧 PASO 8 – PARÁMETROS EXTRA DE PROTECCIÓN

| Parámetro | Valor | ¿Para qué? |
| :--- | :---: | :--- |
| **P135** | **3.9 A** | Corriente máxima de salida (1.5 x 2.6 A) |
| **P156** | **2.32 A** | Corriente de sobrecarga a velocidad nominal (1.2 x 1.93) |
| **P157** | **2.32 A** | Corriente de sobrecarga al 50 % de velocidad |
| **P158** | **2.32 A** | Corriente de sobrecarga al 20 % de velocidad |
| **P271** | **0** | Entradas NPN (activas con 0 V). Si usas PNP, cambia a **3** |
| **P230** | **0** | Zona muerta inactiva (el motor arranca desde 0 Hz) |
| **P297** | **5.0 kHz** | Frecuencia de conmutación (dejar por defecto) |

---

## 🧠 LÓGICA DE OPERACIÓN (CON DI4 PUENTEADO A DI1)

| Paso | Acción | DI1 | DI4 | DI3 | Rampa usada | Resultado |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| 1 | Ascensor detenido | OFF | OFF | X | 1ª (P101) | Motor parado |
| 2 | Arranque en crucero | ON | ON | OFF | 2ª (P102) | Sube a 50 Hz (suave) |
| 3 | Pre-parada (llegando) | ON | ON | ON | 2ª (P103) | Baja suave a 5 Hz |
| 4 | Parada final | OFF | OFF | X | 1ª (P101) | Se detiene (0.3 s) + CC |
| 5 | Arranque en crucero | ON | ON | OFF | 2ª (P102) | Baja a 50 Hz (suave) |
| 6 | Pre-parada (llegando) | ON | ON | ON | 2ª (P103) | Baja suave a 5 Hz |
| 7 | Parada final | OFF | OFF | X | 1ª (P101) | Se detiene (0.3 s) + CC |

> **"X"** = el estado no importa (la orden de Gira/Para es prioritaria).
>
> **Importante:** Al detener (paso 4 o 7), DI1 y DI4 se desactivan **al mismo tiempo** porque están puenteados. El variador cambia instantáneamente a la 1ª Rampa (0.3 s) y se detiene preciso.

---

## 🔌 CONEXIONES FÍSICAS

| Elemento | Terminal variador | Función |
| :--- | :--- | :--- |
| Selector de marcha | **DI1 + DI4** (puenteados) | Gira/Para + 2ª Rampa |
| Selector de dirección | **DI2** | Subir / Bajar |
| Final de carrera pre-parada | **DI3** | Activa velocidad lenta (5 Hz) |
| Común de todos | **GND** | Común |
| Motor | **U, V, W** | Conexión Delta (220 V) |
| Alimentación | **L/L1 + N/L2** | 220 V monofásico |

---

## ✅ VERIFICACIÓN FINAL

1. **P047** debe ser **0**. Si no lo es, busca la causa en la tabla del manual.
2. **P012** muestra el estado de las entradas digitales en hexadecimal:
   - **01** = DI1 activa
   - **02** = DI2 activa
   - **04** = DI3 activa
   - **08** = DI4 activa
3. **P205 = 2** para que el display muestre la frecuencia de salida (opcional).
4. **P207 = 2** para que la barra gráfica muestre la frecuencia de salida (opcional).

---

## 📌 NOTAS FINALES

- **Conexión del motor:** Tu variador es **monofásico 220 V** y el motor es **220 V**. Conecta el motor en **Delta (triángulo)**. NO uses Estrella (380 V) porque el variador no eleva la tensión.
- **Alimentación del variador:** Conecta **fase + neutro** a los bornes L/L1 y N/L2 (el tercer borne L3 no se usa en monofásico).
- **Sentido físico:** Si al probar notas que "Subir" baja o viceversa, **apaga el variador** e intercambia **dos cables** de salida al motor (ej. U y V). No cambies la programación.
- **Entradas NPN:** El común de los selectores/finales de carrera debe ir a **GND** (terminal 5). Si usas PNP, cambia **P271 = 3** y lleva el común a **+24 V** (terminal 9).
- **Optimización:** DI4 puenteado con DI1 ahorra una salida del PLC. La transición 50→5 Hz usa la 2ª Rampa (2.0 s, suave). La parada final 5→0 Hz usa la 1ª Rampa (0.3 s, precisa).

---

## 📊 RESUMEN DE PARÁMETROS ESENCIALES

| Parámetro | Valor |
| :--- | :---: |
| P204 | 6 |
| P400 | 220 |
| P401 | 1.93 |
| P402 | 1450 |
| P403 | 50 |
| P404 | 2 |
| P202 | 0 |
| P133 | 0 |
| P134 | 50 |
| P263 | 1 |
| P264 | 8 |
| P265 | 13 |
| P266 | 14 |
| P220 | 0 |
| P223 | 4 |
| P224 | 1 |
| P221 | 8 |
| P124 | 50 |
| P126 | 5 |
| P100 | 2.0 |
| P101 | 0.3 |
| P102 | 2.0 |
| P103 | 2.0 |
| P104 | 1 |
| P105 | 2 |
| P299 | 0.0 |
| P300 | 1.0 |
| P301 | 3.0 |
| P302 | 20 |
| P135 | 3.9 |
| P156 | 2.32 |
| P157 | 2.32 |
| P158 | 2.32 |
| P271 | 0 |
| P230 | 0 |
| P297 | 5.0 |

Con estos parámetros y la conexión puenteada de DI1 y DI4, tu CFW300 monofásico ejecutará la secuencia completa de ascensor: arranque suave, crucero rápido, transición suave a velocidad lenta, y parada final precisa con frenado CC. **Configuración lista para producción.** Si algo no funciona como esperas, puedes consultarme.
