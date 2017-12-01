---
title: Trigger e azioni dei flussi di lavoro - App per la logica di Azure | Microsoft Docs
description: "Altre informazioni sui tipi di trigger e azioni che è possibile usare per la creazione e l'automazione di flussi di lavoro e processi con App per la logica di Azure"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 9f95c0c486401e0d709829ce8d560f030932eea7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Trigger e azioni dei flussi di lavoro di app per la logica

Tutte le app per la logica iniziano con un trigger seguito da azioni. Questo argomento descrive i tipi di trigger e azioni che è possibile usare per la creazione di integrazioni di sistema e l'automazione di processi o flussi di lavoro aziendali tramite la compilazione di app per la logica. 
  
## <a name="triggers-overview"></a>Panoramica dei trigger 

Tutte le app per la logica iniziano con un trigger, che specifica le chiamate che possono avviare l'esecuzione di un'app per la logica. Ecco i due modi in cui è possibile avviare un'esecuzione del flusso di lavoro:  

* Un trigger di poll  
* Un trigger di push, che chiama l'[API REST del servizio di flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Tutti i trigger contengono questi elementi di livello superiore:  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>Tipi di trigger e input  

Ogni tipo di trigger ha un'interfaccia e *input* diversi che ne definiscono il comportamento. 

| Tipo di trigger | Descrizione | 
| ------------ | ----------- | 
| **Ricorrenza** | Attivato in base a una pianificazione definita. È possibile impostare una data e un'ora future per l'attivazione di questo trigger. In base alla frequenza, è anche possibile specificare le ore e i giorni per l'esecuzione del flusso di lavoro. | 
| **Richiesta**  | Trasforma l'app per la logica in un endpoint che è possibile chiamare. È denominato anche trigger "manuale". | 
| **HTTP** | Controlla (o ne *esegue il polling*) un endpoint Web HTTP. L'endpoint HTTP deve essere conforme a un contratto di attivazione specifico, usando un modello asincrono "202" o restituendo una matrice. | 
| **ApiConnection** | Esegue il polling come un trigger HTTP, ma usa [API gestite da Microsoft](../connectors/apis-list.md). | 
| **HTTPWebhook** | Trasforma l'app per la logica in un endpoint che può essere chiamato, ad esempio il trigger Request, ma chiama un URL specificato per la registrazione e l'annullamento della registrazione. |
| **ApiConnectionWebhook** | Funziona come il trigger **HTTPWebhook**, ma usa API gestite da Microsoft. | 
||| 

Per informazioni su altri elementi, vedere [Linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md). 
  
## <a name="recurrence-trigger"></a>Trigger Recurrence  

Questo trigger viene eseguito in base alla ricorrenza e alla pianificazione specificate e offre un modo semplice per eseguire regolarmente un flusso di lavoro. Ecco un esempio di trigger di ricorrenza di base che viene eseguito ogni giorno:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```
È anche possibile impostare una data e un'ora di inizio per l'attivazione del trigger. Ad esempio, per iniziare un report settimanale ogni lunedì, è possibile pianificare l'avvio dell'app per la logica in un lunedì specifico, come in questo esempio: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

Ecco la definizione per questo trigger: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Sì | String | Unità di tempo per la frequenza di attivazione del trigger. Usare solo uno di questi valori: "second", "minute", "hour", "day", "week" o "month" | 
| interval | Sì | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. <p>Ecco gli intervalli minimo e massimo: <p>- Mese: 1-16 mesi </br>- Giorno: 1-500 giorni </br>- Ora: 1-12.000 ore </br>- Minuto: 1-72.000 minuti </br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "month", la ricorrenza è ogni 6 mesi. | 
| timeZone | No | String | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Specificare il fuso orario che si vuole applicare. | 
| startTime | No | String | Specificare la data e l'ora di inizio in questo formato: <p>AAAA-MM-GGThh:mm:ss se si specifica un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si specifica un fuso orario <p>Ad esempio, per il 18 settembre 2017 alle 14.00, specificare "2017-09-18T14:00:00" e indicare un fuso orario, ad esempio l'ora solare del Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si specifica un fuso orario, è necessario aggiungere la lettera "Z" alla fine senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non viene attivato prima dell'ora di inizio. Per altre informazioni sulle date e le ore di inizio, vedere [Creare e pianificare attività eseguite regolarmente](../connectors/connectors-native-recurrence.md). | 
| weekDays | No | Stringa o matrice di stringhe | Se si specifica "Week" per `frequency`, è possibile specificare uno o più giorni, separati da virgole, in cui eseguire il flusso di lavoro: "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" e "Sunday" | 
| hours | No | Intero o matrice di intero | Se si specifica "Day" o "Week" per `frequency`, è possibile specificare uno o più numeri interi compresi tra 0 e 23, separati da virgole, come ore del giorno in cui eseguire il flusso di lavoro. <p>Ad esempio, se si specifica "10", "12" e "14", gli indicatori di ora corrisponderanno alle 10.00, alle 12.00 e alle 14.00. | 
| minutes | No | Intero o matrice di intero | Se si specifica "Day" o "Week" per `frequency`, è possibile specificare uno o più numeri interi compresi tra 0 e 59, separati da virgole, come minuti dell'ora in cui eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. | 
|||||| 

Ad esempio, il trigger di ricorrenza specifica che l'app per la logica viene eseguita ogni settimana di lunedì alle 10.30, 12.30 e 14.30 ora solare Pacifico, a partire non prima del 9 settembre 2017 alle 14.00:

``` json
{
    "triggers": {
        "myRecurrenceTrigger": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

Per altre informazioni con alcuni esempi di ricorrenza e ora di inizio per questo trigger, vedere [Creare e pianificare attività eseguite regolarmente](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Trigger di richiesta

Questo trigger funge da endpoint che è possibile usare per chiamare l'app per la logica tramite una richiesta HTTP. Un trigger request è simile a questo esempio:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Questo trigger include anche una proprietà facoltativa denominata *schema*:
  
| Nome dell'elemento | Obbligatoria | Tipo | Descrizione |
| ------------ | -------- | ---- | ----------- |
| schema | No | Oggetto | Schema JSON che convalida la richiesta in ingresso. È utile per indicare le proprietà a cui fare riferimento nei passaggi successivi del flusso di lavoro. | 
||||| 

Per richiamare questo endpoint, è necessario chiamare l'API *listCallbackUrl*. Vedere [Workflow Service REST API](https://docs.microsoft.com/rest/api/logic/workflows) (API REST del servizio di flusso di lavoro).

## <a name="http-trigger"></a>Trigger HTTP  

I trigger HTTP eseguono il polling di un endpoint specificato e controllano la risposta per determinare se il flusso di lavoro deve essere eseguito. In questo caso, l'oggetto `inputs` accetta i parametri necessari per la costruzione di una chiamata HTTP:  

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| statico | Sì | String | Usare uno di questi metodi HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD" | 
| Uri | Sì| String | Endpoint HTTP o HTTPS controllato dal trigger. Dimensione massima della stringa: 2 KB | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
| retryPolicy | No | Oggetto | Usare questo oggetto per personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md). | 
| authentication | No | Oggetto | Rappresenta il metodo che deve essere usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). <p>Oltre all'Utilità di pianificazione, è supportata un'altra proprietà: `authority`. Per impostazione predefinita, questo valore è `https://login.windows.net` quando non è specificato, ma è possibile usare un valore diverso, ad esempio `https://login.windows\-ppe.net`. | 
||||| 
 
Con il trigger HTTP, l'API HTTP deve essere conforme a un modello specifico per poter interagire correttamente con l'app per la logica. Il trigger riconosce queste proprietà:  
  
| Response | Obbligatorio | Descrizione | 
| -------- | -------- | ----------- |  
| Codice di stato | Sì | Il codice di stato 200 ("OK") determina un'esecuzione. Nessun altro codice di stato attiva un'esecuzione. | 
| Intestazione retry-after | No | Numero di secondi prima che l'app per la logica esegua di nuovo il polling dell'endpoint. | 
| Intestazione Location | No | URL da chiamare al successivo intervallo di polling. Se non è specificato, viene usato l'URL originale. | 
|||| 

Ecco alcuni esempi di comportamento per tipi di richieste diversi:
  
| Codice della risposta | Nuovo tentativo dopo | Comportamento | 
| ------------- | ----------- | -------- | 
| 200 | {none} | Esegue il flusso di lavoro e quindi controlla di nuovo la presenza di altri dati dopo la ricorrenza definita. | 
| 200 | 10 secondi | Esegue il flusso di lavoro e quindi controlla di nuovo la presenza di altri dati dopo 10 secondi. |  
| 202 | 60 secondi | Non attiva il flusso di lavoro. Il tentativo successivo viene eseguito dopo un minuto, in base alla ricorrenza definita. Se la ricorrenza definita è inferiore a un minuto, l'intestazione retry-after ha la precedenza. In caso contrario, viene usata la ricorrenza definita. | 
| 400 | {none} | Richiesta non valida, non esegue il flusso di lavoro. Se non è definito alcun oggetto `retryPolicy`, viene usato il criterio predefinito. Dopo aver raggiunto il numero di tentativi, il trigger controlla di nuovo la presenza di dati dopo la ricorrenza definita. | 
| 500 | {none}| Errore del server, il flusso di lavoro non viene eseguito. Se non è definito alcun oggetto `retryPolicy`, viene usato il criterio predefinito. Dopo aver raggiunto il numero di tentativi, il trigger controlla di nuovo la presenza di dati dopo la ricorrenza definita. | 
|||| 

Ecco gli output del trigger HTTP: 
  
| Nome dell'elemento | Tipo | Descrizione |
| ------------ | ---- | ----------- |
| headers | Oggetto | Intestazioni della risposta HTTP | 
| body | Oggetto | Corpo della risposta HTTP | 
|||| 

## <a name="api-connection-trigger"></a>Trigger ApiConnection  

Il trigger ApiConnection è simile al trigger HTTP dal punto di vista della funzionalità di base. I parametri per identificare l'azione sono tuttavia diversi. Di seguito è fornito un esempio:  
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| host | Sì | Oggetto | Gateway ospitato e ID per l'app per le API | 
| statico | Sì | String | Usare uno di questi metodi HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD" | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
| retryPolicy | No | Oggetto | Usare questo oggetto per personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md). | 
| authentication | No | Oggetto | Rappresenta il metodo che deve essere usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Ecco le proprietà per l'oggetto `host`:  
  
| Nome dell'elemento | Obbligatorio | Descrizione | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | Sì | Endpoint per l'API gestita | 
| connection name |  | Nome della connessione API gestita usata dal flusso di lavoro. Deve fare riferimento a un parametro denominato `$connection`. |
|||| 

Ecco gli output per un trigger ApiConnection:
  
| Nome dell'elemento | Tipo | Descrizione |
| ------------ | ---- | ----------- |
| headers | Oggetto | Intestazioni della risposta HTTP | 
| body | Oggetto | Corpo della risposta HTTP | 
|||| 
  
## <a name="httpwebhook-trigger"></a>Trigger HTTPWebhook  

Il trigger HTTPWebhook fornisce un endpoint, analogamente al trigger Request, ma il trigger HTTPWebhook chiama anche un URL specificato per la registrazione e l'annullamento della registrazione. Ecco un esempio di come si presenta un trigger HTTPWebhook:  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

Molte di queste sezioni sono facoltative e il comportamento del trigger HTTPWebhook dipende dalle sezioni specificate o omesse. Ecco le proprietà per il trigger HTTPWebhook:
  
| Nome dell'elemento | Obbligatorio | Descrizione | 
| ------------ | -------- | ----------- |  
| subscribe | No | Specifica la richiesta in uscita da chiamare quando viene creato il trigger ed esegue la registrazione iniziale. | 
| unsubscribe | No | Specifica la richiesta in uscita da chiamare quando viene eliminato il trigger. | 
|||| 

È possibile specificare i limiti per un'azione webhook allo stesso modo dei [limiti asincroni HTTP](#asynchronous-limits). Ecco altre informazioni sulle azioni `subscribe` e `unsubscribe`:

* L'azione `subscribe` viene chiamata perché il trigger possa iniziare a essere in ascolto degli eventi. Questa chiamata in uscita inizia con gli stessi parametri delle azioni HTTP standard. Questa chiamata viene effettuata ogni volta che il flusso di lavoro viene modificato, ad esempio quando vengono aggiornate le credenziali o cambiano i parametri di input del trigger. 
  
  Per supportare la chiamata, la funzione `@listCallbackUrl()` restituisce un URL univoco per questo trigger specifico nel flusso di lavoro. Questo URL rappresenta l'identificatore univoco per gli endpoint che usano l'API REST del servizio.
  
* L'azione `unsubscribe` viene chiamata automaticamente quando un'operazione, tra cui quelle seguenti, rende il trigger non valido:

  * Eliminazione o disabilitazione del trigger. 
  * Eliminazione o disabilitazione del flusso di lavoro. 
  * Eliminazione o disabilitazione della sottoscrizione. 
  
  I parametri per questa funzione sono gli stessi del trigger HTTP.

Ecco gli output del trigger HTTPWebhook e il contenuto della richiesta in ingresso:
  
| Nome dell'elemento | Tipo | Descrizione |
| ------------ | ---- | ----------- |
| headers | Oggetto | Intestazioni della risposta HTTP | 
| body | Oggetto | Corpo della risposta HTTP | 
|||| 

## <a name="conditions"></a>Condizioni  

Per qualsiasi trigger, è possibile usare una o più condizioni per determinare se il flusso di lavoro deve essere eseguito o meno. Ad esempio:  

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

In questo caso, il report viene attivato solo mentre il parametro `sendReports` del flusso di lavoro è impostato su true. Le condizioni infine possono fare riferimento al codice di stato del trigger. È possibile, ad esempio, avviare un flusso di lavoro solo quando il sito Web restituisce un codice di stato 500, ad esempio:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> Quando un'espressione fa riferimento a un codice di stato del trigger, il comportamento predefinito, ovvero l'attivazione solo con il codice 200 ("OK"), viene sostituito. Se, ad esempio, si vuole eseguire l'attivazione sia con il codice di stato 200 che con il codice di stato 201, è necessario includere `@or(equals(triggers().code, 200),equals(triggers().code,201))` come condizione.
  
## <a name="start-multiple-runs-for-a-request"></a>Avviare più esecuzioni per una richiesta

Per avviare più esecuzioni per una singola richiesta, `splitOn` è utile, ad esempio, quando si vuole eseguire il polling di un endpoint che può avere più elementi nuovi tra gli intervalli di polling.
  
Con `splitOn`, si specifica la proprietà nel payload della risposta contenente le matrici di elementi, ognuno dei quali verrà usato per avviare un'esecuzione del trigger. Ad esempio, si supponga un'API che restituisce questa risposta:  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
L'app per la logica richiede solo il contenuto di `rows`, in modo da poter costruire il trigger come in questo esempio:  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> Se si usa il comando `SplitOn`, non è possibile ottenere le proprietà esterne alla matrice. Di conseguenza, in questo esempio non è possibile ottenere la proprietà `status` nella risposta restituita dall'API.
> In questo esempio, inoltre, viene usato l'operatore `?` per evitare un errore se la proprietà `rows` non è presente. 

Di conseguenza, nella definizione del flusso di lavoro `@triggerBody().name` restituisce `myFirstRow` per la prima esecuzione e `mySecondRow` per la seconda esecuzione. Gli output del trigger sono simili a questo esempio:  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>Istanza di esecuzione singola

È possibile configurare i trigger di ricorrenza perché vengano attivati solo dopo il completamento di tutte le esecuzioni attive. Se una ricorrenza pianificata avviene durante l'esecuzione dell'istanza del flusso di lavoro, il trigger viene ignorato e attende fino al successivo intervallo di ricorrenza pianificata prima di riprovare.
Per configurare questa impostazione, impostare la proprietà `operationOptions` su `singleInstance`:

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>Panoramica delle azioni

Esistono molti tipi di azioni, ognuna con un comportamento univoco. Ogni tipo di azione ha input diversi che ne definiscono il comportamento. Le azioni di raccolta possono contenere molte altre azioni. 

### <a name="standard-actions"></a>Azioni standard  

| Tipo di azione | Descrizione | 
| ----------- | ----------- | 
| **HTTP** | Chiama un endpoint Web HTTP. | 
| **ApiConnection**  | Funziona come l'azione HTTP, ma usa [API gestite da Microsoft](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Funziona come HTTPWebhook, ma usa API gestite da Microsoft. | 
| **Risposta** | Definisce la risposta per una chiamata in ingresso. | 
| **Funzione** | Rappresenta una funzione di Azure. | 
| **Wait** | Attende per un intervallo di tempo fisso o fino a un momento specifico. | 
| **Workflow** | Rappresenta un flusso di lavoro annidato. | 
| **Componi** | Costruisce un oggetto arbitrario dagli input dell'azione. | 
| **Query** | Filtra una matrice in base a una condizione. | 
| **Select** | Proietta ogni elemento di una matrice in un nuovo valore. Ad esempio, è possibile convertire una matrice di numeri in una matrice di oggetti. | 
| **Tabella** | Converte una matrice di elementi in tabella CSV o HTML. | 
| **Terminate** | Arresta l'esecuzione di un flusso di lavoro. | 
||| 

### <a name="collection-actions"></a>Azioni di raccolta

| Tipo di azione | Descrizione | 
| ----------- | ----------- | 
| **Condition** | Valuta un'espressione e, in base al risultato, esegue il ramo corrispondente. | 
| **Ambito** | Da usare per il raggruppamento logico di altre azioni. | 
| **ForEach** | Questa azione di esecuzione a ciclo continuo scorre una matrice ed esegue azioni interne per ogni elemento. | 
| **Until** | Questa azione di esecuzione a ciclo continuo esegue azioni interne finché una condizione non restituisce true. | 
||| 

## <a name="http-action"></a>Azione HTTP  

Le azioni HTTP chiamano un endpoint specificato e controllano la risposta per determinare se il flusso di lavoro deve essere eseguito. ad esempio:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

In questo caso, l'oggetto `inputs` accetta i parametri necessari per la costruzione di una chiamata HTTP: 

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| statico | Sì | String | Usare uno di questi metodi HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD" | 
| Uri | Sì| String | Endpoint HTTP o HTTPS controllato dal trigger. Dimensione massima della stringa: 2 KB | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
| retryPolicy | No | Oggetto | Usare questo oggetto per personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | String | Definisce il set di comportamenti speciali di cui eseguire l'override. | 
| authentication | No | Oggetto | Rappresenta il metodo che deve essere usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). <p>Oltre all'Utilità di pianificazione, è supportata un'altra proprietà: `authority`. Per impostazione predefinita, questo valore è `https://login.windows.net` quando non è specificato, ma è possibile usare un valore diverso, ad esempio `https://login.windows\-ppe.net`. | 
||||| 

Questa azione HTTP di esempio riprova il recupero delle ultime notizie per due volte, se si verificano errori intermittenti, per un totale di tre esecuzioni e con un ritardo di 30 secondi tra un tentativo e l'altro:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

L'intervallo tra i tentativi viene specificato nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Questo intervallo ha un valore predefinito e minimo di 20 secondi, mentre il valore massimo è di un'ora. Il numero predefinito e massimo di tentativi è pari a quattro ore. Se non si specifica la definizione dei criteri per i tentativi, viene usata una strategia `fixed` con valori predefiniti per l'intervallo e il numero di tentativi. Per disabilitare i criteri per i tentativi, impostare il tipo su `None`.

### <a name="asynchronous-patterns"></a>Modelli asincroni

Per impostazione predefinita, tutte le azioni basate su HTTP supportano il modello di operazione asincrono standard. Di conseguenza, se il server remoto indica che la richiesta viene accettata per l'elaborazione con una risposta "202 ACCEPTED", il motore di App per la logica continua il polling dell'URL specificato nell'intestazione location della risposta fino a raggiungere uno stato terminale, che corrisponde a una risposta diversa da 202.
  
Per disabilitare il comportamento asincrono descritto in precedenza, impostare `operationOptions` su `DisableAsyncPattern` negli input dell'azione. In questo caso, l'output dell'azione è basato sulla risposta 202 iniziale dal server. ad esempio:
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```
<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Limiti asincroni

È possibile limitare la durata di un modello asincrono a un intervallo di tempo specifico. Se l'intervallo di tempo trascorre senza raggiungere uno stato terminale, lo stato dell'azione viene contrassegnato come `Cancelled` con il codice `ActionTimedOut`. Il timeout per il limite è specificato nel formato ISO 8601. È possibile specificare i limiti in questo modo:

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Azione APIConnection

L'azione APIConnection fa riferimento a un connettore gestito da Microsoft. Questa azione richiede un riferimento a una connessione valida e informazioni sull'API e sui parametri.
Ecco un esempio di azione APIConnection:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| host | Sì | Oggetto | Rappresenta le informazioni sul connettore, ad esempio `runtimeUrl`, e il riferimento all'oggetto connection. | 
| statico | Sì | String | Usare uno di questi metodi HTTP: "GET", "POST", "PUT", "DELETE", "PATCH" o "HEAD" | 
| path | Sì | String | Percorso per l'operazione API | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
| retryPolicy | No | Oggetto | Usare questo oggetto per personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | String | Definisce il set di comportamenti speciali di cui eseguire l'override. | 
| authentication | No | Oggetto | Rappresenta il metodo che deve essere usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="apiconnection-webhook-action"></a>Azione webhook APIConnection

L'azione APIConnectionWebhook fa riferimento a un connettore gestito da Microsoft. Questa azione richiede un riferimento a una connessione valida e informazioni sull'API e sui parametri. È possibile specificare i limiti per un'azione webhook allo stesso modo dei [limiti asincroni HTTP](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| host | Sì | Oggetto | Rappresenta le informazioni sul connettore, ad esempio `runtimeUrl`, e il riferimento all'oggetto connection. | 
| path | Sì | String | Percorso per l'operazione API | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
| retryPolicy | No | Oggetto | Usare questo oggetto per personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | No | String | Definisce il set di comportamenti speciali di cui eseguire l'override. | 
| authentication | No | Oggetto | Rappresenta il metodo che deve essere usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Azione di risposta  

Questa azione contiene l'intero payload da una richiesta HTTP e include `statusCode`, `body`, e `headers`:
  
```json
"myResponseAction": {
    "type": "response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

All'azione di risposta si applicano particolari restrizioni che non si applicano ad altre azioni, in particolare:  
  
* Possono essere presenti azioni di risposta nei rami paralleli all'interno di una definizione di app per la logica perché la richiesta in ingresso richiede una risposta deterministica.
  
* Se il flusso di lavoro raggiunge un'azione di risposta dopo che la richiesta in ingresso ha già ricevuto una risposta, l'azione di risposta viene considerata non riuscita o in conflitto. Di conseguenza, l'esecuzione dell'app per la logica viene contrassegnata come `Failed`.
  
* Un flusso di lavoro con azioni di risposta non può usare il comando `splitOn` nella definizione del trigger perché la chiamata crea più esecuzioni. Di conseguenza, verificare se si tratta di questo caso quando l'operazione del flusso di lavoro è PUT e viene restituita una risposta di richiesta non valida.

## <a name="function-action"></a>Azione delle funzioni   

Questa azione consente di rappresentare e chiamare una [funzione di Azure](../azure-functions/functions-overview.md), ad esempio:

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```
| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- |  
| function id | Sì | String | ID risorsa per la funzione di Azure che si vuole chiamare. | 
| statico | No | String | Metodo HTTP usato per chiamare la funzione. Se non è specificato, "POST" è il metodo predefinito. | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
|||||

Quando si salva l'app per la logica, App per la logica di Azure esegue controlli sulla funzione cui si fa riferimento:

* È necessario avere accesso alla funzione.
* È possibile usare solo trigger HTTP standard o trigger webhook JSON generici.
* Per la funzione non deve essere definita alcuna route.
* Sono consentiti solo i livelli di autorizzazione "funzione" e "anonimo".

L'URL del trigger viene recuperato, memorizzato nella cache e usato durante il runtime. Pertanto, se qualsiasi operazione invalida l'URL memorizzato nella cache, l'azione non riesce durante il runtime. Per risolvere questo problema, salvare di nuovo l'app per la logica in modo che possa di nuovo recuperare e memorizzare nella cache l'URL del trigger.

## <a name="wait-action"></a>Azione wait  

Questa azione sospende l'esecuzione del flusso di lavoro per l'intervallo specificato. Questo esempio fa sì che il flusso di lavoro attenda per 15 minuti:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
In alternativa, per attendere fino a un determinato momento, è possibile usare questo esempio:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> L'attesa può essere specificata usando l'oggetto `until` o `interval`, ma non entrambi.
  
| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- | 
| until | No | Oggetto | Attesa in base a una data e un'ora | 
| until timestamp | Sì | String | Data e ora in [formato UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) in corrispondenza delle quali termina l'attesa | 
| interval | No | Oggetto | Attesa in base all'unità e al valore dell'intervallo | 
| interval unit | Sì | String | Unità di tempo. Usare solo uno di questi valori: "second", "minute", "hour", "day", "week" o "month" | 
| interval count | Sì | Integer | Numero intero positivo che rappresenta il numero di unità dell'intervallo usate per l'attesa | 
||||| 

## <a name="workflow-action"></a>Azione workflow   

Questa azione rappresenta un altro flusso di lavoro. App per la logica esegue un controllo di accesso nel flusso di lavoro o, più in particolare, nel trigger e questo significa che è necessario avere accesso al flusso di lavoro.

Gli output di questa azione sono basati su quanto definito nell'azione `response` per il flusso di lavoro figlio. Se non è stata definita un'azione `response`, gli output sono vuoti.

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione | 
| ------------ | -------- | ---- | ----------- |  
| host id | Sì | String| ID risorsa per il flusso di lavoro che si vuole chiamare | 
| host triggerName | Sì | String | Nome del trigger che si vuole richiamare | 
| query | No | Oggetto | Rappresenta tutti i parametri di query che si vuole includere nell'URL. <p>`"queries": { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL. | 
| headers | No | Oggetto | Rappresenta ogni intestazione inviata nella richiesta. <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | No | Oggetto | Rappresenta il payload inviato all'endpoint. | 
|||||   

## <a name="compose-action"></a>Azione compose

Questa azione consente di costruire un oggetto arbitrario e l'output è il risultato della valutazione degli input dell'azione. 

> [!NOTE]
> È possibile usare l'azione `Compose` per costruire qualsiasi output, tra cui oggetti, matrici e altri tipi supportati in modo nativo dalle app per la logica, come file XML e binari.

Ad esempio, è possibile usare l'azione compose per unire output da più azioni:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="select-action"></a>Seleziona azione

Questa azione consente di proiettare ogni elemento di una matrice in un nuovo valore.
Ad esempio, per convertire un array di numeri in un array di oggetti è possibile usare:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| from | Sì | Array | Matrice di origine |
| seleziona | Sì | Qualsiasi | Proiezione applicata a ogni elemento nella matrice di origine |
||||| 

L'output dell'azione `select` è una matrice con la stessa cardinalità della matrice di input. Ogni elemento viene trasformato come definito dalla proprietà `select`. Se l'input è un array vuoto, lo sarà anche l'output.

## <a name="query-action"></a>Azione di query

Questa azione consente di filtrare una matrice in base a una condizione. Questo esempio seleziona i numeri maggiori di 2:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

L'output dell'azione `query` è una matrice con elementi della matrice di input che soddisfano la condizione.

> [!NOTE]
> Se nessun valore soddisfa la condizione `where`, il risultato è una matrice vuota.

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| from | Sì | Array | Matrice di origine |
| dove | Sì | String | Condizione applicata a ogni elemento della matrice di origine |
||||| 

## <a name="table-action"></a>azione Tabella

Questa azione consente di convertire una matrice di elementi in una tabella **CSV** o **HTML**. Ad esempio, si supponga di avere un oggetto `@triggerBody()` con questa matrice:

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

E di voler definire un'azione Table come in questo esempio:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

Il risultato di questo esempio sarà simile a questa tabella HTML: 

<table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>0</td><td>mele</td></tr><tr><td>1</td><td>arance</td></tr></tbody></table>

Per personalizzare la tabella, è possibile specificare le colonne in modo esplicito, ad esempio:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Il risultato di questo esempio sarà simile a questa tabella HTML: 

<table><thead><tr><th>Produce ID</th><th>Descrizione</th></tr></thead><tbody><tr><td>0</td><td>mele fresche</td></tr><tr><td>1</td><td>arance fresche</td></tr></tbody></table>

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| from | Sì | Array | Matrice di origine. Se il valore della proprietà `from` è un array vuoto, l'output sarà una tabella vuota. | 
| format | Sì | String | Il formato della tabella desiderato, **CSV** o **HTML** | 
| columns | No | Array | Colonne di tabella desiderate. Da usare per eseguire l'override della forma di tabella predefinita. | 
| intestazione di colonna | No | String | Intestazione di colonna | 
| valore colonna | Sì | String | Valore della colonna | 
||||| 

## <a name="terminate-action"></a>Azione terminate

Questa azione arresta l'esecuzione del flusso di lavoro, annulla tutte le azioni in fase di elaborazione e ignora tutte le azioni rimanenti. L'azione Terminate non influisce sulle azioni completate.

Ad esempio, per arrestare un'esecuzione con stato "Failed", è possibile usare questo esempio:

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Sì | String | Stato dell'esecuzione di destinazione, che può essere `Failed` o `Cancelled` |
| runError | No | Oggetto | Dettagli dell'errore. Supportato solo quando `runStatus` è impostato su `Failed`. |
| runError code | No | String | Codice di errore dell'esecuzione |
| runError message | No | String | Messaggio di errore dell'esecuzione |
||||| 

## <a name="collection-actions-overview"></a>Panoramica delle azioni di raccolta

Alcune azioni possono contenere altre azioni al loro interno. È possibile fare riferimento alle azioni di riferimento in una raccolta direttamente all'esterno della raccolta. Ad esempio, se si definisce `Http` in un oggetto `scope`, `@body('http')` è ancora valido in qualsiasi punto nel flusso di lavoro. Una raccolta `runAfter` può includere azioni solo con altre azioni nella stessa raccolta.

## <a name="condition-if-action"></a>Condition: azione If

Questa azione consente di valutare una condizione e di eseguire un ramo in base al caso in cui l'espressione restituisce o meno `true`. 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| Azioni | Sì | Oggetto | Azioni interne da eseguire quando `expression` restituisce `true` | 
| expression | Sì | String | Espressione da valutare |
| else | No | Oggetto | Azioni interne da eseguire quando `expression` restituisce `false` |
||||| 

Se una condizione restituisce un valore corretto, viene contrassegnata come `Succeeded`. Le azioni nell'oggetto `actions` o `else` restituiscono: 

* `Succeeded` quando vengono eseguite e riescono
* `Failed` quando vengono eseguite e non riescono
* `Skipped` quando il rispettivo ramo non viene eseguito

Ecco alcuni esempi che mostrano in che modo le condizioni possono usare espressioni in un'azione:
  
| Valore JSON | Risultato | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | Tutti i valori che restituiscono true fanno sì che questa condizione passi. Supporta solo espressioni booleane. Per convertire altri tipi in valori booleani, usare le funzioni `empty` e `equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | Supporta le funzioni di confronto. Per questo esempio, l'azione viene eseguita solo quando l'output di `act1` è maggiore della soglia. | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | Supporta le funzioni logiche per la creazione di espressioni booleane annidate. Per questo esempio, l'azione viene eseguita quando l'output di `act1` è maggiore della soglia o minore di 100. | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | Per controllare se una matrice contiene elementi, è possibile usare funzioni di matrice. Per questo esempio, l'azione viene eseguita quando la matrice `errors` è vuota. | 
| `"expression": "parameters('hasSpecialAction')"` | Errore: condizione non valida perché @ è obbligatorio per le condizioni. |  
|||

## <a name="scope-action"></a>Azione scope

Questa azione consente di raggruppare logicamente le azioni in un flusso di lavoro.

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- |  
| Azioni | Sì | Oggetto | Azioni interne da eseguire all'interno dell'ambito |
||||| 

## <a name="foreach-action"></a>Azione ForEach

Questa azione di esecuzione a ciclo continuo scorre una matrice ed esegue azioni interne per ogni elemento. Per impostazione predefinita, il ciclo `foreach` viene eseguito in parallelo e può eseguire 20 esecuzioni in parallelo contemporaneamente. Per impostare regole di esecuzione, usare il parametro `operationOptions`.

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| Azioni | Sì | Oggetto | Azioni interne da eseguire all'interno del ciclo | 
| foreach | Sì | String | Matrice da scorrere | 
| operationOptions | No | String | Specifica le opzioni dell'operazione per la personalizzazione del comportamento. Attualmente supporta solo `Sequential` per l'esecuzione in sequenza di iterazioni in cui il comportamento predefinito è parallelo. |
||||| 

## <a name="until-action"></a>Azione Until

Questa azione di esecuzione a ciclo continuo esegue azioni interne finché una condizione non restituisce true.

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Nome | Obbligatoria | Tipo | Descrizione | 
| ---- | -------- | ---- | ----------- | 
| Azioni | Sì | Oggetto | Azioni interne da eseguire all'interno del ciclo | 
| expression | Sì | String | Espressione da valutare dopo ogni iterazione | 
| limit | Sì | Oggetto | Limiti per il ciclo. Deve definire almeno un limite. | 
| count | No | Integer | Limite al numero di iterazioni da eseguire | 
| timeout | No | String | Limite di timeout in [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) che specifica per quanto tempo deve essere eseguito il ciclo |
||||| 

## <a name="next-steps"></a>Passaggi successivi

* [Linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md)
* [API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
