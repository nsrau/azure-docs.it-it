---
title: Trasmettere contenuti in streaming con l'integrazione CDN
titleSuffix: Azure Media Services
description: Scopri di più sui contenuti in streaming con l'integrazione CDN, nonché sulla prelettura e sulla prelettura della rete CDN di Origin-Assist.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128059"
---
# <a name="stream-content-with-cdn-integration"></a>Trasmettere contenuti in streaming con l'integrazione CDN

La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo.  

La rete CDN memorizza nella cache il contenuto trasmesso in streaming da un Endpoint di streaming di Servizi multimediali [(origine)](streaming-endpoint-concept.md) per codec, per protocollo di streaming, per bitrate, per formato contenitore e per crittografia/DRM. Per ogni combinazione di crittografia di formato bitrate codec-streaming protocol-container, sarà disponibile una cache della rete CDN separata.

Il contenuto popolare verrà servito direttamente dalla cache della rete CDN finché il frammento video viene memorizzato nella cache. Il contenuto live viene in genere memorizzato nella cache perché molte persone guardano la stessa cosa. I contenuti on-demand possono essere un po 'più complicati perché potresti avere alcuni contenuti che sono popolari e altri no. Se hai milioni di risorse video in cui nessuna di esse è popolare (solo uno o due spettatori alla settimana), ma hai migliaia di persone che guardano tutti i video diversi, la CDN diventa molto meno efficace.

È anche necessario valutare il funzionamento del flusso adattivo. Ogni singolo frammento video viene memorizzato nella cache come propria entità. Ad esempio, immagina la prima volta che un determinato video viene guardato. Se lo spettatore salta la visione solo pochi secondi qua e là, solo i frammenti video associati a ciò che la persona ha guardato vengono memorizzati nella cache nella rete CDN. Con il flusso adattivo, si hanno in genere da 5 a 7 bitrate del video diversi. Se una persona sta guardando una bitrate e un'altra persona sta guardando una velocità di bit diversa, ogni persona viene memorizzata nella cache separatamente nella rete CDN. Anche se due persone stanno guardando lo stesso bitrate, potrebbero essere in streaming su protocolli diversi. Ogni protocollo (HLS, MPEG-DASH, Smooth Streaming) viene memorizzato nella cache separatamente. In conclusione, ogni bitrate e ogni protocollo vengono memorizzati nella cache separatamente; inoltre, vengono memorizzati nella cache solo i frammenti video che sono stati richiesti.

Quando si decide se abilitare o meno la rete CDN [nell'endpoint](streaming-endpoint-concept.md)di streaming di Servizi multimediali, considerare il numero di visualizzatori previsti. La rete CDN aiuta solo se ti aspetti molti spettatori per i tuoi contenuti. Se la concorrenza massima dei visualizzatori è inferiore a 500, è consigliabile disabilitare la rete CDN poiché la rete CDN è scalabile al meglio con la concorrenza.

In questo argomento viene illustrata l'abilitazione [dell'integrazione CDN.](#enable-azure-cdn-integration) Vengono inoltre illustrate le prelettura (caching attivo) e il concetto [di CDN-Prefetch Origin-Assist.](#origin-assist-cdn-prefetch)

## <a name="considerations"></a>Considerazioni

* [L'endpoint](streaming-endpoint-concept.md) `hostname` di streaming e l'URL di streaming rimangono invariati indipendentemente dal fatto che si abiliti o meno la rete CDN.
* Se è necessaria la possibilità di testare il contenuto con o senza rete CDN, creare un altro endpoint di streaming non abilitato per la rete CDN.

## <a name="enable-azure-cdn-integration"></a>Abilitare l'integrazione della rete CDN di Azure

> [!IMPORTANT]
> Non è possibile abilitare la rete CDN per gli account Azure di valutazione o per studenti.
>
> L'integrazione della rete CDN è abilitata in tutti i data center di Azure ad eccezione delle aree Federal Government e China.CDN integration is enabled in all the Azure data centers except Federal Government and China regions.

Dopo il provisioning di un endpoint di streaming con la rete CDN abilitata, è disponibile un tempo di attesa definito in Servizi multimediali prima che venga eseguito l'aggiornamento DNS per eseguire il mapping dell'endpoint di streaming all'endpoint della rete CDN.

Se in seguito si desidera disabilitare o abilitare la rete CDN, l'endpoint di streaming deve essere nello stato **interrotto**. L'abilitazione dell'integrazione della rete CDN di Azure e l'attivazione delle modifiche in tutti i POP della rete CDN potrebbero richiedere fino a due ore. Tuttavia, è possibile avviare l'endpoint di streaming e lo streaming senza interruzioni dall'endpoint di streaming. Una volta completata l'integrazione, il flusso viene recapitato dalla rete CDN. Durante il periodo di provisioning, l'endpoint di streaming sarà nello stato **iniziale** e si potrebbero osservare un peggioramento delle prestazioni.

Quando viene creato, l'endpoint di streaming Standard viene configurato per impostazione predefinita con Standard Verizon.When the Standard streaming endpoint is created, it's configured by default with Standard Verizon. È possibile configurare i provider Premium Verizon o Standard Akamai utilizzando le API REST.

L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon** per gli endpoint di streaming standard. Gli endpoint di streaming Premium possono essere configurati usando tutti **i provider e i livelli di prezzo della rete CDN di Azure**.

> [!NOTE]
> Per informazioni dettagliate sulla rete CDN di Azure, vedere Panoramica della [rete CDN.](../../cdn/cdn-overview.md)

## <a name="determine-if-a-dns-change-was-made"></a>Determinare se è stata apportata una modifica DNS

È possibile determinare se è stata apportata una modifica DNS in un <https://www.digwebinterface.com>endpoint di streaming (il traffico viene indirizzato alla rete CDN di Azure) utilizzando . Se nei risultati vengono visualizzati azureedge.net nomi di dominio, il traffico punta ora alla rete CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

La memorizzazione nella cache della rete CDN è un processo reattivo. Se la rete CDN è in grado di prevedere quale verrà richiesto l'oggetto successivo, la rete CDN può richiedere in modo proattivo e memorizzare nella cache l'oggetto successivo. Con questo processo, è possibile ottenere un riscontro nella cache per tutti (o la maggior parte) degli oggetti, che migliora le prestazioni.

Il concetto di prelettura si sforza di posizionare gli oggetti sul "bordo di Internet" in previsione che questi saranno richiesti dal giocatore imminentemente, riducendo così il tempo per consegnare quell'oggetto al giocatore.

Per raggiungere questo obiettivo, un endpoint di streaming (origine) e una rete CDN devono lavorare di pari passo in due modi:To achieve this goal, a streaming endpoint (origin) and CDN need to work in-hand in a couple ways:

- L'origine di Servizi multimediali deve disporre dell'"intelligenza" (Origin-Assist) per informare CDN dell'oggetto successivo da prelettura.
- La rete CDN esegue la prelettura e la memorizzazione nella cache (parte CDN-prefetch). CDN deve anche avere l'"intelligenza" per informare l'origine se si tratta di una prelettura o di un recupero regolare, gestire le risposte 404 e un modo per evitare un ciclo di prelettura senza fine.

### <a name="benefits"></a>Vantaggi

I vantaggi della funzionalità *CDN-Prefetch di Origin-Assist* includono:

- La prelettura migliora la qualità di riproduzione video preposizionando i segmenti video previsti sul bordo durante la riproduzione, riducendo la latenza per lo spettatore e migliorando i tempi di download dei segmenti video. Questo si traduce in tempi di avvio video più veloci e minori occorrenze di rebuffering.
- Questo concetto è applicabile allo scenario generale di origine CDN e non è limitato ai supporti.
- Akamai ha aggiunto questa funzionalità ad [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Questa funzionalità non è ancora applicabile alla rete CDN Akamai integrata con l'endpoint di streaming di Servizi multimediali. Tuttavia, è disponibile per i clienti di Servizi multimediali che hanno un contratto Akamai preesistente e richiedono un'integrazione personalizzata tra la rete CDN Akamai e l'origine di Servizi multimediali.

### <a name="how-it-works"></a>Funzionamento

Il supporto `Origin-Assist CDN-Prefetch` CDN per le intestazioni (sia per lo streaming live che per lo streaming su richiesta) è disponibile per i clienti che hanno un contratto diretto con Akamai CDN. La funzionalità prevede i seguenti scambi di intestazione HTTP tra la rete CDN Akamai e l'origine di Servizi multimediali:

|Intestazione HTTP|Valori|Mittente|Ricevitore|Scopo|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (predefinito) o 0 |RETE CDN|Origine|Per indicare che la rete CDN è abilitata per la prelettura.|
|`CDN-Origin-Assist-Prefetch-Path`| Esempio: <br/>Frammenti(video -1400000000,format-mpd-time-cmaf)|Origine|RETE CDN|Per fornire il percorso di prelettura alla rete CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (richiesta di prelettura) o 0 (richiesta regolare)|RETE CDN|Origine|Indicare che la richiesta dalla rete CDN è una prelettura.|

Per visualizzare parte dello scambio di intestazione in azione, è possibile provare i seguenti passaggi:

1. Utilizzare Postman o cURL per inviare una richiesta all'origine di Servizi multimediali per un segmento o un frammento audio o video. Assicurarsi di aggiungere `CDN-Origin-Assist-Prefetch-Enabled: 1` l'intestazione nella richiesta.
2. Nella risposta, l'intestazione `CDN-Origin-Assist-Prefetch-Path` con un percorso relativo dovrebbe essere visualizzato come valore.

### <a name="supported-streaming-protocols"></a>Protocolli di streaming supportati

La `Origin-Assist CDN-Prefetch` funzionalità supporta i seguenti protocolli di streaming per lo streaming live e su richiesta:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Smooth Streaming

### <a name="faqs"></a>Domande frequenti

* Cosa succede se l'URL di un percorso di prelettura non è valido in modo che la prelettura della rete CDN ottenga un 404?

    La rete CDN memorizza nella cache solo una risposta 404 per 10 secondi (o un altro valore configurato).

* Supponiamo di avere un video on-demand. Se CDN-prefetch è abilitato, questa funzionalità implica che una volta che un client richiede il primo segmento video, la prelettura avvierà un ciclo per eseguire il prelettura di tutti i segmenti video successivi allo stesso bitrate?

    No, la prelettura CDN viene eseguita solo dopo una richiesta/risposta avviata dal client. La prelettura CDN non viene mai attivata da una prelettura, per evitare un ciclo di prelettura.

* La funzionalità CDN-Prefetch di Origin-Assist è sempre accesa? Come può essere attivato/disattivato?

    Questa funzionalità è disattivata per impostazione predefinita. I clienti devono accenderlo tramite l'API Akamai.

* Per il live streaming, cosa accadrebbe a Origin-Assist se il segmento o il frammento successivo non è ancora disponibile?

    In questo caso, l'origine di `CDN-Origin-Assist-Prefetch-Path` Servizi multimediali non fornirà intestazione e CDN-prefetch non si verificherà.

* Come `Origin-Assist CDN-Prefetch` funzionano con i filtri di manifesto dinamici?

    Questa funzionalità funziona indipendentemente dal filtro del manifesto. Quando il frammento successivo è fuori da una finestra di filtro, il relativo URL sarà ancora individuato esaminando il manifesto client non elaborato e quindi restituito come intestazione di risposta di prelettura della rete CDN. Così CDN otterrà l'URL di un frammento che viene filtrato dal manifesto DASH/HLS/Smooth. Tuttavia, il giocatore non effettuerà mai una richiesta GET alla rete CDN per recuperare il frammento, perché quel frammento non è incluso nel manifesto DASH/HLS/Smooth detenuto dal giocatore (il giocatore non sa l'esistenza di quel frammento).

* Il manifesto DASH MPD/HLS playlist/Smooth può essere prefetched?

    No, DASH MPD, playlist master HLS, playlist di varianti HLS o URL del manifesto uniforme non vengono aggiunti all'intestazione di prelettura.

* Gli URL di prelettura sono relativi o assoluti?

    Mentre Akamai CDN consente entrambi, l'origine di Servizi multimediali fornisce solo URL relativi per il percorso di prelettura perché non c'è alcun vantaggio apparente nell'utilizzo di URL assoluti.

* Questa funzionalità funziona con contenuti protetti da DRM?

    Sì, poiché questa funzionalità funziona a livello HTTP, non decodifica né analizza alcun segmento/frammento. Non importa se il contenuto è crittografato o meno.

* Questa funzionalità funziona con SSAI (Server Side Ad Insertion)?
    
    Lo fa per i contenuti originali/principali (il contenuto video originale prima dell'inserimento dell'annuncio) funziona, poiché SSAI non modifica il timestamp del contenuto di origine dall'origine di Servizi multimediali. Il funzionamento di questa funzione con i contenuti degli annunci dipende dal fatto che l'origine dell'annuncio supporti Origin-Assist. Ad esempio, se il contenuto dell'annuncio è ospitato anche in Servizi multimediali di Azure (stessa origine o di origine separata), anche il contenuto dell'annuncio verrà sottoposto a prelettura.

* Questa funzione funziona con i contenuti UHD/HEVC?

    Sì.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* Assicurarsi di esaminare il documento [Streaming Endpoint (origine).](streaming-endpoint-concept.md)
* L'esempio [in questo repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) illustra come avviare l'endpoint di streaming predefinito con .NET.
