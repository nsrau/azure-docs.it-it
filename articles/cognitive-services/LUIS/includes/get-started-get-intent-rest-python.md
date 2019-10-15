---
title: Ottenere la finalità con una chiamata REST in Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838563"
---
## <a name="prerequisites"></a>Prerequisiti

* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>Ottenere la finalità a livello di codice

È possibile usare Python per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente.

1. Copiare uno dei frammenti di codice seguenti in un file denominato `quickstart-call-endpoint.py`:

    #### <a name="python-27tabp2"></a>[Python 2.7](#tab/P2)

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3.6](#tab/P3)

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. Sostituire il valore del campo `Ocp-Apim-Subscription-Key` con la chiave dell'endpoint LUIS.

1. Installare le dipendenze con il comando `pip install requests`.

1. Eseguire lo script con il comando `python ./quickstart-call-endpoint.py`. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa guida introduttiva, chiudere il progetto di Visual Studio e rimuovere la directory del progetto dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con Python](../luis-get-started-python-add-utterance.md)