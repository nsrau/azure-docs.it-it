---
title: Individuare e aprire il dispositivo Kinect di Azure
description: Informazioni su come trovare e aprire un dispositivo Kinect di Azure con Azure Kinect Senor SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, profondità, RGB, dispositivo, ricerca, apertura
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276664"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Individuare e aprire il dispositivo Kinect di Azure

Questo articolo descrive come trovare, quindi aprire il DK di Azure Kinect. Questo articolo illustra come gestire il caso in cui sono presenti più dispositivi connessi al computer.

È anche possibile fare riferimento all' [esempio di enumerazione SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) che illustra come usare le funzioni in questo articolo.

Vengono illustrate le funzioni seguenti:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Individuare il numero di dispositivi connessi

Per prima cosa, ottenere il numero di dispositivi Kinect di Azure attualmente connessi usando [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Aprire un dispositivo

Per ottenere informazioni su un dispositivo o per leggerne i dati, è necessario innanzitutto aprire un handle per il dispositivo usando [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

Il `index` parametro di [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) indica il dispositivo da aprire se ne sono presenti più di una connessa. Se si prevede di connettere un solo dispositivo, è possibile passare un argomento di `K4A_DEVICE_DEFAULT` o 0 per indicare il primo dispositivo.

Ogni volta che si apre un dispositivo, è necessario chiamare al [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) termine dell'utilizzo dell'handle. Non è possibile aprire altri handle sullo stesso dispositivo fino a quando non si chiude l'handle.

## <a name="identify-a-specific-device"></a>Identificare un dispositivo specifico

I dispositivi Order enumerate by index non cambiano fino a quando i dispositivi non vengono collegati o scollegati. Per identificare un dispositivo fisico, è necessario usare il numero di serie del dispositivo.

Per leggere il numero di serie dal dispositivo, usare la [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) funzione dopo aver aperto un handle.

In questo esempio viene illustrato come allocare la quantità di memoria corretta per archiviare il numero di serie.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Aprire il dispositivo predefinito

Nella maggior parte delle applicazioni, verrà collegato solo un singolo Kinect di Azure per lo stesso computer. Se è necessario connettersi solo al dispositivo singolo previsto, è possibile chiamare [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) con `index` di `K4A_DEVICE_DEFAULT` per aprire il primo dispositivo.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
>[Recupera immagini](retrieve-images.md)

>[!div class="nextstepaction"]
>[Recuperare gli esempi di IMU](retrieve-imu-samples.md)

