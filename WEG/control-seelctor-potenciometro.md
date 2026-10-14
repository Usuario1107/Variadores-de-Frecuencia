# 📋 GUÍA RÁPIDA – CFW300 con Selector y Potenciómetro

## 🎯 Tu aplicación
- **Selector de 1 hilo** → arranca y para el motor
- **Potenciómetro 10 kΩ** → varía la frecuencia (velocidad)
- **Motor**: 220 V, 50 Hz, 1.93 A, conexión Delta

---

## 🔌 CONEXIONES FÍSICAS

| Elemento | Terminal variador | Nota |
|----------|-------------------|------|
| Selector (un hilo) | **DI1** (terminal 1) | El otro extremo del selector va a **GND** (terminal 5) |
| Potenciómetro – extremo 1 | **+10 V** (terminal 9) | Fuente que da el variador |
| Potenciómetro – extremo 2 | **GND** (terminal 7) | Referencia 0 V |
| Potenciómetro – cursor (centro) | **AI1** (terminal 8) | Señal de tensión 0-10 V |
| Motor | **U, V, W** | Conexión Delta |
| Tierra | **PE** | Obligatorio |

> ⚠️ El selector debe ser **NA (normalmente abierto)**. Al cerrarlo, DI1 se conecta a GND y arranca. Al abrirlo, se detiene.

---

## ⚙️ PARÁMETROS A PROGRAMAR

### 🔴 ESENCIALES (sin estos no funciona)

| Parámetro | Valor | ¿Para qué sirve? |
|-----------|-------|------------------|
| **P204** | **6** | Carga estándar de fábrica para **50 Hz** (tu motor es de 50 Hz) |
| **P296** | **2** | Tensión de red 200-240 V (si tu red es 220 V trifásica) |
| **P400** | **220** | Tensión nominal del motor (dato de placa) |
| **P401** | **1.93** | Corriente nominal del motor (dato de placa) |
| **P403** | **50** | Frecuencia nominal del motor (dato de placa) |
| **P402** | **según placa** | RPM del motor (ej. 1450 si es 4 polos a 50 Hz) |
| **P133** | **0** | Frecuencia mínima (0 Hz = arranca desde cero) |
| **P134** | **50** | Frecuencia máxima (igual a la nominal) |
| **P220** | **0** | Siempre Local (no depende de otras entradas) |
| **P221** | **1** | Referencia de velocidad vía **AI1** (potenciómetro) |
| **P224** | **1** | Comando Gira/Para vía **DIx** (selector) |
| **P263** | **1** | **DI1** = función Gira/Para |
| **P231** | **0** | **AI1** = Referencia de Velocidad |
| **P233** | **0** | Señal de AI1 = **0 a 10 V** |
| **P230** | **0** | Zona muerta inactiva (el motor arranca desde 0 Hz) |

---

### 🟡 OPCIONALES (mejoran el funcionamiento, pero no son obligatorios)

| Parámetro | Valor sugerido | ¿Para qué sirve? |
|-----------|----------------|------------------|
| **P202** | **0** | Tipo de control V/f lineal. Usa **1** si es bomba/ventilador (V/f cuadrático, ahorra energía) |
| **P100** | **5.0** | Tiempo de aceleración en segundos (0→50 Hz). Ajústalo a gusto |
| **P101** | **5.0** | Tiempo de desaceleración en segundos (50→0 Hz) |
| **P232** | **1.000** | Ganancia de AI1. Solo cambia si el potenciómetro no recorre todo el rango |
| **P234** | **0.0** | Offset de AI1. Solo si quieres que el motor arranque a cierta frecuencia mínima |
| **P235** | **0.00** | Filtro de AI1. Auméntalo si la velocidad oscila |
| **P404** | **según placa** | Potencia nominal del motor (útil para el modelo térmico) |
| **P156 / P157 / P158** | **1.05 x P401** | Corrientes de sobrecarga del motor (protección térmica) |
| **P271** | **0** | Señal de DI como NPN (por defecto). Cambia a **1/2/3** si usas PNP |

---

## ▶️ CÓMO OPERAR

1. **Potenciómetro al mínimo** (0 V) – el motor no debe girar.
2. **Selector a ON** → el motor arranca y acelera hasta la frecuencia mínima (P133 = 0 Hz).
3. **Gira el potenciómetro lentamente** → la velocidad aumenta proporcionalmente hasta 50 Hz.
4. **Selector a OFF** → el motor desacelera y se detiene según P101.

---

## 🔄 CÓMO RESTAURAR DE FÁBRICA

Si algo sale mal o quieres empezar de cero:

1. Presiona **P** para entrar a programación.
2. Navega hasta **P204**.
3. Cambia a **6** (50 Hz) o **5** (60 Hz).
4. Presiona **P** para confirmar.
5. El variador vuelve a valores de fábrica.

> ⚠️ Después de restaurar, **P296, P295 y P308 NO se modifican**. Verifica que P296 sea correcto para tu red.

---

## ⚠️ SOLUCIÓN DE PROBLEMAS RÁPIDOS

| Problema | Causa probable | Solución |
|----------|----------------|----------|
| El motor no arranca con el selector | DI1 mal configurada o mal cableada | Verifica P263 = 1 y que el común del selector vaya a **GND** (NPN) |
| El motor no varía con el potenciómetro | AI1 mal configurada | Verifica P221 = 1, P231 = 0, P233 = 0 |
| El motor gira al revés | Fases invertidas | Apaga el variador e intercambia **dos cables** del motor (ej. U y V) |
| El potenciómetro funciona al revés | Polaridad invertida | Intercambia las conexiones en **+10 V** y **GND** del potenciómetro |
| Aparece "CONF" en la pantalla | Parámetros incompatibles | Revisa que P221 y P224 no estén en conflicto |
| El motor se calienta mucho | Boost de torque alto (P136) | Baja P136 a 0 o al mínimo necesario |

---

## 📌 NOTAS FINALES

- **Un solo hilo del selector** va a DI1; el otro extremo del selector debe ir a **GND** (para NPN, que es el defecto de fábrica).
- El potenciómetro **NO** necesita fuente externa; el variador le da los 10 V desde el terminal 9.
- Si el motor tiene **1.93 A** y el variador es de **2.6 A**, no hay problema: el motor está dentro del rango del variador.
- Si usas **PNP** (positivo común), cambia **P271 = 1** o **3** y lleva el común del selector a **+24 V** en lugar de GND.

Con esta guía deberías poder hacer funcionar tu motor sin problemas. Si aparece alguna alarma o algo no responde como esperas, puedes consultarme.

---
---

## 🔄 ANEXO: Control de dos sentidos de giro (Avance/Retroceso) con DI1 y DI2

Si ya tienes configurado DI1 para un solo sentido (Gira/Para) y quieres usar **DI2 para el otro sentido**, cambia la configuración de la siguiente manera.

### 🔌 Conexión del selector de 3 posiciones
- **Común del selector** → **GND** (terminal 5)
- **Posición 1 (Avance)** → **DI1** (terminal 1)
- **Posición 2 (Retroceso)** → **DI2** (terminal 2)

### ⚙️ Parámetros a modificar

| Parámetro | Valor anterior | **Nuevo valor** | ¿Para qué sirve? |
|-----------|----------------|-----------------|------------------|
| **P223** | 0 | **4 (Dlx)** | Habilita que el sentido de giro se seleccione por entradas digitales |
| **P263** | 1 (Gira/Para) | **4 (Avance)** | DI1 ahora es "Avance" (giro horario) |
| **P264** | 0 | **5 (Retroceso)** | DI2 ahora es "Retroceso" (giro antihorario) |

> **Nota:** El parámetro **P224** puede quedarse en **1 (Dlx)**. El comando Gira/Para se activa automáticamente al poner cualquiera de las dos entradas en ON. Si ambas están en OFF, el motor se detiene.

### ▶️ Cómo operar
- **Selector en posición 1 (DI1 activa)** → motor gira en un sentido (Avance).
- **Selector en posición 2 (DI2 activa)** → motor gira en sentido contrario (Retroceso).
- **Selector en posición central (ambas OFF)** → motor detenido.

### ⚠️ Ajuste del sentido físico real
Si al probar notas que el sentido real no coincide con lo que esperas (ej. "Avance" gira al revés), **apaga el variador** e intercambia **dos cables cualesquiera** de salida al motor (por ejemplo, U y V). Esto invierte el campo magnético y corrige la dirección sin tocar la programación.

### 📌 Resumen de parámetros finales para dos sentidos
```text
P220 = 0   (Siempre Local)
P223 = 4   (Sentido por Dlx)
P224 = 1   (Gira/Para por Dlx)
P263 = 4   (DI1 = Avance)
P264 = 5   (DI2 = Retroceso)

---
 Configuración del Relé de Salida (DO1)
El parámetro a modificar es P275 (Función de la Salida DO1).

Valor a programar: P275 = 4 (Función F = F*).
