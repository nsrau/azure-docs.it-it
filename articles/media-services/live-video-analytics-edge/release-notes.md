---
title: Analisi di video in tempo reale su IoT Edge note sulla versione-Azure
description: Questo argomento fornisce le note sulla versione di analisi video in tempo reale su IoT Edge versioni, miglioramenti, correzioni di bug e problemi noti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260324"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Note sulla versione di analisi video in tempo reale su IoT Edge

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` nel lettore di feed RSS.

Questo articolo illustra quanto segue:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="june-1-2020"></a>1 giugno 2020

Questa è la prima versione di anteprima pubblica di analisi video in tempo reale su IoT Edge. Il tag di versione è

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funzionalità supportate
* Analizza i flussi video live usando i moduli di intelligenza artificiale scelti e, facoltativamente, registra i video sul dispositivo perimetrale o sul cloud
* Usare nei sistemi operativi Linux AMD64 [supportati](https://docs.microsoft.com/azure/iot-edge/support) da IOT Edge
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
