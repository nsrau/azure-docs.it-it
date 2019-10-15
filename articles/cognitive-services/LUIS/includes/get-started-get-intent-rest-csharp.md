---
title: Ottenere la finalità con una chiamata REST in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838513"
---
## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* Linguaggio di programmazione C# (incluso in VS Community 2017)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

Usare C# per eseguire query sull'[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET dell'endpoint di previsione e ottenere gli stessi risultati visualizzati nella finestra del browser nella sezione precedente. 

1. Creare una nuova applicazione console in Visual Studio. 

    ![Creare una nuova applicazione console in Visual Studio](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Nel progetto di Visual Studio, in Esplora soluzioni, selezionare **Aggiungi riferimento**, quindi selezionare **System.Web** nella scheda Assembly.

    ![Selezionare Aggiungi riferimento, quindi selezionare System.Web nella scheda assembly Assemblies](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Sovrascrivere Program.cs con il codice seguente:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Sostituire il valore di `YOUR_KEY` con la chiave di LUIS.

5. Compilare ed eseguire l'applicazione console. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ![La finestra della console mostra i risultati JSON da Language Understanding](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa guida introduttiva, chiudere il progetto di Visual Studio e rimuovere la directory del progetto dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con C#](../luis-get-started-cs-add-utterance.md)