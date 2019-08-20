---
title: 'Guida introduttiva: Libreria client di QnA Maker per Node.js'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client di QnA Maker per Node.js. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: dd2bb5fc4d6ecf2cac0fa629c59abb0e017d43b0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857273"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Guida introduttiva: Libreria client di QnA Maker per Node.js

Introduzione alla libreria client di QnA Maker per Node.js. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti. 

Usare la libreria client di QnA Maker per Node.js per eseguire queste operazioni:

* Creare una knowledge base 
* Gestire una knowledge base
* Pubblicare una knowledge base

[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/qnamaker?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Esempi Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-qna-maker-azure-resource"></a>Creare una risorsa di Azure per QnA Maker

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per QnA Maker usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. 

Dopo aver ottenuto una chiave dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la risorsa, denominate `QNAMAKER_SUBSCRIPTION_KEY` e `QNAMAKER_HOST`. Usare i valori di chiave e host disponibili nelle pagine **Chiavi** e **Panoramica** della risorsa nel portale di Azure.

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init -y` per creare un'applicazione Node con un file `package.json`. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM `ms-rest-azure` e `azure-cognitiveservices-qnamaker`:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.


## <a name="object-model"></a>Modello a oggetti

Il client di QnA Maker è un oggetto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) che esegue l'autenticazione in Azure con ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare la proprietà [Knowledgebase](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) per creare, gestire e pubblicare la knowledge base. 

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [client.Operations.GetDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) con l'ID operazione per determinare lo [stato della richiesta](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest). 

 
## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di QnA Maker per Node.js:

* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Creare un file denominato `index.js`. Aggiungere la libreria di QnA Maker e la libreria REST di Azure al file.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Creare le variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.


|Variabile di ambiente|Variabile Node.js|Esempio|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|GUID di 32 caratteri|
|`QNAMAKER_HOST`|`endpoint`|`https://westus.api.cognitive.microsoft.com`|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare quindi un oggetto ServiceClientCredentials con la chiave e usarlo con l'endpoint per creare un oggetto [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-). Usare l'oggetto client per ottenere un oggetto [knowledge base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest).


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
* Per i **file**, usare l' oggetto [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest). 
* Per gli **URL**, usare un elenco di stringhe.

Chiamare il metodo [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) e quindi passare l'ID operazione restituito al metodo [Operations.getDetails](#get-status-of-an-operation) per eseguire il polling dello stato. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]


## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un oggetto [UpdatekbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contenente gli oggetti DTO [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) e l'[delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) al metodo [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). Usare il metodo [Operation.getDetail](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base tramite il metodo [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base tramite il metodo [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) con un parametro dell'ID knowledge base. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest). Usare il relativo [ID operazione](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) per eseguire il polling (con logica di ripetizione dei tentativi) e determinare lo stato del metodo originale. 

La chiamata _setTimeout_ nel blocco di codice seguente viene usata per simulare il codice asincrono. Sostituire questo codice con logica di ripetizione dei tentativi. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node index.js` dalla directory dell'applicazione.

```console
node index.js
```

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
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js).