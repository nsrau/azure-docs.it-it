---
title: Analizzare testo in linguaggio naturale in Language Understanding (LUIS) con JavaScript - Servizi cognitivi di Azure | Microsoft Docs
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Con JavaScript inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app pubblica. Nell'endpoint, LUIS applica il modello dell'app pubblica per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d787f744ff0fe7315553e9dd6f4465122f7e06b2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159708"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Guida introduttiva: Analizzare il testo con JavaScript

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analizzare il testo con il browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Analizzare il testo con JavaScript 

È possibile usare JavaScript per accedere agli stessi risultati visualizzati nella finestra del browser nel passaggio precedente. 

1. Copiare il codice seguente e salvarlo in un file HTML:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Aprire il file in un browser. Immettere la chiave dell'endpoint LUIS nel modulo e selezionare **Submit** (Invia).

    ![Esempio di codice HTML visualizzato in un browser con i risultati di LUIS per l'app Home Automation](./media/luis-get-started-js-get-intent/html-results.png)

    I risultati vengono visualizzati sotto il modulo. 

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il file JavaScript.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-javascript-add-utterance.md)
