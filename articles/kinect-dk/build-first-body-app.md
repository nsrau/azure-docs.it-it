---
title: "Avvio rapido: Creare un'applicazione di rilevamento del corpo di Azure Kinect"
description: Istruzioni dettagliate per creare la prima applicazione di rilevamento del corpo di Azure Kinect
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensore, sdk, corpo, rilevamento, articolazione, prima, applicazione
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277843"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Avvio rapido: Creare un'applicazione di rilevamento del corpo di Azure Kinect

Introduzione a Body Tracking SDK Questo argomento di avvio rapido consente di diventare immediatamente operativi con il rilevamento del corpo. È possibile trovare altri esempi in questo [repository Azure-Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="prerequisites"></a>Prerequisiti

- [Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Configurare Body Tracking SDK](body-sdk-setup.md)
- Seguire questo argomento di avvio rapido per [creare la prima applicazione di Azure Kinect](build-first-app.md).
- Acquisire familiarità con le funzioni seguenti di Sensor SDK:
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Esaminare la documentazione sulle funzioni seguenti di Body Tracking SDK:
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Headers

Il rilevamento del corpo usa una singola intestazione, `k4abt.h`. Includere questa intestazione oltre a `k4a.h`. Verificare che il compilatore scelto sia configurato per le cartelle `lib` e `include` di Sensor SDK e Body Tracking SDK. È anche necessario collegarsi ai file `k4a.lib` e `k4abt.lib`. Per eseguire l'applicazione, è necessario che `k4a.dll`, `k4abt.dll`, `onnxruntime.dll` e `dnn_model.onnx` siano presenti nel percorso di esecuzione delle applicazioni.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Aprire il dispositivo e avviare la videocamera

La prima applicazione di rilevamento del corpo presuppone un singolo dispositivo Azure Kinect connesso al PC.

Il rilevamento del corpo si basa su Sensor SDK. Per usare il rilevamento del corpo, è prima necessario aprire e configurare il dispositivo. Usare la funzione [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) per aprire il dispositivo, quindi configurarlo con un oggetto [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html). Per ottenere risultati ottimali, impostare la modalità di profondità su `K4A_DEPTH_MODE_NFOV_UNBINNED` o `K4A_DEPTH_MODE_WFOV_2X2BINNED`. Lo strumento di rilevamento del corpo non viene eseguito se la modalità di profondità è impostata su `K4A_DEPTH_MODE_OFF` o `K4A_DEPTH_MODE_PASSIVE_IR`.

Per altre informazioni su come trovare e aprire il dispositivo, vedere [questa pagina](find-then-open-device.md).

Per altre informazioni sulle modalità di profondità di Azure Kinect, vedere le pagine seguenti di [specifiche hardware](hardware-specification.md) e di enumerazioni [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d).

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>Creare lo strumento di rilevamento

Il primo passaggio per ottenere i risultati del rilevamento del corpo consiste nel creare un apposito strumento. È necessaria la struttura [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) per la calibrazione dei sensori. È possibile eseguire query sulla calibrazione dei sensori con la funzione [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78).

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Ottenere le acquisizioni dal dispositivo Azure Kinect

Per altre informazioni sul recupero dei dati delle immagini, vedere [questa pagina](retrieve-images.md).

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Accodare l'acquisizione e prelevare i risultati

Lo strumento di rilevamento mantiene internamente una coda di input e una coda di output per elaborare in modo asincrono le acquisizioni di Azure Kinect DK con maggiore efficienza. Il passaggio successivo consiste nell'usare la funzione `k4abt_tracker_enqueue_capture()` per aggiungere una nuova acquisizione alla coda di input. Usare la funzione `k4abt_tracker_pop_result()` per prelevare un risultato dalla coda di output. Il valore di timeout dipende dall'applicazione e controlla il tempo di attesa in coda.

La prima applicazione di rilevamento del corpo usa il modello di elaborazione in tempo reale. Per una spiegazione dettagliata degli altri modelli, vedere [Ottenere i risultati del rilevamento del corpo](get-body-tracking-results.md).

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>Accedere ai dati dei risultati del rilevamento del corpo

I risultati del rilevamento del corpo per ogni acquisizione dei sensori vengono archiviati in una struttura [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) di fotogrammi del corpo. Ogni fotogramma del corpo contiene tre componenti chiave, ovvero una raccolta di strutture del corpo, una mappa di indice del corpo in 2D e l'acquisizione di input.

La prima applicazione di rilevamento del corpo accede solo al numero di corpi rilevati. Per una spiegazione dettagliata dei dati di un fotogramma del corpo, vedere [Accedere ai dati nel fotogramma del corpo](access-data-body-frame.md).

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Eseguire la pulizia

Il passaggio finale consiste nell'arrestare lo strumento di rilevamento del corpo e rilasciare il relativo oggetto. È anche necessario arrestare e chiudere il dispositivo.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Codice sorgente completo

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Ottenere i risultati del rilevamento del corpo](get-body-tracking-results.md)
