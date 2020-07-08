---
title: Usare il registratore Kinect di Azure con dispositivi sincronizzati esterni
description: Informazioni su come registrare i dati dai dispositivi configurati per la sincronizzazione esterna usando il registratore Kinect di Azure.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensore, visualizzatore, sincronizzazione esterna, ritardo fase, profondità, RGB, fotocamera, cavo audio, registratore
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276661"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Usare il registratore Kinect di Azure con dispositivi sincronizzati esterni

Questo articolo fornisce indicazioni su come il [registratore Kinect di Azure](azure-kinect-recorder.md) può registrare i dispositivi configurati per la sincronizzazione esterna dei dati.

## <a name="prerequisites"></a>Prerequisiti

- [Configurare più unità Kinect dk di Azure per la sincronizzazione esterna](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Vincoli di sincronizzazione esterni

- Il dispositivo master non può avere la sincronizzazione nel cavo connesso.
- Il dispositivo master deve trasmettere la fotocamera RGB per abilitare la sincronizzazione.
- Tutte le unità devono usare la stessa configurazione della fotocamera (framerate e risoluzione).
- Tutte le unità devono eseguire lo stesso firmware del dispositivo (aggiornare le istruzioni del[firmware](update-device-firmware.md) ).
- Tutti i dispositivi subordinati devono essere avviati prima del dispositivo master.
- Lo stesso valore di esposizione deve essere impostato su tutti i dispositivi.
- L'impostazione del *Master ritardato* di ogni subordinato è relativa al dispositivo master.

## <a name="record-when-each-unit-has-a-host-pc"></a>Registra quando ogni unità dispone di un PC host

Nell'esempio seguente ogni dispositivo dispone di un proprio PC host dedicato.
È consigliabile connettere i dispositivi a PC dedicati per evitare problemi con la larghezza di banda USB e l'utilizzo della CPU/GPU.

### <a name="subordinate-1"></a>Subordinato-1

1. Configurare il registratore per la prima unità

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Avvio del dispositivo in attesa

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Subordinato-2

1. Configurare il registratore per la seconda unità

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Avvio del dispositivo in attesa

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. Avvia registrazione nel database master

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Attendere il completamento della registrazione

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Registrazione in caso di più unità connesse a un singolo PC host

È possibile avere più DKs Kinect di Azure connessi a un singolo PC host. Tuttavia, può essere molto impegnativo per la larghezza di banda USB e il calcolo host. Per ridurre la richiesta:

- Connettere ogni dispositivo al proprio controller host USB.
- Hanno una GPU potente che può gestire il motore di profondità per ogni dispositivo.
- Registrare solo i sensori necessari e usare un framerate inferiore.

Avviare sempre prima i dispositivi subordinati e l'ultimo master.

## <a name="subordinate-1"></a>Subordinato-1

1. Avvia registratore su subordinato

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Il dispositivo passa in stato di attesa

## <a name="master"></a>Master

1. Avvia dispositivo master

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Attendere il completamento della registrazione

## <a name="playing-recording"></a>Riproduzione di una registrazione

È possibile usare il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) per riprodurre la registrazione.



## <a name="tips"></a>Suggerimenti

- Usare l'esposizione manuale per la registrazione di fotocamere sincronizzate. L'esposizione automatica della fotocamera RGB può influisca sulla sincronizzazione temporale.
- Il riavvio del dispositivo subordinato provocherà la perdita della sincronizzazione.
- Alcune [modalità della fotocamera](hardware-specification.md#depth-camera-supported-operating-modes) supportano massimo 15 fps. Si consiglia di non combinare le modalità/frequenza dei fotogrammi tra i dispositivi
- La connessione di più unità a un singolo PC può saturare facilmente la larghezza di banda USB, quindi è consigliabile usare un PC host separato per ogni dispositivo. Prestare attenzione anche al calcolo CPU/GPU.
- Disabilitare il microfono e IMU se non sono necessari per migliorare l'affidabilità.

Per eventuali problemi, vedere [risoluzione dei](troubleshooting.md) problemi

## <a name="see-also"></a>Vedere anche

- [Configurare la sincronizzazione esterna](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Registratore Kinect di Azure](azure-kinect-recorder.md) per le impostazioni del registratore e informazioni aggiuntive.
- [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) per la riproduzione di registrazioni o l'impostazione delle proprietà della fotocamera RGB non disponibili tramite il registratore.
- [Strumento firmware Kinect di Azure](azure-kinect-firmware-tool.md) per l'aggiornamento del firmware del dispositivo.
