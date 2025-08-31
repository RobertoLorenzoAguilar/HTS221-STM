# Proyecto en Mantenimiento

⚠️ **Aviso:** Este repositorio actualmente se encuentra en mantenimiento.

Estamos realizando actualizaciones y mejoras en el proyecto.  
Por favor, vuelva más tarde para acceder a la versión actualizada.

Si necesitas contactarnos, puedes abrir un **issue** y lo atenderemos en cuanto sea posible.

Gracias por tu comprensión.



<img width="1476" height="842" alt="image" src="https://github.com/user-attachments/assets/f97e6590-3e1a-498a-98dd-7c03f20c243a" />


<img width="616" height="601" alt="image" src="https://github.com/user-attachments/assets/1e915c40-be40-4796-9c23-7c4585eacf3d" />


<img width="536" height="602" alt="image" src="https://github.com/user-attachments/assets/c5fd5a4a-5089-4421-b5b7-f6dd718276e7" />

<img width="638" height="367" alt="image" src="https://github.com/user-attachments/assets/82ac4ed5-0a15-4d7e-9bf1-b14f33355189" />

<img width="933" height="803" alt="image" src="https://github.com/user-attachments/assets/9aaa9c5f-b065-4e2c-90f0-4d1219c27852" />


<img width="821" height="603" alt="image" src="https://github.com/user-attachments/assets/3c806a4b-1fde-4b23-90c0-040063ec292c" />

<img width="914" height="743" alt="image" src="https://github.com/user-attachments/assets/6b528adb-0a3c-4486-b388-b663440805a7" />
<img width="1198" height="752" alt="image" src="https://github.com/user-attachments/assets/e643d9a0-e733-4a2b-9eb9-595e39d77e4c" />

hts221.c
<img width="1191" height="760" alt="image" src="https://github.com/user-attachments/assets/bb41a8bf-9f8c-4a62-ba5e-9af66b07dc52" />
<img width="707" height="514" alt="image" src="https://github.com/user-attachments/assets/d8ce6ce6-e48c-4b69-93c0-15b6dd12cf14" />

<img width="857" height="535" alt="image" src="https://github.com/user-attachments/assets/3e0b3e47-8dfa-4c80-9333-4176c39a3d44" />

<img width="785" height="714" alt="image" src="https://github.com/user-attachments/assets/b56f8bc2-0948-4d58-8551-75020f05595f" />





# Flujo del Código - Sensor HTS221

Este documento resume el flujo del programa **main.c** en lo relacionado al
sensor de **temperatura y humedad HTS221** en la placa B-U585I-IOT02A.

---

##  Inicialización

- El sensor se configura en **modo activo a 1 Hz** con `HTS221_Init_Active_1Hz()`.
- Si ocurre un error, se envía un mensaje por UART y el sistema entra en
`Error_Handler`.

---

##  Calibración

- Se cargan los coeficientes de **temperatura** y **humedad** desde el sensor.
- Se almacenan en variables globales (`gCal`, `gHumCal`) para realizar las
conversiones de los valores crudos.

---

##  Bucle Principal

1. Se leen los valores crudos:
   - `HTS221_ReadTempRaw(&t_raw)`
   - `HTS221_ReadHumRaw(&hum_raw)`
2. Se convierten a unidades físicas:
   - Temperatura en °C → `HTS221_ConvertTemp_C(&gCal, t_raw)`
   - Humedad relativa en %RH → `HTS221_ConvertHum_RH(&gHumCal, hum_raw)`
3. Se envían los resultados por **UART** para monitoreo en consola.

---

##  Frecuencia de muestreo

- El bucle se repite cada **1 segundo** (`HAL_Delay(1000)`), permitiendo una
lectura continua del ambiente.

---

Lectura de Sensor HTS221 - STM32 B-U585I-IOT02A

Este proyecto demuestra cómo leer y convertir datos de temperatura y humedad usando el sensor HTS221 en la placa B-U585I-IOT02A, mostrando los resultados por UART.

Flujo del Código - Sensor HTS221

Inicialización:

* El sensor se configura en modo activo a 1 Hz con HTS221\_Init\_Active\_1Hz().
* Si ocurre un error, se envía un mensaje por UART y el sistema entra en Error\_Handler.

Código:
if (HTS221\_Init\_Active\_1Hz() != HAL\_OK) {
print("HTS221 init failed\r\n");
Error\_Handler();
}

Calibración:

* Se cargan los coeficientes de temperatura y humedad desde el sensor.
* Se almacenan en variables globales (gCal, gHumCal) para realizar las conversiones de los valores crudos.

Código:
if (HTS221\_LoadTempCalibration(\&gCal) != HAL\_OK || gCal.loaded == 0) {
print("Calib read failed\r\n");
Error\_Handler();
}

if (HTS221\_LoadHumCalibration(\&gHumCal) != HAL\_OK || gHumCal.loaded == 0) {
printf("Error: no se pudo cargar calibración de humedad\r\n");
Error\_Handler();
} else {
printf("Calibración de humedad cargada OK\r\n");
}

Bucle Principal:

1. Se leen los valores crudos.
2. Se convierten a unidades físicas: °C y %RH.
3. Se envían los resultados por UART.

Código:
while (1) {
int16\_t t\_raw = 0;
int16\_t hum\_raw = 0;

```
if (HTS221_ReadTempRaw(&t_raw) == HAL_OK && HTS221_ReadHumRaw(&hum_raw) == HAL_OK) {

    float t_c = HTS221_ConvertTemp_C(&gCal, t_raw);
    float hum = HTS221_ConvertHum_RH(&gHumCal, hum_raw);

    char msg[96];
    snprintf(msg, sizeof(msg),
             "T_RAW=%d  T=%.2f C  Hum=%.2f %%RH (raw=%d)\r\n",
             t_raw, t_c, hum, hum_raw);

    print(msg);

} else {
    print("Read error\r\n");
}

HAL_Delay(1000); // Frecuencia de muestreo: 1 segundo
```

}

