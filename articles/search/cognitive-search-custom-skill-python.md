---
title: Esempio di abilità personalizzate (Python)
titleSuffix: Azure Cognitive Search
description: For Python developers, learn the tools and techniques for building a custom skill using Azure Functions and Visual Studio. Le competenze personalizzate contengono modelli o logiche definiti dall'utente che è possibile aggiungere a una pipeline di indicizzazione con arricchimento dell'iatura in Ricerca cognitiva di Azure.Custom skills contain user-defined models or logic that you can add to an AI-enriched indexing pipeline in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210466"
---
# <a name="example-create-a-custom-skill-using-python"></a>Esempio: Creare una competenza personalizzata usando PythonExample: Create a custom skill using Python

In questo esempio di competenze di Ricerca cognitiva di Azure si apprenderà come creare una competenza personalizzata dell'API Web usando Python e il codice di Visual Studio.In this Azure Cognitive Search skillset, you will learn how to create a web API custom skill using Python and Visual Studio Code. Nell'esempio viene utilizzata [una funzione](https://azure.microsoft.com/services/functions/) di Azure che implementa l'interfaccia [delle competenze personalizzata.](cognitive-search-custom-skill-interface.md)

L'abilità personalizzata è semplice da progettazione (concatena due stringhe) in modo che è possibile concentrarsi sugli strumenti e le tecnologie utilizzati per lo sviluppo di competenze personalizzate in Python. Una volta che si riesce con una semplice abilità, è possibile diramarsi con scenari più complessi.

## <a name="prerequisites"></a>Prerequisiti

+ Esaminare [l'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md) per un'introduzione all'interfaccia di input/output che deve essere implementata da una competenza personalizzata.

+ Configurare l'ambiente. Abbiamo seguito [questa esercitazione end-to-end](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) per configurare la funzione di Azure senza server usando le estensioni di Visual Studio Code e Python.We followed this tutorial end-to-end to set up serverless Azure Function using Visual Studio Code and Python extensions. L'esercitazione consente l'installazione dei seguenti strumenti e componenti: 

  + [Pitone 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Strumenti di base di Funzioni di AzureAzure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Estensione Funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Questo esempio usa una funzione di Azure per illustrare il concetto di hosting di un'API Web, ma sono possibili altri approcci. Purché si rispettino i [requisiti di interfaccia per una competenza cognitiva](cognitive-search-custom-skill-interface.md), l'approccio adottato è irrilevante. Funzioni di Azure, tuttavia, rende la creazione di una competenza personalizzata estremamente semplice.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

Il modello di progetto Funzioni di Azure in Visual Studio Code crea un progetto che può essere pubblicato in un'app per le funzioni in Azure. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per la gestione, la distribuzione e la condivisione delle risorse.

1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Create new project...`.

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. Per questo motivo, non selezionare una cartella di progetto che fa parte di un'area di lavoro.

1. Selezionare una lingua per il progetto dell'app per le funzioni. Per questa esercitazione, selezionare **Python**.
1. Selezionare la versione Python (la versione 3.7.5 è supportata da Funzioni di Azure)Select the Python version, (version 3.7.5 is supported by Azure Functions)
1. Selezionare un modello per la prima funzione del progetto. Selezionare **Trigger HTTP** per creare una funzione attivata HTTP nella nuova app per le funzioni.
1. Specificare un nome di funzione. In questo caso, usiamo **Concatenator** 
1. Selezionare **Funzione** come livello di autorizzazione. Ciò significa che forniremo un [tasto funzione](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) per chiamare l'endpoint HTTP della funzione. 
1. Selezionare la modalità di apertura del progetto. Per questo passaggio, selezionare **Aggiungi all'area di lavoro** per creare l'app per le funzioni nell'area di lavoro corrente.

Visual Studio Code crea il progetto di app per le funzioni in una nuova area di lavoro. Questo progetto contiene i file di configurazione [host.json](../azure-functions/functions-host-json.md) e [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), oltre ad altri file di progetto specifici per il linguaggio. 

Viene inoltre creata una nuova funzione attivata HTTP nella cartella **Concatenator** del progetto di app per le funzioni. Al suo interno ci sarà\_\_un file chiamato " init__.py", con questo contenuto:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

È ora possibile modificare il codice per seguire [l'interfaccia di abilità personalizzata](cognitive-search-custom-skill-interface.md). Modificare il codice con il contenuto seguente:Modify the code with the following content:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Il **metodo transform_value** esegue un'operazione su un singolo record. È possibile modificare il metodo in base alle proprie esigenze specifiche. Ricordarsi di eseguire qualsiasi convalida dell'input necessaria e di restituire eventuali errori e avvisi generati se non è stato possibile completare l'operazione per il record.

### <a name="debug-your-code-locally"></a>Eseguire il debug del codice in localeDebug your code locally

Visual Studio Code semplifica il debug del codice. Premere 'F5' o accedere al menu **Debug** e selezionare **Avvia debug**.

È possibile impostare qualsiasi punto di interruzione sul codice premendo 'F9' sulla riga di interesse.

Una volta avviato il debug, la funzione verrà eseguita in locale. È possibile utilizzare uno strumento come Postman o Fiddler per inviare la richiesta a localhost. Prendere nota della posizione dell'endpoint locale nella finestra Terminale. 

## <a name="publish-your-function"></a>Pubblica la tua funzione

Quando si è soddisfatti del comportamento della funzione, è possibile pubblicarlo.

1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nella tavolozza dei comandi cercare e selezionare **Distribuisci in un'app per**le funzioni... . 

1. Selezionare la sottoscrizione di Azure in cui si vuole distribuire l'applicazione.

1. Selezionare : crea una nuova app per le funzioni **in AzureSelect - Create New Function App in Azure**

1. Immettere un nome univoco globale per l'app per le funzioni.

1. Selezionare la versione Python (Python 3.7.x funziona per questa funzione).

1. Selezionare una posizione per la nuova risorsa (ad esempio, Stati Uniti occidentali 2).

A questo punto, le risorse necessarie verranno create nella sottoscrizione di Azure per ospitare la nuova funzione di Azure in Azure.At this point, the necessary resources will be created in your Azure subscription to host the new Azure Function on Azure. Attendere il completamento della distribuzione. La finestra di output mostrerà lo stato del processo di distribuzione.

1. Nel [portale di Azure](https://portal.azure.com)passare a **Tutte le risorse** e cercare la funzione pubblicata in base al nome. Se è stato denominato **Concatenatore**, selezionare la risorsa.

1. Fare clic sul pulsante **URL</> Get Function.** In questo modo sarà possibile copiare l'URL per chiamare la funzione.

## <a name="test-the-function-in-azure"></a>Testare la funzione in Azure

Ora che si dispone della chiave host predefinita, testare la funzione come segue:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Request Body
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Questo esempio dovrebbe produrre lo stesso risultato visto in precedenza durante l'esecuzione della funzione nell'ambiente locale.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline

A questo punto è possibile aggiungere la competenza personalizzata al proprio set di competenze. L'esempio seguente mostra come chiamare la competenza per concatenare il titolo e l'autore del documento in un singolo campo che chiamiamo merged_title_author. Sostituire `[your-function-url-here]` con l'URL della nuova funzione di Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! Hai creato la tua prima abilità personalizzata. Ora è possibile seguire lo stesso schema per aggiungere funzionalità personalizzate. Per ulteriori informazioni, fare clic sui collegamenti seguenti.

+ [Power Skills: un archivio di competenze personalizzate](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Aggiungere una competenza personalizzata a una pipeline di arricchimento AIAdd a custom skill to an AI enrichment pipeline](cognitive-search-custom-skill-interface.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)
