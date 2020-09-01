---
title: 'Guida introduttiva: pubblicare la Knowledge base, REST, C#-QnA Maker'
description: In questa guida di avvio rapido basata su REST e C# viene pubblicata una knowledge base e creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: df8ca85ff0d59817a939936aa6738910bd354996
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267021"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Guida introduttiva: Pubblicare una knowledge base in QnA Maker con C#

Questa guida introduttiva basata su REST illustra la procedura di pubblicazione della knowledge base (KB) a livello di codice. Con la pubblicazione viene eseguito il push dell'ultima versione della knowledge base in un indice dedicato di Ricerca cognitiva di Azure e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Pubblicazione): con questa API non sono richieste informazioni nel corpo della richiesta.

## <a name="prerequisites"></a>Prerequisiti

* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.
* ID della knowledge base (KB) di QnA Maker trovato nell'URL nel parametro della stringa di query `kbid` come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-csharp.md).

> [!NOTE]
> I file della soluzione completa sono disponibili nel [repository GitHub **Azure-Samples/cognitive-Services-qnamaker-CSharp** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Creare il progetto per la knowledge base

1. Aprire Visual Studio 2019 Community Edition.
1. Creare un nuovo progetto **App console (.NET Core)** e assegnare il nome `QnaMakerQuickstart` al progetto. Accettare le impostazioni predefinite per le altre impostazioni.

## <a name="add-required-dependencies"></a>Aggiungere le dipendenze obbligatorie

All'inizio di Program.cs sostituire la singola istruzione using con le righe seguenti per aggiungere le dipendenze necessarie al progetto:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>Aggiungere le costanti obbligatorie

Nella classe **Program** aggiungere le costanti necessarie per accedere a QnA Maker.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Aggiungere il metodo Main per pubblicare la knowledge base

Dopo le costanti richieste, aggiungere il codice seguente, che effettua una richiesta HTTPS all'API di QnA Maker per pubblicare una knowledge base e riceve la risposta:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

In caso di pubblicazione riuscita la chiamata API restituisce uno stato 204 senza alcun contenuto nel corpo della risposta.

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma. La richiesta per pubblicare la knowledge base verrà inviata automaticamente all'API QnA Maker e la risposta verrà stampata nella finestra della console.

Dopo aver pubblicato la knowledge base, è possibile eseguire query su di essa dall'endpoint con un'applicazione client o un chatbot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo la pubblicazione della knowledge base, è necessario l'[URL dell'endpoint per generare una risposta](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
