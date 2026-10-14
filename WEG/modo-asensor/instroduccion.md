# README – Aplicación Ascensor con WEG CFW300

## ¿Qué quiero lograr?

Quiero controlar el motor de un ascensor de 2 o 3 pisos usando un variador de frecuencia WEG CFW300 ............. El objetivo principal es que el ascensor se detenga de forma **suave y precisa** al llegar al piso, sin tirones ni golpes, y que el recorrido entre pisos sea rápido y eficiente.

---

## ¿Cómo debe comportarse el ascensor?

1. **Arranque:** El ascensor debe acelerar suavemente desde que se le da la orden de marcha hasta alcanzar una **velocidad de crucero** (rápida) para viajar entre pisos.
2. **Aproximación:** Cuando el ascensor esté cerca del piso destino (detectado por un final de carrera de pre-parada), debe **reducir la velocidad** a un valor lento y controlado. Esta transición debe ser suave, sin frenazos bruscos.
3. **Parada final:** Cuando el ascensor llegue exactamente al nivel del piso (detectado por un final de carrera de parada), el motor debe **detenerse por completo** de forma precisa, quedando "clavado" en la posición sin moverse.
4. **Dirección:** El ascensor debe poder subir y bajar según la orden que reciba.

---

## ¿Qué elementos uso para controlarlo?

- **Variador WEG CFW300** controlando el motor trifásico.
- **Finales de carrera** (contactos on/off) que se activan cuando el ascensor pasa por ciertos puntos:
  - Uno de **pre-parada** (antes de llegar al piso, para reducir velocidad).
  - Uno de **parada o nivelación** (al llegar exactamente al piso, para detener el motor Y QUE QUEDE JUSTO).
- **Selectores o contactos** que simulan las órdenes de marcha, dirección y velocidad lenta.

---

## ¿Cómo quiero que se controlen las entradas digitales?

Quiero usar **4 entradas digitales** en el variador para toda la lógica:

| Entrada | Función         | ¿Qué hace?                                                                         |
| :------ | :-------------- | :--------------------------------------------------------------------------------- |
| **DI1** | Gira/Para       | Activa el arranque y la parada general del motor.                                  |
| **DI2** | Sentido de Giro | Selecciona si el ascensor sube o baja.                                             |
| **DI3** | Multispeed      | Activa la velocidad lenta (aproximación) cuando el ascensor está llegando al piso. |
| **DI4** | 2ª Rampa        | Activa la rampa de desaceleración suave para la transición de 50 Hz a 5 Hz.        |

**¿Por qué 4 entradas y no 3?**
Porque la transición de velocidad rápida a lenta no puede hacerse con una sola rampa. Si uso una rampa corta, el motor frena de golpe al pasar de 50 Hz a 5 Hz (tirón mecánico y pico de corriente). Si uso una rampa larga, la parada final desde 5 Hz a 0 Hz sería lenta y el ascensor se pasaría del piso. Por eso necesito **dos rampas diferentes**: una suave para la transición 50→5 Hz (2ª Rampa) y una corta para la parada final 5→0 Hz (1ª Rampa). La DI4 se encarga de activar la 2ª Rampa justo cuando se activa la velocidad lenta.

**¿Por qué una sola entrada para el sentido de giro?**
Porque usar una sola entrada para el sentido (en lugar de una para Avance y otra para Retroceso) evita conflictos lógicos. Es físicamente imposible ordenar subir y bajar al mismo tiempo, lo que hace el sistema más seguro y confiable. Si se usaran dos entradas separadas (Avance y Retroceso) y por un error se activaran ambas, el variador entraría en estado CONF y detendría el motor de forma abrupta.

---

## ¿Cómo quiero que se comporte la velocidad?

- **Velocidad de crucero:** Rápida (ejemplo: 50 Hz) para viajar entre pisos.
- **Velocidad de aproximación:** Lenta (ejemplo: 5 Hz) para acercarse al piso con precisión.

La transición de rápida a lenta debe ser **suave**, no un cambio brusco. Para lograrlo, el variador debe usar una rampa de desaceleración más larga y controlada (2ª Rampa) en esa etapa.

La parada final (de lento a cero) debe ser **rápida y precisa**, usando una rampa de desaceleración muy corta (1ª Rampa), para que el ascensor no se pase del piso ni se detenga antes.

---

## ¿Cómo quiero que se detenga en el piso?

El frenado final debe combinar:

- Una rampa de desaceleración corta (para detenerse rápido al recibir la orden de parada).
- parametro extra que me ayuda ha lograr eso si tien extras

---

## ¿Qué problema quiero resolver?

El problema principal es que un frenado con un tiempo de desaceleración largo hace que el motor se detenga antes de llegar al piso. Pero un frenado con tiempo corto desde alta velocidad produce un golpe o tirón mecánico. La solución es **llegar al piso ya en velocidad lenta** y desde ahí hacer la parada final con rampa corta y frenado CC. Así se logra suavidad y precisión al mismo tiempo.

Además, el variador no cambia de rampa automáticamente al cambiar de velocidad. Necesito decirle explícitamente cuándo usar la 2ª Rampa (suave) y cuándo usar la 1ª Rampa (corta). Eso se logra con la entrada DI4.

---

## ¿Cómo debe ser la secuencia completa de operación?

| Paso | Acción                | DI1 (Gira/Para) | DI2 (Sentido) | DI3 (Multispeed) | DI4 (2ª Rampa) | Resultado                                         |
| :--: | :-------------------- | :-------------: | :-----------: | :--------------: | :------------: | :------------------------------------------------ |
|  1   | Ascensor detenido     |       OFF       |       X       |        X         |       X        | Motor parado                                      |
|  2   | Arranque en crucero   |       ON        |      OFF      |       OFF        |      OFF       | Sube a 50 Hz (rampa de aceleracion 1 lenta)       |
|  3   | Pre-parada (llegando) |       ON        |      OFF      |        ON        |       ON       | Baja a 5 Hz (con desaceleracion larga)            |
|  4   | Parada final          |       OFF       |       X       |        X         |       X        | Motor se detiene preciso (con desacelacion corta) |
|  5   | Arranque en crucero   |       ON        |      ON       |       OFF        |      OFF       | Baja a 50 Hz (rampa de aceleracion 1 lenta)       |
|  6   | Pre-parada (llegando) |       ON        |      ON       |        ON        |       ON       | Baja a 5 Hz (con desaceleracion larga)            |
|  7   | Parada final          |       OFF       |       X       |        X         |       X        | Motor se detiene preciso con desacelacion corta   |

**Nota:** "X" significa que el estado no importa (la orden de Gira/Para es prioritaria).

---

## ¿Qué necesito del manual o del asistente?

Necesito que me den los **parámetros exactos** para configurar el CFW300 y lograr todo lo descrito arriba. Específicamente:

0. resetear de fabria y lo paramtros basicos del motor
1. Parámetros para configurar las 4 entradas digitales (DI1 a DI4) con sus funciones.
2. Parámetros para configurar las dos velocidades Multispeed (crucero y aproximación).
3. Parámetros para configurar las dos rampas de desaceleración (1ª y 2ª).
4. Parámetros para configurar el sentido de giro por una sola entrada digital.
5. Cualquier parámetro adicional necesario para que la lógica funcione correctamente.
