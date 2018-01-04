---
title: Errore e gestione delle eccezioni per la logica App in Azure | Documenti Microsoft
description: Modelli di errore e gestione delle eccezioni nelle App per la logica.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Gestire errori ed eccezioni nelle app di logica

Logica App in Azure fornisce strumenti avanzati e modelli che consentono di garantire che le integrazioni affidabile e resiliente agli errori. Qualsiasi architettura comporta la necessità di gestire in modo appropriato i tempi di inattività o problemi da sistemi dipendenti. Logica App rende gestione degli errori di un'esperienza di prima classe. Fornisce gli strumenti che necessari per agire su eccezioni ed errori nei flussi di lavoro.

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Il tipo più semplice di gestione degli errori e delle eccezioni è costituito dai criteri di ripetizione. Se i timeout di una richiesta iniziale o ha esito negativo (qualsiasi richiesta che comporta un 429 o risposta 5xx), un criterio di ripetizione definisce se e come l'azione deve essere riprovata. 

Sono disponibili quattro tipi di criteri di tentativi: default, none, fisso, intervallo e intervallo esponenziale. Se un criterio di ripetizione non viene fornito nella definizione del flusso di lavoro, viene utilizzato il criterio predefinito, come definito dal servizio. 

È possibile configurare i criteri di ripetizione dei tentativi nell'*input* di una determinata azione o trigger se ripetibile. Analogamente, è possibile configurare criteri di tentativi (se applicabile) nella finestra di progettazione logica App. Per impostare un criterio di ripetizione, in Progettazione applicazione logica, passare a **impostazioni** per un'azione specifica.

Per informazioni sulle limitazioni di criteri di tentativi, vedere [limiti di App per la logica e la configurazione](../logic-apps/logic-apps-limits-and-config.md). Per ulteriori informazioni sulla sintassi supportata, vedere il [ripetere la sezione criteri nel flusso di lavoro azioni e trigger][retryPolicyMSDN].

### <a name="default"></a>Predefinito

Se non si definisce un criterio di ripetizione (**retryPolicy** è definito), viene usato il criterio predefinito. Il criterio predefinito è un criterio di intervallo esponenziale che invia fino a quattro tentativi, a migliorare in modo esponenziale intervalli scalati 7,5 secondi. L'intervallo è limitata a compreso tra 5 e 45 secondi. Il criterio predefinito è equivalente al criterio in questo esempio di definizione del flusso di lavoro HTTP:

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

Se **retryPolicy** è impostato su **Nessuno**, non viene ripetuta una richiesta non riuscita.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **nessuna** |

### <a name="fixed-interval"></a>Intervallo fisso

Se **retryPolicy** è impostato su **fissa**, i criteri di tentativi di una richiesta non riuscita mediante l'attesa dell'intervallo di tempo prima di inviare la richiesta successiva specificato.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **predefinito** |
| count | Sì | Integer | Numero di tentativi. Deve essere compreso tra 1 e 90. |
| interval | Sì | string | Intervallo di tentativi [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e PT1D. |

### <a name="exponential-interval"></a>Intervallo esponenziale

Se **retryPolicy** è impostato su **esponenziale**, i criteri di tentativi di una richiesta non riuscita dopo un intervallo di tempo casuale da un intervallo di crescita esponenziale. Per ogni tentativo si garantisce l'invio di un intervallo casuale maggiore di **minimumInterval** e minore di **maximumInterval**. Una variabile casuale uniforme nell'intervallo indicato nella tabella seguente viene generata per ogni nuovo tentativo alla **conteggio**:

**Intervallo variabile casuale**

| Numero di tentativi | Intervallo minimo | Intervallo massimo |
| ------------ |  ------------ |  ------------ |
| 1 | Massimo (0, **minimumInterval**) | Minimo (intervallo, **maximumInterval**) |
| 2 | Massimo (intervallo, **minimumInterval**) | Minimo (2 * intervallo, **maximumInterval**) |
| 3 | Max (2 * intervallo, **definiti in minimumInterval**) | Minimo (4 * intervallo, **maximumInterval**) |
| 4 | Massimo (4*intervallo, **minimumInterval**) | Minimo (8 * intervallo, **maximumInterval**) |
| ... |

Per i criteri di tipo esponenziale, **conteggio** e **intervallo** sono necessari. I valori del parametro **definiti in minimumInterval** e **maximumInterval** sono facoltativi. È possibile aggiungere in modo da ignorare i valori predefiniti di PT5S e PT1D, rispettivamente.

| Nome dell'elemento | Obbligatoria | type | DESCRIZIONE |
| ------------ | -------- | ---- | ----------- |
| type | Sì | string | **esponenziale** |
| count | Sì | Integer | Numero di tentativi. Deve essere compreso tra 1 e 90.  |
| interval | Sì | string | Intervallo di tentativi [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e PT1D. |
| minimumInterval | No  | string | Intervallo minimo in tentativi [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra PT5S e **intervallo**. |
| maximumInterval | No  | string | Intervallo minimo in tentativi [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve essere compreso tra **intervallo** e PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Intercettare gli errori con la proprietà runAfter

Ogni azione di logica app dichiara le azioni che devono essere completata prima dell'inizio dell'azione. È simile a ordine i passaggi nel flusso di lavoro. Nella definizione di azione, questo ordinamento è noto come il **runAfter** proprietà. 

Il **runAfter** proprietà è un oggetto che descrive l'azione di eseguire le azioni e gli stati di azione. Per impostazione predefinita, tutte le azioni che sono state aggiunte tramite Progettazione applicazione logica vengono impostate per l'esecuzione dopo il passaggio precedente, se il risultato del passaggio precedente è **Succeeded**. 

Tuttavia, è possibile personalizzare il **runAfter** valore attivano azioni quando operazioni sopra descritte avranno come risultato **Failed**, **ignorati**, o un set di possibili valori sono i seguenti. Se si desidera aggiungere un elemento a un argomento del Bus di servizio di Azure designato dopo un'azione specifica **Insert_Row** ha esito negativo, è possibile utilizzare la seguente **runAfter** configurazione:

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

Si noti che **runAfter** è impostato su attivato se il **Insert_Row** risultato dell'azione è **Failed**. Per eseguire l'azione, se lo stato dell'azione **Succeeded**, **non riuscito**, o **ignorati**, utilizzare la seguente sintassi:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Azioni che eseguono e completata correttamente, dopo che ha eseguito un'azione precedente sono contrassegnate come **Succeeded**. Ciò significa che se si catch tutti correttamente gli errori in un flusso di lavoro, l'esecuzione di se stesso è contrassegnato come **Succeeded**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Ambiti e risultati per la valutazione delle azioni

È possibile raggruppare le azioni all'interno di un [ambito](../logic-apps/logic-apps-loops-and-scopes.md), in modo analogo al modo in cui è eseguito dopo le singole azioni. Un ambito viene utilizzato un raggruppamento logico di azioni. 

Gli ambiti sono utili per organizzare le azioni di App per la logica e per l'esecuzione di valutazioni di aggregazione dello stato di un ambito. L'ambito in sé riceve uno stato dopo che sono state completate tutte le azioni al suo interno. Lo stato dell'ambito è determinato con gli stessi criteri usati per un'esecuzione: Se l'azione finale in un branch di esecuzione è **Failed** o **Aborted**, lo stato è **Failed**.

Per generare le azioni specifiche per gli eventuali errori che si è verificato all'interno dell'ambito, è possibile utilizzare **runAfter** con un ambito che è contrassegnato come **Failed**. Se *qualsiasi* azioni nell'ambito hanno esito negativo, se si utilizza **runAfter** per un ambito, è possibile creare una singola azione per intercettare gli errori.

### <a name="get-the-context-of-failures-with-results"></a>Ottenere il contesto degli errori relativi a risultati

Sebbene intercettare gli errori di un ambito è utile, è possibile anche contesto consentono di comprendere esattamente le azioni che non è riuscita e per comprendere eventuali errori o i codici di stato che sono stati restituiti. Il  **@result()** funzione del flusso di lavoro fornisce un contesto sul risultato di tutte le azioni in un ambito.

Il  **@result()** funzione accetta un parametro singolo (nome dell'ambito) e restituisce una matrice di tutti i risultati di azioni da tale ambito. Questi oggetti azione includono gli stessi attributi di  **@actions()** Genera oggetto, inclusi l'ora di inizio azione, ora di fine di azione, lo stato di azione, input azione, ID di correlazione di azione e azione. 

Per inviare il contesto di tutte le azioni che non è riuscita in un ambito, può essere facilmente un  **@result()** funzione con un **runAfter** proprietà.

Per eseguire un'azione *per ogni* azione in un ambito con un **Failed** risultati, e per filtrare la matrice di risultati alle azioni che non è riuscita, è possibile associare  **@result()** con un [Filter_array](../connectors/connectors-native-query.md) azione e un [foreach](../logic-apps/logic-apps-loops-and-scopes.md) ciclo. Con la matrice di risultati filtrato, è possibile eseguire un'azione per ogni errore utilizzando il **foreach** ciclo. 

Ecco un esempio che invia un HTTP POST richiesta con il corpo della risposta di tutte le azioni che non è nell'ambito My_Scope:

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

Di seguito è riportata una procedura dettagliata per descrivere cosa accade nell'esempio precedente:

1. Per ottenere il risultato di tutte le azioni all'interno di My_Scope, il **Filter_array** filtri azione  **@result('My_Scope')**.

2. La condizione per **Filter_array** qualsiasi  **@result()** elemento il cui stato uguale a **Failed**. Questa condizione filtra la matrice di tutti i risultati dell'azione da My_Scope, in una matrice con i risultati solo azione non riuscita.

3. Eseguire un **foreach** azione il *matrice filtrata* output. Questo passaggio esegue un'azione *per ogni* risultato di azione non riuscita precedentemente filtrato.

    Se non è riuscita un'unica azione nell'ambito, le azioni nel **foreach** eseguire una sola volta. Più azioni non riuscite di modo che un'azione per errore.

4. Inviare una richiesta POST HTTP sul **foreach** elemento corpo della risposta, o  **@item() ['output'] ['body']**. Il  **@result()** forma di elemento è lo stesso come il  **@actions()** forma. Può essere analizzato allo stesso modo.

5. Includere due intestazioni personalizzate con il nome di azione non riuscita  **@item() ['name']** e di eseguire client ID rilevamento  **@item() ['clientTrackingId']**.

Per riferimento, di seguito è riportato un esempio di un singolo  **@result()** elemento. Viene illustrato il **nome**, **corpo**, e **clientTrackingId** le proprietà che vengono analizzate nell'esempio precedente. All'esterno una **foreach** action  **@result()** restituisce una matrice di questi oggetti.

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

Per diverse eccezioni modelli, è possibile utilizzare le espressioni descritte in precedenza nell'articolo. Potrebbe scegliere di eseguire un'unica eccezione che gestisce l'azione all'esterno dell'ambito che accetta l'intera matrice filtrata di errori e rimuovere il **foreach**. È inoltre possibile includere altre proprietà utili dal  **@result()** risposta, come descritto in precedenza.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostica e telemetria di Azure

I modelli descritti in questo articolo è fornire grandi modi per gestire errori ed eccezioni all'interno di un'esecuzione, ma è anche possibile identificare e rispondere agli errori indipendentemente dall'esecuzione stessa. [Diagnostica di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornisce un modo semplice per inviare tutti gli eventi del flusso di lavoro (inclusi tutti gli stati di esecuzione e l'azione) a un account di archiviazione di Azure o a un hub di eventi nell'hub eventi di Azure. 

Per valutare gli stati di esecuzione, è possibile monitorare le metriche e i registri o pubblicarli in qualsiasi strumento di monitoraggio che si preferisce. Un potenziale consiste nel flusso di tutti gli eventi tramite gli hub di eventi per [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/). Nel flusso Analitica, è possibile scrivere query in tempo reale in base alle eventuali anomalie, medie o errori dai log di diagnostica. È possibile utilizzare Analitica di flusso per inviare informazioni da altre origini dati, ad esempio code, argomenti, SQL, database di Azure Cosmos o Power BI.

## <a name="next-steps"></a>Passaggi successivi

* Vedere come un cliente [compilazioni con App in Azure per la logica di gestione degli errori](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Altre informazioni sono disponibili [logica App esempi e scenari](../logic-apps/logic-apps-examples-and-scenarios.md).
* Informazioni su come creare [automatizzata le distribuzioni per App per la logica](../logic-apps/logic-apps-create-deploy-template.md).
* Informazioni su come [compilare e distribuire App con Visual Studio per la logica](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
