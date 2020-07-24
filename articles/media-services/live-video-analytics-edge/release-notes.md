---
title: Analisi di video in tempo reale su IoT Edge note sulla versione-Azure
description: Questo argomento fornisce le note sulla versione di analisi video in tempo reale su IoT Edge versioni, miglioramenti, correzioni di bug e problemi noti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091780"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Note sulla versione di analisi video in tempo reale su IoT Edge

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` nel lettore di feed RSS.

Questo articolo illustra quanto segue:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="july-13-2020"></a>13 luglio 2020

Questo tag di versione per l'aggiornamento del 2020 luglio del modulo è:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nelle guide introduttive ed esercitazioni, i manifesti della distribuzione usano un tag 1 (Live-Video-Analytics: 1). Quindi, la semplice ridistribuzione di tali manifesti dovrebbe aggiornare il modulo sui dispositivi perimetrali >.

### <a name="new-features"></a>Nuove funzionalità
* È ora possibile creare topologie Graph con un nodo di sink di asset, oltre a un nodo di sink di file a valle di un nodo del processore del Gate del segnale. Per [un](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) esempio, vedere.

### <a name="bug-fixes"></a>Correzioni di bug
* Miglioramenti alla convalida delle proprietà desiderate

## <a name="june-1-2020"></a>1 giugno 2020

Questa è la prima versione di anteprima pubblica di analisi video in tempo reale su IoT Edge. Il tag di versione è

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funzionalità supportate
* Analizza i flussi video live usando i moduli di intelligenza artificiale scelti e, facoltativamente, registra i video sul dispositivo perimetrale o sul cloud
* Usare nei sistemi operativi Linux AMD64 [supportati](../../iot-edge/support.md) da IOT Edge
* Distribuire e configurare il modulo tramite l'hub Internet delle cose usando portale di Azure o Visual Studio Code
* Gestire le [topologie Graph e le istanze Graph](media-graph-concept.md#media-graph-topologies-and-instances) in modalità remota o locale tramite le seguenti chiamate al metodo diretto

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Passaggi successivi

[Panoramica](overview.md)
