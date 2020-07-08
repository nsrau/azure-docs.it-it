---
title: Recuperare gli esempi di IMU Kinect di Azure
description: Informazioni su come recuperare gli esempi di IMU Kinect di Azure con Azure Kinect SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, configure, depth, color, RBG, camera, Sensor, SDK, IMU, Motion Sensor, Motion, giroscopio, giroscopio, accelerometro, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276688"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Recuperare gli esempi di IMU Kinect di Azure

Il dispositivo Kinect di Azure fornisce l'accesso alle unità di movimento inerziali (IMUs), inclusi i tipi di accelerometro e giroscopio. Per accedere agli esempi di IMUs, è prima necessario aprire e configurare il dispositivo, quindi acquisire i dati di IMU. Per altre informazioni, vedere [trova e Apri dispositivo](find-then-open-device.md).

Gli esempi di IMU vengono generati con una frequenza molto più elevata rispetto alle immagini. Gli esempi vengono segnalati all'host a una frequenza inferiore rispetto a quella campionata. Quando si è in attesa di un campione IMU, più campioni diventeranno spesso disponibili nello stesso momento.

Per informazioni dettagliate sulla velocità di Reporting IMU, vedere la [specifica hardware](hardware-specification.md) di Kinect DK per Azure.

## <a name="configure-and-start-cameras"></a>Configurare e avviare le fotocamere

> [!NOTE]
> I sensori IMU possono funzionare solo quando le fotocamere colore e/o profondità sono in esecuzione. I sensori IMU non possono funzionare da soli.

Per avviare le fotocamere, utilizzare [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Esempi di accesso a IMU

 Ogni [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) contiene un accelerometro e la lettura di un giroscopio acquisita quasi allo stesso tempo.

È possibile ottenere gli esempi di IMU sullo stesso thread in cui si ottengono le acquisizioni di immagini o su thread separati.

Per recuperare gli esempi di IMU non appena sono disponibili, è consigliabile chiamare sul proprio [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) thread. L'API dispone anche di un accodamento interno sufficiente per consentire la verifica degli esempi solo dopo la restituzione di ogni acquisizione di immagini.

Poiché è presente una coda interna di esempi di IMU, è possibile usare il modello seguente senza eliminare i dati:

1. Attendere un'acquisizione, a ogni frequenza dei fotogrammi.
2. Elaborare l'acquisizione.
3. Recuperare tutti gli esempi di IMU in coda.
4. Ripetere l'attesa alla successiva acquisizione.

Per recuperare tutti gli esempi di IMU attualmente in coda, è possibile chiamare [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) con un valore `timeout_in_ms` pari a 0 in un ciclo fino a quando la funzione non restituisce `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT`indica che non sono presenti campioni in coda e che nessuno è arrivato nel timeout specificato.

## <a name="usage-example"></a>Esempio di utilizzo

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Passaggi successivi

Ora che si sa come usare gli esempi di IMU, è possibile anche
>[!div class="nextstepaction"]
>[Accedere ai dati di input del microfono](access-mics.md)
