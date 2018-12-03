---
title: Eseguire la migrazione dall'API Video Indexer di Azure v1 alla v2
titlesuffix: Azure Media Services
description: Questo argomento illustra come eseguire la migrazione dall'API Video Indexer di Azure v1 alla v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ad2e2dace7d94c196d46beed957b39f3953246b5
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292059"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Eseguire la migrazione dall'API Video Indexer di Azure v1 alla v2

> [!Note]
> L'API Video Indexer v1 è stata deprecata l'1 agosto 2018. È ora necessario usare l'API Video Indexer v2. <br/>Per le attività di sviluppo con le API Video Indexer v2, fare riferimento alle istruzioni disponibili [qui](https://api-portal.videoindexer.ai/). 

Questo articolo descrive le modifiche introdotte nella versione 2.  

## <a name="api-changes"></a>Modifiche all'API

### <a name="authorization-and-operations"></a>Autorizzazione e operazioni

Nella versione v2 di Video Indexer è stato modificato il modello di autenticazione e autorizzazione dell'API. Sono presenti due set di API: 

* Authorization 
* Operazioni

L'API **Authorization** viene usata per ottenere i token di accesso per chiamare l'API **Operations**. L'API **Operations** contiene tutte le API di Video Indexer, ad esempio per il caricamento di video, il recupero di informazioni dettagliate e altre operazioni.

Dopo aver [sottoscritto](video-indexer-get-started.md) l'API **Authorization** sarà possibile ottenere i token di accesso passando la chiave di sottoscrizione (come nella versione 1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Come ottenere e usare il token di accesso per API delle operazioni

Quando si chiama l'API **Operations**, la chiave di sottoscrizione non viene più usata. Si passano invece i token di accesso ottenuti dall'API **Authorization**. 

Ogni richiesta deve includere un token valido, corrispondente al livello di accesso dell'API che si sta chiamando. Ad esempio le operazioni sull'utente, come ottenere i propri account, richiedono un token di accesso utente. Le operazioni a livello di account, ad esempio elencare tutti i video, richiedono un token di accesso account. Le operazioni sui video, ad esempio la reindicizzazione, richiedono un token di accesso account o un token di accesso video.

Per semplificare le operazioni è possibile usare l'API **Authorization** > **GetAccounts** per ottenere gli account senza prima ottenere un token utente. È anche possibile richiedere di ottenere gli account con token validi, in modo da evitare una chiamata aggiuntiva per ottenere un token di account.

Per altre informazioni sui vari token di accesso, vedere [Usare l'API Video Indexer di Azure](video-indexer-use-apis.md).

### <a name="locations"></a>Località

Ogni chiamata all'API deve includere la posizione dell'account di Video Indexer. Le chiamate API senza posizione o con una posizione errata avranno esito negativo.

I valori applicabili sono descritti nella tabella seguente. **Valore parametro** è il valore passato durante l'uso dell'API.

|**Nome**|**Valore parametro**|**Descrizione**|
|---|---|---|
|Versione di valutazione|versione di valutazione|Usato per gli account in versione di valutazione. Ad esempio: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Stati Uniti occidentali|westus2|Usato per l'area di Azure Stati Uniti occidentali 2.  Ad esempio: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Europa settentrionale |northeurope|Usato per l'area di Azure Europa settentrionale. Ad esempio: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Asia orientale|eastasia|Usato per l'area di Azure Asia orientale. Ad esempio: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Modello di dati

Video Indexer ha ora un modello di dati semplificato, per fornire informazioni dettagliate molto più chiare. Per altre informazioni sull'output prodotto dall'API v2, vedere [Esaminare l'output di Video Indexer generato dall'API v2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Le definizioni dell'API Video Indexer sono state aggiornate di conseguenza e sono disponibili per il download tramite il [portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Esempi di differenze tra V1 e V2

Le nuove API V2 prevedono 3 parametri principali:

1. [LOCATION]: come descritto in precedenza. Trial, westus2, northeurope o eastasia.
2. [YOUR_ACCOUNT_ID]: ID GUID dell'account. Restituito quando si recuperano tutti gli account (descritto di seguito).
3. [YOUR_VIDEO_ID]: ID del video (ad esempio "d4fa369abc"). Restituito quando si carica un video o durante la ricerca di video.

#### <a name="uploading-a-video-in-v1"></a>Caricamento di un video nella versione 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Caricamento di un video nella versione 2:

1. Ottenere un token di accesso per la richiesta di caricamento:

  È possibile recuperare tutti gli account e i relativi token di accesso:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Oppure recuperare il token di accesso dell'account specificato:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Caricare un video:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Recupero di informazioni dettagliate nella versione 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Recupero di informazioni dettagliate nella versione 2:

1. Usare il token di accesso account oppure ottenere un token di accesso a livello di video:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Ottenere le informazioni dettagliate:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Recupero dello stato di elaborazione video nella versione 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Recupero dello stato di elaborazione video nella versione 2:

Nell'API v2 lo stato di elaborazione viene restituito come parte dell'API Get Video Index.

1. Usare il token di accesso account oppure ottenere un token di accesso a livello di video:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Ottenere le informazioni dettagliate:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Passaggi successivi

[Usare l'API Video Indexer di Azure](video-indexer-use-apis.md)

