---
title: Registratore DK di Azure
description: Informazioni su come registrare i flussi di dati da Sensor SDK a un file usando il registratore Kinect di Azure.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, record, riproduzione, lettore, Matroska, MKV, flussi, profondità, RGB, fotocamera, colore, IMU, audio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276706"
---
# <a name="azure-kinect-dk-recorder"></a>Registratore DK di Azure

Questo articolo illustra come usare l' `k4arecorder` utilità da riga di comando per registrare i flussi di dati da Sensor SDK a un file.

>[!NOTE]
>Registratore Kinect di Azure non registra l'audio.

## <a name="recorder-options"></a>Opzioni registratore

`k4arecorder`In sono disponibili vari argomenti della riga di comando per specificare il file di output e le modalità di registrazione.

Le registrazioni vengono archiviate nel [formato Matroska. MKV](record-file-format.md). La registrazione usa più tracce video per il colore e la profondità, oltre a informazioni aggiuntive quali la taratura della fotocamera e i metadati.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>File di record

Esempio 1. Modalità 640X576 (record Depth NFOV uncontenitoried), RGB 1080p a 30 fps con IMU.
Premere i tasti **CTRL + C** per arrestare la registrazione.

```
k4arecorder.exe output.mkv
```

Esempio 2. Record WFOV non suddiviso in contenitori (1MP), RGB 3072p a 15 fps senza IMU, per 10 secondi.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Esempio 3. Registrare WFOV 2x2 in contenitori a 30 fps per 5 secondi e salvare in output. MKV.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>È possibile usare il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) per configurare i controlli della fotocamera RGB prima della registrazione, ad esempio per impostare un bilanciamento del bianco manuale.

## <a name="verify-recording"></a>Verificare la registrazione

È possibile aprire il file di output con estensione MKV con il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md).

Per estrarre tracce o visualizzare informazioni sui file, `mkvinfo` sono disponibili strumenti come parte di [mkvtoolnix](https://mkvtoolnix.download/) Toolkit.

## <a name="next-steps"></a>Passaggi successivi

[Uso del registratore con unità sincronizzate esterne](record-external-synchronized-units.md)