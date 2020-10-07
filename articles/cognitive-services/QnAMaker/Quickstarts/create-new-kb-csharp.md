---
title: 'Guida introduttiva: Creare una knowledge base in REST, C# - QnA Maker'
description: Questa guida introduttiva basata su C# REST illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 6b64c29e6a26f2f9585e7f2c737f98e4646b1332
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777710"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Guida introduttiva: creare una Knowledge base in QnA Maker usando C# con REST

Questa guida introduttiva illustra come creare e pubblicare a livello di codice una knowledge base QnA Maker di esempio. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../Concepts/knowledge-base.md). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Creare la knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Ottenere i dettagli dell'operazione](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentazione](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  di riferimento [Esempio C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* È necessario avere una [risorsa QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito.

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `qna-maker-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```dotnetcli
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di Program.cs sostituire la singola istruzione using con le righe seguenti per aggiungere le dipendenze necessarie al progetto:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie

Nella parte superiore della classe Program aggiungere le costanti necessarie per accedere a QnA Maker.

Impostare i valori seguenti nelle variabili di ambiente:

* `QNA_MAKER_SUBSCRIPTION_KEY` - La **chiave** è una stringa di 32 caratteri ed è disponibile nella risorsa QnA Maker, nella pagina Avvio rapido del portale di Azure. Non è la stessa chiave dell'endpoint di previsione.
* `QNA_MAKER_ENDPOINT` -L'**endpoint** è l'URL per la creazione, nel formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Non è lo stesso URL usato per eseguire query sull'endpoint di previsione.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>Aggiungere la definizione di knowledge base

Dopo le costanti, aggiungere la definizione di knowledge base seguente:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>Aggiungere strutture e funzioni di supporto
Aggiungere il blocco di codice seguente all'interno della classe Program:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>Aggiungere una richiesta POST per creare la knowledge base

Nel codice seguente viene effettuata una richiesta HTTPS all'API QnA Maker per creare una knowledge base e viene ricevuta la risposta:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

Questa chiamata API restituisce una risposta JSON che include l'ID operazione. Usare l'ID operazione per determinare se la knowledge base è stata creata.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Aggiungere la richiesta GET per determinare lo stato della creazione

Verificare lo stato dell'operazione.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

Questa chiamata API restituisce una risposta JSON che include lo stato dell'operazione:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Ripetere la chiamata fino a quando l'esito non è positivo o negativo:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Aggiungere il metodo CreateKB

Il metodo seguente consente di creare la knowledge base e ripetere i controlli sullo stato.  L' _create_ **ID dell'operazione** di creazione viene restituito nella **posizione**del campo dell'intestazione Post-risposta, quindi viene usato come parte della route nella richiesta GET. Dal momento che la creazione della knowledge base può richiedere tempo, è necessario ripetere le chiamate per controllare lo stato fino a quando questo indica un esito positivo o negativo dell'operazione. Quando l'operazione riesce, viene restituito l'ID della knowledge base in **resourceLocation**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>Aggiungere il metodo CreateKB a Main

Modificare il metodo Main in modo che chiami il metodo CreateKB:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma. La richiesta per creare la knowledge base verrà inviata automaticamente all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker.


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
