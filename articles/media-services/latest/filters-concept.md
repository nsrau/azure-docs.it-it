---
title: Definizione dei filtri in Servizi multimediali di Azure
description: Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251466"
---
# <a name="filters"></a>Filtri

Quando distribuisci i tuoi contenuti ai clienti (eventi Live Streaming o Video su richiesta), il tuo cliente potrebbe aver bisogno di una flessibilità maggiore rispetto a quanto descritto nel file manifesto della risorsa predefinita. Servizi multimediali di Azure offre [manifesti dinamici basati](filters-dynamic-manifest-overview.md) su filtri predefiniti. 

I filtri sono regole lato server che consentono ai clienti di eseguire operazioni come: 

- Riprodurre una sola sezione di un video (anziché il video intero). Ad esempio:
  - Ridurre il manifesto in modo da mostrare solo una sottoclip di un evento live ("filtro di sottoclip") o
  - Tagliare l'inizio di un video ("trimming di un video").
- Distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").

Servizi multimediali consente di creare **filtri Account** e **Risorse** per i contenuti. Inoltre, è possibile associare i filtri creati in precedena a un **localizzatore**di flusso .

## <a name="defining-filters"></a>Definizione di filtri

Sono disponibili due tipi di filtri: 

* [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) (globali): possono essere applicati a qualsiasi asset nell’account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell’account.
* [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters) (locali): possono essere applicati solo a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell’asset. 

**I** tipi di filtri account e **di asset** non hanno esattamente le stesse proprietà per la definizione/descrizione del filtro. Tranne quando si crea il **filtro asset**, è necessario specificare il nome dell'asset a cui si desidera associare il filtro.

A seconda dello scenario, decidere quale tipo di filtro è più adatto (asset o account). I filtri asset sono più adatti per i profili di dispositivo (filtro di rendering), mentre i filtri asset possono essere usati per tagliare un asset specifico.

Utilizzare le seguenti proprietà per descrivere i filtri. 

|Nome|Descrizione|
|---|---|
|firstQuality|La prima velocità in bit di qualità del filtro.|
|presentationTimeRange|L’intervallo di tempo di presentazione. Questa proprietà viene utilizzata per filtrare i punti di inizio/fine del manifesto, la lunghezza della finestra di presentazione e la posizione iniziale live. <br/>Per altre informazioni, vedere [Intervallo di tempo di presentazione](#presentationtimerange).|
|brani|Le condizioni di selezione dei brani. Per ulteriori informazioni, vedere [brani](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Utilizzare questa proprietà con i **filtri asset**. Non è consigliabile impostare la proprietà per i **filtri account**.

|Nome|Descrizione|
|---|---|
|**endTimestamp**|È adatto per i video on demand (VoD).<br/>Per la presentazione Live Streaming, viene automaticamente ignorato e applicato quando la presentazione termina e lo streaming diventa VoD.<br/>Si tratta di un valore long che rappresenta un punto finale assoluto della presentazione, arrotondato all'inizio GOP successivo più vicino. L'unità è la scala cronologica, quindi un endTimestamp di 180000000 sarebbe per 3 minuti.<br/>Utilizzare startTimestamp e endTimestamp per tagliare i frammenti che saranno nella playlist (manifesto).<br/>Ad esempio, startTimestamp-40000000 e endTimestamp-100000000 utilizzando la scala cronologica predefinita genereranno una playlist che contiene frammenti compresi tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**forceEndTimestamp**|Si applica solo al Live Streaming.<br/>Indica se la proprietà endTimestamp deve essere presente. Se true, è necessario specificare endTimestamp o restituire un codice di richiesta non valido.<br/>Valori consentiti: false, true.|
|**liveBackoffDuration**|Si applica solo al Live Streaming.<br/> Questo valore definisce l'ultima posizione live che un client può cercare.<br/>Utilizzando questa proprietà, è possibile ritardare la posizione di riproduzione dal vivo e creare un buffer sul lato server per i lettori.<br/>L'unità per questa proprietà è la scala cronologica (vedi sotto).<br/>La durata massima di live off è 300 secondi (3000000000).<br/>Ad esempio, un valore pari a 2000000000 indica che l'ultimo contenuto disponibile è di 20 secondi in ritardo rispetto al live edge reale.|
|**presentationWindowDuration**|Si applica solo al Live Streaming.<br/>Utilizzare presentationWindowDuration per applicare una finestra scorrevole di frammenti da includere in una playlist.<br/>L'unità per questa proprietà è la scala cronologica (vedi sotto).<br/>Ad esempio, impostare presentationWindowDuration = 1200000000 per applicare una finestra temporale scorrevole di due minuti. Nella playlist verranno inclusi i contenuti multimediali che rientrano nei 2 minuti dell’arco live. Se un frammento attraversa il limite, l'intero frammento verrà incluso nella playlist. La durata minima della finestra di presentazione è di 60 secondi.|
|**startTimestamp**|Si applica a Video on Demand (VoD) o Live Streaming.<br/>Si tratta di un valore long che rappresenta un punto iniziale assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino. L'unità è la scala cronologica, quindi un startTimestamp di 15000000sarebbe per 15 secondi.<br/>Utilizzare startTimestamp e endTimestampp per tagliare i frammenti che saranno nella playlist (manifesto).<br/>Ad esempio, startTimestamp-40000000 e endTimestamp-100000000 utilizzando la scala cronologica predefinita genereranno una playlist che contiene frammenti compresi tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**timescale**|Si applica a tutti i timestamp e durate in un intervallo di tempo di presentazione, specificato come numero di incrementi in un secondo.<br/>Il valore predefinito è 10000000 - dieci milioni di incrementi in un secondo, dove ogni incremento sarebbe lungo 100 nanosecondi.<br/>Ad esempio, se si desidera impostare un startTimestamp a 30 secondi, è necessario utilizzare un valore di 300000000 quando si utilizza la scala cronologica predefinita.|

### <a name="tracks"></a>Brani

Specificate un elenco di condizioni delle proprietà della traccia di filtro (FilterTrackPropertyConditions) in base alle quali le tracce del flusso (Live Streaming o Video su richiesta) devono essere incluse nel manifesto creato dinamicamente. I filtri vengono combinati usando gli operatori logici **AND** e **O**.

Le condizioni delle proprietà di filtro indicano i tipi di brano, i valori (descritti nella tabella seguente) e le operazioni (Uguale, DiversoDa). 

|Nome|Descrizione|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per i brani in un asset, ottenere ed esaminare il file manifesto.|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

### <a name="example"></a>Esempio

L'esempio seguente definisce un filtro Live Streaming:The following example defines a Live Streaming filter: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Associazione di filtri a Streaming Locator

È possibile specificare un elenco di [filtri di asset o account](filters-concept.md) nel [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). [Il pacchetto dinamico](dynamic-packaging-overview.md) applica questo elenco di filtri insieme a quelli specificati dal client nell'URL. Questa combinazione genera un [manifesto dinamico](filters-dynamic-manifest-overview.md), che si basa sui filtri negli URL e i filtri specificati nel localizzatore di flusso. 

Vedere gli esempi seguenti:

* [Associare filtri a Streaming Locator - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associare filtri a Streaming Locator - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aggiornamento dei filtri
 
**I localizzatori** di streaming non sono aggiornabili mentre i filtri possono essere aggiornati. 

Non è consigliabile aggiornare la definizione dei filtri associati a un **localizzatore**di streaming pubblicato attivamente, soprattutto quando la rete CDN è abilitata. I server di streaming e le reti CDN possono avere cache interne che possono causare la restituzione di dati memorizzati nella cache non aggiornati. 

Se è necessario modificare la definizione del filtro, è consigliabile creare un nuovo filtro e aggiungerlo all'URL **di Streaming Locator** o pubblicare un nuovo **localizzatore** di streaming che faccia riferimento direttamente al filtro.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti spiegano come creare filtri in modo programmatico.  

- [Creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Creare filtri con l’interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

