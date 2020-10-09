---
title: API di riproduzione Kinect di Azure
description: Informazioni su come usare l'SDK del sensore Kinect di Azure per aprire un file di registrazione usando l'API di riproduzione.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, profondità, RGB, record, riproduzione, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276658"
---
# <a name="the-azure-kinect-playback-api"></a>API di riproduzione Kinect di Azure

L'SDK del sensore fornisce un'API per la registrazione dei dati del dispositivo in un file Matroska (. MKV). Il formato del contenitore Matroska archivia le tracce video, gli esempi di IMU e la calibrazione del dispositivo. Le registrazioni possono essere generate tramite l'utilità della riga di comando [k4arecorder](record-sensor-streams-file.md) fornita. Le registrazioni possono anche essere personalizzate e registrate direttamente usando l'API di registrazione.

Per ulteriori informazioni sull'API di registrazione, vedere [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Per ulteriori informazioni sulle specifiche del formato di file Matroska, vedere la pagina relativa al [formato del file di registrazione](record-file-format.md) .

## <a name="use-the-playback-api"></a>Usare l'API di riproduzione

È possibile aprire i file di registrazione usando l'API di riproduzione. L'API di riproduzione fornisce l'accesso ai dati dei sensori nello stesso formato del resto dell'SDK del sensore.

### <a name="open-a-record-file"></a>Aprire un file di record

Nell'esempio seguente si apre una registrazione usando, si [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) stampa la lunghezza della registrazione e quindi si chiude il file con [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Leggi acquisizioni

Una volta aperto il file, è possibile iniziare a leggere le catture dalla registrazione. Nell'esempio seguente le acquisizioni vengono lette nel file.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Ricerca all'interno di una registrazione

Una volta raggiunta la fine del file, potrebbe essere necessario tornare indietro e leggerla nuovamente. Questo processo può essere eseguito leggendo le versioni precedenti di [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , ma potrebbe essere molto lento a seconda della durata della registrazione.
È invece possibile usare la [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) funzione per passare a un punto specifico del file.

In questo esempio vengono specificati i timestamp in microsecondi per cercare i vari punti del file.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Leggere le informazioni sui tag

Le registrazioni possono anche contenere diversi metadati, ad esempio il numero di serie del dispositivo e le versioni del firmware. Questi metadati vengono archiviati nei tag di registrazione, a cui è possibile accedere utilizzando la [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) funzione.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Elenco di tag di record

Di seguito è riportato un elenco di tutti i tag predefiniti che possono essere inclusi in un file di registrazione. Molti di questi valori sono disponibili come parte dello [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct e possono essere letti con la [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) funzione.

Se un tag non esiste, si presuppone che abbia il valore predefinito.

| Nome del tag                     | Valore predefinito      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Campo | Note     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | OFF              | `color_format` / `color_resolution`  | Valori possibili: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" e così via                                      |
| `K4A_DEPTH_MODE`             | OFF              | `depth_mode` / `depth_track_enabled` | Valori possibili: "OFF," NFOV_UNBINNED "," PASSIVE_IR "e così via                                                |
| `K4A_IR_MODE`                | OFF              | `depth_mode` / `ir_track_enabled`    | Valori possibili: "OFF", "ACTIVE", "PASSIVe"                                                                    |
| `K4A_IMU_MODE`               | OFF              | `imu_track_enabled`                  | Valori possibili: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.jssu" | N/D                                  | Vedere [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | Valore archiviato in nanosecondi, l'API fornisce microsecondi.                                                        |
| `K4A_WIRED_SYNC_MODE`        | STANDALONE       | `wired_sync_mode`                    | Valori possibili: "STANDALONE", "MASTER", "subordinata"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | Valore archiviato in nanosecondi, l'API fornisce microsecondi.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N/D                                  | Versione del firmware dei colori del dispositivo, ad esempio "1. x. XX"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N/D                                  | Versione del firmware di profondità del dispositivo, ad esempio "1. x. XX"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N/D                                  | Registrazione del numero di serie del dispositivo                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | Vedere [Sincronizzazione timestamp](record-playback-api.md#timestamp-synchronization) di seguito.                       |
| `K4A_COLOR_TRACK`            | nessuno               | N/D                                  | Vedere [registrazione del formato del file-identificazione delle tracce](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | nessuno               | N/D                                  | Vedere [registrazione del formato del file-identificazione delle tracce](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | nessuno               | N/D                                  | Vedere [registrazione del formato del file-identificazione delle tracce](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | nessuno               | N/D                                  | Vedere [registrazione del formato del file-identificazione delle tracce](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Sincronizzazione timestamp

Per il formato Matroska è necessario che le registrazioni inizino con un timestamp di zero. Quando si esegue la [sincronizzazione esterna delle fotocamere](record-external-synchronized-units.md), il primo timestamp da di ogni dispositivo può essere diverso da zero.

Per mantenere i timestamp originali dai dispositivi tra la registrazione e la riproduzione, il file archivia un offset da applicare ai timestamp.

Il `K4A_START_OFFSET_NS` tag viene usato per specificare un offset timestamp, in modo che i file possano essere risincronizzati dopo la registrazione. Questo offset del timestamp può essere aggiunto a ogni timestamp nel file per ricostruire i timestamp originali del dispositivo.

L'offset iniziale è disponibile anche nello [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct.
