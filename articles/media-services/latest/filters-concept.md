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
ms.openlocfilehash: 2e188a0e8ee8b5f2037c07c3f15fd78a42852ce9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023229"
---
# <a name="filters"></a>Filtri

Quando si distribuiscono contenuti ai clienti (eventi live streaming o video on demand), il client potrebbe richiedere una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure offre [manifesti dinamici](filters-dynamic-manifest-overview.md) basati su filtri predefiniti. 

I filtri sono regole lato server che consentono ai clienti di eseguire operazioni come: 

- Riprodurre una sola sezione di un video (anziché il video intero). Ad esempio:
  - Ridurre il manifesto in modo da mostrare solo una sottoclip di un evento live ("filtro di sottoclip") o
  - Tagliare l'inizio di un video ("trimming di un video").
- Distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").

Servizi multimediali consente di creare filtri per gli **account** e **filtri di asset** per il contenuto. Inoltre, è possibile associare i filtri creati in precedenza a un **localizzatore di streaming**.

## <a name="defining-filters"></a>Definizione di filtri

Sono disponibili due tipi di filtri: 

* [Filtri account](/rest/api/media/accountfilters) (globali): possono essere applicati a qualsiasi asset nell’account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell’account.
* [Filtri asset](/rest/api/media/assetfilters) (locali): possono essere applicati solo a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell’asset. 

I **filtri di account** e i tipi di filtri di **Asset** hanno esattamente le stesse proprietà per la definizione o la descrizione del filtro. Tranne quando si crea il **filtro asset**, è necessario specificare il nome dell'asset a cui si desidera associare il filtro.

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
|**endTimestamp**|È adatto per i video on demand (VoD).<br/>Per la presentazione in streaming live, viene ignorato e applicato automaticamente al termine della presentazione e il flusso diventa VoD.<br/>Si tratta di un valore Long che rappresenta un punto finale assoluto della presentazione, arrotondato all'inizio GOP successivo più vicino. L'unità è la scala cronologica, quindi un endTimestamp di 1,8 miliardi sarà per 3 minuti.<br/>Usare startTimestamp e endTimestamp per tagliare i frammenti che saranno presenti nella playlist (manifesto).<br/>Ad esempio, startTimestamp = 40000000 e endTimestamp = 100000000 usando la scala cronologica predefinita genereranno una playlist che contiene frammenti compresi tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**forceEndTimestamp**|Si applica solo a Live streaming.<br/>Indica se la proprietà endTimestamp deve essere presente. Se true, è necessario specificare endTimestamp o viene restituito un codice di richiesta non valido.<br/>Valori consentiti: false, true.|
|**liveBackoffDuration**|Si applica solo a Live streaming.<br/> Questo valore definisce la posizione dinamica più recente a cui un client può effettuare la ricerca.<br/>Utilizzando questa proprietà, è possibile ritardare la posizione di riproduzione in tempo reale e creare un buffer sul lato server per i giocatori.<br/>L'unità per questa proprietà è una scala cronologica (vedere di seguito).<br/>La durata massima del backup attivo è di 300 secondi (3 miliardi).<br/>Ad esempio, il valore 2 miliardi indica che l'ultimo contenuto disponibile è di 20 secondi di ritardo rispetto al Real Edge Live.|
|**presentationWindowDuration**|Si applica solo a Live streaming.<br/>Usare presentationWindowDuration per applicare una finestra temporale scorrevole di frammenti da includere in una playlist.<br/>L'unità per questa proprietà è una scala cronologica (vedere di seguito).<br/>Ad esempio, impostare presentationWindowDuration = 1200000000 per applicare una finestra temporale scorrevole di due minuti. Nella playlist verranno inclusi i contenuti multimediali che rientrano nei 2 minuti dell’arco live. Se un frammento attraversa il limite, l'intero frammento verrà incluso nella playlist. La durata minima della finestra di presentazione è di 60 secondi.|
|**startTimestamp**|Si applica a video on demand (VoD) o streaming live.<br/>Si tratta di un valore Long che rappresenta un punto di inizio assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino. L'unità è la scala cronologica, quindi un startTimestamp di 150 milioni per 15 secondi.<br/>Usare startTimestamp e endTimestampp per tagliare i frammenti che saranno presenti nella playlist (manifesto).<br/>Ad esempio, startTimestamp = 40000000 e endTimestamp = 100000000 usando la scala cronologica predefinita genereranno una playlist che contiene frammenti compresi tra 4 secondi e 10 secondi della presentazione VoD. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**timescale**|Si applica a tutti i timestamp e le durate in un intervallo di tempo di presentazione, specificato come numero di incrementi in un secondo.<br/>Il valore predefinito è 10 milioni-10 milioni incrementi in un secondo, in cui ogni incremento è di 100 nanosecondi.<br/>Se, ad esempio, si desidera impostare un startTimestamp a 30 secondi, utilizzare il valore 300 milioni quando si utilizza la scala cronologica predefinita.|

### <a name="tracks"></a>Brani

Si specifica un elenco di condizioni di proprietà del rilevamento dei filtri (FilterTrackPropertyConditions) in base alle tracce del flusso (streaming live o video su richiesta) che devono essere incluse nel manifesto creato dinamicamente. I filtri vengono combinati usando gli operatori logici **AND** e **O**.

Le condizioni delle proprietà di filtro indicano i tipi di brano, i valori (descritti nella tabella seguente) e le operazioni (Uguale, DiversoDa). 

|Nome|Descrizione|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per i brani in un asset, ottenere ed esaminare il file manifesto.|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

### <a name="example"></a>Esempio

Nell'esempio seguente viene definito un filtro di streaming live: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associazione di filtri con il localizzatore di streaming

È possibile specificare un elenco di [filtri asset o account](filters-concept.md) nel [localizzatore di streaming](/rest/api/media/streaminglocators/create#request-body). Il [Packager dinamico](dynamic-packaging-overview.md) applica questo elenco di filtri insieme a quelli specificati dal client nell'URL. Questa combinazione genera un [manifesto dinamico](filters-dynamic-manifest-overview.md), basato sui filtri presenti nell'URL e nei filtri specificati nel localizzatore di streaming. 

Vedere gli esempi seguenti:

* [Associare filtri a un localizzatore di streaming-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associare filtri a un localizzatore di streaming-CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aggiornamento dei filtri
 
I **localizzatori di streaming** non sono aggiornabili mentre i filtri possono essere aggiornati. 

Non è consigliabile aggiornare la definizione dei filtri associati a un **localizzatore di streaming**pubblicato attivamente, soprattutto quando è abilitata la rete CDN. I server di flusso e CDNs possono avere cache interne che possono causare la restituzione di dati non aggiornati memorizzati nella cache. 

Se è necessario modificare la definizione del filtro, è consigliabile creare un nuovo filtro e aggiungerlo all'URL del **localizzatore di streaming** o pubblicare un nuovo **localizzatore di streaming** che faccia riferimento direttamente al filtro.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti spiegano come creare filtri in modo programmatico.  

- [Creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Creare filtri con l’interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)
