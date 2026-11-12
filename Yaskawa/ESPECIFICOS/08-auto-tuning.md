# Guía Completa: Auto-Sintonización (Auto-Tuning) y Seguridad del Variador
**Equipo:** Variador de Frecuencia Yaskawa GPD 515 (CIMR-G5M4018)

Este documento detalla el procedimiento para realizar la Auto-Sintonización (Auto-Tuning) del motor, la gestión de errores durante el proceso, el respaldo de los parámetros y la configuración de seguridad mediante contraseña y niveles de acceso.

---

## 1. Preparación Previa (Antes de Sintonizar)

El menú de Auto-Tuning llenará automáticamente los parámetros del motor (Grupo E2). Sin embargo, es obligatorio configurar previamente los datos base de la red eléctrica y el motor en el menú de Programación.

**Requisitos previos en el menú `Programming`:**
1.  **`A1-01` (Access Level):** Configurar en **`4`** (Advanced) para tener visibilidad total.
2.  **`A1-02` (Control Method):** Verificar que esté en **`2`** (Open Loop Vector). El Auto-Tuning no aparece en modo V/f (0).
3.  **`E1-01` (Input Voltage):** Configurar al voltaje de la red (ej. **`380`** V).
4.  **`E1-04` (Max Frequency):** Configurar a la frecuencia de la placa (ej. **`50.0`** Hz).
5.  **`E1-05` (Max Voltage):** Configurar al voltaje de la placa del motor (ej. **`380`** V).
6.  **`E1-06` (Base Frequency):** Configurar a la frecuencia de la placa (ej. **`50.0`** Hz).
7.  **`E1-02` (Motor Selection):** Configurar en **`0`** (Motor estándar TEFC) o **`2`** (Motor Vector). 

> ⚠️ **ADVERTENCIA CRÍTICA DE SEGURIDAD:** El motor **DEBE** estar desconectado de la carga mecánica (poleas, bombas, bandas). La Auto-Sintonización inyecta corriente y hace girar el motor. Si está acoplado, la prueba fallará o causará daños a la máquina.

---

## 2. Proceso de Auto-Sintonización (Paso a Paso)

*(Basado en la Sección 2.2a del manual, Páginas 48-50)*

1.  **Acceder al menú:** Presionar la tecla **MENU** y seleccionar **`Auto-Tuning`**. Presionar **DATA/ENTER**.
2.  **Ingresar Datos de la Placa:** El variador solicitará los datos uno por uno. Usar las flechas para cambiar el valor y **DATA/ENTER** para confirmar:
    *   **Rated Voltage:** Voltaje de placa (ej. `380` V).
    *   **Rated Current:** Corriente de placa (FLA) en Amperios (ej. `12.5` A).
    *   **Rated Frequency:** Frecuencia de placa (ej. `50.0` Hz).
    *   **Rated Speed:** Velocidad nominal en RPM (ej. `1450` RPM).
    *   **Number of Poles:** Número de polos (usualmente `4`).
    *   **Motor Mode:** Seleccionar el tipo de motor (usualmente `2` para Vector, o `0` para estándar).
3.  **Ejecutar la Prueba:** Cuando la pantalla muestre **`Tuning Ready? Press RUN key`**, presionar el botón **RUN** (Verde).
4.  **Proceso:** El motor emitirá un zumbido y luego girará a alta velocidad. La pantalla mostrará **`Tune Proceeding`**.
5.  **Finalización:** Si todo es correcto, la pantalla mostrará **`Tune Successful`** (Sintonización Exitosa).

---

## 3. ¿Qué hacer si la Auto-Sintonización falla? (Tune Aborted)

Si el proceso se interrumpe, la pantalla mostrará **`Tune Aborted`** seguido del motivo (Sección 6.2, Página 209). Aquí las causas y soluciones:

| Mensaje de Error | Causa Probable | Solución |
| :--- | :--- | :--- |
| **Data Invalid** (Datos Inválidos) | Los datos ingresados no coinciden con el motor o están fuera de rango. | Verificar los datos de la placa. Revisar que la corriente sea menor a la del variador (41A). |
| **Resistance** (Resistencia) | El motor está conectado a la carga o hay un mal contacto en los cables T1, T2, T3. | Desconectar el motor de la máquina. Apretar los terminales de salida. |
| **Over Load** (Sobrecarga) | El motor está trabajando muy forzado (muy grande o con carga pesada). | Desacoplar la carga. Aumentar el tiempo de aceleración (`C1-01`). |
| **Motor Speed** (Velocidad del Motor) | El motor no alcanzó la velocidad solicitada durante la prueba. | Desconectar la carga. Verificar que la placa del motor indique la velocidad correcta. |
| **PG Direction** (Dirección del PG) | (Solo con encoder) El sentido de giro del encoder es opuesto al del motor. | Intercambiar dos cables del motor (T1 y T2) o cambiar el parámetro `F1-05`. |

---

## 4. ¿Qué hacer si la Sintonización es Exitosa?

Si el mensaje es **`Tune Successful`**, el variador ha llenado automáticamente todos los parámetros del motor (`E2-01` al `E2-08`). 

**Pasos a seguir:**
1.  **Conectar la carga:** Volver a acoplar el motor a la máquina.
2.  **Verificar el funcionamiento:** Presionar **LOCAL/REMOTE** (apagar LEDs SEQ y REF). Subir la frecuencia (ej. 30 Hz) y presionar **RUN**. El motor debe girar suavemente.
3.  **Ajustar Rampas:** Si el motor arranca muy brusco o se dispara por sobrecorriente (`OC`), aumentar el parámetro **`C1-01`** (Aceleración). Si se dispara por sobretensión (`OV`) al frenar, aumentar **`C1-02`** (Desaceleración).
4.  **Verificar Sentido de Giro:** Si el sentido es incorrecto, apagar el equipo, esperar que el LED CHARGE se apague, e intercambiar dos cables en los terminales T1 y T2.

---

## 5. Guardar la Configuración (Copia de Seguridad)

Una vez que el equipo esté funcionando correctamente, es fundamental guardar la copia de seguridad (Sección 5.46, Página 193).

1.  Ir al parámetro **`o2-03`** (User Parameter Default Value).
2.  Configurarlo en **`1`** (Set Default). 
3.  Presionar **DATA/ENTER**. El equipo guardará hasta 50 parámetros modificados en su memoria no volátil.
4.  *Para restaurar esta copia en el futuro*, ir al parámetro **`A1-03`** (Initialize) y configurarlo en **`1110`** (User Initialize).

---

## 6. Bloquear el Equipo (Contraseña y Nivel de Acceso)

Para evitar que operadores no autorizados modifiquen la Auto-Sintonización, las rampas o el método de control, se debe configurar el equipo para que solo muestre los parámetros básicos.

### Paso A: Restringir el Nivel de Acceso
1.  Ir al parámetro **`A1-01`** (Access Level).
2.  Cambiarlo a **`2`** (Quick-start) o **`1`** (User Program). El equipo ahora solo mostrará los parámetros básicos o los que el usuario haya guardado en la lista `A2-01` al `A2-32`.

### Paso B: Crear la Contraseña
1.  Ir al parámetro **`A1-05`** (Password). *(Nota: Si no ves este parámetro, asegúrate de estar en nivel Avanzado `A1-01=4`)*.
2.  Escribir un número de 4 dígitos (ej. `1234`). Presionar **DATA/ENTER**.

### ¿Qué pasa si alguien intenta cambiar algo?
*   Si un operador intenta cambiar el Nivel de Acceso (`A1-01`) o cualquier otro parámetro, el equipo le pedirá la clave en la pantalla **`A1-04`** (Enter Password).
*   **No lo saca del menú ni tiene que reiniciar el equipo.** Simplemente, el valor no cambiará hasta que se ingrese la contraseña correcta.
*   **Para desbloquear el equipo:** Ir al parámetro **`A1-04`**, escribir `1234` y presionar **DATA/ENTER**. El acceso completo se restablece inmediatamente.
