---
title: Acquisire la sincronizzazione dei dispositivi Kinect di Azure
description: Informazioni su come sincronizzare i dispositivi di acquisizione Kinect di Azure con Azure Kinect Sensor SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, depth, RGB, Internal, External, Synchronization, Daisy Chain, offset fase
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276703"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Acquisire la sincronizzazione dei dispositivi Kinect di Azure

L'hardware Kinect di Azure può allineare il tempo di acquisizione delle immagini di colore e profondità. L'allineamento tra le fotocamere nello stesso dispositivo è la **sincronizzazione interna**. L'allineamento del tempo di acquisizione tra più dispositivi connessi è la **sincronizzazione esterna**.

## <a name="device-internal-synchronization"></a>Sincronizzazione interna del dispositivo

L'acquisizione di immagini tra le singole fotocamere viene sincronizzata nell'hardware. In ogni [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) che contiene immagini del sensore di colore e profondità, i timestamp delle immagini sono allineati in base alla modalità operativa dell'hardware. Per impostazione predefinita, le immagini di un'acquisizione sono al centro dell'esposizione allineata. I tempi relativi delle acquisizioni di profondità e colori possono essere modificati usando il `depth_delay_off_color_usec` campo di [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Sincronizzazione esterna del dispositivo

Vedere [Setup External Synchronization](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) for Hardware Setup.

Il software per ogni dispositivo connesso deve essere configurato per funzionare in modalità **Master** o **subordinato** . Questa impostazione è configurata nel [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

Quando si usa la sincronizzazione esterna, è necessario che le fotocamere subordinate vengano sempre avviate prima che i timestamp vengano allineati correttamente.

### <a name="subordinate-mode"></a>Modalità subordinata

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Modalità Master

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Recupero dello stato di Jack di sincronizzazione

Per recuperare a livello di codice lo stato corrente dei jack di input e di output della sincronizzazione, usare la funzione [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) .

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile abilitare e acquisire la sincronizzazione dei dispositivi. È anche possibile esaminare come usare 

>[!div class="nextstepaction"]
>[API di registrazione e riproduzione dell'SDK del sensore Kinect di Azure](record-playback-api.md)
