---
title: Risultati del rilevamento del corpo di Azure Kinect Get
description: Informazioni su come ottenere i risultati del rilevamento del corpo usando Azure Kinect Body Tracking SDK.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, body, tracking, joint
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276757"
---
# <a name="get-body-tracking-results"></a>Ottenere i risultati del rilevamento del corpo

Body Tracking SDK usa un oggetto Body Tracker per elaborare le acquisizioni di Azure Kinect DK e genera i risultati del rilevamento del corpo. Mantiene inoltre lo stato globale dello strumento di rilevamento, le code di elaborazione e la coda di output. L'uso dello strumento di traccia del corpo prevede tre passaggi:

- Creazione di un tracker
- Acquisire immagini di profondità e IR con il DK di Azure
- Accodare l'acquisizione e pop i risultati.

## <a name="create-a-tracker"></a>Creazione di un tracker


Il primo passaggio nell'uso del rilevamento del corpo consiste nel creare uno strumento di individuazione e richiede il passaggio della calibrazione del sensore [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) struttura. È possibile eseguire query sulla calibrazione del sensore usando la funzione [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) di Azure Kinect Sensor SDK.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Acquisisci immagini Depth e IR

L'acquisizione di immagini con Azure Kinect DK è illustrata nella pagina [recuperare le immagini](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED``K4A_DEPTH_MODE_WFOV_2X2BINNED`le modalità o sono consigliate per prestazioni e accuratezza ottimali. Non usare le `K4A_DEPTH_MODE_OFF` modalità o `K4A_DEPTH_MODE_PASSIVE_IR` .

Le modalità DK di Azure Kinect supportate sono descritte nella [specifica hardware](hardware-specification.md) di Azure Kinect dk e nelle enumerazioni [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Accodare l'acquisizione e prelevare i risultati

Lo strumento di rilevamento mantiene internamente una coda di input e una coda di output per elaborare in modo asincrono le acquisizioni di Azure Kinect DK con maggiore efficienza. Utilizzare la funzione [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) per aggiungere una nuova acquisizione alla coda di input. Usare la funzione [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o pop un risultato della coda di output. L'utilizzo del valore di timeout dipende dall'applicazione e controlla il tempo di attesa dell'accodamento.

### <a name="real-time-processing"></a>Elaborazione in tempo reale
Usare questo modello per le applicazioni a thread singolo che necessitano di risultati in tempo reale e possono contenere i frame eliminati. L' `simple_3d_viewer` esempio disponibile in [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) è un esempio di elaborazione in tempo reale.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Elaborazione sincrona
Usare questo modello per le applicazioni che non necessitano di risultati in tempo reale o che non possono contenere i frame eliminati.

La velocità effettiva di elaborazione può essere limitata.

L' `simple_sample.exe` esempio disponibile in [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) è un esempio di elaborazione sincrona.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Accedere ai dati nel frame del corpo](access-data-body-frame.md)
