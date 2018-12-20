---
title: Ottenere la finalità - Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: In questa guida introduttiva di Java si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c55721e803b85c536067a90019f75dd5aa0f7845
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085349"
---
# <a name="quickstart-get-intent-using-java"></a>Guida introduttiva: Ottenere la finalità con Java

In questa guida introduttiva si passano le espressioni a un endpoint LUIS per ottenere la restituzione di finalità ed entità.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prerequisiti

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Ottenere la finalità tramite browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice 

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