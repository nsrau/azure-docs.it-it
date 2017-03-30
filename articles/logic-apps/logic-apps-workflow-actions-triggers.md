---
title: 'Azioni e trigger del flusso di lavoro: App per la logica di Azure | Microsoft Docs'
description: 
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
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 3a240ff317e1b3ea450703965629c08053668856
ms.lasthandoff: 03/22/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Azioni e trigger del flusso di lavoro per App per la logica di Azure

Le app per la logica sono costituite da trigger e da azioni. Sono disponibili sei tipi di trigger. Ogni tipo ha un'interfaccia e un comportamento diversi. Per altre informazioni dettagliate, vedere il [linguaggio di definizione del flusso di lavoro](logic-apps-workflow-definition-language.md).  
  
Per altre informazioni sui trigger e sulle azioni e su come usarli per compilare app per la logica per migliorare i processi e i flussi di lavoro aziendali, continuare la lettura.  
  
### <a name="triggers"></a>Trigger  

Un trigger specifica le chiamate che possono avviare un'esecuzione del flusso di lavoro delle app per la logica. Di seguito sono indicati i due diversi modi per avviare un'esecuzione del flusso di lavoro:  
  
-   Un trigger di poll  

-   Un trigger di push, chiamando l'[API REST del servizio di flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Tutti i trigger contengono questi elementi di livello superiore:  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>Tipi e input dei trigger  

È possibile usare questi tipi di trigger:
  
-   **Request** \- Imposta l'app per la logica come endpoint per le chiamate  
  
-   **Recurrence** \- Esegue l'attivazione in base a una pianificazione definita  
  
-   **HTTP** \- Esegue il polling di un endpoint Web HTTP. L'endpoint HTTP deve essere conforme a uno specifico contratto di attivazione, usando un modello asincrono 202 o restituendo una matrice  
  
-   **ApiConnection** \- Esegue il polling come il trigger HTTP, ma sfrutta le [API gestite da Microsoft](https://docs.microsoft.com/azure/connectors/apis-list)  
  
-   **HTTPWebhook** \- Apre un endpoint, analogamente al trigger Manual, ma chiama anche un URL specificato per la registrazione e l'annullamento della registrazione  
  
-   **ApiConnectionWebhook** \- Funziona come il trigger HTTPWebhook, sfruttando le API gestite da Microsoft       
    Ogni tipo di trigger ha un diverso set di **input** che ne definisce il comportamento.  
  
## <a name="request-trigger"></a>Trigger di richiesta  

Questo trigger funge da endpoint chiamato tramite una richiesta HTTP per richiamare l'app per la logica. Un trigger request è simile a questo esempio:  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

Esiste anche una proprietà facoltativa denominata **schema**:  
  
|Nome dell'elemento|Obbligatorio|Descrizione|  
|----------------|------------|---------------|  
|schema|No|Schema JSON che convalida la richiesta in ingresso. È utile per indicare le proprietà a cui fare riferimento nei passaggi successivi del flusso di lavoro.|

Per richiamare questo endpoint, è necessario chiamare l'API *listCallbackUrl*. Vedere [Workflow Service REST API](https://docs.microsoft.com/rest/api/logic/workflows) (API REST del servizio di flusso di lavoro).  
  
## <a name="recurrence-trigger"></a>Trigger Recurrence  

Un trigger Recurrence viene eseguito in base a una pianificazione definita. Tale trigger può essere simile a questo esempio:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Come si può osservare, è un modo semplice per eseguire un flusso di lavoro.  
  
|Nome dell'elemento|Obbligatorio|Descrizione|  
|----------------|------------|---------------|  
|frequency|Sì|Frequenza con cui viene eseguito il trigger. Usare solo uno di questi possibili valori: second, minute, hour, day, week, month o year|  
|interval|Sì|Intervallo della frequenza specificata per la ricorrenza|  
|startTime|No|Se viene fornito un valore startTime senza una differenza dall'ora UTC, viene usato tale valore timeZone.|  
|timeZone|no|Se viene fornito un valore startTime senza una differenza dall'ora UTC, viene usato tale valore timeZone.|  
  
È anche possibile pianificare l'avvio dell'esecuzione di un trigger in futuro. Se, ad esempio, si vuole avviare un report settimanale ogni lunedì, è possibile pianificare l'avvio dell'app per la logica ogni lunedì creando il trigger seguente:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>Trigger HTTP  

I trigger HTTP eseguono il polling di un endpoint specificato e controllano la risposta per determinare se il flusso di lavoro deve essere eseguito. L'oggetto inputs usa il set di parametri necessari per costruire una chiamata HTTP:  
  
|Nome dell'elemento|Obbligatorio|Descrizione|Tipo|  
|----------------|------------|---------------|--------|  
|statico|sì|Può essere uno dei metodi HTTP seguenti: GET, POST, PUT, DELETE, PATCH o HEAD|String|  
|Uri|sì|Endpoint http o https che viene chiamato. Il valore massimo è di 2 kilobyte.|String|  
|query|No|Oggetto che rappresenta i parametri di query da aggiungere all'URL. `"queries" : { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL.|Oggetto|  
|headers|No|Oggetto che rappresenta ogni intestazione inviata alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|Oggetto|  
|body|No|Oggetto che rappresenta il payload inviato all'endpoint.|Oggetto|  
|retryPolicy|No|Oggetto che consente di personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx.|Oggetto|  
|authentication|No|Rappresenta il metodo con cui autenticare la richiesta. Per informazioni dettagliate su questo oggetto, vedere [Autenticazione in uscita dell'Utilità di pianificazione](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Oltre all'utilità di pianificazione, esiste un'altra proprietà supportata: `authority`. Per impostazione predefinita, questo valore è `https://login.windows.net` quando non viene specificato, ma è possibile usare un gruppo di destinatari diverso, ad esempio `https://login.windows\-ppe.net`|Oggetto|  
  
Con il trigger HTTP, l'API HTTP deve essere conforme a un modello specifico per poter interagire correttamente con l'app per la logica. Sono necessari i campi seguenti:  
  
|Response|Descrizione|  
|------------|---------------|  
|Codice di stato|Il codice di stato 200 \(OK\) attiva un'esecuzione. Nessun altro codice di stato attiva un'esecuzione.|  
|Intestazione Retry\-after|Numero di secondi prima che l'app per la logica esegua di nuovo il polling dell'endpoint.|  
|Intestazione Location|URL da chiamare al successivo intervallo di polling. Se non è specificato, viene usato l'URL originale.|  
  
Di seguito sono riportati alcuni esempi di comportamenti diversi a seconda del tipo di richiesta:  
  
|Codice della risposta|Retry\-After|Comportamento|  
|-----------------|----------------|------------|  
|200|\(nessuna\)|Trigger non valido. Retry\-After è obbligatorio, altrimenti il motore non esegue mai il polling per la richiesta successiva.|  
|202|60|Non attiva il flusso di lavoro. Verrà eseguito un altro tentativo entro un minuto.|  
|200|10|Esegue il flusso di lavoro e verifica di nuovo la disponibilità di altri contenuti entro 10 secondi.|  
|400|\(nessuna\)|Richiesta non valida, non esegue il flusso di lavoro. Se non è definito alcun **criterio per i tentativi**, vengono usati i criteri predefiniti. Dopo che è stato raggiunto il numero di tentativi, il trigger non è più valido.|  
|500|\(nessuna\)|Errore del server, non esegue il flusso di lavoro.  Se non è definito alcun **criterio per i tentativi**, vengono usati i criteri predefiniti. Dopo che è stato raggiunto il numero di tentativi, il trigger non è più valido.|  
  
Gli output di un trigger HTTP sono simili a questo esempio:  
  
|Nome dell'elemento|Descrizione|Tipo|  
|----------------|---------------|--------|  
|headers|Intestazioni della risposta http.|Oggetto|  
|body|Corpo della risposta http.|Oggetto|  
  
## <a name="api-connection-trigger"></a>Trigger ApiConnection  

Il trigger ApiConnection è simile al trigger HTTP dal punto di vista della funzionalità di base. I parametri per identificare l'azione sono tuttavia diversi. Di seguito è fornito un esempio:  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
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

|Nome dell'elemento|Obbligatoria|Tipo|Descrizione|  
|----------------|------------|--------|---------------|  
|host|Sì||ID e gateway ospitato da ApiApp.|  
|statico|Sì|String|Può essere uno dei metodi HTTP seguenti: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**|  
|query|No|Oggetto|Rappresenta i parametri di query da aggiungere all'URL. `"queries" : { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL.|  
|headers|No|Oggetto|Rappresenta ogni intestazione inviata alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|No|Oggetto|Rappresenta il payload inviato all'endpoint.|  
|retryPolicy|No|Oggetto|Consente di personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx.|  
|authentication|No|Oggetto|Rappresenta il metodo con cui autenticare la richiesta. Per informazioni dettagliate su questo oggetto, vedere [Autenticazione in uscita dell'Utilità di pianificazione](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)|  
  
Le proprietà dell'host sono:  
  
|Nome dell'elemento|Obbligatorio|Descrizione|  
|----------------|------------|---------------|  
|api runtimeUrl|Sì|Endpoint dell'API gestita.|  
|connection name||Deve essere un riferimento a un parametro denominato `$connection` ed è il nome della connessione API gestita usata dal flusso di lavoro.|
  
Gli output di un trigger ApiConnection sono:
  
|Nome dell'elemento|Tipo|Descrizione|  
|----------------|--------|---------------|  
|headers|Oggetto|Intestazioni della risposta http.|  
|body|Oggetto|Corpo della risposta http.|  
  
## <a name="httpwebhook-trigger"></a>Trigger HTTPWebhook  

Il trigger HTTPWebhook apre un endpoint, analogamente al trigger manual, ma il trigger HTTPWebhook chiama anche un URL specificato per la registrazione e l'annullamento della registrazione. Ecco un esempio di come si presenta un trigger HTTPWebhook:  

```json
"myappspottrigger": {
    "type": "httpWebhook",
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

Molte di queste sezioni sono facoltative e il comportamento del webhook dipende dalle sezioni che vengono fornite o omesse.  
Le proprietà di un webhook sono le seguenti:  
  
|Nome dell'elemento|Obbligatorio|Descrizione|  
|----------------|------------|---------------|  
|subscribe|No|Richiesta in uscita chiamata quando il trigger viene creato ed esegue la registrazione iniziale.|  
|unsubscribe|No|Richiesta in uscita quando il trigger viene eliminato.|  
  
-   **Subscribe** è la chiamata in uscita effettuata per iniziare ad ascoltare gli eventi. Questa chiamata inizia con lo stesso set di parametri delle normali azioni HTTP. Questa chiamata in uscita viene effettuata ogni volta che il flusso di lavoro viene modificato, ad esempio quando vengono aggiornate le credenziali o cambiano i parametri di input del trigger.
  
    Per supportare questa chiamata, è disponibile una nuova funzione: `@listCallbackUrl()`. Questa funzione restituisce un URL univoco per il trigger specifico nel flusso di lavoro. Rappresenta l'identificatore univoco per gli endpoint che usano l'API REST del servizio.  
  
-   **Unsubscribe** viene chiamata quando un'operazione rende questo trigger non valido, tra cui:  
  
    -   Eliminazione o disabilitazione del trigger  
  
    -   Eliminazione o disabilitazione del flusso di lavoro  
  
    -   Eliminazione o disabilitazione della sottoscrizione  
  
    L'app per la logica chiama automaticamente l'azione unsubscribe. I parametri di questa funzione sono gli stessi del trigger HTTP.  
  
    Gli output del trigger HTTPWebhook sono i contenuti della richiesta in ingresso:  
  
|Nome dell'elemento|Tipo|Descrizione|  
|-----------------|--------|---------------|  
|headers|Oggetto|Intestazioni della richiesta http.|  
|body|Oggetto|Corpo della richiesta http.|  

I limiti per un'azione webhook possono essere specificati nello stesso modo dei [limiti asincroni HTTP](#asynchronous-limits).
  

## <a name="conditions"></a>Condizioni  

Per qualsiasi trigger, è possibile usare una o più condizioni per determinare se il flusso di lavoro deve essere eseguito o meno. Ad esempio:  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

In questo caso, il report viene attivato solo mentre il parametro `sendReports` del flusso di lavoro è impostato su true. Le condizioni infine possono fare riferimento al codice di stato del trigger. È possibile, ad esempio, avviare un flusso di lavoro solo quando il sito Web restituisce un codice di stato 500, come indicato di seguito:
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> Quando un'espressione fa riferimento al codice di stato del trigger \(in qualsiasi modo\), il comportamento predefinito \(attivazione solo con 200 \(OK\)\) viene sostituito. Se, ad esempio, si vuole eseguire l'attivazione sia con il codice di stato 200 che con il codice di stato 201, è necessario includere `@or(equals(triggers().code, 200),equals(triggers().code,201))` come condizione.  
  
## <a name="start-multiple-runs-for-a-request"></a>Avviare più esecuzioni per una richiesta

Per avviare più esecuzioni per una singola richiesta, `splitOn` è utile, ad esempio, quando si vuole eseguire il polling di un endpoint che può avere più elementi nuovi tra gli intervalli di polling.
  
Con `splitOn`, si specifica la proprietà nel payload della risposta contenente le matrici di elementi, ognuno dei quali verrà usato per avviare un'esecuzione del trigger. Si consideri ad esempio uno scenario in cui un'API restituisce la risposta seguente:  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
L'app per la logica richiede solo il contenuto di Rows, quindi è possibile costruire il trigger come in questo esempio:  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
Nella definizione del flusso di lavoro `@triggerBody().name` restituisce quindi `mycoolrow` per la prima esecuzione e `another row` per la seconda esecuzione. Gli output del trigger sono simili a questo esempio:  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

Se quindi si usa `SplitOn`, non è possibile ottenere le proprietà esterne alla matrice, in questo caso il campo `Status`.  
  
> [!NOTE]  
> In questo esempio viene usato l'operatore `?` per poter evitare un errore se la proprietà `Rows` non è presente. 
  
## <a name="single-run-instance"></a>Istanza di esecuzione singola

È possibile configurare trigger con una proprietà recurrence in modo che vengano attivati solo se tutte le esecuzioni attive sono state completate. Se una ricorrenza pianificata si verifica mentre è in corso un'esecuzione, il trigger viene ignorato e attende fino al successivo intervallo di ricorrenza pianificata prima di riprovare.

È possibile configurate questa impostazione tramite le opzioni dell'operazione:

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>Tipi e input  

Esistono molti tipi di azioni, ognuna con un comportamento univoco. Le azioni di raccolta possono contenere diverse altre azioni.

### <a name="standard-actions"></a>Azioni standard  

-   **HTTP** Questa azione chiama un endpoint Web HTTP.  
  
-   **ApiConnection** \- Questa azione si comporta come l'azione HTTP, ma usa le API gestite da Microsoft.  
  
-   **ApiConnectionWebhook** \- Come HTTPWebhook, ma usa le API gestite da Microsoft.  
  
-   **Response** \- Questa azione definisce una risposta per una chiamata in ingresso.  
  
-   **Wait** \- Questa semplice azione attende per un intervallo di tempo fisso o fino a un'ora specifica.  
  
-   **Workflow** \- Questa azione rappresenta un flusso di lavoro annidato.  

### <a name="collection-actions"></a>Azioni di raccolta

-   **Scope** \- Questa azione è un raggruppamento logico di altre azioni.

-   **Condition** \- Questa azione restituisce un'espressione ed esegue il corrispondente ramo di risultati.

-   **ForEach** \- Questa azione di riproduzione a ciclo continuo scorre una matrice ed esegue azioni interne per ogni elemento.

-   **Until** \- Questa azione di riproduzione a ciclo continuo esegue azioni interne finché una condizione non restituisce true.
  
Ogni tipo di azione ha un set diverso di **input** che definiscono il comportamento di un'azione.  
  
## <a name="http-action"></a>Azione HTTP  

Le azioni HTTP chiamano un endpoint specificato e controllano la risposta per determinare se il flusso di lavoro deve essere eseguito. L'oggetto **inputs** usa il set di parametri necessari per costruire la chiamata HTTP:  
  
|Nome dell'elemento|Obbligatoria|Tipo|Descrizione|  
|----------------|------------|--------|---------------|  
|statico|Sì|String|Può essere uno dei metodi HTTP seguenti: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**|  
|Uri|Sì|String|Endpoint http o https che viene chiamato. La lunghezza massima è 2 kilobyte.|  
|query|No|Oggetto|Rappresenta i parametri di query da aggiungere all'URL. `"queries" : { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL.|  
|headers|No|Oggetto|Rappresenta ogni intestazione inviata alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|No|Oggetto|Rappresenta il payload inviato all'endpoint.|  
|retryPolicy|No|Oggetto|Consente di personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx.|  
|operationsOptions|No|String|Definisce il set di comportamenti speciali di cui eseguire l'override.|  
|authentication|No|Oggetto|Rappresenta il metodo con cui autenticare la richiesta. Per informazioni dettagliate su questo oggetto, vedere [Autenticazione in uscita dell'Utilità di pianificazione](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Oltre all'utilità di pianificazione, esiste un'altra proprietà supportata, `authority`, che, per impostazione predefinita, è `https://login.windows.net` quando non è specificata, ma è possibile usare un gruppo di destinatari diverso, ad esempio `https://login.windows\-ppe.net`|  
  
Le azioni HTTP \(e di connessione API\) supportano i criteri per i tentativi. I criteri per i tentativi vengono applicati agli errori intermittenti, caratterizzati dai codici di stato HTTP 408, 429 e 5xx, oltre che alle eccezioni per la connettività. Questi criteri vengono descritti con l'oggetto *retryPolicy* definito di seguito:
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
L'intervallo tra tentativi viene specificato nel formato ISO 8601. Questo intervallo ha un valore predefinito e minimo di 20 secondi, mentre il valore massimo è di un'ora. Il numero predefinito e massimo di tentativi è pari a quattro ore. Se la definizione dei criteri per i tentativi non è specificata, viene usata una strategia `fixed` con valori di intervallo e di numero di tentativi predefiniti. Per disabilitare i criteri per i tentativi, impostare il tipo su `None`.  
  
L'azione seguente, ad esempio, prova a recuperare di nuovo le ultime novità per due volte, se si verificano errori intermittenti, per un totale di tre esecuzioni, con un ritardo di 30 secondi tra un tentativo e l'altro:  
  
```json
"latestNews" : {
    "type": "http",
    "inputs": {
        "method": "GET",
        "uri": "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```
### <a name="asynchronous-patterns"></a>Modelli asincroni

Per impostazione predefinita, tutte le azioni basate su HTTP supportano il modello di operazione asincrono standard. Se quindi il server remoto indica che la richiesta viene accettata per l'elaborazione con una risposta 202 \(Accepted\), il motore di App per la logica continua il polling dell'URL specificato nell'intestazione location della risposta fino a raggiungere uno stato terminale \(una risposta diversa da 202\).  
  
Per disabilitare il comportamento asincrono descritto prima, impostare un'opzione `DisableAsyncPattern` negli input dell'azione. In questo caso, l'output dell'azione si basa sulla risposta 202 iniziale dal server.  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>Limiti asincroni

La durata di un modello asincrono può essere limitata a un intervallo di tempo specifico.  Se l'intervallo di tempo trascorre senza raggiungere uno stato terminale, lo stato dell'azione verrà contrassegnato come `Cancelled` con il codice `ActionTimedOut`.  Il timeout per il limite è specificato nel formato ISO 8601.  I limiti possono essere specificati con la sintassi seguente:

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>Connessione API  

La connessione API è un'azione che fa riferimento a un connettore gestito da Microsoft.
Questa azione richiede un riferimento a una connessione valida e informazioni sull'API e sui parametri obbligatori.

|Nome dell'elemento|Obbligatoria|Tipo|Descrizione|  
|----------------|------------|--------|---------------|  
|host|Sì|Oggetto|Rappresenta le informazioni sul connettore, ad esempio il valore di runtimeUrl e il riferimento all'oggetto connection|
|statico|Sì|String|Può essere uno dei metodi HTTP seguenti: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** o **HEAD**|  
|path|Sì|String|Percorso dell'operazione API.|  
|query|No|Oggetto|Rappresenta i parametri di query da aggiungere all'URL. `"queries" : { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL.|  
|headers|No|Oggetto|Rappresenta ogni intestazione inviata alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|No|Oggetto|Rappresenta il payload inviato all'endpoint.|  
|retryPolicy|No|Oggetto|Consente di personalizzare il comportamento in caso di nuovo tentativo per gli errori 4xx o 5xx.|  
|operationsOptions|No|String|Definisce il set di comportamenti speciali di cui eseguire l'override.|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>Azione webhook di connessione API

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
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

I limiti per un'azione webhook possono essere specificati nello stesso modo dei [limiti asincroni HTTP](#asynchronous-limits).
  
## <a name="response-action"></a>Azione di risposta  

Questo tipo di azione contiene l'intero payload della risposta da una richiesta HTTP e include statusCode, body e headers:  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
L'azione response ha particolari restrizioni che non si applicano alle altre azioni. In particolare:  
  
-   Le azioni response non possono essere parallele in una definizione perché è necessaria una risposta deterministica alla richiesta in ingresso.  
  
-   Se un'azione response viene raggiunta dopo che la richiesta in ingresso ha ricevuto una risposta, l'azione viene considerata non riuscita \(conflict\) e di conseguenza l'esecuzione è `Failed`.  
  
-   Un flusso di lavoro con azioni response non può avere `splitOn` nel trigger perché una chiamata genera più esecuzioni. Verrà quindi convalidato quando il flusso è PUT e genererà una richiesta non valida.  
  
## <a name="wait-action"></a>Azione wait  

L'azione `wait` sospende l'esecuzione del flusso di lavoro per l'intervallo specificato. Per attendere 15 minuti, ad esempio, è possibile usare questo frammento:  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
In alternativa, per attendere fino a un determinato momento, è possibile usare questo esempio:  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> La durata dell'attesa può essere specificata usando l'oggetto **interval** o l'oggetto **until**, ma non entrambi.  
  
|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|interval|No|Oggetto|Durata dell'attesa basata su un periodo di tempo.|  
|interval unit|Sì|String|Uno di questi intervalli: second, minute, hour, day, week, month, year.|  
|interval count|Sì|String|Durata basata sull'unità interna specificata.|  
|until|No|Oggetto|Durata dell'attesa basata su data e ora.|  
|until timestamp|Sì|String|Data e ora in UTC in cui l'attesa scade.|  

## <a name="query-action"></a>Azione di query

L'azione `query` consente di filtrare una matrice in base a una condizione. Per selezionare numeri maggiori di 2, ad esempio, è possibile usare:

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

L'output dell'azione `query` è una matrice con elementi della matrice di input che soddisfano la condizione.

> [!NOTE]
> Se nessun valore soddisfa la condizione `where`, il risultato è una matrice vuota.

|Nome|Obbligatoria|Tipo|Descrizione|
|--------|------------|--------|---------------|
|from|Sì|Array|Matrice di origine.|
|dove|Sì|String|Condizione da applicare a ogni elemento della matrice di origine.|

## <a name="terminate-action"></a>Azione terminate

L'azione terminate arresta l'esecuzione del flusso di lavoro, interrompendo eventuali azioni in elaborazione e ignorando le azioni rimanenti. Per terminare un'esecuzione con stato **Failed**, ad esempio, è possibile usare il frammento seguente:

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> Le azioni già completate non sono interessate dall'azione terminate.

|Nome|Obbligatoria|Tipo|Descrizione|
|--------|------------|--------|---------------|
|runStatus|Sì|String|Stato dell'esecuzione di destinazione. **Failed** o **Cancelled**.|
|runError|No|Oggetto|Dettagli dell'errore. Supportato solo quando **runStatus** è impostato su **Failed**.|
|runError code|No|String|Codice errore di esecuzione.|
|runError message|No|String|Messaggio di errore di esecuzione.|

## <a name="compose-action"></a>Azione compose

L'azione compose consente di costruire un oggetto arbitrario. L'output dell'azione compose è il risultato della valutazione degli input. È possibile, ad esempio, usare l'azione compose per unire gli output di più azioni:

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> L'azione **Compose** può essere usata per costruire qualsiasi output, inclusi oggetti, matrici e altri tipi supportati a livello nativo dalle app per la logica, ad esempio file XML e binari.

## <a name="workflow-action"></a>Azione workflow   

|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|host id|Sì|String|ID risorsa del flusso di lavoro che si vuole chiamare.|  
|host triggerName|Sì|String|Nome del trigger che si vuole richiamare.|  
|query|No|Oggetto|Rappresenta i parametri di query da aggiungere all'URL. `"queries" : { "api-version": "2015-02-01" }`, ad esempio, aggiunge `?api-version=2015-02-01` all'URL.|  
|headers|No|Oggetto|Rappresenta ogni intestazione inviata alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|No|Oggetto|Rappresenta il payload inviato all'endpoint.|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
Poiché viene eseguito un controllo dell'accesso sul flusso di lavoro \(più in particolare, sul trigger\), è necessario l'accesso al flusso di lavoro.  
  
Gli output dell'azione `workflow` si basano su quanto definito nell'azione `response` nel flusso di lavoro figlio. Se non sono state definite azioni `response`, gli output sono vuoti.  

## <a name="collection-actions-scopes-and-loops"></a>Azioni di raccolta (ambiti e cicli)

Alcuni tipi di azioni possono contenere azioni. È possibile fare riferimento alle azioni di riferimento in una raccolta direttamente all'esterno della raccolta. Se si è definito `http` in un ambito, `@body('http')` è ancora valido in qualsiasi punto del flusso di lavoro. Le azioni in una raccolta possono essere eseguite (`runAfter`) solo dopo le altre azioni nella stessa raccolta.

## <a name="scope-action"></a>Azione scope

L'azione `scope` consente di raggruppare logicamente le azioni in un flusso di lavoro.

|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|Azioni|Sì|Oggetto|Azioni interne da eseguire nell'ambito|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>Azione ForEach

Questa azione di riproduzione a ciclo continuo scorre una matrice ed esegue azioni interne per ogni elemento. Per impostazione predefinita, il ciclo foreach viene eseguito in parallelo (20 esecuzioni in parallelo per volta). È possibile impostare regole di esecuzione usando il parametro `operationOptions`.

|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|Azioni|Sì|Oggetto|Azioni interne da eseguire nel ciclo|
|foreach|Sì|string|Matrice da scorrere|
|operationOptions|no|string|Qualsiasi opzione di operazione per il comportamento. Attualmente supporta solo `sequential` per eseguire le iterazioni in sequenza. Il comportamento predefinito è parallelo|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Azione Until

Questa azione di riproduzione a ciclo continuo esegue azioni interne finché una condizione non restituisce true.

|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|Azioni|Sì|Oggetto|Azioni interne da eseguire nel ciclo|
|expression|Sì|string|Espressione da valutare dopo ogni iterazione|
|limit|sì|Oggetto|Limiti per il ciclo: deve essere definito almeno un limite|
|count|no|int|Limite per il numero di iterazioni che possono essere eseguite|
|timeout|no|string|Timeout per la durata del ciclo.  Formato ISO 8601|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>Condizioni: azione If

L'azione `If` consente di valutare una condizione e di eseguire un ramo se l'espressione restituisce `true`.

|Nome|Obbligatoria|Tipo|Descrizione|  
|--------|------------|--------|---------------|  
|Azioni|Sì|Oggetto|Azioni interne da eseguire quando l'espressione restituisce `true`|
|expression|Sì|string|Espressione da valutare|
|else|no|Oggetto|Azioni interne da eseguire quando l'espressione restituisce `false`|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
La tabella seguente illustra alcuni esempi di come le condizioni possono usare le espressioni in un'azione:  
  
|Valore JSON|Risultato|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|Tutti i valori che restituiscono true fanno sì che questa condizione passi. Sono supportate solo le espressioni booleane. Per convertire gli altri tipi in booleani, usare le funzioni `empty`, `equals`.|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|Sono supportate le funzioni di confronto. In questo esempio l'azione viene eseguita solo quando l'output di act1 è maggiore della soglia.|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|Sono supportate anche le funzioni logiche per creare espressioni booleane annidate. In questo caso, l'azione viene eseguita quando l'output di act1 è superiore alla soglia o inferiore a 100.|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|È possibile usare le funzioni di matrice per controllare se una matrice contiene elementi. In questo caso, l'azione viene eseguita quando la matrice di errori è vuota.| 
|`"expression": "parameters('hasSpecialAction')"`|Errore: condizione non valida perché @ è obbligatorio per le condizioni.|  
  
Se una condizione restituisce un valore corretto, viene contrassegnata come `Succeeded`. Le azioni negli oggetti `actions` o `else` restituiscono `Succeeded` quando vengono eseguite correttamente, `Failed` quando non vengono eseguite correttamente o `Skipped` quando tale ramo non viene eseguito.

## <a name="next-steps"></a>Passaggi successivi

[API REST del servizio flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
