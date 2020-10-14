---
title: Analisi di video in tempo reale su IoT Edge note sulla versione-Azure
description: Questo argomento fornisce le note sulla versione di analisi video in tempo reale su IoT Edge versioni, miglioramenti, correzioni di bug e problemi noti.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 18da50fe763ef7c5fc3c06ac00052679c488cae7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015635"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Note sulla versione di analisi video in tempo reale su IoT Edge

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` nel lettore di feed RSS.

Questo articolo illustra quanto segue:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

<hr width=100%>

## <a name="september-22-2020"></a>22 settembre 2020

Questo tag di versione per l'aggiornamento di settembre 2020 del modulo è:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> Nelle guide introduttive ed esercitazioni, i manifesti della distribuzione usano un tag 1 (Live-Video-Analytics: 1). Quindi, la semplice ridistribuzione di tali manifesti dovrebbe aggiornare il modulo sui dispositivi perimetrali >.

### <a name="module-updates"></a>Aggiornamenti del modulo

* Un nuovo nodo di estensione del grafico, [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) è disponibile per l'integrazione con il modulo di [analisi spaziale](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(anteprima) di servizi cognitivi.
* Aggiunta del supporto per i dispositivi Linux ARM64: usare i [passaggi manuali](deploy-iot-edge-device.md) per la distribuzione in tali dispositivi.

### <a name="documentation-updates"></a>Aggiornamenti della documentazione

* Sono disponibili [istruzioni](deploy-azure-stack-edge-how-to.md) per l'uso di analisi video in tempo reale su IOT Edge nei dispositivi Azure stack Edge.
* Nuova esercitazione sullo sviluppo di modelli di visione artificiale specifici dello scenario con il [servizio visione personalizzata](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) e l'uso di questo servizio per [analizzare i video live](custom-vision-tutorial.md) in tempo reale.

<hr width=100%>

## <a name="august-19-2020"></a>19 agosto 2020

Questo tag di versione per l'aggiornamento del 2020 agosto del modulo è:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Nelle guide introduttive ed esercitazioni, i manifesti della distribuzione usano un tag 1 (Live-Video-Analytics: 1). Quindi, la semplice ridistribuzione di tali manifesti dovrebbe aggiornare il modulo sui dispositivi perimetrali >.

### <a name="module-updates"></a>Aggiornamenti del modulo

* È ora possibile ottenere prestazioni elevate per il trasferimento di contenuti dati tra analisi video in tempo reale in IoT Edge e l'estensione personalizzata usando gRPC Framework. Per [iniziare](analyze-live-video-use-your-grpc-model-quickstart.md) , vedere.
* Distribuzione a livello di area più ampia di analisi video in tempo reale e solo il servizio cloud è stato aggiornato.  
* Il video live Analytics è ora disponibile in 25 aree aggiuntive in tutto il mondo. Di seguito è riportato l' [elenco](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) di tutte le aree disponibili.  
* La [configurazione](https://aka.ms/lva-edge/setup-resources-for-samples) per avvio rapido è stata aggiornata anche con il supporto di nuove aree.
    * Nessuna chiamata all'azione per chiunque abbia già configurato le risorse

### <a name="bug-fixes"></a>Correzioni di bug 

* Rimuovere l'uso di un'estensione di Azure deprecata nello script di configurazione

<hr width=100%>

## <a name="july-13-2020"></a>13 luglio 2020

Questo tag di versione per l'aggiornamento del 2020 luglio del modulo è:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nelle guide introduttive ed esercitazioni, i manifesti della distribuzione usano un tag 1 (Live-Video-Analytics: 1). Quindi, la semplice ridistribuzione di tali manifesti dovrebbe aggiornare il modulo sui dispositivi perimetrali >.

### <a name="module-updates"></a>Aggiornamenti del modulo

* È ora possibile creare topologie Graph con un nodo di sink di asset, oltre a un nodo di sink di file a valle di un nodo del processore del Gate del segnale. Per [un](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) esempio, vedere.

### <a name="bug-fixes"></a>Correzioni di bug

* Miglioramenti alla convalida delle proprietà desiderate

<hr width=100%>

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

[Overview](overview.md)