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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 61b877c322fcd58472990c328beea2e309502bce
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652349"
---
# <a name="define-account-filters-and-asset-filters"></a>Definire filtri account e filtri asset  

Quando si distribuiscono contenuti ai clienti (eventi di Streaming Live o Video on Demand) il client potrebbe essere necessario una maggiore flessibilità rispetto a quanto descritto nel file manifesto predefinito dell'asset. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

I filtri sono regole lato server che consentono ai clienti di eseguire operazioni come: 

- Riprodurre una sola sezione di un video (anziché il video intero). Ad esempio: 
  - Ridurre il manifesto in modo da mostrare solo una sottoclip di un evento live ("filtro di sottoclip") o
  - Tagliare l'inizio di un video ("trimming di un video").
- Distribuire solo i rendering specificati e/o le tracce di lingua specificate, se supportate dal dispositivo usato per la riproduzione dei contenuti ("filtro di rendering"). 
- Regolare la finestra di presentazione (DVR) in modo da ottenere una lunghezza limitata della finestra nel lettore ("regolazione finestra presentazione").

Servizi multimediali offre [manifesti dinamici](filters-dynamic-manifest-overview.md) basati su filtri predefiniti. Dopo aver definito i filtri, è possibile usarli nell'URL di streaming. È possibile applicare filtri ai protocolli di streaming a bitrate adattivo: Apple HLS (HTTP Live Streaming), MPEG-DASH e Smooth Streaming.

Nella tabella seguente sono disponibili alcuni esempi di URL con filtri:

|Protocollo|Esempio|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`<br/>Per HLS v3, usare: `format=m3u8-aapl-v3`.|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definire filtri

Sono disponibili due tipi di filtri di asset: 

* [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) (globali): possono essere applicati a qualsiasi asset nell’account di Servizi multimediali di Azure e hanno una durata equivalente a quella dell’account.
* [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters) (locali): possono essere applicati solo a uno degli asset a cui è stato associato il filtro in fase di creazione e hanno una durata equivalente a quella dell’asset. 

I [filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e i [filtri Asset](https://docs.microsoft.com/rest/api/media/assetfilters) hanno esattamente le stesse proprietà per la definizione/descrizione del filtro. Tranne quando si crea il **filtro asset**, è necessario specificare il nome dell'asset a cui si desidera associare il filtro.

A seconda dello scenario, decidere quale tipo di filtro è più adatto (asset o account). I filtri asset sono più adatti per i profili di dispositivo (filtro di rendering), mentre i filtri asset possono essere usati per tagliare un asset specifico.

Utilizzare le seguenti proprietà per descrivere i filtri. 

|NOME|DESCRIZIONE|
|---|---|
|firstQuality|La prima velocità in bit di qualità del filtro.|
|presentationTimeRange|L’intervallo di tempo di presentazione. Questa proprietà viene utilizzata per filtrare i punti di inizio/fine del manifesto, la lunghezza della finestra di presentazione e la posizione iniziale live. <br/>Per altre informazioni, vedere [Intervallo di tempo di presentazione](#presentationtimerange).|
|brani|Le condizioni di selezione dei brani. Per ulteriori informazioni, vedere [brani](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Utilizzare questa proprietà con i **filtri asset**. Non è consigliabile impostare la proprietà per i **filtri account**.

|NOME|DESCRIZIONE|
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

|NOME|DESCRIZIONE|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per le tracce in un Asset, ottenere ed esaminare il file manifesto.|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

## <a name="associate-filters-with-streaming-locator"></a>Associare i filtri localizzatore di Streaming

È possibile specificare un elenco di filtri di asset o account, si applica anche per il localizzatore di Streaming. Il [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) si applica questo elenco di filtri insieme a quelli del client specifica l'URL. Questa combinazione genera una [dyanamic manifesto](filters-dynamic-manifest-overview.md), basata su filtri nell'URL + filtri è specificare nel localizzatore di Streaming. È consigliabile usare questa funzionalità se si desidera applicare i filtri, ma non si desidera esporre i nomi dei filtri nell'URL.

## <a name="definition-example"></a>Esempio di definizione

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

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti spiegano come creare filtri in modo programmatico.  

- [Creare filtri con le API REST](filters-dynamic-manifest-rest-howto.md)
- [Creare filtri con .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Creare filtri con l’interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

