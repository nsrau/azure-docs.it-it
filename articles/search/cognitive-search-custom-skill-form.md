---
title: Competenza personalizzata per il riconoscitore dei moduli
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare una competenza personalizzata per il riconoscimento dei moduli usando C 'NET e Visual Studio.Learn how to create a Form Recognizer custom skill using C'è e Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274575"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Esempio: creare una competenza personalizzata per il riconoscitore di moduli

In questo esempio di competenze di Ricerca cognitiva di Azure verrà illustrato come creare una competenza personalizzata per il riconoscimento dei moduli usando C'è e Visual Studio.In this Azure Cognitive Search skillset, you'll learn how to create a Form Recognizer custom skill using C'è and Visual Studio. Il sistema di riconoscimento dei moduli analizza i documenti ed estrae le coppie chiave/valore e i dati della tabella. Eseguendo il wrapping di Livello di riconoscimento dei moduli [nell'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md), è possibile aggiungere questa funzionalità come passaggio in una pipeline di arricchimento end-to-end. La pipeline può quindi caricare i documenti ed eseguire altre trasformazioni.

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualsiasi edizione).
- Almeno cinque forme dello stesso tipo. È possibile utilizzare dati di esempio forniti con questa guida.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Eseguire il training del modello

Prima di usare questa competenza, dovrai addestrare un modello Riconoscitore moduli con i moduli di input. Seguire la [guida introduttiva di cURL](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) per informazioni su come eseguire il training di un modello. È possibile utilizzare i moduli di esempio forniti in tale guida introduttiva oppure i propri dati. Dopo aver eseguito il training del modello, copiarne il valore ID in una posizione sicura.

## <a name="set-up-the-custom-skill"></a>Impostare le competenze personalizzate

Questa esercitazione usa il progetto AnalyzeForm nel repository GitHub [di Azure Search Power Skills.This](https://github.com/Azure-Samples/azure-search-power-skills) tutorial uses the [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) project in the Azure Search Power Skills GitHub repository. Clonare questo repository nel computer locale e passare a **Vision/AnalyzeForm/** per accedere al progetto. Aprire _quindi AnalyzeForm.csproj_ in Visual Studio.Then open AnalyzeForm.csproj in Visual Studio. Questo progetto crea una risorsa Funzione di Azure che soddisfa [l'interfaccia delle competenze personalizzate](cognitive-search-custom-skill-interface.md) e può essere usata per l'arricchimento di Ricerca cognitiva di Azure.This project creates an Azure Function resource that fulfills the custom skill interface and can be used for Azure Cognitive Search enrichment. Accetta i documenti del modulo come input e restituisce (come testo) le coppie chiave/valore specificate.

Aggiungere innanzitutto variabili di ambiente a livello di progetto. Individuare il progetto **AnalyzeForm** nel riquadro sinistro, fare clic con il pulsante destro del mouse su di esso e scegliere **Proprietà**. Nella finestra **Proprietà** fare clic sulla scheda **Debug** e quindi individuare il campo **Variabili di ambiente.** Fare clic su **Aggiungi** per aggiungere le seguenti variabili:
* `FORMS_RECOGNIZER_ENDPOINT_URL`con il valore impostato sull'URL dell'endpoint.
* `FORMS_RECOGNIZER_API_KEY`con il valore impostato sulla chiave di sottoscrizione.
* `FORMS_RECOGNIZER_MODEL_ID`con il valore impostato sull'ID del modello sottoposto a training.
* `FORMS_RECOGNIZER_RETRY_DELAY`con il valore impostato su 1000. Questo valore è il tempo in millisecondi che il programma attenderà prima di ritentare la query.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`con il valore impostato su 100. Questo valore è il numero di volte in cui il programma eseguirà una query sul servizio durante il tentativo di ottenere una risposta corretta.

Successivamente, aprire _AnalyzeForm.cs_ `fieldMappings` e trovare la variabile, che fa riferimento al file *field-mappings.json.* Questo file (e la variabile che vi fa riferimento) definisce l'elenco di chiavi che si desidera estrarre dai moduli e un'etichetta personalizzata per ogni chiave. Ad esempio, un `{ "Address:", "address" }, { "Invoice For:", "recipient" }` valore indica che lo script `Address:` salverà solo i valori per i campi e `Invoice For:` i campi rilevati e etichetterà tali valori rispettivamente con `"address"` e `"recipient"`, .

Infine, annotare la `contentType` variabile. Questo script esegue il modello di riconoscimento moduli specificato nei documenti remoti `application/json`a cui fa riferimento l'URL, pertanto il tipo di contenuto è . Se si desidera analizzare i file locali includendo i relativi flussi `contentType` di byte nelle richieste HTTP, è necessario modificare il [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) appropriato per il file.

## <a name="test-the-function-from-visual-studio"></a>Testare la funzione da Visual Studio

Dopo aver modificato il progetto, salvarlo e impostare il progetto **AnalyzeForm** come progetto di avvio in Visual Studio (se non è già impostato). Quindi premere **F5** per eseguire la funzione nell'ambiente locale. Usare un servizio REST come [Postman](https://www.postman.com/) per chiamare la funzione.

### <a name="http-request"></a>Richiesta HTTP

Verrà effettuare la seguente richiesta per chiamare la funzione.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo della richiesta

Iniziare con il modello di corpo della richiesta riportato di seguito.

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

Qui dovrai fornire l'URL di un modulo con lo stesso tipo dei moduli con cui ti sei qualificato. A scopo di test, è possibile utilizzare uno dei moduli di formazione. Se è stata seguita la guida introduttiva di cURL, i moduli si troveranno in un account di archiviazione BLOB di Azure.If you followed the cURL quickstart, your forms will located in an Azure blob storage account. Aprire Esplora archivi di Azure, individuare un file di modulo, fare clic con il pulsante destro del mouse su di esso e scegliere Ottieni firma di **accesso condiviso**. La finestra di dialogo successiva fornirà un URL e un token di firma di accesso condiviso. Immettere queste `"formUrl"` stringhe `"formSasToken"` rispettivamente nei campi e del corpo della richiesta.

> [!div class="mx-imgBorder"]
> ![Esplora archivi di Azure; un documento pdf è selezionato](media/cognitive-search-skill-form/form-sas.png)

Se si vuole analizzare un documento remoto che non si trova `"formUrl"` nell'archiviazione `"formSasToken"` BLOB di Azure, incollarne l'URL nel campo e lasciare vuoto il campo.

> [!NOTE]
> Quando la competenza è integrata in un set di competenze, l'URL e il token verranno forniti da Ricerca cognitiva.

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

Quando si è soddisfatti del comportamento della funzione, è possibile pubblicarlo.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Scegliere **Crea nuova** > **pubblicazione**.
1. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account**.
1. Seguire le istruzioni visualizzate sullo schermo. Specificare un nome univoco per il servizio app, la sottoscrizione di Azure, il gruppo di risorse, il piano di hosting e l'account di archiviazione da usare. È possibile creare un nuovo gruppo di risorse, un nuovo piano di hosting e un nuovo account di archiviazione, se non sono già disponibili. Al termine, selezionare **Crea**.
1. Al termine della distribuzione, notare l'URL del sito. Questo URL è l'indirizzo dell'app per le funzioni in Azure.This URL is the address of your function app in Azure. Salvarlo in una posizione temporanea.
1. Nel [portale di Azure](https://portal.azure.com)passare al gruppo di `AnalyzeForm` risorse e cercare la funzione pubblicata. Nella sezione **Gestisci** dovrebbe essere presente un elenco Chiavi host. Copiare la chiave host *predefinita* e salvarla in un percorso temporaneo.

## <a name="connect-to-your-pipeline"></a>Connettersi alla pipeline

Per usare questa competenza in una pipeline di Ricerca cognitiva, è necessario aggiungere una definizione di competenza al set di competenze. Il blocco JSON seguente è una definizione di competenza di esempio (è necessario aggiornare gli input e gli output per riflettere lo scenario specifico e l'ambiente del set di competenze). Sostituire `AzureFunctionEndpointUrl` con l'URL `AzureFunctionDefaultHostKey` della funzione e sostituirlo con la chiave host.

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

In questa guida è stata creata una competenza personalizzata dal servizio Azure Form Recognizer.In this guide, you created a custom skill from the Azure Form Recognizer service. Per altre informazioni sulle competenze personalizzate, vedere le risorse seguenti. 

* [Competenze di alimentazione di Ricerca di Azure: un repository di competenze personalizzateAzure Search Power Skills: a repository of custom skills](https://github.com/*zure-Samples/azure-search-power-skills)
* [Aggiungere una competenza personalizzata a una pipeline di arricchimento AIAdd a custom skill to an AI enrichment pipeline](cognitive-search-custom-skill-interface.md)
* [Definire un insieme di competenze](cognitive-search-defining-skillset.md)
* [Creare un set di competenze (REST)Create a skillset (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Mappare i campi arricchiti](cognitive-search-output-field-mapping.md)
