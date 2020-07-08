---
title: Registrazione video continua-Azure
description: La registrazione video continua (CVR) si riferisce al processo di registrazione continua del video da un'origine video. In questo argomento viene illustrata la CVR.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9a785125d4cfb2324224f4676e1d429342ec325c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260624"
---
# <a name="continuous-video-recording"></a>Registrazione continua di video  

## <a name="suggested-pre-reading"></a>Letture consigliate  

* [Concetti relativi al grafo multimediale](media-graph-concept.md)
* [Concetto di registrazione video](video-recording-concept.md)

## <a name="overview"></a>Panoramica

La registrazione video continua (CVR) si riferisce al processo di registrazione continua del video da un'origine video. Analisi video in tempo reale su IoT Edge supporta la registrazione continua dei video, su base 24x7, da una fotocamera CCTV tramite un [grafico multimediale](media-graph-concept.md) costituito da un nodo di origine RTSP e da un nodo di sink di asset. Il diagramma seguente illustra una rappresentazione grafica di un grafico multimediale di questo tipo. La rappresentazione JSON della [topologia Graph](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances) di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

![Registrazione continua di video](./media/continuous-video-recording/continuous-video-recording-overview.png)

Il grafico multimediale illustrato in precedenza può essere eseguito in un dispositivo perimetrale, con il video di registrazione del sink di asset in un [Asset](terminology.md#asset)di servizi multimediali di Azure. Il video verrà registrato fino a quando il grafico multimediale rimane nello stato attivato. Poiché il video viene registrato come asset, è possibile riprodurlo usando le funzionalità di streaming esistenti di servizi multimediali. Per altri dettagli, vedere [riproduzione del contenuto registrato](video-playback-concept.md) .

## <a name="resilient-recording"></a>Registrazione resiliente

L'analisi di video in tempo reale su IoT Edge supporta il funzionamento in condizioni di rete meno-perfette, in cui il dispositivo perimetrale può occasionalmente perdere la connettività con il cloud o subire un calo della larghezza di banda disponibile. Per tenere conto di questo problema, il video dell'origine viene registrato localmente in una cache e viene sincronizzato automaticamente con l'asset con cadenza periodica. Se si esamina la [topologia Graph JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json), si noterà che sono definite le proprietà seguenti:

```
    "segmentLength": "PT30S",
    "localMediaCacheMaximumSizeMiB": "2048",
    "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```
Le ultime due proprietà sono rilevanti per la registrazione resiliente (entrambe sono anche proprietà obbligatorie per un nodo sink di asset). La proprietà localMediaCachePath indica al sink di asset di usare il percorso della cartella per memorizzare i dati multimediali prima del caricamento nell'asset. [Questo](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module) articolo illustra come il modulo Edge può usare la risorsa di archiviazione locale del dispositivo. La proprietà localMediaCacheMaximumSizeMiB definisce la quantità di spazio su disco che il sink di asset può usare come cache (1 MiB = 1024 * 1024 byte). 

Se il modulo perimetrale perde la connettività per un periodo di tempo molto lungo e il contenuto archiviato nella cartella della cache raggiunge il valore localMediaCacheMaximumSizeMiB, il sink di asset avvierà l'eliminazione dei dati dalla cache, a partire dai dati meno recenti. Ad esempio, se il dispositivo ha perso la connettività alle ore 10.00 e la cache raggiunge il limite massimo alle 18.00, il sink di asset inizia a eliminare i dati registrati alle 10.00. 

Quando viene ripristinata la connettività di rete, il sink di asset inizierà il caricamento dalla cache, iniziando di nuovo dai dati meno recenti. Nell'esempio precedente, si supponga che il video sia stato rimosso dalla cache dal momento in cui è stata ripristinata la connettività (ad esempio, 6:13:02), il sink di asset inizierà il caricamento dal contrassegno 10:05AM.

Se in seguito si esamina l'asset usando [queste](playback-recordings-how-to.md) API, si noterà che si verifica un gap nell'asset da circa 10.00 a 10:05AM.

## <a name="segmented-recording"></a>Registrazione segmentata  

Come illustrato in precedenza, il nodo sink di asset registrerà il video in una cache locale e caricherà periodicamente il video nel cloud. La proprietà segmentLength (illustrata nella sezione precedente) consentirà di controllare il costo delle transazioni di scrittura associato alla scrittura dei dati nell'account di archiviazione in cui viene registrata l'asset. Se, ad esempio, si aumenta il periodo di caricamento da 30 secondi a 5 minuti, il numero di transazioni di archiviazione diminuirà di un fattore di 10 (5 * 60/30).

La proprietà segmentLength garantisce che il modulo Edge caricherà il video al massimo una volta per ogni segmentLength secondi. Questa proprietà ha un valore minimo di 30 secondi (impostazione predefinita) e può essere aumentata di 30 secondi incrementi fino a un massimo di 5 minuti.

>[!NOTE]
>Vedere [questo](playback-recordings-how-to.md) articolo per l'effetto di segmentLength sulla riproduzione.


## <a name="see-also"></a>Vedere anche

* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)
* [Riproduzione del contenuto registrato](video-playback-concept.md)


## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: registrazione video continua](continuous-video-recording-tutorial.md)
