---
title: Analizzare testo in linguaggio naturale in Language Understanding (LUIS) con C# - Servizi cognitivi di Azure | Microsoft Docs
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Con C# inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app pubblica. Nell'endpoint, LUIS applica il modello dell'app pubblica per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: b6ddd48fc6bfa5c099e42f3717a2113f871b4f9a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163261"
---
# <a name="quickstart-analyze-text-using-c"></a>Guida introduttiva: Analizzare il testo con C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* Linguaggio di programmazione C# (incluso in VS Community 2017)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analizzare il testo con il browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-c"></a>Analizzare il testo con C# 

Usare C# per eseguire query sull'[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET dell'endpoint di previsione e ottenere gli stessi risultati visualizzati nella finestra del browser nella sezione precedente. 

1. Creare una nuova applicazione console in Visual Studio. 

    ![Accesso al menu delle impostazioni utente di LUIS](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Nel progetto di Visual Studio, in Esplora soluzioni, selezionare **Aggiungi riferimento**, quindi selezionare **System.Web** nella scheda Assembly.

    ![Accesso al menu delle impostazioni utente di LUIS](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Sovrascrivere Program.cs con il codice seguente:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Sostituire il valore di `YOUR_KEY` con la chiave di LUIS.

5. Compilare ed eseguire l'applicazione console. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ![La finestra della console mostra i risultati JSON da Language Understanding](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa guida introduttiva, chiudere il progetto di Visual Studio e rimuovere la directory del progetto dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con C#](luis-get-started-cs-add-utterance.md)