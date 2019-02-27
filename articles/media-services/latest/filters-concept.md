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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372959"
---
# <a name="define-account-filters-and-asset-filters"></a>Definire filtri account e filtri asset  

Quando si distribuiscono contenuti ai clienti (flusso di eventi dal vivo o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i filtri account e i filtri asset per i propri contenuti. 

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
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

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
|presentationTimeRange|L’intervallo di tempo di presentazione. Questa proprietà viene utilizzata per filtrare i punti di inizio/fine del manifesto, la lunghezza della finestra di presentazione e la posizione iniziale live. <br/>Per altre informazioni, vedere [Intervallo di tempo di presentazione](#PresentationTimeRange).|
|brani|Le condizioni di selezione dei brani. Per ulteriori informazioni, vedere [brani](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Utilizzare questa proprietà con i **filtri asset**. Non è consigliabile impostare la proprietà per i **filtri account**.

|NOME|DESCRIZIONE|
|---|---|
|**endTimestamp**|Il limite di tempo finale assoluto. È adatto per i video on demand (VoD). Per la presentazione live, viene automaticamente ignorato e applicato al termine della presentazione, quando il flusso diventa VoD.<br/><br/>Il valore rappresenta un punto finale assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino.<br/><br/>Utilizzare StartTimestamp ed EndTimestamp per tagliare la playlist (manifesto). Ad esempio, se StartTimestamp = 40000000 ed EndTimestamp = 100000000, verrà generata una playlist che contiene elementi multimediali tra StartTimestamp ed EndTimestamp. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.<br/><br/>Vedere anche la seguente definizione di **forceEndTimestamp**.|
|**forceEndTimestamp**|Si applica ai filtri live.<br/><br/>**forceEndTimestamp** è un valore booleano che indica se **endTimestamp** è stato impostato su un valore valido o meno. <br/><br/>Se il valore è **true**, il valore **endTimestamp** deve essere specificato. Se non è specificato, viene restituita una richiesta non valida.<br/><br/>Se, ad esempio, si vuole definire un filtro che inizia in corrispondenza di 5 minuti nel video di input e viene mantenuto fino alla fine del flusso, bisogna impostare **forceEndTimestamp** su false e omettere l'impostazione **endTimestamp**.|
|**liveBackoffDuration**|Si applica solo a contenuti live. La proprietà viene utilizzata per definire la posizione della riproduzione live. Con questa regola, è possibile ritardare la posizione di riproduzione live e creare un buffer lato server per i lettori. LiveBackoffDuration dipende dalla posizione live. La durata massima di backoff live è di 300 secondi.|
|**presentationWindowDuration**|Si applica ai contenuti live. Usare **presentationWindowDuration** per applicare una finestra temporale scorrevole alla playlist. Ad esempio, impostare presentationWindowDuration = 1200000000 per applicare una finestra temporale scorrevole di due minuti. Nella playlist verranno inclusi i contenuti multimediali che rientrano nei 2 minuti dell’arco live. Se un frammento attraversa il limite, l'intero frammento verrà incluso nella playlist. La durata minima della finestra di presentazione è di 60 secondi.|
|**startTimestamp**|Si applica ai flussi live o VoD. Il valore rappresenta un punto iniziale assoluto del flusso. Viene arrotondato all'inizio GOP successivo più vicino.<br/><br/>Utilizzare **startTimestamp** e **endTimestamp** per tagliare la playlist (manifesto). Ad esempio, se startTimestamp = 40000000 ed endTimestamp = 100000000, verrà generata una playlist che contiene elementi multimediali tra StartTimestamp ed EndTimestamp. Se un frammento attraversa il limite, l'intero frammento verrà incluso nel manifesto.|
|**timescale**|Si applica ai flussi live o VoD. La scala cronologica usata dai timestamp e dalle durate specificate in precedenza. La scala cronologica predefinita è 10000000. È possibile usare una scala cronologica diversa. Il valore predefinito è 10000000 HNS (centinaia di nanosecondi).|

### <a name="tracks"></a>Brani

Specificare un elenco di condizioni delle proprietà dei brani (FilterTrackPropertyConditions) in base alle quali i brani del flusso (live o video on demand) devono essere inclusi nel manifesto creato dinamicamente. I filtri vengono combinati usando gli operatori logici **AND** e **O**.

Le condizioni delle proprietà di filtro indicano i tipi di brano, i valori (descritti nella tabella seguente) e le operazioni (Uguale, DiversoDa). 

|NOME|DESCRIZIONE|
|---|---|
|**Bitrate**|Usare la velocità in bit del brano per il filtro.<br/><br/>Il valore consigliato è un intervallo di velocità in bit, in bit al secondo. Ad esempio, "0-2427000".<br/><br/>Nota: anche se è possibile usare un valore di velocità in bit specifico, ad esempio 250000 (bit al secondo), questo approccio è sconsigliato, perché la velocità in bit esatta può variare da un asset a un altro.|
|**FourCC**|Usare il valore FourCC del brano come filtro.<br/><br/>Il valore è il primo elemento di formato codec, come specificato in [RFC 6381](https://tools.ietf.org/html/rfc6381). Attualmente sono supportati i seguenti codec: <br/>Per i video: "avc1", "hev1", "hvc1"<br/>Per l’audio: "mp4a", "ec-3"<br/><br/>Per determinare i valori di FourCC per i brani in un asset, [ottenere ed esaminare il file manifesto](#get-and-examine-manifest-files).|
|**Lingua**|Usare la lingua del brano come filtro.<br/><br/>Il valore è il tag di una lingua che si desidera includere, come specificato in RFC 5646. Ad esempio, "en".|
|**Nome**|Usare il nome del brano come filtro.|
|**Tipo**|Usare il tipo di brano come filtro.<br/><br/>Sono consentiti i seguenti valori: "video", "audio" o "text".|

## <a name="example"></a>Esempio

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

