---
title: Gestione delle eccezioni in App per la logica | Microsoft Docs
description: Informazioni sui modelli per la gestione degli errori e delle eccezioni con App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan

---
# <a name="logic-apps-error-and-exception-handling"></a>Gestione degli errori e delle eccezioni in App per la logica
App per la logica offre un set completo di strumenti e modelli per garantire la solidità e la resilienza delle integrazioni dell'utente in caso di errori.  In qualsiasi architettura di integrazione, una delle sfide è garantire che i tempi di inattività o i problemi dei sistemi dipendenti vengano gestiti in modo appropriato.  App per la logica ottimizza la gestione degli errori offrendo gli strumenti necessari per intervenire in caso di eccezioni ed errori nei flussi di lavoro.

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi
Il tipo più semplice di gestione degli errori e delle eccezioni è costituito dai criteri di ripetizione dei tentativi.  Tali criteri definiscono se l'azione dovrà essere ripetuta in caso di timeout o esito negativo (con restituzione di una risposta 429 o 5xx) della richiesta iniziale.  Per impostazione predefinita, tutte le azioni vengono ripetute altre 4 volte a intervalli di 20 secondi.  Se la prima richiesta ha ricevuto una risposta `500 Internal Server Error` , il motore del flusso di lavoro viene sospeso per 20 secondi e quindi ripete la richiesta.  Se dopo tutti i tentativi la risposta è ancora un'eccezione o un errore, il flusso di lavoro continua e contrassegna l'azione con lo stato `Failed`.

È possibile configurare i criteri di ripetizione dei tentativi nell'elemento **inputs** di una determinata azione.  I criteri di ripetizione possono essere configurati fino a un massimo di 4 tentativi a intervalli di 1 ora.  I dettagli completi sulle proprietà di input sono [disponibili su MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se si vuole che l'azione HTTP venga ripetuta 4 volte con un'attesa di 10 minuti tra i singoli tentativi, si userà la definizione seguente:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Per altri dettagli sulla sintassi supportata, vedere la [sezione retryPolicy su MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Proprietà runAfter per rilevare gli errori
Ogni azione di un'app per la logica dichiara le azioni che devono essere completate prima del proprio avvio.  Si ottiene così una sorta di ordinamento dei passaggi del flusso di lavoro.  Tale ordinamento è costituito dalla proprietà `runAfter` nella definizione dell'azione,  un oggetto che descrive le azioni e gli stati delle azioni che determineranno l'esecuzione dell'azione.  Per impostazione predefinita, tutte le azioni aggiunte tramite la finestra di progettazione vengono impostate per essere eseguite dopo il passaggio precedente (`runAfter`) se il passaggio precedente risulta `Succeeded`.  È possibile personalizzare questo valore per attivare le azioni quando lo stato delle azioni precedenti è `Failed`, `Skipped` o un possibile insieme di questi valori.  Se si vuole aggiungere un elemento a un argomento del bus di servizio designato dopo l'esito negativo di una specifica azione `Insert_Row`, si userà la configurazione `runAfter` seguente:

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

Si noti che la proprietà `runAfter` è impostata per essere attivata se l'azione `Insert_Row` è `Failed`.  Per eseguire l'azione se lo stato dell'azione è `Succeeded`, `Failed` o `Skipped`, la sintassi sarebbe la seguente:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Le azioni eseguite dopo l'esito negativo di un'azione precedente e completate correttamente verranno contrassegnate come `Succeeded`.  Con questo comportamento, se tutti gli errori in un flusso di lavoro vengono rilevati l'esecuzione stessa verrà contrassegnata come `Succeeded`.
> 
> 

## <a name="scopes-and-results-to-evaluate-actions"></a>Ambiti e risultati per la valutazione delle azioni
Così come si può configurare l'esecuzione dopo singole azioni, è anche possibile raggruppare insieme le azioni all'interno di un [ambito](app-service-logic-loops-and-scopes.md) , che fungerà da raggruppamento logico delle azioni.  Gli ambiti sono utili sia per organizzare le azioni delle app per la logica sia per eseguire valutazioni aggregate sullo stato di un ambito.  L'ambito in sé riceve uno stato dopo che sono state completate tutte le azioni al suo interno.  Lo stato dell'ambito è determinato con gli stessi criteri usati per un'esecuzione: se l'azione finale in un ramo di esecuzione è `Failed` o `Aborted`, lo stato sarà `Failed`.

È possibile usare `runAfter` dopo che è un ambito è stato contrassegnato come `Failed` per attivare azioni specifiche per gli eventuali errori verificatisi all'interno dell'ambito.  L'esecuzione dopo l'esito negativo di un ambito consente di creare una singola azione per rilevare gli errori in caso di esito negativo di *qualsiasi* azione all'interno dell'ambito.

### <a name="getting-the-context-of-failures-with-results"></a>Recupero del contesto degli errori con i risultati
Rilevare gli errori è molto utile, ma può essere opportuno anche il contesto per comprendere esattamente quali azioni hanno avuto esito negativo e tutti gli errori o i codici di stato restituiti.  La funzione `@result()` del flusso di lavoro include il contesto nel risultato di tutte le azioni all'interno di un ambito.

`@result()` accetta un unico parametro, il nome dell'ambito, e restituisce una matrice dei risultati di tutte le azioni di tale ambito.  Tali oggetti azione includono gli stessi attributi dell'oggetto `@actions()` , come ora di inizio, ora di fine, stato, input, ID di correlazione e output dell'azione.  Una funzione `@result()` può essere associata facilmente a `runAfter` per inviare il contesto di qualsiasi azione non riuscita all'interno di un ambito.

Se si vuole eseguire un'azione *per ogni* azione di un ambito con stato `Failed`, è possibile associare `@result()` a un'azione **[Filtra matrice](../connectors/connectors-native-query.md)** e a un ciclo **[ForEach](app-service-logic-loops-and-scopes.md)**.  Ciò consente di filtrare la matrice dei risultati in modo da ottenere le azioni non riuscite.  La matrice dei risultati filtrata può quindi essere usata per eseguire un'azione per ogni errore con il ciclo **ForEach** .  Di seguito sono riportati un esempio e la relativa spiegazione dettagliata.  L'esempio invierà una richiesta HTTP POST con il corpo della risposta di qualsiasi azione non riuscita all'interno dell'ambito `My_Scope`.

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

Ecco la procedura dettagliata eseguita:

1. Azione **Filtra matrice** per filtrare `@result('My_Scope')` in modo da ottenere il risultato di tutte le azioni all'interno di `My_Scope`
2. **Filtra matrice** con qualsiasi elemento `@result()` con stato `Failed` come condizione.  La matrice dei risultati di tutte le azioni di `My_Scope` viene così filtrata in modo da ottenere una matrice solo dei risultati delle azioni non riuscite.
3. Esecuzione di un'azione **For Each** sugli output **Matrice filtrata**.  Verrà così eseguita un'azione *per ogni* risultato di azione non riuscita precedentemente filtrato.
   * Se una sola azione nell'ambito ha avuto esito negativo, le azioni in `foreach` vengono eseguite una sola volta.  Molte azioni non riuscite determinano un'azione per errore.
4. Invio di HTTP POST nel corpo della risposta dell'elemento `foreach`, ovvero `@item()['outputs']['body']`.  La forma dell'elemento `@result()` è uguale alla forma di `@actions()` e può essere analizzata nello stesso modo.
5. Inclusione anche di due intestazioni personalizzate con il nome dell'azione non riuscita `@item()['name']` e l'ID rilevamento client dell'esecuzione non riuscita `@item()['clientTrackingId']`.

Come riferimento, di seguito è riportato un esempio di un singolo elemento `@result()` .  È possibile osservare le proprietà `name`, `body` e `clientTrackingId` analizzate nell'esempio precedente.  Si noti che all'esterno di `foreach`, `@result()` restituisce una matrice di questi oggetti.

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
            "message": "/docs/foo/bar does not exist"
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

Le espressioni riportate sopra possono essere usate per eseguire diversi modelli di gestione delle eccezioni.  È possibile scegliere di eseguire una singola azione di gestione delle eccezioni all'esterno dell'ambito che accetta l'intera matrice filtrata degli errori e rimuovere `foreach`.  Si possono anche includere altre proprietà utili della risposta `@result()` illustrata sopra.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostica e telemetria di Azure
I modelli precedenti sono un ottimo modo per gestire gli errori e le eccezioni in un'esecuzione, ma è possibile anche identificare e rispondere agli errori indipendentemente dall'esecuzione.  [Diagnostica di Azure](app-service-logic-monitor-your-logic-apps.md) consente di inviare in modo semplice tutti gli eventi del flusso di lavoro (inclusi tutti gli stati delle esecuzioni e delle azioni) a un account di archiviazione di Azure o un hub eventi di Azure.  È possibile monitorare i log e le metriche o pubblicarli nello strumento di monitoraggio preferito per valutare gli stati delle esecuzioni.  Una possibile opzione consiste nel trasmettere tutti gli eventi tramite un hub eventi di Azure ad [Analisi di flusso](https://azure.microsoft.com/services/stream-analytics/).  In Analisi di flusso è possibile scrivere query dinamiche per qualsiasi anomalia, media o errore dei log di diagnostica.  Analisi di flusso può facilmente inviare output ad altre origini dati come query, argomenti, SQL, DocumentDB e Power BI.

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come un cliente ha implementato una solida gestione delle eccezioni con App per la logica](app-service-logic-scenario-error-and-exception-handling.md)
* [Altri esempi e scenari di app per la logica](app-service-logic-examples-and-scenarios.md)
* [Informazioni su come creare distribuzioni automatizzate delle app per la logica](app-service-logic-create-deploy-template.md)
* [Progettare e distribuire app per la logica in Visual Studio](app-service-logic-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9


<!--HONumber=Oct16_HO2-->


