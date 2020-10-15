---
title: Usare Azure Kinect Sensor SDK per registrare il formato di file
description: Informazioni su come usare il formato di file registrato di Azure Kinect Sensor SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, Sensor, SDK, profondità, RGB, record, riproduzione, Matroska, MKV
ms.openlocfilehash: 3a78ed158c78df1c9a6eadcc0bfb2e40b22750bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501781"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Usare Azure Kinect Sensor SDK per registrare il formato di file

Per registrare i dati dei sensori, viene usato il formato del contenitore Matroska (. MKV), che consente di archiviare più tracce usando un'ampia gamma di codec. Il file di registrazione contiene tracce per l'archiviazione di colore, profondità, immagini IR e IMU.

I dettagli di basso livello del formato del contenitore. MKV sono disponibili nel [sito Web di Matroska](https://www.matroska.org/index.html).

| Nome traccia | Formato codec                          |
|------------|---------------------------------------|
| Colore      | Mode-Dependent (MJPEG, NV12 o YUY2) |
| DEPTH      | b16g (scala di grigi a 16 bit, big endian)   |
| IR         | b16g (scala di grigi a 16 bit, big endian)   |
| IMU        | Struttura personalizzata, vedere la [struttura di esempio IMU](record-file-format.md#imu-sample-structure) di seguito. |

## <a name="using-third-party-tools"></a>Uso di strumenti di terze parti

`ffmpeg`È possibile usare strumenti come o il `mkvinfo` comando di [mkvtoolnix](https://mkvtoolnix.download/) Toolkit per visualizzare ed estrarre informazioni dai file di registrazione.

Ad esempio, il comando seguente estrae la traccia Depth come sequenza di PNG a 16 bit nella stessa cartella:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

Il `-map 0:1` parametro estrae Track index 1, che per la maggior parte delle registrazioni sarà di profondità. Se la registrazione non contiene una traccia dei colori, `-map 0:0` verrebbe usata.

Il `-vsync 0` parametro impone a ffmpeg di estrarre i frame così come sono anziché tentare di trovare una corrispondenza con un framerate di 30 fps, 15 fps o 5 fps.

## <a name="imu-sample-structure"></a>Struttura di esempio IMU

Se i dati IMU vengono estratti dal file senza usare l'API di riproduzione, i dati saranno in formato binario.
Di seguito è riportata la struttura dei dati IMU. Tutti i campi sono little-endian.

| Campo                        | Type     |
|------------------------------|----------|
| Timestamp accelerometro (μs) | uint64   |
| Dati accelerometro (x, y, z) | float [3] |
| Timestamp giroscopio (μs)     | uint64   |
| Dati giroscopio (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>Identificazione delle tracce

Potrebbe essere necessario identificare la traccia contenente colore, profondità, IR e così via. L'identificazione delle tracce è necessaria quando si utilizzano strumenti di terze parti per leggere un file Matroska.
I numeri di traccia variano in base alla modalità della fotocamera e al set di tracce abilitate. I tag vengono usati per identificare il significato di ogni traccia.

L'elenco dei tag seguenti è associato a un elemento Matroska specifico e può essere usato per cercare la traccia o l'allegato corrispondente.

Questi tag sono visualizzabili con strumenti quali `ffmpeg` e `mkvinfo` .
L'elenco completo dei tag è elencato nella pagina [registrazione e riproduzione](record-playback-api.md) .

| Nome del tag             | Destinazione Tag             | Valore del tag             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Traccia colori            | UID traccia Matroska    |
| K4A_DEPTH_TRACK      | Traccia profondità            | UID traccia Matroska    |
| K4A_IR_TRACK         | Traccia IR               | UID traccia Matroska    |
| K4A_IMU_TRACK        | IMU Track              | UID traccia Matroska    |
| K4A_CALIBRATION_FILE | Allegato calibrazione | Nome file allegato   |

## <a name="next-steps"></a>Passaggi successivi

[Registrare e riprodurre](record-playback-api.md)
