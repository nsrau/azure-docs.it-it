---
title: Personalizzare un modello di linguaggio con l'API dell'indicizzatore videoCustomize a Language model with Video Indexer API
titlesuffix: Azure Media Services
description: Scopri come personalizzare un modello di lingua con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127972"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Personalizzare un modello di linguaggio con l'API Dell'indicizzatore video

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute.

Per una panoramica dettagliata e le procedure consigliate per i modelli linguistici personalizzati, vedere [Personalizzare un modello linguistico con Video Indexer](customize-language-model-overview.md).

È possibile usare le API di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È possibile anche usare il sito Web, come descritto in [Personalizzare il modello linguistico usando il sito Web di Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

La creazione di un'API del modello di [linguaggio](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) crea un nuovo modello di linguaggio personalizzato nell'account specificato. È possibile caricare i file per il modello linguistico in questa chiamata. In alternativa, è possibile creare il modello linguistico qui e caricare i file per il modello in un secondo momento aggiornando il modello linguistico.

> [!NOTE]
> È comunque necessario eseguire il training del modello con i relativi file abilitati per fare in modo che il modello possa apprendere il contenuto dei file. La sezione successiva contiene le indicazioni per eseguire il training di una lingua.

Per caricare i file da aggiungere al modello di linguaggio, è necessario caricare i file nel corpo utilizzando FormData oltre a fornire i valori per i parametri richiesti sopra. Per eseguire questa attività, è possibile eseguire questa operazione in due modi:There are two ways to do this task:

* Chiave sarà il nome del file e il valore sarà il file txt.
* Chiave sarà il nome del file e il valore sarà un URL al file txt.

### <a name="response"></a>Risposta

La risposta fornisce metadati sul modello di lingua appena creato insieme ai metadati in ognuno dei file del modello seguendo il formato di questo output JSON di esempio:The response provides metadata on the newly created Language model along with metadata on each of the model's files following the format of this example JSON output:

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

Il training di un'API del modello di [linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) eseguito il training di un modello di linguaggio personalizzato nell'account specificato con il contenuto dei file che sono stati caricati e abilitati nel modello di linguaggio.

> [!NOTE]
> È necessario prima creare il modello linguistico e caricare i relativi file. È possibile caricare i file durante la creazione del modello di lingua o aggiornando il modello di lingua.

### <a name="response"></a>Risposta

La risposta fornisce metadati sul modello di lingua appena addestrato insieme ai metadati in ognuno dei file del modello seguendo il formato di questo output JSON di esempio:The response provides metadata on the newly trained Language model along with metadata on each of the model's files following the format of this example JSON output:

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

L'oggetto `id` restituito è un ID univoco utilizzato `languageModelId` per distinguere tra modelli linguistici, mentre viene usato sia per `linguisticModelId` caricare un video per [indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e [reindicizzare le](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API di un video (noto anche come in Video Indexer caricare/reindicizzare le API).

## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

L'API di eliminazione di un modello di [linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) elimina un modello di linguaggio personalizzato dall'account specificato. Qualsiasi video che utilizzava il modello di lingua eliminato manterrà lo stesso indice fino a quando non reindicizzerai il video. Se reindicizzare il video, è possibile assegnare un nuovo modello di lingua al video. In caso contrario, Video Indexer utilizzerà il modello predefinito per reindicizzare il video.

### <a name="response"></a>Risposta

Non è presente alcun contenuto restituito quando il modello di linguaggio viene eliminato correttamente.

## <a name="update-a-language-model"></a>Aggiornare un modello linguistico

L'aggiornamento di un'API del modello di [linguaggio](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) aggiorna un modello di persona del linguaggio personalizzato nell'account specificato.

> [!NOTE]
> È necessario aver già creato il modello linguistico. È possibile usare questa chiamata per abilitare o disabilitare tutti i file nel modello, aggiornare il nome del modello linguistico e caricare i file da aggiungervi.

Per caricare i file da aggiungere al modello di linguaggio, è necessario caricare i file nel corpo utilizzando FormData oltre a fornire i valori per i parametri richiesti sopra. Per eseguire questa attività, è possibile eseguire questa operazione in due modi:There are two ways to do this task:

* Chiave sarà il nome del file e il valore sarà il file txt.
* Chiave sarà il nome del file e il valore sarà un URL al file txt.

### <a name="response"></a>Risposta

La risposta fornisce metadati sul modello di lingua appena addestrato insieme ai metadati in ognuno dei file del modello seguendo il formato di questo output JSON di esempio:The response provides metadata on the newly trained Language model along with metadata on each of the model's files following the format of this example JSON output:

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

Utilizzare `id` il dei file restituiti nella risposta per scaricare il contenuto del file.

## <a name="update-a-file-from-a-language-model"></a>Aggiornare un file da un modello linguistico

L'aggiornamento di [un file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) consente `enable` di aggiornare il nome e lo stato di un file in un modello di lingua personalizzato nell'account specificato.

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

Utilizzare `id` il del file restituito nella risposta per scaricare il contenuto del file.

## <a name="get-a-specific-language-model"></a>Ottenere un modello linguistico specifico

L'API [get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) restituisce informazioni sul modello di linguaggio specificato nell'account specificato, ad esempio la lingua e i file presenti nel modello di linguaggio.

### <a name="response"></a>Risposta

La risposta fornisce metadati sul modello di lingua specificato insieme ai metadati in ognuno dei file del modello che seguono il formato di questo output JSON di esempio:The response provides metadata on the specified Language model along with metadata on each of the model's files following the format of this example JSON output:

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

Utilizzare `id` il del file restituito nella risposta per scaricare il contenuto del file.

## <a name="get-all-the-language-models"></a>Ottenere tutti i modelli linguistici

L'API [get all](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) restituisce tutti i modelli di linguaggio personalizzati nell'account specificato in un elenco.

### <a name="response"></a>Risposta

La risposta fornisce un elenco di tutti i modelli di lingua nell'account e ognuno dei relativi metadati e file seguendo il formato di questo output JSON di esempio:The response provides a list of all of the Language models in your account and each of their metadata and files following the format of this example JSON output:

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

L'API [di eliminazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) elimina il file specificato dal modello di linguaggio specificato nell'account specificato.

### <a name="response"></a>Risposta

Non è presente alcun contenuto restituito quando il file viene eliminato correttamente dal modello di linguaggio.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Ottenere i metadati su un file da un modello linguistico

I [metadati get di un'API di file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) restituiscono il contenuto e i metadati del file specificato dal modello di lingua scelto nel tuo account.

### <a name="response"></a>Risposta

La risposta fornisce il contenuto e i metadati del file in formato JSON, analogamente a questo esempio:

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

## <a name="download-a-file-from-a-language-model"></a>Scaricare un file da un modello di linguaDownload a file from a Language model

L'API di [download di un file](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) scarica un file di testo contenente il contenuto del file specificato dal modello di lingua specificato nell'account specificato. Questo file di testo dovrebbe corrispondere al contenuto del file di testo caricato in origine.

### <a name="response"></a>Risposta

La risposta sarà il download di un file di testo con il contenuto del file in formato JSON.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello linguistico usando il sito Web](customize-language-model-with-website.md)
