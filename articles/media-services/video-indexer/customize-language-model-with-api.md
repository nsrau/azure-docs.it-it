---
title: Usare le API di Video Indexer per personalizzare un modello linguistico - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello linguistico con le API di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: b7517c8a8745569635a9570c02c851854eebeb96
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289493"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personalizzare un modello linguistico con le API di Video Indexer

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute. 

Per una panoramica dettagliata e le procedure consigliate per i modelli linguistici personalizzati, vedere [Personalizzare un modello linguistico con Video Indexer](customize-language-model-overview.md).

È possibile usare le API di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È possibile anche usare il sito Web, come descritto in [Personalizzare il modello linguistico usando il sito Web di Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

L'API [Crea un modello di linguaggio](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) crea un nuovo modello di linguaggio personalizzato nell'account specificato. È possibile caricare i file per il modello linguistico in questa chiamata. In alternativa, è possibile creare il modello linguistico qui e caricare i file per il modello in un secondo momento aggiornando il modello linguistico.

> [!NOTE]
> È comunque necessario eseguire il training del modello con i relativi file abilitati per fare in modo che il modello possa apprendere il contenuto dei file. La sezione successiva contiene le indicazioni per eseguire il training di una lingua.

Per caricare i file da aggiungere al modello linguistico, è necessario caricare i file nel corpo usando i dati del modulo, oltre a fornire i valori per i parametri obbligatori precedenti. A questo scopo è possibile procedere in due modi: 

1. La chiave sarà il nome del file e il valore sarà il file txt.
2. La chiave sarà il nome del file e il valore sarà un URL al file txt.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico appena creato, insieme ai metadati su ogni file di modello, seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Eseguire il training di un modello linguistico

L'API di [training di un modello di linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) consente di eseguire il training di un modello di lingua personalizzato nell'account specificato con il contenuto dei file caricati e abilitati nel modello di lingua. 

> [!NOTE]
> È necessario prima creare il modello linguistico e caricare i relativi file. È possibile caricare i file quando si crea o si aggiorna il modello linguistico. 

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico di cui è stato appena eseguito il training, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

È necessario usare il valore **ID** restituito del modello di lingua per il parametro **linguisticModelId** quando si [carica un video in index](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e per il parametro **languageModelId** durante la [reindicizzazione di un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

 
## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

L'API [Elimina un modello di linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) Elimina un modello di lingua personalizzato dall'account specificato. Qualsiasi video che usava il modello linguistico eliminato manterrà lo stesso indice finché non si reindicizza il video. Se si reindicizza il video, è possibile assegnare un nuovo modello linguistico al video. In alternativa, verrà usato il modello predefinito per reindicizzare il video.

### <a name="response"></a>Risposta

Quando l'eliminazione del modello linguistico viene completata, non viene restituito alcun contenuto.

## <a name="update-a-language-model"></a>Aggiornare un modello linguistico

L'API [Aggiorna un modello di linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) aggiorna un modello di persona della lingua personalizzata nell'account specificato.

> [!NOTE]
> È necessario aver già creato il modello linguistico. È possibile usare questa chiamata per abilitare o disabilitare tutti i file nel modello, aggiornare il nome del modello linguistico e caricare i file da aggiungervi.

Per caricare i file da aggiungere al modello linguistico, è necessario caricare i file nel corpo usando i dati del modulo, oltre a fornire i valori per i parametri obbligatori precedenti. A questo scopo è possibile procedere in due modi: 

1. La chiave sarà il nome del file e il valore sarà il file txt.
2. La chiave sarà il nome del file e il valore sarà un URL al file txt.


### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico di cui è stato appena eseguito il training, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Usare l' **ID** dei file restituiti nella risposta per scaricare il contenuto del file.

## <a name="update-a-file-from-a-language-model"></a>Aggiornare un file da un modello linguistico

Con l' [aggiornamento di un file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) è possibile aggiornare il nome e **abilitare** lo stato di un file in un modello di lingua personalizzato nell'account specificato.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul file che è stato aggiornato seguendo il formato di output JSON di esempio riportato di seguito.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Usare l' **ID** del file restituito nella risposta per scaricare il contenuto del file.

## <a name="get-a-specific-language-model"></a>Ottenere un modello linguistico specifico

L'API [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) restituisce informazioni sul modello di lingua specificato nell'account specificato, ad esempio la lingua e i file inclusi nel modello di lingua. 

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico specificato, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio di seguito.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Usare l' **ID** del file restituito nella risposta per scaricare il contenuto del file.

## <a name="get-all-the-language-models"></a>Ottenere tutti i modelli linguistici

L'API [Get all](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) restituisce tutti i modelli di linguaggio personalizzati nell'account specificato in un elenco.

### <a name="response"></a>Risposta

La risposta restituisce un elenco di tutti i modelli linguistici nell'account e ognuno dei relativi metadati e file seguendo il formato dell'output JSON riportato di seguito.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Eliminare un file da un modello linguistico

L'API [Delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) Elimina il file specificato dal modello di lingua specificato nell'account specificato. 

### <a name="response"></a>Risposta

Non viene restituito contenuto quando il file viene eliminato con successo dal modello linguistico.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Ottenere i metadati su un file da un modello linguistico

Il [ottenere i metadati di un'API file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) restituisce il contenuto dei metadati e nel file specificato dal modello di lingua scelto nell'account.

### <a name="response"></a>Risposta

La risposta restituisce il contenuto e metadati del file in formato JSON, simile al seguente:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Il contenuto di questo file di esempio sono le parole "hello" e "world"in due righe distinte.

## <a name="download-a-file-from-a-language-model"></a>Scaricare un file da un modello di lingua

Il [download di un'API file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) Scarica un file di testo contenente il contenuto del file specificato dal modello di lingua specificato nell'account specificato. Questo file di testo dovrebbe corrispondere al contenuto del file di testo caricato in origine.

### <a name="response"></a>Risposta

La risposta sarà il download di un file di testo con il contenuto del file in formato JSON. 

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello linguistico usando il sito Web](customize-language-model-with-website.md)
