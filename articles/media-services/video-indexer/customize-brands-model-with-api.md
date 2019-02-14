---
title: Usare Azure Video Indexer per personalizzare il modello Marchi
titlesuffix: Azure Media Services
description: Questo articolo illustra come usare Azure Video Indexer per personalizzare il modello Marchi.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001656"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizzare un modello Marchi con l'API di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, Microsoft viene citata in un contenuto audio o video o se appare nel testo visivo di un video, Video Indexer lo rileva come un marchio. Un modello Marchi personalizzato consente di escludere determinati marchi dal rilevamento e includere i marchi che devono far parte del modello che potrebbe non essere presente nel database di marchi di Bing.

Per una panoramica dettagliata, vedere [Panoramica](customize-brands-model-overview.md).

È possibile usare le API di Video Indexer per creare, usare e modificare modelli Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Marchi usando il sito Web di Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Creare un marchio

Consente di creare un nuovo marchio personalizzato e aggiungerlo al modello Marchi personalizzato per l'account specificato.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Oltre a questi parametri, è necessario fornire un oggetto JSON del corpo della richiesta che fornisce informazioni sul nuovo marchio seguendo il formato dell'esempio riportato di seguito.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Impostare **enabled** su true per inserire il marchio nell'elenco *Include*, in modo che venga rilevato da Video Indexer. Impostare **enabled** su false per inserire il marchio nell'elenco *Exclude*, in modo che non venga rilevato da Video Indexer.

Il campo **referenceUrl** può contenere qualsiasi sito Web di riferimento per il marchio, ad esempio un collegamento alla relativa pagina di Wikipedia.

Il valore **tag** è un elenco di tag per il marchio. Viene visualizzato nel campo *Categoria* del marchio nel sito Web di Video Indexer. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

### <a name="response"></a>Risposta

La risposta specifica informazioni sul marchio che è stato appena creato seguendo il formato dell'esempio riportato di seguito.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Eliminare un marchio

Rimuove un marchio dal modello Marchi personalizzato per l'account specificato. L'account viene specificato nel parametro **accountId**. Una volta chiamato correttamente, il marchio non sarà più presente negli elenchi di marchi *Include* oppure *Exclude*.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|id|numero intero|Sì|L'id del marchio (generato quando è stato creato il marchio)|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

Non viene restituito contenuto quando il marchio viene eliminato.

## <a name="get-a-specific-brand"></a>Recuperare un marchio specifico

Consente di cercare i dettagli di un marchio nel modello Marchi personalizzato per l'account specificato usando l'ID del marchio.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|id|numero intero|Sì|L'ID del marchio (generato quando è stato creato il marchio)|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta specifica informazioni sul marchio che è stato cercato (usando l'ID del marchio) seguendo il formato dell'esempio riportato di seguito.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> Se **enabled** è impostato su **true**, il marchio è presente nell'elenco *Include* in modo che Video Indexer lo rilevi, mentre se **enabled** è impostato su false il marchio è presente nell'elenco *Exclude*, dunque Video Indexer non lo rileverà.

## <a name="update-a-specific-brand"></a>Aggiornare un marchio specifico

Consente di cercare i dettagli di un marchio nel modello Marchi personalizzato per l'account specificato usando l'ID del marchio.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|id|numero intero|Sì|L'ID del marchio (generato quando è stato creato il marchio)|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Oltre a questi parametri, è necessario fornire un oggetto JSON del corpo della richiesta che fornisca informazioni aggiornate sul marchio da aggiornare seguendo il formato dell'esempio seguente.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> In questo esempio il marchio che è stato creato nel corpo della richiesta di esempio nella sezione **Creare un marchio** viene aggiornato con un nuovo tag e una nuova descrizione. Anche il valore **enabled** è stato modificato in false per inserirlo nell'elenco *Exclude*.

### <a name="response"></a>Risposta

La risposta specifica informazioni aggiornate sul marchio che è stato aggiornato seguendo il formato dell'esempio riportato di seguito.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Ottenere tutti i marchi

Restituisce tutti i marchi nel modello Marchi personalizzato per l'account specificato indipendentemente dal fatto che si intenda inserire il marchio nell'elenco *Include* oppure *Exclude*.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta restituisce un elenco di tutti i marchi nell'account e ognuno dei relativi dettagli seguendo il formato dell'esempio riportato di seguito.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Il marchio denominato *Example* è presente nell'elenco *Include* in modo che Video Indexer lo rilevi, e il marchio denominato *Example2* è presente nell'elenco *Exclude*, dunque Video Indexer non lo rileverà.

## <a name="get-brands-model-settings"></a>Ottenere le impostazioni del modello Marchi

Restituisce le impostazioni del modello Marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi di Bing non sono abilitati, Video Indexer rileverà solo i marchi dal modello Marchi personalizzato dell'account specificato.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta mostra se i marchi Bing sono abilitati seguendo il formato dell'esempio riportato di seguito.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> Se **useBuiltIn** è impostato su true, i marchi Bing sono abilitati. Se *useBuiltin* è impostato su false, i marchi Bing sono disabilitati. Il valore **state** può essere ignorato perché è stato deprecato.

## <a name="update-brands-model-settings"></a>Aggiornare le impostazioni del modello Marchi

Aggiorna le impostazioni del modello Marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi di Bing non sono abilitati, Video Indexer rileverà solo i marchi dal modello Marchi personalizzato dell'account specificato.

### <a name="request-url"></a>URL richiesta:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Oltre a questi parametri, è necessario fornire un oggetto JSON del corpo della richiesta che fornisce informazioni sul nuovo marchio seguendo il formato dell'esempio riportato di seguito.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> Se **useBuiltIn** è impostato su true, i marchi Bing sono abilitati. Se *useBuiltin* è impostato su false, i marchi Bing sono disabilitati.

### <a name="response"></a>Risposta

Non viene restituito alcun contenuto quando il l'impostazione del modello Marchi viene aggiornato correttamente.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Marchi usando il sito Web](customize-brands-model-with-website.md)
