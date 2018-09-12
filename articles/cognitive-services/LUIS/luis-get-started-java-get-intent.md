---
title: Analizzare testo in linguaggio naturale in Language Understanding (LUIS) con Java - Servizi cognitivi di Azure | Microsoft Docs
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Con Java inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app pubblica. Nell'endpoint, LUIS applica il modello dell'app pubblica per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 4dd5437940994a2f264b5a11baebcd67fdddb43d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163091"
---
# <a name="quickstart-analyze-text-using-java"></a>Guida introduttiva: Analizzare il testo con Java

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prerequisiti

* [JDK SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analizzare il testo con il browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-java"></a>Analizzare il testo con Java 

È possibile usare Java per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente. 

1. Copiare il codice seguente per creare una classe in un file denominato `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Sostituire il valore della variabile `YOUR-KEY` con la chiave di LUIS.

3. Compilare il programma Java con `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Eseguire l'applicazione con `java -cp ":lib/*" LuisGetRequest.java`. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ![La finestra della console mostra i risultati JSON da Language Understanding](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il file Java. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-java-add-utterance.md)