---
title: Ottenere la finalità con una chiamata REST in Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838553"
---
## <a name="prerequisites"></a>Prerequisiti

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) o un altro IDE
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

È possibile usare Java per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente. Assicurarsi di aggiungere le librerie Apache al progetto.

1. Copiare il codice seguente per creare una classe in un file denominato `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Sostituire il valore della variabile `YOUR-KEY` con la chiave di LUIS.

3. Sostituire il segnaposto con il percorso del file e compilare il programma Java da una riga di comando: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Sostituire il segnaposto con il percorso del file ed eseguire l'applicazione da una riga di comando: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ![La finestra della console mostra i risultati JSON da Language Understanding](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa guida introduttiva, chiudere il progetto di Visual Studio e rimuovere la directory del progetto dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con Java](../luis-get-started-java-add-utterance.md)