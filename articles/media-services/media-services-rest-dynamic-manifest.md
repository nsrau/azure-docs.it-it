---
title: Creazione di filtri con l&quot;API REST di Servizi multimediali di Azure | Microsoft Docs
description: "Questo argomento descrive come creare filtri che il client può usare per trasmettere in streaming sezioni specifiche di un flusso. Servizi multimediali crea manifesti dinamici per consentire questo streaming selettivo."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako;cenkdin
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: e10c7b29469f9756803aaba64596bf86830893fa
ms.lasthandoff: 02/16/2017


---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Creazione di filtri con l'API REST di Servizi multimediali di Azure
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partire dalla versione 2.11, Servizi multimediali consente di definire filtri per i propri asset. I filtri sono costituiti da regole lato server che consentono ai clienti di eseguire operazioni particolari, come riprodurre solo una sezione di un video (anziché il video intero) oppure specificare solo un sottoinsieme di rendering audio e video, in modo che possa essere gestito dal dispositivo del cliente (anziché tutti i rendering associati all'asset). Il filtro degli asset viene archiviato attraverso **manifesti dinamici**creati su richiesta del cliente per trasmettere un video in streaming in base ai filtri specificati.

Per altre informazioni sui filtri e sul manifesto dinamico, vedere [Filtri e manifesti dinamici](media-services-dynamic-manifest-overview.md).

Questo argomento illustra come usare le API REST per creare, aggiornare ed eliminare filtri. 

## <a name="types-used-to-create-filters"></a>Tipi usati per la creazione dei filtri
Durante la creazione dei filtri vengono usati i tipi seguenti:  

* [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> Quando si usa l'API REST di Servizi multimediali, tenere presenti le seguenti considerazioni:
> 
> Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md).
> 
> Dopo avere stabilito la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 che indica un altro URI di Servizi multimediali. Le chiamate successive dovranno essere eseguite al nuovo URI, come descritto in [Connessione a un account di Servizi multimediali mediante l'API REST](media-services-rest-connect-programmatically.md). 
> 
> 

## <a name="create-filters"></a>Creare filtri
### <a name="create-global-filters"></a>Creare filtri globali
Per creare un filtro globale, usare le richieste HTTP seguenti:  

#### <a name="http-request"></a>Richiesta HTTP
Intestazioni richiesta

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Corpo della richiesta 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>Risposta HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Creare AssetFilters locali
Per creare un AssetFilter locale, usare le richieste HTTP seguenti:  

#### <a name="http-request"></a>Richiesta HTTP
Intestazioni richiesta

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Corpo della richiesta 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>Risposta HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Elencare i filtri
### <a name="get-all-global-filters-in-the-ams-account"></a>Ottenere tutti i **Filter**globali nell'account di Servizi multimediali di Azure
Per elencare i filtri, usare le richieste HTTP seguenti: 

#### <a name="http-request"></a>Richiesta HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Ottenere gli **AssetFilter**associati a un asset
#### <a name="http-request"></a>Richiesta HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Ottenere un **AssetFilter** in base all'ID
#### <a name="http-request"></a>Richiesta HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Aggiornare i filtri
Usare PATCH, PUT o MERGE per aggiornare un filtro con i nuovi valori di proprietà.  Per altre informazioni su queste operazioni, vedere [PATCH, PUT, MERGE](http://msdn.microsoft.com/library/dd541276.aspx).

Se si aggiorna un filtro, l'endpoint di streaming può impiegare fino a due minuti per aggiornare le regole. Se il contenuto è stato trasmesso usando dei filtri (e memorizzato nelle cache dei proxy e delle reti CDN), l'aggiornamento del filtro può determinare un errore del lettore. È consigliabile quindi cancellare la cache dopo aver aggiornato il filtro. Se questa operazione non è consentita, prendere in considerazione la possibilità di usare un filtro diverso.  

### <a name="update-global-filters"></a>Aggiornare filtri globali
Per aggiornare un filtro globale, usare le richieste HTTP seguenti: 

#### <a name="http-request"></a>Richiesta HTTP
Intestazioni della richiesta: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Corpo della richiesta: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Aggiornare AssetFilters locali
Per aggiornare un filtro locale, usare le richieste HTTP seguenti: 

#### <a name="http-request"></a>Richiesta HTTP
Intestazioni della richiesta: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Corpo della richiesta: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Eliminare filtri
### <a name="delete-global-filters"></a>Eliminare filtri globali
Per eliminare un filtro globale, usare le richieste HTTP seguenti:

#### <a name="http-request"></a>Richiesta HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Eliminare AssetFilter locali
Per eliminare un AssetFilter locale, usare le richieste HTTP seguenti:

#### <a name="http-request"></a>Richiesta HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Creare URL di streaming basati su filtri
Per informazioni su come pubblicare e distribuire asset, vedere [Informazioni generali sulla distribuzione di contenuti ai clienti](media-services-deliver-content-overview.md).

Gli esempi seguenti illustrano come aggiungere filtri agli URL di streaming.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Filtri e manifesti dinamici](media-services-dynamic-manifest-overview.md)


