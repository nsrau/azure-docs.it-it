---
title: Gestione delle eccezioni e degli errori - App per la logica di Azure | Microsoft Docs
description: Informazioni sui modelli per la gestione degli errori e delle eccezioni in App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996597"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Gestire errori ed eccezioni in App per la logica di Azure

Il modo adottato da qualsiasi architettura di integrazione per gestire in maniera appropriata i tempi di inattività o i problemi causati da sistemi dipendenti può costituire una difficoltà. Per contribuire a creare integrazioni solide e resilienti che consentano di gestire correttamente problemi ed errori, App per la logica offre un'esperienza all'avanguardia per la gestione degli errori e delle eccezioni. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Il metodo di base per gestire eccezioni ed errori consiste nell'usare *criteri di ripetizione* in qualsiasi trigger o azione, purché il metodo sia supportato. I criteri di ripetizione specificano se e come l'azione o il trigger ripete la richiesta in caso di timeout o esito negativo della richiesta originale, cioè qualsiasi richiesta con una risposta 408, 429 o 5xx. Se non vengono usati altri criteri di ripetizione, si usano quelli predefiniti. 

Ecco i tipi di criteri di ripetizione: 

| Type | Descrizione | 
|------|-------------| 
| **Default** | Questi criteri inviano fino a quattro richieste di ripetizione a intervalli con *crescita esponenziale* di 7,5 secondi ma con un limite massimo compreso tra 5 e 45 secondi. | 
| **Intervallo esponenziale**  | Questi criteri attendono un intervallo casuale selezionato da un intervallo con crescita esponenziale prima di inviare la richiesta successiva. | 
| **Intervallo fisso**  | Questi criteri attendono l'intervallo specificato prima di inviare la richiesta successiva. | 
| **None**  | Questi criteri non ripetono la richiesta. | 
||| 

Per informazioni sulle restrizioni dei criteri di ripetizione, vedere [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Modificare i criteri di ripetizione

Per selezionare criteri di ripetizione diversi, seguire questa procedura: 

1. Aprire l'app per la logica in Logic App Designer. 

2. Aprire le **impostazioni** per un'azione o un trigger.

3. Se l'azione o il trigger supporta i criteri di ripetizione, selezionare il tipo desiderato in **Criteri di ripetizione**. 

In alternativa, è possibile specificare manualmente i criteri di ripetizione nella sezione `inputs` per un'azione o un trigger che supporta i criteri di ripetizione. Se non si specificano criteri di ripetizione, l'azione usa i criteri predefiniti.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Value | Type | Descrizione |
|-------|------|-------------|
| <*retry-policy-type*> | String | Il tipo di criteri di ripetizione da usare: `default`, `none`, `fixed` o `exponential` | 
| <*retry-interval*> | String | L'intervallo di ripetizione in cui il valore deve usare il [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). L'intervallo minimo predefinito è `PT5S`, l'intervallo massimo è `PT1D`. Quando si usano i criteri a intervallo esponenziale, è possibile specificare valori minimi e massimi diversi. | 
| <*retry-attempts*> | Integer | Numero di tentativi di ripetizione, che deve essere compresi tra 1 e 90 | 
||||

*Facoltativo*

| Value | Type | Descrizione |
|-------|------|-------------|
| <*minimum-interval*> | String | Per i criteri a intervallo esponenziale, l'intervallo più piccolo per l'intervallo selezionato casualmente in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximum-interval*> | String | Per i criteri a intervallo esponenziale, l'intervallo più grande per l'intervallo selezionato casualmente in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Di seguito sono riportate altre informazioni sui diversi tipi di criteri.

<a name="default-retry"></a>

### <a name="default"></a>Predefinito

Se non vengono specificati i criteri di ripetizione, l'azione usa i criteri predefiniti, che in realtà sono [criteri a intervallo esponenziale](#exponential-interval) che inviano fino a quattro richieste di ripetizione a intervalli con crescita esponenziale scalati di 7,5 secondi. L'intervallo è compreso tra 5 e 45 secondi. 

Anche se non è espressamente definito nell'azione o nel trigger, qui sotto viene mostrato il comportamento dei criteri predefiniti in un'azione HTTP di esempio:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nessuna

Per specificare che l'azione o il trigger non ripete richieste con esito negativo, impostare <*retry-policy-type*> su `none`.

### <a name="fixed-interval"></a>Intervallo fisso

Per specificare che l'azione o il trigger attende l'intervallo specificato prima di inviare la richiesta successiva, impostare <*retry-policy-type*> su `fixed`.

*Esempio*

Dopo una prima richiesta con esito negativo, questi criteri di ripetizione provano altre due volte a ricevere le ultime notizie con un intervallo di 30 secondo tra ciascun tentativo:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervallo esponenziale

Per specificare che l'azione o il trigger attende un intervallo causale prima di inviare la richiesta successiva, impostare <*retry-policy-type*> su `exponential`. L'intervallo casuale viene selezionato da un intervallo con crescita esponenziale. Facoltativamente, è inoltre possibile sostituire gli intervalli minimo e massimo predefiniti specificando i propri intervalli minimo e massimo.

**Intervalli variabili casuali**

Questa tabella illustra come App per la logica genera una variabile casuale uniforme nell'intervallo specificato per ogni nuovo tentativo fino al numero di tentativi (incluso):

| Numero di ripetizioni | Intervallo minimo | Intervallo massimo |
|--------------|------------------|------------------|
| 1 | max(0, <*minimum-interval*>) | min(interval, <*maximum-interval*>) |
| 2 | max(interval, <*minimum-interval*>) | min(2 * interval, <*maximum-interval*>) |
| 3 | max(2 * interval, <*minimum-interval*>) | min(4 * interval, <*maximum-interval*>) |
| 4 | max(4 * interval, <*minimum-interval*>) | min(8 * interval, <*maximum-interval*>) |
| .... | .... | .... | 
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

Rilevare gli errori è molto utile, ma può essere opportuno anche il contesto per comprendere esattamente quali azioni hanno avuto esito negativo e tutti gli errori o i codici di stato restituiti. L'espressione `@result()` offre il contesto relativo al risultato di tutte le azioni all'interno di un ambito.

L'espressione `@result()` accetta un unico parametro (il nome dell'ambito) e restituisce una matrice dei risultati di tutte le azioni all'interno di tale ambito. Tali oggetti azione includono gli stessi attributi come la  **\@actions()** oggetto, ad esempio l'ora di inizio, ora di fine, stato, input, gli ID di correlazione e output dell'azione. Per inviare il contesto per qualsiasi azione non riuscita all'interno di un ambito, è possibile associare un'  **\@result()** funzioni con un **runAfter** proprietà.

Per eseguire un'azione per ogni azione in un ambito con un **Failed** risultati, per filtrare la matrice di risultati fino a ottenere le azioni non riuscite, è possibile associare  **\@result()** con un **[ Filtra matrice](../connectors/connectors-native-query.md)** azione e una [ **per ogni** ](../logic-apps/logic-apps-control-flow-loops.md) ciclo. La matrice dei risultati filtrata può quindi essere usata per eseguire un'azione per ogni errore con il ciclo **For each**. 

Di seguito è riportato un esempio, con una spiegazione dettagliata, che invia una richiesta HTTP POST con il corpo della risposta di qualsiasi azione non riuscita all'interno dell'ambito "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Ecco la procedura dettagliata che descrive quanto accaduto in questo esempio:

1. Per ottenere il risultato di tutte le azioni nell'ambito "My_Scope", l'azione **Filter Array** usa questa espressione filtro: `@result('My_Scope')`

2. La condizione per **Filter Array** richiede qualsiasi elemento `@result()` con stato uguale a **Failed**. Questa condizione filtra la matrice che ha tutti i risultati dell'azione da "My_Scope" fino a una matrice con i soli risultati delle azioni non riuscite.

3. Eseguire un'azione del ciclo **For each** sugli output della *matrice filtrata*. Questo passaggio esegue un'azione per ogni risultato di azione non riuscita precedentemente filtrato.

   Se una sola azione nell'ambito ha avuto esito negativo, le azioni nel ciclo **For each** vengono eseguite una sola volta. 
   Molte azioni non riuscite determinano un'azione per errore.

4. Inviare HTTP POST nel corpo della risposta dell'elemento **For each**, cioè l'espressione `@item()['outputs']['body']`. 

   La forma dell'elemento `@result()` è uguale alla forma di `@actions()` e può essere analizzata nello stesso modo.

5. Inclusione di due intestazioni personalizzate con il nome dell'azione non riuscita (`@item()['name']`) e l'ID rilevamento client dell'esecuzione non riuscita (`@item()['clientTrackingId']`).

Come riferimento, di seguito è riportato un esempio di un singolo elemento `@result()`, che visualizza le proprietà **name**, **body** e **clientTrackingId** analizzate nell'esempio precedente. All'esterno di un'azione **For each**, `@result()` restituisce una matrice di questi oggetti.

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

Le espressioni descritte in precedenza in questo articolo possono essere usate per eseguire diversi modelli di gestione delle eccezioni. È possibile scegliere di eseguire una singola azione di gestione delle eccezioni all'esterno dell'ambito che accetta l'intera matrice filtrata degli errori e rimuovere l'azione **For each**. È anche possibile includere altre proprietà utili della  **\@result()** risposta come descritto in precedenza.

## <a name="azure-diagnostics-and-metrics"></a>Diagnostica di Azure e metriche

I modelli precedenti sono un ottimo modo per gestire gli errori e le eccezioni in un'esecuzione, ma è possibile anche identificare e rispondere agli errori indipendentemente dall'esecuzione. 
[Diagnostica di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) consente di inviare in modo semplice tutti gli eventi del flusso di lavoro, inclusi tutti gli stati delle esecuzioni e delle azioni, a un account di archiviazione di Azure o a un hub eventi creato in Hub eventi di Azure. 

Per valutare gli stati delle esecuzioni è possibile monitorare i log e le metriche o pubblicarli nello strumento di monitoraggio preferito. Una possibile opzione consiste nel trasmettere tutti gli eventi tramite Hub eventi ad [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/). In Analisi di flusso è possibile scrivere query dinamiche in base a un'anomalia, una media o un errore dei log di diagnostica. È possibile usare Analisi di flusso per inviare informazioni ad altre origini dati, ad esempio a code, argomenti, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come un cliente ha implementato una solida gestione delle eccezioni con App per la logica di Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Altri esempi e scenari di app per la logica](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
