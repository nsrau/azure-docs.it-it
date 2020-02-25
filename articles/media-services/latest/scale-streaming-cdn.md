---
title: Ridimensionamento dei flussi con la rete CDN
titleSuffix: Azure Media Services
description: Informazioni su un servizio di streaming che fornisce contenuti direttamente a un'app lettore client o a una rete per la distribuzione di contenuti (CDN).
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
ms.openlocfilehash: 90fa3b06e2696e9b45c333c75c8a8e117d5c0c96
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563115"
---
# <a name="scaling-streaming-with-cdn"></a>Ridimensionamento dei flussi con la rete CDN

La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo.  

La rete CDN memorizza nella cache il contenuto trasmesso da un endpoint di streaming di servizi multimediali [(Origin)](streaming-endpoint-concept.md) per codec, per protocollo di streaming, per velocità in bit, per ogni formato di contenitore e per crittografia/DRM. Per ogni combinazione di codec-Streaming Protocol-Container Format-bitrate-Encryption, sarà presente una cache della rete CDN separata. 

Il contenuto popolare verrà servito direttamente dalla cache della rete CDN, purché il frammento video venga memorizzato nella cache. Il contenuto live viene in genere memorizzato nella cache perché molte persone guardano la stessa cosa. Il contenuto su richiesta può essere un po' più complicato perché potrebbe essere presente contenuto popolare e altri non. Se si hanno milioni di asset video in cui nessuno di essi è popolare (solo uno o due visualizzatori alla settimana) ma si hanno migliaia di persone che guardano tutti i video, la rete CDN diventa molto meno efficace. 

È anche necessario valutare il funzionamento del flusso adattivo. Ogni singolo frammento video viene memorizzato nella cache come entità in sé. Si supponga, ad esempio, la prima volta che un determinato video viene guardato. Se il Visualizzatore Ignora solo pochi secondi qui e qui, solo i frammenti video associati a ciò che l'utente ha guardato vengono memorizzati nella cache nella rete CDN. Con il flusso adattivo, si hanno in genere da 5 a 7 bitrate del video diversi. Se una persona sta osservando una velocità in bit e un altro utente sta osservando una velocità in bit diversa, ognuno di essi viene memorizzato nella cache separatamente nella rete CDN. Anche se due persone stanno osservando la stessa velocità in bit, potrebbero trasmettere flussi su protocolli diversi. Ogni protocollo (HLS, MPEG-DASH, Smooth Streaming) viene memorizzato nella cache separatamente. In conclusione, ogni bitrate e ogni protocollo vengono memorizzati nella cache separatamente; inoltre, vengono memorizzati nella cache solo i frammenti video che sono stati richiesti.

Per decidere se abilitare o meno la rete CDN nell'endpoint di [streaming](streaming-endpoint-concept.md)di servizi multimediali, prendere in considerazione il numero di visualizzatori previsti. La rete CDN consente solo di prevedere molti visualizzatori per il contenuto. Se la concorrenza massima del visualizzatore è inferiore a 500, è consigliabile disabilitare la rete CDN poiché la rete CDN si adatta meglio alla concorrenza. 

Questo argomento illustra l'abilitazione dell'integrazione della rete [CDN](#enable-azure-cdn-integration). Viene inoltre illustrata la prelettura (memorizzazione nella cache attiva) e il concetto di [prelettura della rete CDN Origin-Assist](#origin-assist-cdn-prefetch) .

## <a name="considerations"></a>Considerazioni

* L' [endpoint di streaming](streaming-endpoint-concept.md) `hostname` e l'URL di streaming rimangono invariati, indipendentemente dal fatto che la rete CDN sia abilitata.
* Se è necessario testare il contenuto con o senza la rete CDN, creare un altro endpoint di streaming non abilitato per la rete CDN.

## <a name="enable-azure-cdn-integration"></a>Abilitare l'integrazione della rete CDN di Azure

> [!IMPORTANT]
> Non è possibile abilitare la rete CDN per gli account Azure di valutazione o per studenti.
>
> L'integrazione della rete CDN è abilitata in tutti i Data Center di Azure, ad eccezione del governo federale e della Cina.

Quando viene eseguito il provisioning di un endpoint di streaming con la rete CDN abilitata, è previsto un tempo di attesa definito in servizi multimediali prima di eseguire l'aggiornamento DNS per eseguire il mapping dell'endpoint di streaming all'endpoint rete CDN.

Se in seguito si desidera disabilitare o abilitare la rete CDN, l'endpoint di streaming deve essere nello stato **interrotto**. L'abilitazione dell'integrazione della rete CDN di Azure e l'attivazione delle modifiche in tutti i POP della rete CDN potrebbero richiedere fino a due ore. Tuttavia, è possibile avviare l'endpoint di streaming e il flusso senza interruzioni dall'endpoint di streaming e, una volta completata l'integrazione, il flusso viene recapitato dalla rete CDN. Durante il periodo di provisioning, l'endpoint di streaming sarà nello stato **iniziale** ed è possibile che si verifichi un calo delle prestazioni.

Quando viene creato l'endpoint di streaming standard, questo viene configurato per impostazione predefinita con Verizon standard. È possibile configurare Premium Verizon o provider Akamai standard usando le API REST.

L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon** per gli endpoint di streaming standard. Gli endpoint di streaming Premium possono essere configurati usando tutti **i provider e i livelli di prezzo della rete CDN di Azure**. 

> [!NOTE]
> Per informazioni dettagliate sulla rete CDN di Azure, vedere la panoramica della rete [CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Determinare se è stata apportata una modifica DNS

È possibile determinare se è stata apportata una modifica DNS a un endpoint di streaming (il traffico viene indirizzato alla rete CDN di Azure) usando https://www.digwebinterface.com. Se nei risultati vengono visualizzati i nomi di dominio azureedge.net, il traffico viene ora puntato alla rete CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origine-supporto della rete CDN-prelettura

La memorizzazione nella cache della rete CDN è un processo reattivo. Se la rete CDN è in grado di stimare l'oggetto successivo che verrà richiesto, la rete CDN può attivamente richiedere e memorizzare nella cache l'oggetto successivo. Con questo processo, è possibile ottenere un riscontro nella cache per tutti (o la maggior parte) degli oggetti, migliorando quindi le prestazioni.

Il concetto di prelettura si impegna a posizionare gli oggetti in "Edge of the Internet" in previsione che questi verranno richiesti dal giocatore in modo imminente, riducendo così il tempo necessario per il recapito dell'oggetto al lettore.

Per raggiungere questo obiettivo, è necessario che un endpoint di streaming (Origin) e una rete CDN funzionino a mano: 

- L'origine di servizi multimediali deve avere l'"Intelligence" (Origin-Assist) per informare la rete CDN l'oggetto successivo per la prelettura e 
- La rete CDN esegue la prelettura e la memorizzazione nella cache (parte di prelettura della rete CDN). La rete CDN deve anche avere l'"Intelligence" per informare l'origine se si tratta di una prelettura o di una normale operazione di recupero, gestione delle risposte 404 e un modo per evitare un ciclo di prelettura infinito.

### <a name="benefits"></a>Vantaggi

I vantaggi della funzionalità di *prelettura della rete CDN Origin-Assist* includono:

- La prelettura migliora la qualità della riproduzione dei video: pre-posizionamento dei segmenti video previsti al bordo durante la riproduzione, riducendo la latenza al visualizzatore e migliorando i tempi di download dei segmenti video. In questo modo si ottiene un tempo di avvio video più rapido e si riducono le occorrenze del buffering.
- Questo concetto è applicabile a uno scenario di origine CDN generale, non limitato ai supporti.
- Akamai ha aggiunto questa funzionalità a [Akamai cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Questa funzionalità non è ancora applicabile alla rete CDN Akamai integrata con l'endpoint di streaming di servizi multimediali. Tuttavia, è disponibile per i clienti di servizi multimediali che hanno un contratto Akamai preesistente e richiedono l'integrazione personalizzata tra la rete CDN Akamai e l'origine di servizi multimediali.

### <a name="how-it-works"></a>Funzionamento

Supporto della rete CDN per la rete *CDN Origin-Assist: le intestazioni prefetch* (per lo streaming live e video su richiesta) sono disponibili per i clienti che hanno un contratto diretto con la rete CDN Akamai. La funzionalità prevede gli scambi di intestazioni HTTP seguenti tra la rete CDN Akamai e l'origine di servizi multimediali:

|Intestazione HTTP|Valori|Mittente|Ricevitore|Scopo|
| ---- | ---- | ---- | ---- | ----- |
|Rete CDN-origine-assistenza-prelettura-abilitata | 1 (valore predefinito) o 0 |RETE CDN|Origine|Per indicare che la rete CDN è abilitato per la prelettura|
|Rete CDN-origine-assistenza-prelettura-percorso| Esempio: <br/>Frammenti (video = 1400000000, Format = MPD-Time-CMAF)|Origine|RETE CDN|Per fornire il percorso di prelettura alla rete CDN|
|Rete CDN-origine-assistenza-prelettura-richiesta|1 (richiesta di prelettura) o 0 (richiesta normale)|RETE CDN|Origine|Per indicare che la richiesta dalla rete CDN è una prelettura|

Per visualizzare parte dello scambio di intestazioni in azione, è possibile provare a eseguire la procedura seguente:

1. Usare il post o il curl per inviare una richiesta all'origine di servizi multimediali per un segmento o un frammento audio o video. Assicurarsi di aggiungere l'intestazione CDN-Origin-Assist-prefetch-Enabled: 1 nella richiesta.
2. Nella risposta dovrebbe essere visualizzata l'intestazione CDN-Origin-Assist-prefetch-Path con un percorso relativo come valore.

### <a name="supported-streaming-protocols"></a>Protocolli di streaming supportati 

La funzionalità di *prelettura della rete CDN Origin-Assist* supporta i seguenti protocolli di streaming per lo streaming live e on demand:

* HLS V3
* HLS V4
* HLS CMAF
* TRATTINO (CSF)
* TRATTINO (CMAF)
* Smooth Streaming

### <a name="faqs"></a>Domande frequenti

* Cosa accade se un URL del percorso di prelettura non è valido in modo che la prelettura della rete CDN ottenga 404? 

    La rete CDN memorizza nella cache solo una risposta 404 per 10 secondi (o un altro valore configurato).
* Si supponga di avere un video su richiesta. Se la rete CDN-prelettura è abilitata, questa funzionalità implica che una volta che un client richiede il primo segmento video, la prelettura avvierà un ciclo per prerecuperare tutti i segmenti video successivi con la stessa velocità in bit? 

    No, la prelettura della rete CDN viene eseguita solo dopo una richiesta/risposta avviata dal client. Rete CDN: la prelettura non viene mai attivata da una prelettura, per evitare un ciclo di prelettura. 
* Origine-supporto della rete CDN-la funzionalità di prelettura è sempre attiva? Come è possibile attivare/disattivare? 

    questa funzionalità è disattivata per impostazione predefinita. I clienti devono attivarla tramite l'API Akamai.
* Per lo streaming live, cosa accadrebbe ad Origin-Assist se il segmento o il frammento successivo non è ancora disponibile? 

    In questo caso, l'origine di servizi multimediali non fornirà l'intestazione CDN-Origin-Assist-prefetch-Path e la rete CDN-prefetch non verrà eseguita.
* Come viene utilizzata la *prelettura* per la rete CDN con i filtri manifesto dinamici? 

    Questa funzionalità funziona indipendentemente dal filtro del manifesto. Quando il frammento successivo è esterno a una finestra di filtro, l'URL viene comunque individuato esaminando il manifesto client non elaborato e quindi restituito come intestazione della risposta di prelettura della rete CDN. Ciò significa che la rete CDN otterrà l'URL di un frammento che viene escluso dal manifesto DASH/HLS/Smooth. Tuttavia, il lettore non effettuerà mai una richiesta GET alla rete CDN per recuperare il frammento, perché tale frammento non è incluso in DASH/HLS/Smooth manifest utilizzato dal lettore (il lettore non conosce l'esistenza del frammento).
* È possibile precaricare la playlist MPD/HLS o il manifesto smussato?

    No, DASH MPD, playlist Master HLS, playlist della variante HLS o URL del manifesto smussato non è stato aggiunto all'intestazione di prelettura.
* Gli URL di prelettura sono relativi o assoluti? 

    Sebbene la rete CDN di Akamai consenta entrambe, l'origine di servizi multimediali fornisce solo URL relativi per il percorso di prelettura perché non esiste alcun vantaggio evidente nell'uso degli URL assoluti.
* Questa funzionalità funziona con contenuti protetti da DRM?

    Sì, poiché questa funzionalità funziona a livello HTTP, non decodifica né analizza alcun segmento o frammento. Non interessa se il contenuto è crittografato o meno.
* Questa funzionalità funziona con l'inserimento di annunci lato server (SSAI)?
    
    Per il contenuto originale/principale (il contenuto video originale prima dell'inserimento di annunci) funziona, poiché SSAI non modifica il timestamp del contenuto di origine dall'origine di servizi multimediali. Indipendentemente dal fatto che questa funzionalità funzioni con il contenuto di Active Directory, dipende dal fatto che l'origine ad supporti l'origine. Se, ad esempio, i contenuti di Active Directory sono ospitati anche in servizi multimediali di Azure (stessa o origine separata), anche il contenuto di Active Directory verrà precaricato.
* Questa funzionalità funziona con i contenuti UHD/HEVC?

    Sì.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

L'esempio [in questo repository](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) illustra come avviare l'endpoint di streaming predefinito con .NET.
