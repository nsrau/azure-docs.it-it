---
title: Ottenere il modello con una chiamata REST in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368418"
---
## <a name="prerequisites"></a>Prerequisites

* Azure Language Understanding: chiave di creazione della risorsa di 32 caratteri e URL dell'endpoint di creazione. Usare il [portale di Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) o l'[interfaccia della riga di comando di Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importare l'app [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) dal repository GitHub cognitive-services-language-understanding.
* L'ID applicazione di LUIS per l'app TravelAgent importata. L'ID applicazione viene visualizzato nel dashboard delle applicazioni.
* L'ID della versione all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1".
* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Cambiare il modello a livello di codice

1. Creare un file denominato `model.py`. Aggiungere il codice seguente:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Sostituire i valori che iniziano con `YOUR-` con i propri valori.

    |Informazioni|Scopo|
    |--|--|
    |`YOUR-KEY`|La chiave di creazione di 32 caratteri.|
    |`YOUR-ENDPOINT`| L'endpoint dell'URL di creazione. Ad esempio: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Il nome della risorsa è stato impostato quando è stata creata la risorsa.|
    |`YOUR-APP-ID`| L'ID app di LUIS. |

    Le chiavi e le risorse assegnate sono visibili nel portale LUIS nella sezione Gestisci della pagina **Risorse di Azure**. L'ID app è disponibile nella stessa sezione Gestisci della pagina **Impostazioni applicazione**.

1. Da un prompt dei comandi nella stessa directory in cui è stato creato il file immettere il comando seguente per eseguire il file:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per un'app](../luis-concept-best-practices.md)
