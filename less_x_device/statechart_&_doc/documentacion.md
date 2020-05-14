# TP1 Statechart Proyecto LESS-X

### Requerimientos del proyecto
1) El equipo debe priorizar el bajo consumo por sobre los demas aspectos. La autonomia del equipo deberia ser como minimo de un año (Enviando mensajes cada 6 hs).

2) El equipo debe ser capaz de medir humedad y temperatura para luego enviar los mensajes a la nube.

3) El equipo debe incluir conectividad WiFi.

4) La frecuencia de medicion debe poder configurarse desde la nube.

5) El equipo debe tener la capacidad de reportar asincronicamente en caso de detectar condiciones especificas de humedad y temperatura.

6) El equipo debe poder auto-checkearse e informar de fallas al usuario.

### Descripcion de funcionamiento

#### Descripción de la máquina de estados principal

Al encender el equipo por primera vez (estado **FIRST BOOT**) el equipo va a llevar a cabo una serie de auto-verificaciones, en caso de encontrar fallas lo reportará al usuario.
Luego pasará al estado **MEASURE** en donde se preparara para realizar las actividades de medición y envío de mensajes entrando en el modo de operación opEnterMode(**MODE_FULL_ACTIVE**) y realizando las mediciones con opMeasure(**MEASURE_MODE_FULL**). 
Luego se procedera al envio del mensaje con la información recolectada (estado **SEND_MESSAGE**). Por ser el primer boot del equipo (**viFirstBoot** = true), se pasará directamente al pedido de configuraciones (estado **OTA CONFIG**) para luego entrar al ciclo **SLEEP/TICK**.

Una vez en el estado SLEEP el equipo entrará al modo de sueño profundo opEnterMode(**MODE_DEEP_SLEEP**) por el tiempo que haya configurado el usuario almacenado en **CONF_DEEP_SLEEP_TIME**. Una vez cumplido ese tiempo se pasará al estado TICK en donde se usará un modo de consumo mínimo que permita verificar condiciones de posibles alertas opEnterMode(**MODE_MINIMAL_ACTIVE**).
Una vez en el estado **TICK** se pasará al estado **MEASUR**E o se regresará al estado **SLEEP**.
Para que se realice lo primero la cantidad de "ticks" contados deberá coincidir con **CONF_TICKS_TO_MEASURE** seteados por el usuario o bien haberse detectado un evento de alerta **evAlertDetected**.
En caso de no cumplirse ninguna de las condiciones anteriormente mencionadas se regresará al estado **SLEEP**.
Cada vez que se salga del estado **SEND MESSAGE** se verificará si la cantidad de mensajes enviados coincide con la seteada por el usuario **CONF_MSGS_TO_GET_OTA_CONFIGS**. En caso de ser así se pedirá un nuevo string de configuraciones a la nube **opGetOtaConfigs**. Esta operacion debera verificar que los valores obtenidos son validos y deberá guardarlos en memoria RTC para no ser perdidos durante el modo **MODE_DEEP_SLEEP**.


### Diagrama de estados
![](https://i.imgur.com/FS2Cf2e.png)

### Código Yakindu SCT [less_x_device.sct]
```
/** LESS-X  ESP32 based Device */

interface:
// Events
in event evAlertDetected

// Actions
operation opCheckAlert(void:void): boolean
operation opEnterMode(Mode:integer)
operation opMeasure(MeasureMode:integer)
operation opSendMessage(void:void)
operation opGetOtaConfigs(void:void)

//Constants

// Modes of operation
const MODE_FULL_ACTIVE: integer = 0
const MODE_MINIMAL_ACTIVE: integer = 1
const MODE_DEEP_SLEEP: integer = 2

// Measure Modes
const MEASURE_MODE_ALERT: integer = 0
const MEASURE_MODE_FULL: integer = 1

//Configurable Params
const CONF_TICKS_TO_MEASURE: integer = 5
const CONF_MSGS_TO_GET_OTA_CONFIGS: integer = 3
const CONF_DEEP_SLEEP_TIME: integer = 50

// Internal vars
internal:
var viMessageCounter: integer
var viTickCounter: integer
var viFirstBoot: boolean
```

