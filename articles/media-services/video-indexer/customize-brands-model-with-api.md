---
title: Usare Azure Video Indexer per personalizzare il modello Marchi
titleSuffix: Azure Media Services
description: Questo articolo illustra come usare Video Indexer di Azure per personalizzare il modello di marchi.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 81ba4cc7be5f9361d21aaea2ba78d0fd6f0f8c95
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289918"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizzare un modello Marchi con l'API di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, il nome Microsoft viene citato in un contenuto audio o video o se appare nel testo visivo di un video, Video Indexer lo rileva come un marchio. Un modello Marchi personalizzato consente di escludere determinati marchi dal rilevamento e includere i marchi che devono far parte del modello che potrebbe non essere presente nel database di marchi di Bing.

Per una panoramica dettagliata, vedere [Panoramica](customize-brands-model-overview.md).

È possibile usare le API di Video Indexer per creare, usare e modificare modelli Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Marchi usando il sito Web di Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Creare un marchio

L'API [Create a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) crea una nuova personalizzazione personalizzata e la aggiunge al modello Custom Brands per l'account specificato. 

> [!NOTE]
> Impostando **abilitato** (nel corpo) su true, il marchio nell'elenco di *inclusione* deve essere rilevato da video Indexer. Impostare **enabled** su false per inserire il marchio nell'elenco *Exclude*, in modo che non venga rilevato da Video Indexer.

Altri parametri che è possibile impostare nel corpo:

* Il campo **referenceUrl** può contenere qualsiasi sito Web di riferimento per il marchio, ad esempio un collegamento alla relativa pagina di Wikipedia.
* Il valore **tag** è un elenco di tag per il marchio. Viene visualizzato nel campo *Categoria* del marchio nel sito Web di Video Indexer. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

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

L'API [Delete a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) rimuove un marchio dal modello Custom Brands per l'account specificato. L'account viene specificato nel parametro **accountId**. Una volta chiamato correttamente, il marchio non sarà più presente negli elenchi di marchi *Include* oppure *Exclude*.

### <a name="response"></a>Risposta

Non viene restituito contenuto quando il marchio viene eliminato.

## <a name="get-a-specific-brand"></a>Recuperare un marchio specifico

L'API [Get a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) consente di cercare i dettagli di un marchio nel modello Custom Brands per l'account specificato usando l'ID del marchio.

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

L'API [Update a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) consente di cercare i dettagli di un marchio nel modello Custom Brands per l'account specificato usando l'ID del marchio.

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

L'API [Get All Brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) restituisce tutti i marchi nel modello Custom Brands per l'account specificato, indipendentemente dal fatto che il marchio debba essere incluso nell'elenco *Includi* o *Escludi* marchi.

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

L'API [Get Brands Settings](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) restituisce le impostazioni del modello di marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi di Bing non sono abilitati, Video Indexer rileverà solo i marchi dal modello Marchi personalizzato dell'account specificato.

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

I [marchi di aggiornamento](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aggiornano le impostazioni del modello di marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi di Bing non sono abilitati, Video Indexer rileverà solo i marchi dal modello Marchi personalizzato dell'account specificato.

Il flag **useBuiltIn** impostato su true indica che i marchi Bing sono abilitati. Se *useBuiltin* è impostato su false, i marchi Bing sono disabilitati.

### <a name="response"></a>Risposta

Non viene restituito alcun contenuto quando il l'impostazione del modello Marchi viene aggiornato correttamente.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Marchi usando il sito Web](customize-brands-model-with-website.md)
