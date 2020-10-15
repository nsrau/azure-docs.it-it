---
title: Recuperare i dati dell'immagine Kinect di Azure
description: Informazioni su come recuperare i dati dell'immagine Kinect di Azure usando Kinect Sensor SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, recupero, sensore, fotocamera, SDK, profondità, RGB, immagini, colore, acquisizione, risoluzione, buffer
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87538915"
---
# <a name="retrieve-azure-kinect-image-data"></a>Recuperare i dati dell'immagine Kinect di Azure

Questa pagina fornisce informazioni dettagliate su come recuperare le immagini dal Kinect di Azure. Questo articolo illustra come acquisire e accedere alle immagini coordinate tra il colore e la profondità del dispositivo. Per accedere alle immagini, è prima necessario aprire e configurare il dispositivo, quindi è possibile acquisire le immagini.
Prima di configurare e acquisire un'immagine, è necessario [trovare e aprire il dispositivo](find-then-open-device.md).

È anche possibile fare riferimento all' [esempio di streaming SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) che illustra come usare le funzioni in questo articolo.

Vengono illustrate le funzioni seguenti:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Configurare e avviare il dispositivo

Le due fotocamere disponibili sul dispositivo Kinect supportano più modalità, risoluzioni e formati di output. Per un elenco completo, vedere le [specifiche hardware](hardware-specification.md)del kit di sviluppo Kinect di Azure.

La configurazione di streaming viene impostata utilizzando i valori nella [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) struttura.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Una volta avviate le fotocamere, continueranno a acquisire i dati fino a quando non [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) viene chiamato o il dispositivo non viene chiuso.

## <a name="stabilization"></a>Stabilizzazione

Quando si avviano dispositivi con la funzionalità di sincronizzazione di più dispositivi, è consigliabile usare un'impostazione di esposizione fissa.
Il set di esposizioni manuale può richiedere fino a otto acquisizioni dal dispositivo prima che immagini e framerate si stabilizzi. L'esposizione automatica può richiedere fino a 20 acquisizioni prima che immagini e framerate si stabilizzi.

## <a name="get-a-capture-from-the-device"></a>Ottenere un'acquisizione dal dispositivo

Le immagini vengono acquisite dal dispositivo in modo correlato. Ogni immagine acquisita contiene un'immagine di profondità, un'immagine IR, un'immagine a colori o una combinazione di immagini.

Per impostazione predefinita, l'API restituirà un'acquisizione solo dopo aver ricevuto tutte le immagini richieste per la modalità flusso. È possibile configurare l'API per restituire le acquisizioni parziali con solo immagini di profondità o colori non appena sono disponibili cancellando il [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parametro di [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

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

Una volta che l'API ha restituito correttamente un'acquisizione, è necessario chiamare al [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) termine dell'utilizzo dell'oggetto Capture.

## <a name="get-an-image-from-the-capture"></a>Ottenere un'immagine dall'acquisizione

Per recuperare un'immagine acquisita, chiamare la funzione appropriata per ogni tipo di immagine. Uno dei valori possibili:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

[`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) Una volta terminato l'uso dell'immagine, è necessario chiamare su qualsiasi handle restituito da queste funzioni.

## <a name="access-image-buffers"></a>Accedere ai buffer di immagine

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) dispone di molte funzioni di accesso per ottenere le proprietà dell'immagine.

Per accedere al buffer di memoria dell'immagine, utilizzare [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

Nell'esempio seguente viene illustrato come accedere a un'immagine di profondità acquisita. Questo stesso principio si applica ad altri tipi di immagine. Tuttavia, assicurarsi di sostituire la variabile del tipo di immagine con il tipo di immagine corretto, ad esempio IR o color.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come acquisire e coordinare le immagini delle fotocamere tra il colore e la profondità usando il dispositivo Kinect di Azure. È anche possibile:

>[!div class="nextstepaction"]
>[Recuperare esempi di IMU](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Accedere ai microfoni](access-mics.md)
