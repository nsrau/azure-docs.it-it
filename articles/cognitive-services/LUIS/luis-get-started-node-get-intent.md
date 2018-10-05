---
title: Guida introduttiva per Node.js - stima finalità - LUIS
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Con Node.js inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app pubblica. Nell'endpoint, LUIS applica il modello dell'app pubblica per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 37a94b217e2b9c0d417ad0bdd7678b13e333305d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038919"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Guida introduttiva: ottenere la finalità usando Node.js

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> La soluzione Node.js completa è disponibile nel [**repository Github degli esempi LUIS**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Ottenere la finalità tramite browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il file Node.js.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-node-add-utterance.md)