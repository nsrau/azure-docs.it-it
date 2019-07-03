---
title: Come creare un set di dati di training per un modello personalizzato - riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare che il set di dati di training è ottimizzato per il training di un modello di riconoscimento di Form.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ad9bba53390e3c4262f999ebcc57ab354f1e3d69
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537632"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Creare un set di dati di training per un modello personalizzato

Quando si usa il modello di riconoscimento modulo personalizzato, è fornire i propri dati di training in modo che il modello è possibile eseguire il training per i moduli specifici del settore. È possibile eseguire il training di un modello con cinque moduli compilati o un form vuoto (è necessario includere la parola "vuoto" nel nome del file) e due moduli compilati. Anche se si dispone di sufficienti moduli compilati per il training con, l'aggiunta di un form vuoto nel set di dati di training può migliorare l'accuratezza del modello.

## <a name="training-data-tips"></a>Suggerimenti sui dati di training

È importante usare un set di dati ottimizzato per il training. Usare i suggerimenti seguenti per assicurarsi di ottengono risultati ottimali dal [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operazione:

* Se possibile, usare i documenti PDF basato su testo invece di documenti basati su immagine. Analizzati i PDF vengono gestiti come immagini.
* Se sono stati disponibili, usare un form vuoto e due moduli compilati.
* Per moduli compilati, usare gli esempi che hanno tutti i relativi campi compilati.
* Utilizzare moduli con valori diversi in ogni campo.
* Se le immagini del modulo sono di qualità inferiore, usare un set di dati più grande (10-15 immagini, ad esempio).

## <a name="general-input-requirements"></a>Requisiti generali di input

Assicurarsi che il set di dati di training segue anche i requisiti di input per tutto il contenuto per il riconoscimento modulo. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Caricare i dati di training

Quando è stato raccolto il set di documenti di formato che si userà per il training, è necessario caricarlo in un contenitore di archiviazione blob di Azure. Se non si sa come creare un account di archiviazione di Azure con un contenitore, seguire le [avvio rapido di archiviazione di Azure per il portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizzare i dati presenti nelle sottocartelle (facoltative)

Per impostazione predefinita, il [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API userà solo documenti di formato che si trovano nella radice del contenitore di archiviazione. Tuttavia, è possibile eseguire il training con dati presenti nelle sottocartelle se non è specificata nella chiamata API. In genere, il corpo del [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) chiamata ha il formato seguente, dove `<SAS URL>` è l'URL di firma di accesso condiviso del contenitore:

```json
{
  "source":"<SAS URL>"
}
```

Se si aggiunge il contenuto seguente al corpo della richiesta, l'API esegue il training in documenti contenuti nelle sottocartelle. Il `"prefix"` campo è facoltativo e limiterà il set di dati di training per i file i cui percorsi iniziano con la stringa specificata. Pertanto, un valore di `"Test"`, ad esempio, causa l'API di esaminare solo i file o le cartelle che iniziano con la parola "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come creare un set di dati di training, seguire una Guida introduttiva per il training di un modello di riconoscimento modulo personalizzato e iniziare a usarla nei form.

* [Avvio rapido: Eseguire il training di un modello ed estrarre i dati del modulo usando cURL](./quickstarts/curl-train-extract.md)
* [Avvio rapido: Eseguire il training di un modello ed estrarre i dati del modulo usando l'API REST con Python](./quickstarts/python-train-extract.md)

