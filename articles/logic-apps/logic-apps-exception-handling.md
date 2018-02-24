---
title: Gestione degli errori e delle eccezioni per App per la logica in Azure | Microsoft Docs
description: Modelli per la gestione degli errori e delle eccezioni in App per la logica.
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Gestire gli errori e le eccezioni in App per la logica

App per la logica in Azure offre un set completo di strumenti e modelli per garantire la solidità e la resilienza delle integrazioni dell'utente in caso di errori. Nelle architetture di integrazione una delle difficoltà consiste nel garantire che i tempi di inattività o i problemi dei sistemi dipendenti vengano gestiti in modo appropriato. App per la logica consente di avere un'esperienza di prima classe nella gestione degli errori. Offre gli strumenti necessari per risolvere le eccezioni e gli errori nei flussi di lavoro.

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Il tipo più semplice di gestione degli errori e delle eccezioni è costituito dai criteri di ripetizione. In caso di timeout o esito negativo della richiesta iniziale, con restituzione di una risposta 429 o 5xx, i criteri di ripetizione definiscono se e come l'azione dovrà essere ripetuta. 

Esistono quattro tipi di criteri di ripetizione: predefinito, nessuno, a intervallo fisso e a intervallo esponenziale. Se i criteri di ripetizione non vengono indicati nella definizione del flusso di lavoro, vengono usati i criteri predefiniti specificati dal servizio. 

È possibile configurare i criteri di ripetizione dei tentativi nell'*input* di una determinata azione o trigger se ripetibile. Analogamente è possibile configurare i criteri di ripetizione, se applicabili, nella finestra di progettazione di App per la logica. Per configurare i criteri di ripetizione, nella finestra di progettazione di App per la logica passare a **Impostazioni** per scegliere un'azione specifica.

Per informazioni sulle restrizioni dei criteri di ripetizione, vedere [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md). Per altre informazioni sulla sintassi supportata, vedere la [sezione relativa ai criteri di ripetizione in Trigger e azioni dei flussi di lavoro][retryPolicyMSDN].

### <a name="default"></a>Predefinito

Se i criteri di ripetizione non vengono definiti, ovvero **retryPolicy** non viene specificato, vengono usati i criteri predefiniti. I criteri predefiniti sono criteri a intervallo esponenziale che consentono di inviare fino a 4 ripetizioni di tentativi, a intervalli con crescita esponenziale ridimensionati di 7,5 secondi. L'intervallo è compreso tra 5 e 45 secondi. Tali criteri predefiniti equivalgono ai criteri nella definizione del flusso di lavoro HTTP di questo esempio:

```json
"HTTP":
{
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
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>Nessuna

Se **retryPolicy** è impostato su **none**, la richiesta non riuscita non viene ripetuta.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **nessuna** |

### <a name="fixed-interval"></a>Intervallo fisso

Se **retryPolicy** è impostato su **fixed**, i criteri ripeteranno una richiesta non riuscita attendendo l'intervallo di tempo specificato prima di inviare la richiesta successiva.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **fixed** |
| count | Sì | Integer | Numero di tentativi. Deve essere compreso tra 1 e 90. |
| interval | Sì | string | Intervallo di tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e PT1D. |

### <a name="exponential-interval"></a>Intervallo esponenziale

Se **retryPolicy** è impostato su **exponential**, i criteri ripeteranno una richiesta non riuscita dopo un intervallo di tempo casuale di un intervallo a crescita esponenziale. Per ogni tentativo si garantisce l'invio di un intervallo casuale maggiore di **minimumInterval** e minore di **maximumInterval**. Una variabile casuale uniforme nell'intervallo indicato nella tabella seguente verrà generata per ogni nuovo tentativo fino al valore di **count** incluso:

**Intervallo variabile casuale**

| Numero di ripetizioni | Intervallo minimo | Intervallo massimo |
| ------------ |  ------------ |  ------------ |
| 1 | Massimo (0, **minimumInterval**) | Minimo (intervallo, **maximumInterval**) |
| 2 | Massimo (intervallo, **minimumInterval**) | Minimo (2 * intervallo, **maximumInterval**) |
| 3 | Massimo (2 * intervallo, **minimumInterval**) | Minimo (4 * intervallo, **maximumInterval**) |
| 4 | Massimo (4*intervallo, **minimumInterval**) | Minimo (8 * intervallo, **maximumInterval**) |
| ... |

Per i criteri di tipo esponenziale, è necessario impostare i valori per **count** e **interval**. I valori per **minimumInterval** e **maximumInterval** sono facoltativi. È possibile aggiungerli per sostituire rispettivamente i valori predefiniti di PT5S e PT1D.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **exponential** |
| count | Sì | Integer | Numero di tentativi. Deve essere compreso tra 1 e 90.  |
| interval | Sì | string | Intervallo di tentativi in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e PT1D. |
| minimumInterval | No  | string | Intervallo minimo di ripetizioni in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e il valore per **interval**. |
| maximumInterval | No  | string | Intervallo minimo di ripetizioni in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra l'**intervallo** e PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Rilevare gli errori con la proprietà runAfter

Ogni azione di un'app per la logica dichiara le azioni che devono essere completate prima del proprio avvio. Si ottiene così una sorta di ordinamento dei passaggi nel flusso di lavoro. Nella definizione dell'azione tale ordinamento è noto come proprietà **runAfter**. 

La proprietà **runAfter** è un oggetto che descrive le azioni e gli stati delle azioni che determineranno l'esecuzione dell'azione. Per impostazione predefinita tutte le azioni aggiunte tramite la finestra di progettazione di App per la logica vengono impostate per essere eseguite dopo il passaggio precedente, se il risultato del passaggio precedente è **Succeeded**. 

È tuttavia possibile personalizzare il valore di **runAfter** per attivare le azioni quando lo stato delle azioni precedenti è **Failed**, **Skipped** o un possibile insieme di questi valori. Se si vuole aggiungere un elemento a un argomento del bus di servizio di Azure designato in seguito all'esito negativo di una specifica azione **Insert_Row**, è possibile usare la configurazione di **runAfter** seguente:

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

Si noti che **runAfter** è impostato per attivarsi se il risultato dell'azione **Insert_Row** è **Failed**. Per eseguire l'azione se lo stato dell'azione è **Succeeded**, **Failed** o **Skipped** usare la sintassi seguente:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Le azioni eseguite e completate correttamente in seguito all'esito negativo di un'azione precedente vengono contrassegnate come **Succeeded**. Vale a dire che se tutti gli errori in un flusso di lavoro vengono rilevati, l'esecuzione stessa verrà contrassegnata come **Succeeded**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Ambiti e risultati per la valutazione delle azioni

È possibile raggruppare tutte le azioni all'interno di un [ambito](../logic-apps/logic-apps-loops-and-scopes.md), analogamente al modo in cui si avvia l'esecuzione in seguito a singole azioni. Un ambito avvia un raggruppamento logico di azioni. 

Gli ambiti sono utili sia per organizzare le azioni di App per la logica sia per eseguire valutazioni aggregate sullo stato di un ambito. L'ambito in sé riceve uno stato dopo che sono state completate tutte le azioni al suo interno. Lo stato dell'ambito è determinato con gli stessi criteri usati per un'esecuzione: se l'azione finale in un ramo di esecuzione risulta **Failed** o **Aborted** lo stato è **Failed**.

È possibile usare **runAfter** con un ambito contrassegnato come **Failed** per attivare azioni specifiche per eventuali errori che si sono verificati all'interno dell'ambito. Se si usa **runAfter** per un ambito è possibile creare una singola azione per rilevare gli errori in caso di esito negativo di una *qualsiasi* azione all'interno dell'ambito.

### <a name="get-the-context-of-failures-with-results"></a>Recuperare il contesto degli errori con i risultati

Rilevare gli errori è molto utile, ma può essere opportuno anche il contesto per comprendere esattamente quali azioni hanno avuto esito negativo ed eventuali errori o codici di stato restituiti. La funzione del flusso di lavoro **@result()** offre il contesto relativo al risultato di tutte le azioni all'interno di un ambito.

La funzione **@result()** accetta un unico parametro, il nome dell'ambito, e restituisce una matrice dei risultati di tutte le azioni all'interno di tale ambito. Tali oggetti azione includono gli stessi attributi dell'oggetto **@actions()**, tra cui ora di inizio, ora di fine, stato, input, ID di correlazione e output dell'azione. 

Per inviare il contesto di una qualsiasi azione non riuscita all'interno di un ambito, è possibile associare una funzione **@result()** a una proprietà **runAfter**.

Se si vuole eseguire un'azione *per ogni* azione di un ambito con stato **Failed**, è possibile associare **@result()** a un'azione [Filter_array](../connectors/connectors-native-query.md) e a un ciclo [foreach](../logic-apps/logic-apps-loops-and-scopes.md). Ciò consente anche di filtrare la matrice dei risultati in modo da ottenere le azioni non riuscite. La matrice dei risultati filtrata può essere usata per eseguire un'azione per ogni errore con il ciclo **foreach**. 

L'esempio seguente invia una richiesta HTTP POST con il corpo della risposta di un'azione non riuscita all'interno dell'ambito My_Scope:

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

Ecco la procedura dettagliata che descrive quanto accaduto nell'esempio precedente:

1. Per ottenere il risultato di tutte le azioni all'interno di My_Scope, l'azione **Filter_array** filtra **@result('My_Scope')**.

2. La condizione per **Filter_array** è qualsiasi elemento **@result()** con stato uguale a **Failed**. Questa condizione filtra la matrice di tutti i risultati dell'azione da My_Scope a una matrice con i soli risultati delle azioni non riuscite.

3. Eseguire un'azione **foreach** sugli output della *matrice filtrata*. Questo passaggio esegue un'azione *per ogni* risultato di azione non riuscita precedentemente filtrato.

    Se una sola azione nell'ambito ha avuto esito negativo, le azioni in **foreach** vengono eseguite una sola volta. Molte azioni non riuscite determinano un'azione per errore.

4. Inviare HTTP POST nel corpo della risposta dell'elemento **foreach**, ovvero **@item()['outputs']['body']**. La forma dell'elemento **@result()** è la stessa di **@actions()**. Può essere analizzata allo stesso modo.

5. Includere due intestazioni personalizzate con il nome dell'azione non riuscita **@item()['name']** e l'ID di rilevamento del client dell'esecuzione non riuscita **@item()['clientTrackingId']**.

Come riferimento, di seguito è riportato un esempio di un solo elemento **@result()**. Mostra le proprietà **name**, **body** e **clientTrackingId** analizzate nell'esempio precedente. All'esterno di un'azione **foreach** **@result()** restituisce una matrice di questi oggetti.

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

Le espressioni riportate sopra nell'articolo possono essere usate per eseguire diversi modelli di gestione delle eccezioni. È possibile scegliere di eseguire una singola azione di gestione delle eccezioni all'esterno dell'ambito che accetta l'intera matrice filtrata degli errori e rimuovere **foreach**. È anche possibile includere altre proprietà utili della risposta **@result()** come illustrato sopra.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostica e telemetria di Azure

I modelli descritti in questo articolo sono un ottimo modo per gestire gli errori e le eccezioni in un'esecuzione, ma è possibile anche identificare e rispondere agli errori indipendentemente dall'esecuzione. [Diagnostica di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) consente di inviare in modo semplice tutti gli eventi del flusso di lavoro, inclusi tutti gli stati delle esecuzioni e delle azioni, a un account di archiviazione di Azure o a un hub eventi in Hub eventi di Azure. 

Per valutare gli stati delle esecuzioni è possibile monitorare i log e le metriche o pubblicarli nello strumento di monitoraggio preferito. Una possibile opzione consiste nel trasmettere tutti gli eventi tramite Hub eventi ad [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/). In Analisi di flusso è possibile scrivere query dinamiche in base a un'anomalia, una media o un errore dei log di diagnostica. È possibile usare Analisi di flusso per inviare informazioni ad altre origini dati, ad esempio a code, argomenti, SQL, Azure Cosmos DB o Power BI.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come un cliente [implementa la gestione degli errori con App per la logica in Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Altri [esempi e scenari di App per la logica](../logic-apps/logic-apps-examples-and-scenarios.md).
* Informazioni su come creare [distribuzioni automatizzate per le app per la logica](../logic-apps/logic-apps-create-deploy-template.md).
* Informazioni su come [compilare e distribuire le app per la logica con Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
