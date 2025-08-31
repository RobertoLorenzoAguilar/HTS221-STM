# Proyecto en Mantenimiento

⚠️ **Aviso:** Este repositorio se encuentra actualmente en mantenimiento.

Estamos realizando actualizaciones y mejoras en el proyecto.  
Por favor, regresa más tarde para acceder a la versión actualizada.

Si necesitas contactarnos, puedes abrir un **issue** y lo atenderemos lo antes posible.

Gracias por tu comprensión.

---

## Imágenes del Proyecto

![Imagen 1](https://github.com/user-attachments/assets/f97e6590-3e1a-498a-98dd-7c03f20c243a)
![Imagen 2](https://github.com/user-attachments/assets/1e915c40-be40-4796-9c23-7c4585eacf3d)
![Imagen 3](https://github.com/user-attachments/assets/c5fd5a4a-5089-4421-b5b7-f6dd718276e7)
![Imagen 4](https://github.com/user-attachments/assets/82ac4ed5-0a15-4d7e-9bf1-b14f33355189)
![Imagen 5](https://github.com/user-attachments/assets/9aaa9c5f-b065-4e2c-90f0-4d1219c27852)
![Imagen 6](https://github.com/user-attachments/assets/3c806a4b-1fde-4b23-90c0-040063ec292c)
![Imagen 7](https://github.com/user-attachments/assets/6b528adb-0a3c-4486-b388-b663440805a7)
![Imagen 8](https://github.com/user-attachments/assets/e643d9a0-e733-4a2b-9eb9-595e39d77e4c)

**Archivos de Código: hts221.c**

![Imagen 9](https://github.com/user-attachments/assets/bb41a8bf-9f8c-4a62-ba5e-9af66b07dc52)
![Imagen 10](https://github.com/user-attachments/assets/d8ce6ce6-e48c-4b69-93c0-15b6dd12cf14)
![Imagen 11](https://github.com/user-attachments/assets/3e0b3e47-8dfa-4c80-9333-4176c39a3d44)
![Imagen 12](https://github.com/user-attachments/assets/b56f8bc2-0948-4d58-8551-75020f05595f)

---

# Flujo del Código - Sensor HTS221

Este documento describe el flujo del programa **main.c** para el sensor de temperatura y humedad **HTS221** en la placa **B-U585I-IOT02A**.

---

## Inicialización

- Configura el sensor en **modo activo a 1 Hz**:

```c
if (HTS221_Init_Active_1Hz() != HAL_OK) {
    print("HTS221 init failed
");
    Error_Handler();
}
```

- Si ocurre un error, se notifica por UART y el sistema entra en `Error_Handler`.

---

## Calibración

- Carga los coeficientes de **temperatura** y **humedad** desde el sensor.
- Los guarda en variables globales (`gCal`, `gHumCal`) para convertir los valores crudos.

```c
if (HTS221_LoadTempCalibration(&gCal) != HAL_OK || gCal.loaded == 0) {
    print("Calib read failed
");
    Error_Handler();
}

if (HTS221_LoadHumCalibration(&gHumCal) != HAL_OK || gHumCal.loaded == 0) {
    printf("Error: no se pudo cargar calibración de humedad
");
    Error_Handler();
} else {
    printf("Calibración de humedad cargada OK
");
}
```

---

## Bucle Principal

1. Lee los valores crudos:

```c
HTS221_ReadTempRaw(&t_raw);
HTS221_ReadHumRaw(&hum_raw);
```

2. Convierte a unidades físicas:

```c
float t_c = HTS221_ConvertTemp_C(&gCal, t_raw);
float hum = HTS221_ConvertHum_RH(&gHumCal, hum_raw);
```

3. Envía los resultados por **UART**:

```c
char msg[96];
snprintf(msg, sizeof(msg),
         "T_RAW=%d  T=%.2f C  Hum=%.2f %%RH (raw=%d)
",
         t_raw, t_c, hum, hum_raw);
print(msg);
```

---

## Frecuencia de Muestreo

- El bucle se repite cada **1 segundo** con `HAL_Delay(1000)` para una lectura continua.

---

Este proyecto muestra cómo leer y convertir datos de temperatura y humedad usando el sensor **HTS221** en la placa **B-U585I-IOT02A**, mostrando los resultados por UART.
