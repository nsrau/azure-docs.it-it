---
title: Gestione degli errori e delle eccezioni per App per la logica in Azure | Microsoft Docs
description: Modelli per la gestione degli errori e delle eccezioni in App per la logica.
services: logic-apps
documentationcenter: 
author: dereklee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 91819d0fba30dd2ada981435fa13b8ae0a7fcc45
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Gestire gli errori e le eccezioni in App per la logica

Nelle architetture di integrazione una delle difficoltà consiste nel garantire che i tempi di inattività o i problemi dei sistemi dipendenti vengano gestiti in modo appropriato. Per creare solide integrazioni resilienti in caso di problemi ed errori, App per la logica offre un'esperienza all'avanguardia per la gestione degli errori e delle eccezioni. 

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Per la gestione degli errori e delle eccezioni più semplice, è possibile usare i criteri di ripetizione. Tali criteri definiscono se e come l'azione ripete la richiesta in caso di timeout o esito negativo, con restituzione di una risposta 429 o 5xx, della richiesta iniziale. 

Esistono quattro tipi di criteri di ripetizione: predefinito, nessuno, a intervallo fisso e a intervallo esponenziale. Se la definizione del flusso di lavoro non ha criteri di ripetizione, vengono invece usati i criteri predefiniti specificati dal servizio.

Per configurare i criteri di ripetizione, se applicabili, aprire Progettazione app per la logica per l'app per la logica e passare a **Impostazioni** per un'azione specifica nell'app per la logica. È anche possibile definire i criteri di ripetizione nella sezione **inputs** di un'azione o trigger specifico, se ripetibile, nella definizione del flusso di lavoro. Di seguito è illustrata la sintassi generale:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Per altre informazioni sulla sintassi e sulla sezione **inputs**, vedere la [sezione relativa ai criteri di ripetizione in Trigger e azioni dei flussi di lavoro][retryPolicyMSDN]. Per informazioni sulle restrizioni dei criteri di ripetizione, vedere [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Predefinito

Quando non si definiscono criteri di ripetizione nella sezione **retryPolicy**, l'app per la logica usa il criterio predefinito, costituito da [criteri con un intervallo esponenziale ](#exponential-interval) che invia fino a quattro tentativi a intervalli con crescita esponenziale scalati di 7,5 secondi. L'intervallo è compreso tra 5 e 45 secondi. Tali criteri equivalgono ai criteri nella definizione del flusso di lavoro HTTP di questo esempio:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Nessuna

Se si imposta **retryPolicy** su **none**, i criteri non ripeteranno le richieste non riuscite.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE | 
| ------------ | -------- | ---- | ----------- | 
| type | Sì | string | **nessuna** | 
||||| 

### <a name="fixed-interval"></a>Intervallo fisso

Se si imposta **retryPolicy** su **fixed**, i criteri ripeteranno una richiesta non riuscita attendendo l'intervallo di tempo specificato prima di inviare la richiesta successiva.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **fixed** |
| count | Sì | Integer | Numero di tentativi di ripetizione, che deve essere compresi tra 1 e 90 | 
| interval | Sì | string | Intervallo di ripetizione dei tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), che deve essere compreso tra PT5S e PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervallo esponenziale

Se si imposta **retryPolicy** su **exponential**, i criteri ripeteranno una richiesta non riuscita dopo un intervallo di tempo casuale di un intervallo a crescita esponenziale. I criteri garantiscono anche l'invio di ogni tentativo a un intervallo casuale maggiore di **minimumInterval** e minore di **maximumInterval**. Nei criteri esponenziali **count** e **interval** sono obbligatori, mentre i valori per **minimumInterval** e **maximumInterval** sono facoltativi. Per eseguire l'override rispettivamente dei valori predefiniti PT5S e PT1D, è possibile aggiungere questi valori.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **exponential** |
| count | Sì | Integer | Numero di tentativi di ripetizione, che deve essere compresi tra 1 e 90  |
| interval | Sì | string | Intervallo di ripetizione dei tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), che deve essere compreso tra PT5S e PT1D. |
| minimumInterval | No  | string | Intervallo minimo di ripetizione dei tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), che deve essere compreso tra PT5S e **interval** |
| maximumInterval | No  | string | Intervallo minimo di ripetizione dei tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), che deve essere compreso tra **interval** e PT1D | 
||||| 

Questa tabella illustra come una variabile casuale uniforme nell'intervallo indicato viene generata per ogni nuovo tentativo fino al valore di **count** incluso:

**Intervallo variabile casuale**

| Numero di ripetizioni | Intervallo minimo | Intervallo massimo |
| ------------ | ---------------- | ---------------- |
| 1 | Massimo (0, **minimumInterval**) | Minimo (intervallo, **maximumInterval**) |
| 2 | Massimo (intervallo, **minimumInterval**) | Minimo (2 * intervallo, **maximumInterval**) |
| 3 | Massimo (2 * intervallo, **minimumInterval**) | Minimo (4 * intervallo, **maximumInterval**) |
| 4 | Massimo (4*intervallo, **minimumInterval**) | Minimo (8 * intervallo, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Rilevare e gestire gli errori con la proprietà RunAfter

Ogni azione dell'app per la logica dichiara le azioni che devono terminare prima dell'avvio dell'azione, analogamente a come si specifica l'ordine dei passaggi nel flusso di lavoro. Nella definizione di un'azione la proprietà **runAfter** specifica questo ordinamento ed è un oggetto che descrive le azioni e gli stati delle azioni che determineranno l'esecuzione dell'azione.

Per impostazione predefinita, tutte le azioni aggiunte nella finestra di progettazione di App per la logica vengono impostate per essere eseguite dopo il passaggio precedente quando il risultato del passaggio precedente è **Succeeded**. È tuttavia possibile personalizzare il valore di **runAfter** in modo che le azioni vengano generate quando le azioni precedenti restituiscono **Failed**, **Skipped** o una combinazione di questi valori. Ad esempio, per aggiungere un elemento a un argomento del bus di servizio specifico dopo che un'azione **Insert_Row** specifica ha esito negativo, è possibile usare questa definizione **runAfter** di esempio:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

La proprietà **runAfter** viene impostata in modo che venga eseguita quando lo stato dell'azione **Insert_Row** è **Failed**. Per eseguire l'azione se lo stato dell'azione è **Succeeded**, **Failed** o **Skipped** usare la sintassi seguente:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Le azioni eseguite e completate correttamente in seguito all'esito negativo di un'azione precedente vengono contrassegnate come **Succeeded**. Con questo comportamento, se tutti gli errori in un flusso di lavoro vengono rilevati, l'esecuzione stessa verrà contrassegnata come **Succeeded**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Valutare le azioni con ambiti e i relativi risultati

Come quando si eseguono i passaggi dopo le singole azioni con la proprietà **runAfter**, è possibile raggruppare le azioni in un [ambito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Quando si vuole raggruppare le azioni logicamente, valutare lo stato di aggregazione dell'ambito ed eseguire azioni in base a tale stato, è possibile usare gli ambiti. Al termine dell'esecuzione di tutte le azioni in un ambito, l'ambito stesso ottiene il proprio stato. 

Per controllare lo stato di un ambito, è possibile usare lo gli stessi criteri usati per controllare lo stato di esecuzione di un'app per la logica, ad esempio **Succeeded**, **Failed** e così via. 

Per impostazione predefinita, quando tutte le azioni dell'ambito hanno esito positivo lo stato dell'ambito viene contrassegnato come **Succeeded**. Se l'azione finale in un ambito restituisce **Failed** o **Aborted**, lo stato dell'ambito viene contrassegnato come **Failed**. 

Per rilevare le eccezioni in un ambito **Failed** ed eseguire le azioni che gestiscono tali errori, è possibile usare la proprietà **runAfter** per tale ambito **Failed**. In tal modo, se *una qualsiasi* azione nell'ambito ha esito negativo e si usa la proprietà **runAfter** per tale ambito, è possibile creare una singola azione per rilevare gli errori.

Per i limiti degli ambiti, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Ottenere il contesto e i risultati per gli errori

Rilevare gli errori è molto utile, ma può essere opportuno anche il contesto per comprendere esattamente quali azioni hanno avuto esito negativo e tutti gli errori o i codici di stato restituiti. La funzione del flusso di lavoro **@result()** offre il contesto relativo al risultato di tutte le azioni all'interno di un ambito.

La funzione **@result()** accetta un unico parametro, il nome dell'ambito, e restituisce una matrice dei risultati di tutte le azioni all'interno di tale ambito. Questi oggetti azione includono gli stessi attributi dell'oggetto **@actions()**, ad esempio l'ora di inizio, l'ora di fine, lo stato, gli input, gli ID correlazione e gli output dell'azione. Per inviare il contesto per una qualsiasi azione non riuscita all'interno di un ambito, è possibile associare una funzione **@result()** a una proprietà **runAfter**.

Per eseguire un'azione *per ogni* azione di un ambito con stato **Failed**, è possibile associare **@result()** a un'azione **[Filtra matrice](../connectors/connectors-native-query.md)** e a un ciclo **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)**. Ciò consente anche di filtrare la matrice dei risultati in modo da ottenere le azioni non riuscite. La matrice dei risultati filtrata può quindi essere usata per eseguire un'azione per ogni errore con il ciclo **ForEach** . 

Ecco un esempio, seguito da una spiegazione dettagliata, che invia una richiesta HTTP POST con il corpo della risposta di qualsiasi azione non riuscita all'interno dell'ambito "My_Scope":

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Ecco la procedura dettagliata che descrive quanto accaduto in questo esempio:

1. Per ottenere il risultato di tutte le azioni all'interno di "My_Scope", l'azione **Filtra matrice** filtra **@result('My_Scope')**.

2. La condizione per **Filtra matrice** è qualsiasi elemento **@result()** con stato uguale a **Failed**. Questa condizione filtra la matrice di tutti i risultati dell'azione da "My_Scope" fino a una matrice con i soli risultati delle azioni non riuscite.

3. Eseguire un'azione del ciclo **For Each** sugli output della *matrice filtrata*. Questo passaggio esegue un'azione *per ogni* risultato di azione non riuscita precedentemente filtrato.

   Se una sola azione nell'ambito ha avuto esito negativo, le azioni in **foreach** vengono eseguite una sola volta. 
   Molte azioni non riuscite determinano un'azione per errore.

4. Inviare HTTP POST nel corpo della risposta dell'elemento **foreach**, che è **@item()['outputs']['body']**. La forma dell'elemento **@result()** è uguale alla forma di **@actions()** e può essere analizzata nello stesso modo.

5. Includere due intestazioni personalizzate con il nome dell'azione non riuscita **@item()['name']** e l'ID di rilevamento del client dell'esecuzione non riuscita **@item()['clientTrackingId']**.

Come riferimento, di seguito è riportato un esempio di un singolo elemento **@result()**, che mostra le proprietà **name**, **body**, e **clientTrackingId** analizzate nell'esempio precedente. All'esterno di un'azione **foreach** **@result()** restituisce una matrice di questi oggetti.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Le espressioni descritte in precedenza in questo articolo possono essere usate per eseguire diversi modelli di gestione delle eccezioni. È possibile scegliere di eseguire una singola azione di gestione delle eccezioni all'esterno dell'ambito che accetta l'intera matrice filtrata degli errori e rimuovere l'azione **foreach**. È anche possibile includere altre proprietà utili della risposta **@result()** come illustrato in precedenza.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostica e telemetria di Azure

I modelli precedenti sono un ottimo modo per gestire gli errori e le eccezioni in un'esecuzione, ma è possibile anche identificare e rispondere agli errori indipendentemente dall'esecuzione. 
[Diagnostica di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) consente di inviare in modo semplice tutti gli eventi del flusso di lavoro, inclusi tutti gli stati delle esecuzioni e delle azioni, a un account di archiviazione di Azure o a un hub eventi creato in Hub eventi di Azure. 

Per valutare gli stati delle esecuzioni è possibile monitorare i log e le metriche o pubblicarli nello strumento di monitoraggio preferito. Una possibile opzione consiste nel trasmettere tutti gli eventi tramite Hub eventi ad [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/). In Analisi di flusso è possibile scrivere query dinamiche in base a un'anomalia, una media o un errore dei log di diagnostica. È possibile usare Analisi di flusso per inviare informazioni ad altre origini dati, ad esempio a code, argomenti, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come un cliente ha implementato una solida gestione delle eccezioni con App per la logica di Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Altri esempi e scenari di app per la logica](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Informazioni su come creare distribuzioni automatizzate di app per la logica](../logic-apps/logic-apps-create-deploy-template.md)
* [Compilare e distribuire app per la logica con Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9