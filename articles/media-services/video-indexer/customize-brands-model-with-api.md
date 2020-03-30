---
title: Personalizzare un modello di brand con l'API Video Indexer
titleSuffix: Azure Media Services
description: Scopri come personalizzare un modello Brands con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128003"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizzare un modello Marchi con l'API di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Ad esempio, se Microsoft è menzionata nei contenuti video o audio o se viene visualizzata nel testo visivo di un video, Video Indexer lo rileva come un marchio nel contenuto. Un modello Marchi personalizzato consente di escludere determinati marchi dal rilevamento e includere i marchi che devono far parte del modello che potrebbe non essere presente nel database di marchi di Bing.

Per una panoramica dettagliata, vedere [Panoramica](customize-brands-model-overview.md).

È possibile usare le API di Video Indexer per creare, usare e modificare modelli Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Marchi usando il sito Web di Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Creare un marchio

La creazione di [un'API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) di marca crea un nuovo marchio personalizzato e lo aggiunge al modello Brands personalizzato per l'account specificato.

> [!NOTE]
> L'impostazione `enabled` (nel corpo) su true inserisce il marchio nell'elenco *Includi* per l'Indicizzatore Video da rilevare. L'impostazione `enabled` su false inserisce il marchio nell'elenco *Escludi,* in modo che L'indicizzatore video non lo rilevi.

Alcuni altri parametri che è possibile impostare nel corpo:

* Il `referenceUrl` valore può essere qualsiasi sito web di riferimento per il marchio, ad esempio un link alla sua pagina di Wikipedia.
* Il `tags` valore è un elenco di tag per il marchio. Questo tag viene visualizzato nel campo *Categoria* del brand nel sito Web Indicizzatore video. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

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

[L'API di eliminazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) di un brand rimuove un brand dal modello Brands personalizzato per l'account specificato. L'account viene `accountId` specificato nel parametro. Una volta chiamato correttamente, il marchio non sarà più presente negli elenchi di marchi *Include* oppure *Exclude*.

### <a name="response"></a>Risposta

Non sono stati restituiti contenuti quando il marchio viene eliminato correttamente.

## <a name="get-a-specific-brand"></a>Recuperare un marchio specifico

L'API [Get a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) ti consente di cercare i dettagli di un brand nel modello Brands personalizzato per l'account specificato utilizzando l'ID del brand.

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
> `enabled`l'impostazione per `true` indica che il marchio è presente nell'elenco `enabled` *Includi* per l'indicizzatore video da rilevare e che il falso indica che il marchio è incluso nell'elenco *Escludi,* pertanto L'indicizzatore video non lo rileverà.

## <a name="update-a-specific-brand"></a>Aggiornare un marchio specifico

L'aggiornamento di [un'API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) del brand consente di cercare i dettagli di un brand nel modello Brands personalizzato per l'account specificato utilizzando l'ID del brand.

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

L'API [Get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) restituisce tutti i brand nel modello Brands personalizzato per l'account specificato, indipendentemente dal fatto che il brand sia destinato ad essere incluso nell'elenco *Includi* o *Escludi* brand.

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
> Il brand denominato *Example* è nell'elenco *Includi* per l'Indicizzatore Video da rilevare e il brand denominato *Example2* è nell'elenco *Exclude,* quindi L'Indicizzatore Video non lo rileverà.

## <a name="get-brands-model-settings"></a>Ottenere le impostazioni del modello Marchi

L'API [get brands settings](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) restituisce le impostazioni del modello Brands nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i brand Bing non sono abilitati, L'indicizzatore video rileverà solo i brand del modello Brand personalizzato dell'account specificato.

### <a name="response"></a>Risposta

La risposta mostra se i marchi Bing sono abilitati seguendo il formato dell'esempio riportato di seguito.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`se impostato su true rappresenta l'abilitazione dei marchi Bing. Se `useBuiltin` è false, le marche Bing sono disabilitate. Il `state` valore può essere ignorato perché è stato deprecato.

## <a name="update-brands-model-settings"></a>Aggiornare le impostazioni del modello Marchi

L'API [Update Brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aggiorna le impostazioni del modello Brands nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i brand Bing non sono abilitati, L'indicizzatore video rileverà solo i brand del modello Brand personalizzato dell'account specificato.

Il `useBuiltIn` flag impostato su true indica che i marchi Bing sono abilitati. Se `useBuiltin` è false, le marche Bing sono disabilitate.

### <a name="response"></a>Risposta

Non sono presenti contenuti restituiti quando l'impostazione del modello Brands viene aggiornata correttamente.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Marchi usando il sito Web](customize-brands-model-with-website.md)
