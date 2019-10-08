---
title: 'Guida introduttiva: Libreria client di QnA Maker per .NET'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client di QnA Maker per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 31bd85ca9b106758dbb7bfd399b7a493ea7fea9f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803095"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Guida introduttiva: Libreria client di QnA Maker per .NET

Introduzione alla libreria client di QnA Maker per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti. 

Usare la libreria client di QnA Maker per .NET per:

* Creare una knowledge base 
* Gestire una knowledge base
* Pubblicare una knowledge base
* Generare una risposta dalla knowledge base

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Esempi C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-qna-maker-azure-resource"></a>Creare una risorsa di Azure per QnA Maker

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per QnA Maker usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. 

Dopo aver ottenuto una chiave e un endpoint per la risorsa, [creare la variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `QNAMAKER_SUBSCRIPTION_KEY` per la chiave. Il nome della risorsa viene usato nell'URL dell'endpoint.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito. 

Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando dotnet `new` per creare una nuova app console con il nome `qna-maker-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
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

### <a name="install-the-sdk"></a>Installare l'SDK

Nella directory dell'applicazione installare la libreria client di QnA Maker per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.


## <a name="object-model"></a>Modello a oggetti

Il client di QnA Maker è un oggetto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare la proprietà [Knowledgebase](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) per creare, gestire e pubblicare la knowledge base. 

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) con l'ID operazione per determinare lo [stato della richiesta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di QnA Maker per .NET:

* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file **Program.cs** nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Nel metodo **main** creare una variabile per la chiave di Azure della risorsa, estratta da una variabile di ambiente denominata `QNAMAKER_SUBSCRIPTION_KEY`. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedere alla variabile sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui è in esecuzione. I metodi verranno creati in un secondo momento.

Creare quindi un oggetto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) con la propria chiave e usarlo con l'endpoint per creare un oggetto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet).

Impostare la variabile `<your-custom-domain>` di **Endpoint** sul nome del dominio personalizzato. La località è riportata nella pagina **Panoramica** per la risorsa QnA Maker nel portale di Azure.

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticare il runtime per la generazione di una risposta

Nel metodo **main** creare una variabile per l'autenticazione della risorsa, estratta dalle variabili di ambiente denominate `QNAMAKER_ENDPOINT_HOSTNAME` e `QNAMAKER_ENDPOINT_KEY`. Quando si pubblica la knowledge base, vengono restituiti questi valori. Dopo la pubblicazione, è possibile trovare queste impostazioni nella pagina di **impostazioni** del portale di QnA Maker. 

Creare un oggetto [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) per eseguire una query sulla knowledge base e generare una risposta o eseguire il training da apprendimento attivo.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
* Per i **file**, usare l' oggetto [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet). 
* Per gli **URL**, usare un elenco di stringhe.

Chiamare il metodo [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet), quindi passare l'ID operazione restituito al metodo [MonitorOperation](#get-status-of-an-operation) per eseguire il polling dello stato. 

La riga finale del codice seguente restituisce l'ID knowledge base dalla risposta di MonitorOperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Per creare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente. 

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contenente gli oggetti DTO per l'[aggiunta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), l'[aggiornamento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) e l'[eliminazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) al metodo [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Usare il metodo [MonitorOperation](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Per aggiornare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente. 

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) per scaricare il database come un elenco di [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker perché il risultato di questo metodo non è un file TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base con il metodo [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Questo metodo accetta l'ID knowledge base e [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Accedere ad altre proprietà di QueryDTO, ad esempio [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) da usare nel chatbot. 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base usando il metodo [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) con un parametro dell'ID knowledge base. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet). Usare il relativo [ID operazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) per eseguire il polling (con logica di ripetizione dei tentativi) e determinare lo stato del metodo originale. 

Il _ciclo_ e _Task.Delay_ nel blocco di codice seguente vengono usati per simulare la logica di ripetizione dei tentativi. Dovranno essere sostituiti con la propria logica di ripetizione dei tentativi. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando dotnet `run` dalla directory dell'applicazione.

Tutti i frammenti di codice in questo articolo sono [disponibili](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) e possono essere eseguiti come singolo file.

```console
dotnet run
```

Il [codice sorgente per questo argomento di avvio rapido](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) è disponibile nel repository GitHub di esempi di QnA Maker in C#.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Esercitazione: Creare una knowledge base e rispondere](../tutorials/create-publish-query-in-portal.md)

* [Informazioni sull'API QnA Maker](../Overview/overview.md)
* [Modificare una knowledge base](../how-to/edit-knowledge-base.md)
* [Ottenere l'analisi di utilizzo](../how-to/get-analytics-knowledge-base.md)