# TP1 Horno de Microondas

### Consigna a cumplir
El horno debe contar con tres modos de cocción seleccionable por el botón de modo. Debe incluir un botón de comenzar/terminar y un sensor de apertura de puerta.

### Descripcion de funcionamiento
El horno permanecera en modo espera **IDLE** hasta que el usuario presione el boton comenzar (dispara **evComenzarTerminar**) lo cual llevara al estado **CALENTANDO** con el modo de calentamiento por defecto (modo 1) o presione el boton de modo (dispara **evSeleccionarModo**) entrando en el estado **SELECTOR MODO** cambiando de modo (a modo 2), si el usuario vuelve a presionar el boton cambio de modo, cambiara al modo 3, si lo presiona otra vez pasara al modo 1. Desde el estado **SELECTOR MODO** el usuario puede presionar el boton comenzar/terminar para comenzar a calentar en el modo seleccionado.

Si en el proceso de calentar el usuario abre la puerta, se disparara el evento **evPuertaAbierta** lo cual detendra el proceso de calentar y llevara al microondas al modo **IDLE** otra vez.

Siempre que el horno este calentando, una luz roja titilara. Esto se logra con las señales internas **si/noTitilar**.

### Diagrama de estados
![](https://i.imgur.com/8C7jL4Z.png)

### Código Yakindu SCT [horno_microondas.sct]
```
/* Control horno microondas */
 
//todo lo que se relaciona con el mundo exterior
interface:

// Eventos que son entradas para el sistema
in event evPuertaAbierta
in event evComenzarTerminar
in event evSeleccionarModo

// Operaciones
operation opCalentador(Action:boolean, Mode: integer): void
operation opLuzRoja(Action:boolean,Status:boolean): void

const ON: boolean = true
const OFF: boolean = false

const ONoFF: boolean = true
const TOGGLE: boolean = true

internal:
var viModoElegido: integer=1

//Eventos Internos (señales internas)
event siTitilar
event siNoTitilar
```
