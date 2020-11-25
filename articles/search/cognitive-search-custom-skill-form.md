---
title: Competenze personalizzate del riconoscimento moduli (C#)
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare un'abilità personalizzata di riconoscimento del modulo usando C# e Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 58f1c2621165a7074c04752832c6560b2fd3e423
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011966"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Esempio: creare un'abilità personalizzata di riconoscimento del modulo

In questo esempio di competenze di Azure ricerca cognitiva si apprenderà come creare un'abilità personalizzata di riconoscimento del modulo usando C# e Visual Studio. Il riconoscimento del modulo analizza i documenti ed estrae le coppie chiave/valore e i dati della tabella. Eseguendo il wrapping del riconoscimento form nell' [interfaccia skill personalizzata](cognitive-search-custom-skill-interface.md), è possibile aggiungere questa funzionalità come passaggio in una pipeline di arricchimento end-to-end. La pipeline può quindi caricare i documenti ed eseguire altre trasformazioni.

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualsiasi edizione).
- Almeno cinque forme dello stesso tipo. È possibile utilizzare i dati di esempio forniti con questa guida.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Eseguire il training del modello

È necessario eseguire il training di un modello di riconoscimento form con i moduli di input prima di usare questa competenza. Seguire la [Guida introduttiva di curl](../cognitive-services/form-recognizer/quickstarts/curl-train-extract.md) per informazioni su come eseguire il training di un modello. È possibile usare i moduli di esempio disponibili in questa Guida introduttiva oppure è possibile usare i propri dati. Una volta eseguito il training del modello, copiarne il valore ID in una posizione sicura.

## <a name="set-up-the-custom-skill"></a>Configurare l'abilità personalizzata

Questa esercitazione usa il progetto [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) nel repository GitHub [Power Skills di ricerca di Azure](https://github.com/Azure-Samples/azure-search-power-skills) . Clonare il repository nel computer locale e passare a **Vision/AnalyzeForm/** per accedere al progetto. Aprire quindi _AnalyzeForm. csproj_ in Visual Studio. Questo progetto crea una risorsa di funzione di Azure che soddisfa l' [interfaccia skill personalizzata](cognitive-search-custom-skill-interface.md) e può essere usata per l'arricchimento ricerca cognitiva di Azure. Accetta documenti form come input e restituisce come testo le coppie chiave/valore specificate.

In primo luogo, aggiungere variabili di ambiente a livello di progetto. Individuare il progetto **AnalyzeForm** nel riquadro sinistro, fare clic con il pulsante destro del mouse su di esso e scegliere **Proprietà**. Nella finestra **Proprietà** fare clic sulla scheda **debug** , quindi individuare il campo **variabili di ambiente** . Fare clic su **Aggiungi** per aggiungere le variabili seguenti:
* `FORMS_RECOGNIZER_ENDPOINT_URL` con il valore impostato sull'URL dell'endpoint.
* `FORMS_RECOGNIZER_API_KEY` con il valore impostato sulla chiave di sottoscrizione.
* `FORMS_RECOGNIZER_MODEL_ID` con il valore impostato sull'ID del modello sottoposto a training.
* `FORMS_RECOGNIZER_RETRY_DELAY` con il valore impostato su 1000. Questo valore è il tempo in millisecondi di attesa del programma prima di ritentare la query.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` con il valore impostato su 100. Questo valore indica il numero di volte in cui il programma eseguirà una query sul servizio durante il tentativo di ottenere una risposta corretta.

Successivamente, aprire _AnalyzeForm.cs_ e trovare la `fieldMappings` variabile, che fa riferimento all' *field-mappings.jssu* file. Questo file (e la variabile che vi fa riferimento) definisce l'elenco di chiavi che si desidera estrarre dai form e un'etichetta personalizzata per ciascuna chiave. Il valore indica, ad esempio, che lo `{ "Address:", "address" }, { "Invoice For:", "recipient" }` script salverà solo i valori per i campi rilevati `Address:` e `Invoice For:` che verranno etichettati `"address"` rispettivamente con e `"recipient"` .

Infine, prendere nota della `contentType` variabile. Questo script esegue il modello di riconoscimento form specificato nei documenti remoti a cui fa riferimento l'URL, quindi il tipo di contenuto è `application/json` . Se si desidera analizzare i file locali includendo i relativi flussi di byte nelle richieste HTTP, è necessario modificare il `contentType` [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) appropriato per il file.

## <a name="test-the-function-from-visual-studio"></a>Testare la funzione da Visual Studio

Dopo aver modificato il progetto, salvarlo e impostare il progetto **AnalyzeForm** come progetto di avvio in Visual Studio (se non è già impostato). Premere quindi **F5** per eseguire la funzione nell'ambiente locale. Per chiamare la funzione, usare un servizio REST, ad esempio [postazione](https://www.postman.com/) .

### <a name="http-request"></a>Richiesta HTTP

Si effettuerà la richiesta seguente per chiamare la funzione.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo della richiesta

Iniziare con il modello del corpo della richiesta riportato di seguito.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Qui è necessario specificare l'URL di un modulo con lo stesso tipo dei moduli con cui si è eseguito il training. A scopo di test, è possibile usare uno dei moduli di training. Se è stata seguita la Guida introduttiva di cURL, i moduli saranno posizionati in un account di archiviazione BLOB di Azure. Aprire Azure Storage Explorer, individuare un file di modulo, fare clic con il pulsante destro del mouse su di esso e scegliere **Ottieni firma di accesso condiviso**. La finestra di dialogo successiva fornirà un URL e un token di firma di accesso condiviso. Immettere queste stringhe rispettivamente nei `"formUrl"` `"formSasToken"` campi e del corpo della richiesta.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer; è selezionato un documento PDF](media/cognitive-search-skill-form/form-sas.png)

Se si vuole analizzare un documento remoto che non si trovi nell'archivio BLOB di Azure, incollare il relativo URL nel `"formUrl"` campo e lasciare `"formSasToken"` vuoto il campo.

> [!NOTE]
> Quando l'abilità è integrata in un skillt, l'URL e il token verranno forniti da ricerca cognitiva.

### <a name="response"></a>Risposta

La risposta dovrebbe essere simile all'esempio seguente:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Pubblicare la funzione in Azure

Quando si è soddisfatti del comportamento della funzione, è possibile pubblicarla.

1. Nel **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **pubblica**. Scegliere **Crea nuova**  >  **pubblicazione**.
1. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account**.
1. Seguire le istruzioni visualizzate sullo schermo. Specificare un nome univoco per il servizio app, la sottoscrizione di Azure, il gruppo di risorse, il piano di hosting e l'account di archiviazione che si vuole usare. È possibile creare un nuovo gruppo di risorse, un nuovo piano di hosting e un nuovo account di archiviazione, se non sono già presenti. Al termine, selezionare **Crea**.
1. Al termine della distribuzione, si noti l'URL del sito. Questo URL è l'indirizzo dell'app per le funzioni in Azure. Salvarlo in un percorso temporaneo.
1. Nella [portale di Azure](https://portal.azure.com)passare al gruppo di risorse e cercare la `AnalyzeForm` funzione pubblicata. Nella sezione **Gestisci** dovrebbe essere presente un elenco Chiavi host. Copiare la chiave host *predefinita* e salvarla in un percorso temporaneo.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline

Per usare questa competenza in una pipeline di ricerca cognitiva, è necessario aggiungere una definizione di competenze al proprio esperto. Il blocco JSON seguente è una definizione di competenze di esempio (è necessario aggiornare gli input e gli output in modo da riflettere lo scenario e l'ambiente di competenze specifici). Sostituire `AzureFunctionEndpointUrl` con l'URL della funzione e sostituire `AzureFunctionDefaultHostKey` con la chiave host.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stata creata un'abilità personalizzata dal servizio di riconoscimento dei moduli di Azure. Per ulteriori informazioni sulle competenze personalizzate, vedere le risorse seguenti. 

* [Azure Search Skills Power Skills: repository di competenze personalizzate](https://github.com/Azure-Samples/azure-search-power-skills)
* [Aggiungere un'abilità personalizzata a una pipeline di arricchimento di intelligenza artificiale](cognitive-search-custom-skill-interface.md)
* [Definire un insieme di competenze](cognitive-search-defining-skillset.md)
* [Creare un oggetto di competenze (REST)](/rest/api/searchservice/create-skillset)
* [Mappare campi arricchiti](cognitive-search-output-field-mapping.md)