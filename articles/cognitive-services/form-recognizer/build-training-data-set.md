---
title: Come creare un set di dati di training per un modello personalizzato - Grado di riconoscimento dei moduliHow to build a training data set for a custom model - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare che il set di dati di training sia ottimizzato per il training di un modello di riconoscimento dei moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380627"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Creare un set di dati di training per un modello personalizzatoBuild a training data set for a custom model

Quando si usa il modello personalizzato Riconoscitore moduli, si forniscono i propri dati di training in modo che il modello possa eseguire il training ai moduli specifici del settore. È possibile eseguire il training di un modello con cinque moduli compilati o un modulo vuoto (è necessario includere la parola "vuoto" nel nome del file) più due moduli compilati. Anche se si dispone di moduli compilati sufficienti per il training, l'aggiunta di un modulo vuoto al set di dati di training può migliorare l'accuratezza del modello.

Se si desidera usare dati di training con etichetta manuale, è consigliabile iniziare con almeno cinque forme dello stesso tipo. È comunque possibile utilizzare moduli senza etichetta e un modulo vuoto nello stesso set di dati.

## <a name="training-data-tips"></a>Suggerimenti per i dati di training

È importante usare un set di dati ottimizzato per il training. Usare i suggerimenti seguenti per ottenere i migliori risultati dall'operazione [Train Custom Model:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Se possibile, utilizzare documenti PDF basati su testo anziché documenti basati su immagini. I PDF scansionati vengono gestiti come immagini.
* Per i moduli compilati, utilizzare esempi con tutti i relativi campi compilati.
* Usare moduli con valori diversi in ogni campo.
* Se le immagini del modulo sono di qualità inferiore, utilizzare un set di dati più grande (10-15 immagini, ad esempio).
* La dimensione totale del set di dati di training può essere fino a 500 pagine.

## <a name="general-input-requirements"></a>Requisiti generali di input

Assicurati che il set di dati di training segua anche i requisiti di input per tutto il contenuto del sistema di riconoscimento dei moduli. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Carica i tuoi dati di allenamento

Dopo aver creato il set di documenti del modulo che verrà usato per il training, è necessario caricarlo in un contenitore di archiviazione BLOB di Azure.When you've put together the set of form documents that you'll use for training, you need to upload it to an Azure blob storage container. Se non si sa come creare un account di archiviazione di Azure con un contenitore, seguire la guida introduttiva di Archiviazione di Azure per il portale di Azure.If you don't know how to create an Azure storage account with a container, following the [Azure Storage quickstart for Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizzare i dati in sottocartelle (facoltativo)

Per impostazione predefinita, l'API [Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) utilizzerà solo i documenti modulo che si trovano nella radice del contenitore di archiviazione. Tuttavia, è possibile eseguire il training con i dati nelle sottocartelle se li si specifica nella chiamata API. In genere, il corpo della chiamata Train Custom `<SAS URL>` [Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) ha il seguente formato, dove è l'URL della firma di accesso condiviso del contenitore:

```json
{
  "source":"<SAS URL>"
}
```

Se si aggiunge il contenuto seguente al corpo della richiesta, l'API verrà addestrata con i documenti che si trovano nelle sottocartelle. Il `"prefix"` campo è facoltativo e limiterà il set di dati di training ai file i cui percorsi iniziano con la stringa specificata. Così un `"Test"`valore di , per esempio, farà sì che l'API di guardare solo i file o le cartelle che iniziano con la parola "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a creare un set di dati di training, segui una guida introduttiva per eseguire il training di un modello di riconoscimento dei moduli personalizzato e iniziare a usarlo nei moduli.

* [Guida introduttiva: Eseguire il training di un modello ed estrarre i dati del modulo tramite cURL](./quickstarts/curl-train-extract.md)
* [Guida introduttiva: Eseguire il training di un modello ed estrarre i dati del modulo usando l'API REST con PythonQuickstart: Train a model and extract form data using the REST API with Python](./quickstarts/python-train-extract.md)
* [Eseguire il training con le etichette usando l'API REST e Python](./quickstarts/python-labeled-data.md)