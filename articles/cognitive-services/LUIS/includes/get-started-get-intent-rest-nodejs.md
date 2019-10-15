---
title: Ottenere la finalità con una chiamata REST in Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838512"
---
## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> La soluzione Node.js completa è disponibile nel repository GitHub [**Azure-Samples**](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

È possibile usare Node.js per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente.

1. Copiare il frammento di codice seguente:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Creare il file `.env` con il testo seguente o impostare queste variabili nell'ambiente di sistema:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Impostare la variabile di ambiente `LUIS_ENDPOINT_KEY` sul valore della chiave.

4. Installare le dipendenze eseguendo il comando seguente al prompt della riga di comando: `npm install`.

5. Eseguire il codice con il comando `npm start`. Vengono visualizzati gli stessi valori illustrati in precedenza nella finestra del browser.


## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa guida introduttiva, chiudere il progetto di Visual Studio e rimuovere la directory del progetto dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con Node.js](../luis-get-started-node-add-utterance.md)