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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225423"
---
# <a name="filters"></a>Filtri

Quando si distribuiscono contenuti ai clienti (eventi di Streaming Live o Video on Demand) il client potrebbe essere necessario una maggiore flessibilità rispetto a quanto descritto nel file manifesto predefinito dell'asset. Servizi multimediali di Azure offre [manifesti dinamici](filters-dynamic-manifest-overview.md) basata su filtri predefiniti. 

I filtri sono regole lato server che consentono ai clienti di eseguire operazioni come: 

- Riprodurre una sola sezione di un video (anziché il video intero). Ad esempio:
  - Ridurre il manifesto in modo da mostrare solo una sottoclip di un evento live ("filtro di sottoclip") o
  - Tagliare l'inizio di un video ("trimming di un video").
- Distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").

Servizi multimediali consente di creare **filtri dell'Account** e **filtri di Asset** per il contenuto. Inoltre, è possibile associare i filtri creati in precedenza con un **localizzatore di Streaming**.

## <a name="defining-filters"></a>Definizione di filtri

Esistono due tipi di filtri: 

* [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) (globali): possono essere applicati a qualsiasi asset nell’account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell’account.
* [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters) (locali): possono essere applicati solo a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell’asset. 

**I filtri dell'account** e **filtri di Asset** tipi hanno esattamente le stesse proprietà per la definizione/descrizione del filtro. Tranne quando si crea il **filtro asset**, è necessario specificare il nome dell'asset a cui si desidera associare il filtro.

A seconda dello scenario, decidere quale tipo di filtro è più adatto (asset o account). I filtri asset sono più adatti per i profili di dispositivo (filtro di rendering), mentre i filtri asset possono essere usati per tagliare un asset specifico.

Utilizzare le seguenti proprietà per descrivere i filtri. 

|NOME|Descrizione|
|---|---|
|firstQuality|La prima velocità in bit di qualità del filtro.|
|presentationTimeRange|L’intervallo di tempo di presentazione. Questa proprietà viene utilizzata per filtrare i punti di inizio/fine del manifesto, la lunghezza della finestra di presentazione e la posizione iniziale live. <br/>Per altre informazioni, vedere [Intervallo di tempo di presentazione](#presentationtimerange).|
|brani|Le condizioni di selezione dei brani. Per ulteriori informazioni, vedere [brani](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Utilizzare questa proprietà con i **filtri asset**. Non è consigliabile impostare la proprietà per i **filtri account**.

|NOME|Descrizione|
|---|---|
|**endTimestamp**|È adatto per i video on demand (VoD).<br/>Per la presentazione Live Streaming, vengono automaticamente ignorato e applicato quando le entità finali di presentazione e il flusso diventa VoD.<br/>Si tratta di un valore long che rappresenta un punto finale assoluto della presentazione, arrotondato all'inizio GOP successivo più vicino. L'unità è il valore di scala cronologica, pertanto un endTimestamp di 1800000000 sarebbe per 3 minuti.<br/>Usare startTimestamp ed endTimestamp per tagliare i frammenti che saranno presente nella playlist del (manifesto).<br/>Ad esempio, startTimestamp = 40000000 ed endTimestamp = 100000000 utilizzando la scala cronologica predefinito genera una playlist contenente frammenti compresa tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**forceEndTimestamp**|Si applica allo Streaming Live.<br/>Indica se la proprietà endTimestamp deve essere presente. Se true, è necessario specificare endTimestamp o viene restituito un codice di richiesta non valida.<br/>Valori consentiti: false, true.|
|**liveBackoffDuration**|Si applica allo Streaming Live.<br/> Questo valore definisce la posizione in tempo reale più recente che un client può tentare di.<br/>Utilizzo di questa proprietà, è possibile ritardare la posizione di riproduzione in tempo reale e creare un buffer sul lato server per i lettori.<br/>L'unità per questa proprietà è scala cronologica (vedere sotto).<br/>Il valore massimo in tempo reale la durata di Backoff è 300 secondi (3000000000).<br/>Ad esempio, un valore pari a 2000000000 indica che il contenuto disponibile più recente è 20 secondi ritardato dal margine live reale.|
|**presentationWindowDuration**|Si applica allo Streaming Live.<br/>Usare presentationWindowDuration per applicare una finestra temporale scorrevole di frammenti da includere in un elenco di riproduzione.<br/>L'unità per questa proprietà è scala cronologica (vedere sotto).<br/>Ad esempio, impostare presentationWindowDuration = 1200000000 per applicare una finestra temporale scorrevole di due minuti. Nella playlist verranno inclusi i contenuti multimediali che rientrano nei 2 minuti dell’arco live. Se un frammento attraversa il limite, l'intero frammento verrà incluso nella playlist. La durata minima della finestra di presentazione è di 60 secondi.|
|**startTimestamp**|Si applica a Video on Demand (VoD) o lo Streaming Live.<br/>Si tratta di un valore long che rappresenta un punto assoluto iniziale del flusso. Viene arrotondato all'inizio GOP successivo più vicino. L'unità è il valore di scala cronologica, in modo da poter un startTimestamp di 150000000 per 15 secondi.<br/>Usare startTimestamp ed endTimestampp per tagliare i frammenti che saranno presente nella playlist del (manifesto).<br/>Ad esempio, startTimestamp = 40000000 ed endTimestamp = 100000000 utilizzando la scala cronologica predefinito genera una playlist contenente frammenti compresa tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**timescale**|Si applica a tutti i timestamp e le durate in un intervallo di tempo di presentazione, specificato come numero di incrementi in un secondo.<br/>Valore predefinito è 10000000 - dieci milioni incrementi in un secondo, in cui ogni incremento potrebbe essere lungo di 100 nanosecondi.<br/>Ad esempio, se si desidera impostare un startTimestamp a 30 secondi, si utilizzerebbe un valore di 300000000 quando si usa la scala cronologica predefinito.|

### <a name="tracks"></a>Brani

Si specifica un elenco di track proprietà di condizioni di filtro (FilterTrackPropertyConditions) basato su cui le tracce del flusso (Live Streaming o Video on Demand) devono essere incluso nel manifesto creato dinamicamente. I filtri vengono combinati usando gli operatori logici **AND** e **O**.

Le condizioni delle proprietà di filtro indicano i tipi di brano, i valori (descritti nella tabella seguente) e le operazioni (Uguale, DiversoDa). 

|NOME|Descrizione|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per le tracce in un Asset, ottenere ed esaminare il file manifesto.|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

### <a name="example"></a>Esempio

L'esempio seguente definisce un filtro di Live Streaming: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associare i filtri localizzatore di Streaming

È possibile specificare un elenco delle [filtri di asset o account](filters-concept.md) nel [localizzatore di Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). Il [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) si applica questo elenco di filtri insieme a quelli del client specifica l'URL. Questa combinazione genera una [manifesto dinamico](filters-dynamic-manifest-overview.md), basata su filtri nell'URL + filtri è specificare al localizzatore di Streaming. 

Vedere gli esempi seguenti:

* [Associare i filtri localizzatore di Streaming - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associare i filtri localizzatore di Streaming - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>L'aggiornamento di filtri
 
**I localizzatori di streaming** non sono aggiornabili mentre i filtri possono essere aggiornati. 

Non è consigliabile aggiornare la definizione di filtri associata pubblicati attivamente **localizzatore di Streaming**, soprattutto quando la rete CDN è abilitata. Streaming Server e le reti CDN possono avere cache interne che possono comportare dati memorizzati nella cache non aggiornati da restituire. 

Se è necessario modificare la definizione del filtro prendere in considerazione la creazione di un nuovo filtro e aggiungendolo al **localizzatore di Streaming** URL o la pubblicazione di un nuovo **localizzatore di Streaming** che fa riferimento il filtro direttamente.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti spiegano come creare filtri in modo programmatico.  

- [Creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Creare filtri con l’interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

