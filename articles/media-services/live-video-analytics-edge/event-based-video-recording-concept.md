---
title: Registrazione video basata su eventi-Azure
description: Per registrazione video basata su eventi (EVR) si intende il processo di registrazione video attivato da un evento. L'evento in questione potrebbe avere origine a causa dell'elaborazione del segnale video stesso (ad esempio, il rilevamento sul movimento) o da un'origine indipendente (ad esempio, l'apertura di una porta).  In questo articolo sono descritti alcuni casi d'uso correlati alla registrazione video basata su eventi.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f3efd2b9be41928ab4721d6db4aa84c0f1f57e2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568498"
---
# <a name="event-based-video-recording"></a>Registrazione di video basata su eventi  
 
## <a name="suggested-pre-reading"></a>Letture consigliate  

* [Registrazione continua di video](continuous-video-recording-concept.md)
* [Riproduzione del contenuto registrato](video-playback-concept.md)
* [Concetti relativi al grafo multimediale](media-graph-concept.md)

## <a name="overview"></a>Panoramica 

Per registrazione video basata su eventi (EVR) si intende il processo di registrazione video attivato da un evento. L'evento in questione potrebbe avere origine a causa dell'elaborazione del segnale video, ad esempio il rilevamento sul movimento, oppure da un'origine indipendente, ad esempio l'apertura di una porta. 

È possibile registrare video (attivato da un evento) da una fotocamera CCTV a un asset di servizi multimediali usando un grafico multimediale costituito da un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) , da un nodo di [sink di asset](media-graph-concept.md#asset-sink) e da altri nodi, come descritto nei casi d'uso seguenti. È possibile configurare il nodo [sink di asset](media-graph-concept.md#asset-sink) per generare nuove risorse ogni volta che si verifica un evento, in modo che il video che corrisponde a ogni evento si trovi nella propria risorsa. È anche possibile scegliere di usare un asset per conservare il video per tutti gli eventi. 

In alternativa al nodo sink di asset, è possibile usare un nodo [sink di file](media-graph-concept.md#file-sink) per acquisire brevi frammenti di video (correlati a un evento di interesse) a una posizione specificata nella file system locale sul dispositivo perimetrale. 

In questo articolo sono descritti alcuni casi d'uso correlati alla registrazione video basata su eventi.

### <a name="video-recording-based-on-motion-detection"></a>Registrazione video basata sul rilevamento del movimento  

In questo caso di utilizzo, è possibile registrare clip video solo quando viene rilevato movimento nel video e ricevere un avviso quando viene generato un clip video di questo tipo. Questo potrebbe essere rilevante negli scenari di sicurezza commerciale che coinvolgono la protezione dell'infrastruttura critica. Generando avvisi e registrando un video quando viene rilevato un movimento imprevisto, è possibile migliorare l'efficienza dell'operatore umano, perché l'azione è necessaria solo quando viene generato l'avviso.

Il diagramma seguente illustra una rappresentazione grafica di un grafico multimediale che risolve questo caso d'uso. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Registrazione video basata sul rilevamento del movimento":::

Nel diagramma il nodo di origine RTSP acquisisce il feed video live dalla fotocamera e lo recapita a un nodo del [processore di rilevamento del movimento](media-graph-concept.md#motion-detection-processor) . Al momento del rilevamento del movimento nel video in tempo reale, il nodo del processore di rilevamento del movimento genera un evento che va al nodo del [processore del Gate del segnale](media-graph-concept.md#signal-gate-processor) , oltre che al nodo del sink di messaggi dell'hub. Il secondo nodo invia gli eventi all'hub IoT Edge, da dove possono essere indirizzati ad altre destinazioni per attivare gli avvisi. 

Un evento del nodo del rilevamento del movimento attiverà il nodo del processore del controllo del segnale e configurerà il feed video live dal nodo di origine RTSP al nodo sink di asset. In assenza di eventi successivi di rilevamento del movimento, il controllo si chiuderà dopo un periodo di tempo configurato. Questa operazione determina la durata del video registrato.

### <a name="video-recording-based-on-events-from-other-sources"></a>Registrazione video basata sugli eventi di altre origini  

In questo caso di utilizzo, è possibile usare i segnali di un altro sensore Internet per attivare la registrazione del video. Il diagramma seguente illustra una rappresentazione grafica di un grafico multimediale che risolve questo caso d'uso. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Registrazione video basata sul rilevamento del movimento":::

Nel diagramma il sensore esterno invia gli eventi all'hub IoT Edge. Gli eventi vengono quindi indirizzati al nodo del processore del Gate del segnale tramite il nodo di [origine del messaggio dell'hub](media-graph-concept.md#iot-hub-message-source) Internet. Il comportamento del nodo del processore del controllo del segnale è identico a quello del caso di utilizzo precedente. verrà aperto e il flusso del feed video in tempo reale passerà dal nodo di origine RTSP al nodo sink di file (o al nodo sink di asset) quando viene attivato dall'evento esterno. 

Se si usa un nodo di sink di file, il video verrà registrato nella file system locale sul dispositivo perimetrale. Altrimenti, se si usa un nodo sink di asset, il video verrà registrato in un asset.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Registrazione video basata su un modulo esterno di inferenza 

In questo caso di utilizzo è possibile registrare clip video in base a un segnale da un sistema di logica esterno. Un esempio di questo caso d'uso potrebbe essere la registrazione di un clip video solo quando viene rilevato un camion nel feed video di traffico su un'autostrada. Il diagramma seguente illustra una rappresentazione grafica di un grafico multimediale che risolve questo caso d'uso. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Registrazione video basata sul rilevamento del movimento":::

Nel diagramma il nodo di origine RTSP acquisisce il feed video live dalla fotocamera e lo recapita a due rami: uno ha un nodo del [processore](media-graph-concept.md#signal-gate-processor) di controllo del segnale e l'altro usa un nodo di [estensione http](media-graph-concept.md) per inviare dati a un modulo logico esterno. Il nodo estensione HTTP consente al grafico multimediale di inviare fotogrammi immagine (in formati JPEG, BMP o PNG) a un servizio di inferenza esterno su REST. Questo percorso del segnale può in genere supportare solo frequenze di fotogrammi insufficienti (<5fps). È possibile usare il nodo [processore filtro frequenza frame](media-graph-concept.md#frame-rate-filter-processor) per abbassare la frequenza dei fotogrammi del video che passa al nodo estensione http.

I risultati del servizio di inferenza esterno vengono recuperati dal nodo di estensione HTTP e inoltrati all'hub IoT Edge tramite il nodo del sink di messaggi dell'hub Internet, dove possono essere ulteriormente elaborati dal modulo di logica esterno. Se il servizio di inferenza è in grado di rilevare i veicoli, ad esempio, il modulo per la logica potrebbe cercare un veicolo specifico, ad esempio un bus o un camion. Quando il modulo della logica rileva l'oggetto di interesse, può attivare il nodo del processore del Gate del segnale inviando un evento tramite l'hub IoT Edge al nodo di origine del messaggio dell'hub Internet nel grafico. L'output del Gate del segnale viene visualizzato in un nodo sink di file o in un nodo di sink di asset. Nel primo caso, il video verrà registrato nella file system locale sul dispositivo perimetrale. Nel secondo caso, il video verrà registrato in un asset.

Un miglioramento di questo esempio prevede l'uso di un processore del rilevamento di movimento in avanti rispetto al nodo del processore del filtro della frequenza dei fotogrammi. In questo modo si riduce il carico sul servizio di inferenza, ad esempio la notte, quando potrebbe verificarsi un lungo periodo di tempo quando non ci sono veicoli sull'autostrada. 

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: registrazione video basata su eventi](event-based-video-recording-tutorial.md)
