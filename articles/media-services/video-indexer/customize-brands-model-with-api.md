---
title: Personalizzare un modello di marchi con Video Indexer API
titleSuffix: Azure Media Services
description: Informazioni su come personalizzare un modello di marchi con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128003"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizzare un modello Marchi con l'API di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, Microsoft è citata in contenuti video o audio o se viene visualizzata in un testo visivo in un video, Video Indexer lo rileva come marchio nel contenuto. Un modello Marchi personalizzato consente di escludere determinati marchi dal rilevamento e includere i marchi che devono far parte del modello che potrebbe non essere presente nel database di marchi di Bing.

Per una panoramica dettagliata, vedere [Panoramica](customize-brands-model-overview.md).

È possibile usare le API di Video Indexer per creare, usare e modificare modelli Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Marchi usando il sito Web di Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Creare un marchio

L'API [Create a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) crea una nuova personalizzazione personalizzata e la aggiunge al modello Custom Brands per l'account specificato.

> [!NOTE]
> Impostando `enabled` (nel corpo) su true, il marchio viene inserito nell'elenco di *inclusione* per video Indexer da rilevare. `enabled`Se si imposta su false, il marchio viene inserito nell'elenco di *esclusione* , quindi video Indexer non lo rileva.

Altri parametri che è possibile impostare nel corpo:

* Il `referenceUrl` valore può essere qualsiasi sito Web di riferimento per il marchio, ad esempio un collegamento alla pagina di Wikipedia.
* Il `tags` valore è un elenco di tag per il marchio. Questo tag viene visualizzato nel campo *categoria* del marchio nel sito web video Indexer. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

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

L'API [Delete a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) rimuove un marchio dal modello Custom Brands per l'account specificato. L'account è specificato nel `accountId` parametro. Una volta chiamato correttamente, il marchio non sarà più presente negli elenchi di marchi *Include* oppure *Exclude*.

### <a name="response"></a>Risposta

Non viene restituito alcun contenuto quando il marchio viene eliminato correttamente.

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
> `enabled`l'impostazione di su `true` indica che il marchio si trova nell'elenco di *inclusione* per video Indexer da rilevare e `enabled` che se è false significa che il marchio è incluso nell'elenco di *esclusione* , quindi video Indexer non lo rileverà.

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
> L' *esempio* denominato è nell'elenco di *inclusione* per video Indexer da rilevare e il marchio denominato *example2* si trova nell'elenco di *esclusione* , quindi video Indexer non lo rileva.

## <a name="get-brands-model-settings"></a>Ottenere le impostazioni del modello Marchi

L'API [Get Brands Settings](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) restituisce le impostazioni del modello di marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi Bing non sono abilitati, Video Indexer rileverà solo i marchi del modello Custom Brands dell'account specificato.

### <a name="response"></a>Risposta

La risposta mostra se i marchi Bing sono abilitati seguendo il formato dell'esempio riportato di seguito.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`l'impostazione di true indica che i marchi Bing sono abilitati. Se `useBuiltin` è false, i marchi Bing sono disabilitati. Il `state` valore può essere ignorato perché è stato deprecato.

## <a name="update-brands-model-settings"></a>Aggiornare le impostazioni del modello Marchi

L'API [Aggiorna marche](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aggiorna le impostazioni del modello di marchi nell'account specificato. Le impostazioni del modello Marchi rappresentano se il rilevamento dal database di marchi di Bing è abilitato o meno. Se i marchi Bing non sono abilitati, Video Indexer rileverà solo i marchi del modello Custom Brands dell'account specificato.

Il `useBuiltIn` flag impostato su true significa che i marchi Bing sono abilitati. Se `useBuiltin` è false, i marchi Bing sono disabilitati.

### <a name="response"></a>Risposta

Non è stato restituito alcun contenuto quando l'impostazione del modello di marchi è stata aggiornata correttamente.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Marchi usando il sito Web](customize-brands-model-with-website.md)
