---
title: Riferimento ai tipi di trigger e di azioni - App per la logica di Azure | Microsoft Docs
description: Informazioni sui tipi di trigger e di azioni in App per la logica di Azure come descritti dallo Schema del linguaggio di definizione del flusso di lavoro
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: a7a34c703b9c6589679cf2035785c005f13f06cb
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822817"
---
# <a name="trigger-and-action-types-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Riferimento ai tipi di trigger e di azioni per il linguaggio di definizione del flusso di lavoro nelle App per la logica di Azure

Nelle [app per la logica di Azure](../logic-apps/logic-apps-overview.md) tutti i flussi di lavoro delle app per la logica iniziano con trigger seguiti da azioni. Questo articolo descrive i tipi di trigger e di azione che è possibile usare durante la creazione di app per la logica per automatizzare attività, processi e flussi di lavoro. È possibile creare flussi di lavoro delle app per la logica con Progettazione app per la logica, sia visivamente oppure creando direttamente le definizioni dei flussi di lavoro sottostanti con il [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md). È possibile creare app per la logica nel portale di Azure o in Visual Studio. La definizione sottostante per l'intero flusso di lavoro, inclusi i trigger e le azioni, usa Javascript Object Notation (JSON).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Panoramica dei trigger

Tutte le app per la logica iniziano con un trigger che definisce le chiamate che creano un'istanza e avviano il flusso di lavoro di un'app per la logica. Ecco le categorie generali di trigger:

* Un trigger di *polling*, che controlla l'endpoint di un servizio a intervalli regolari

* Un trigger *push*, che crea una sottoscrizione a un endpoint e fornisce un *URL di callback* in modo che l'endpoint possa inviare una notifica al trigger quando si verifica l'evento specificato o sono disponibili i dati. Il trigger rimane quindi in attesa della risposta dell'endpoint prima dell'attivazione. 

I trigger hanno questi elementi di livello superiore, anche se alcuni sono facoltativi:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*trigger-name*> | string | Nome del trigger | 
| <*trigger-type*> | string | Tipo di trigger, ad esempio "Http" o "ApiConnection" | 
| <*trigger-inputs*> | Oggetto JSON | Input che definiscono il comportamento del trigger | 
| <*time-unit*> | string | Unità di tempo per la frequenza di attivazione del trigger: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Valore che specifica quanto spesso il trigger viene attivato in base alla frequenza, ovvero il numero di unità di tempo di attesa fino alla successiva attivazione del trigger <p>Ecco gli intervalli minimo e massimo: <p>- Month: 1-16 mesi </br>- Day: 1-500 giorni </br>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Month", la ricorrenza è ogni 6 mesi. | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | Una matrice contenente una o più [condizioni](#trigger-conditions) che determinano se eseguire il flusso di lavoro. Disponibile solo per i trigger. | 
| <*runtime-config-options*> | Oggetto JSON | È possibile modificare il comportamento di runtime del trigger impostando le proprietà `runtimeConfiguration`. Per altre informazioni vedere [Impostazioni di configurazione di runtime](#runtime-config-options). | 
| <*splitOn-expression*> | string | Per i trigger che restituiscono una matrice è possibile specificare un'espressione che [ suddivide o *esegue il debatch*](#split-on-debatch) degli elementi della matrice in più istanze del flusso di lavoro per l'elaborazione. | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Elenco dei tipi di trigger

Ogni tipo di trigger ha un'interfaccia e input diversi che ne definiscono il comportamento. 

### <a name="built-in-triggers"></a>Trigger predefiniti

| Tipo di trigger | Descrizione | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica o *esegue il polling* di qualsiasi endpoint. L'endpoint deve essere conforme a un contratto del trigger specifico, usando un modello asincrono "202" o restituendo una matrice. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Crea un endpoint richiamabile dall'app per la logica, ma chiama l'URL specificato per la registrazione o l'annullamento della registrazione. |
| [**Recurrence**](#recurrence-trigger) | Attivato in base a una pianificazione definita. È possibile impostare una data e un'ora future per l'attivazione di questo trigger. In base alla frequenza, è anche possibile specificare le ore e i giorni per l'esecuzione del flusso di lavoro. | 
| [**Request**](#request-trigger)  | Crea un endpoint richiamabile per l'app per la logica ed è anche conosciuto come trigger "manuale". Ad esempio, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Trigger per l'API gestita

| Tipo di trigger | Descrizione | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica o *esegue il polling* di un endpoint usando [API gestite da Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Crea un endpoint richiamabile per l'app per la logica chiamando [API gestite da Microsoft](../connectors/apis-list.md) per la sottoscrizione e per l'annullamento della sottoscrizione. | 
||| 

## <a name="triggers---detailed-reference"></a>Trigger - Riferimento dettagliato

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Trigger APIConnection  

Questo trigger verifica o *esegue il polling* di un endpoint usando le [API gestite da Microsoft](../connectors/apis-list.md) in modo che i parametri del trigger possono essere diversi in base all'endpoint. Numerose sezioni in questa definizione di trigger sono facoltative. Il comportamento del trigger dipende dalle sezioni incluse o escluse.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | string | Nome del trigger | 
| <*nome connessione*> | string | Il nome della connessione all'API gestita usata dal flusso di lavoro | 
| <*method-type*> | string | Metodo HTTP per la comunicazione con l'API gestita: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*api-operation*> | string | L'operazione API da chiamare | 
| <*time-unit*> | string | Unità di tempo per la frequenza di attivazione del trigger: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Valore che specifica quanto spesso il trigger viene attivato in base alla frequenza, ovvero il numero di unità di tempo di attesa fino alla successiva attivazione del trigger <p>Ecco gli intervalli minimo e massimo: <p>- Month: 1-16 mesi </br>- Day: 1-500 giorni </br>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Month", la ricorrenza è ogni 6 mesi. | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella chiamata API. Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*splitOn-expression*> | string | Per i trigger che restituiscono matrici, questa espressione fa riferimento alla matrice da usare in modo che sia possibile creare ed eseguire un'istanza del flusso di lavoro per ogni elemento della matrice, anziché usare un ciclo "for each". <p>Ad esempio, questa espressione rappresenta un elemento nella matrice restituito nel contenuto del corpo del trigger: `@triggerbody()?['value']` |
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). |
||||

*Outputs*
 
| Elemento | Type | Descrizione |
|---------|------|-------------|
| headers | Oggetto JSON | Intestazioni dalla risposta |
| Corpo | Oggetto JSON | Il corpo dalla risposta |
| Codice di stato | Integer | Il codice di stato della risposta |
|||| 

*Esempio*

Questa definizione di trigger verifica quotidianamente l'e-mail nella posta in arrivo di un account Office 365 Outlook: 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Trigger ApiConnectionWebhook

Questo trigger invia una richiesta di sottoscrizione a un endpoint usando un'[API gestita da Microsoft](../connectors/apis-list.md), fornisce un *URL di callback* a cui l'endpoint può inviare una risposta e attende che l'endpoint risponda. Per altre informazioni, vedere [Sottoscrizioni agli endpoint](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*nome connessione*> | string | Il nome della connessione all'API gestita usata dal flusso di lavoro | 
| <*body-content*> | Oggetto JSON | Qualsiasi contenuto del messaggio da inviare come payload all'API gestita | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella chiamata API <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*splitOn-expression*> | string | Per i trigger che restituiscono matrici, questa espressione fa riferimento alla matrice da usare in modo che sia possibile creare ed eseguire un'istanza del flusso di lavoro per ogni elemento della matrice, anziché usare un ciclo "for each". <p>Ad esempio, questa espressione rappresenta un elemento nella matrice restituito nel contenuto del corpo del trigger: `@triggerbody()?['value']` |
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

*Esempio*

Questa definizione di trigger sottoscrive l'API Outlook di Office 365, fornisce un URL di callback all'endpoint dell'API e attende che l'endpoint risponda quando arriva una nuova e-mail.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>Trigger HTTP

Questo trigger verifica o esegue il polling dell'endpoint specificato in base alla pianificazione della ricorrenza specificata. La risposta dell'endpoint determina se il flusso di lavoro è in esecuzione.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*method-type*> | string | Metodo HTTP da usare per il polling dell'endpoint specificato: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*endpoint-URL*> | string | L'URL HTTP o HTTPS per l'endpoint di cui eseguire il polling <p>Dimensione massima della stringa: 2 KB | 
| <*time-unit*> | string | Unità di tempo per la frequenza di attivazione del trigger: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Valore che specifica quanto spesso il trigger viene attivato in base alla frequenza, ovvero il numero di unità di tempo di attesa fino alla successiva attivazione del trigger <p>Ecco gli intervalli minimo e massimo: <p>- Month: 1-16 mesi </br>- Day: 1-500 giorni </br>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Month", la ricorrenza è ogni 6 mesi. | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*header-content*> | Oggetto JSON | Le intestazioni da inviare con la richiesta <p>Ad esempio, per impostare il linguaggio e il tipo per una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | string | Il contenuto del messaggio da inviare come payload con la richiesta | 
| <*authentication-method*> | Oggetto JSON | Il metodo usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). Oltre all'Utilità di pianificazione, la proprietà `authority` è supportata. Quando non è specificato, il valore predefinito è `https://login.windows.net`, ma è possibile usare un valore diverso, ad esempio `https://login.windows\-ppe.net`. |
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella richiesta <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla richiesta. | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

*Outputs*

| Elemento | Type | Descrizione |
|---------|------|-------------| 
| headers | Oggetto JSON | Intestazioni dalla risposta | 
| Corpo | Oggetto JSON | Il corpo dalla risposta | 
| Codice di stato | Integer | Il codice di stato della risposta | 
|||| 

*Requisiti per le richieste in ingresso*

Per funzionare correttamente con l'app per la logica, l'endpoint deve essere conforme a un modello o contratto di trigger specifico e riconoscere queste proprietà:  
  
| Risposta | Obbligatoria | Descrizione | 
|----------|----------|-------------| 
| Codice di stato | Sì | Il codice di stato "200 OK" avvia un'esecuzione. Nessun altro codice di stato avvia un'esecuzione. | 
| Intestazione retry-after | No  | Numero di secondi prima che l'app per la logica esegua di nuovo il polling dell'endpoint. | 
| Intestazione Location | No  | URL da chiamare al successivo intervallo di polling. Se non è specificato, viene usato l'URL originale. | 
|||| 

*Comportamenti di esempio per diverse richieste*

| Codice di stato | Nuovo tentativo dopo | Comportamento | 
|-------------|-------------|----------|
| 200 | {none} | Esegue il flusso di lavoro e quindi controlla di nuovo la presenza di altri dati dopo la ricorrenza definita. | 
| 200 | 10 secondi | Esegue il flusso di lavoro e quindi controlla di nuovo la presenza di altri dati dopo 10 secondi. |  
| 202 | 60 secondi | Non attiva il flusso di lavoro. Il tentativo successivo viene eseguito dopo un minuto, in base alla ricorrenza definita. Se la ricorrenza definita è inferiore a un minuto, l'intestazione retry-after ha la precedenza. In caso contrario, viene usata la ricorrenza definita. | 
| 400 | {none} | Richiesta non valida, non esegue il flusso di lavoro. Se non è definito alcun oggetto `retryPolicy`, viene usato il criterio predefinito. Dopo aver raggiunto il numero di tentativi, il trigger controlla di nuovo la presenza di dati dopo la ricorrenza definita. | 
| 500 | {none}| Errore del server, il flusso di lavoro non viene eseguito. Se non è definito alcun oggetto `retryPolicy`, viene usato il criterio predefinito. Dopo aver raggiunto il numero di tentativi, il trigger controlla di nuovo la presenza di dati dopo la ricorrenza definita. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Trigger HTTPWebhook  

Questo trigger rende l'app per la logica richiamabile tramite la creazione di un endpoint che può registrare una sottoscrizione chiamando l'URL dell'endpoint specificato. Quando si crea il trigger nel flusso di lavoro, una richiesta in uscita effettua la chiamata per registrare la sottoscrizione. In questo modo il trigger può iniziare l'ascolto degli eventi. Quando un'operazione rende il trigger non valido, una richiesta in uscita effettua automaticamente una chiamata per annullare la sottoscrizione. Per altre informazioni, vedere [Sottoscrizioni agli endpoint](#subscribe-unsubscribe).

È inoltre possibile specificare [limiti asincroni](#asynchronous-limits) in un trigger **HTTPWebhook**.
Il comportamento del trigger dipende dalle sezioni usate o omesse. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Alcuni valori, ad esempio <*method-type*>, sono disponibili per gli oggetti `"subscribe"` e `"unsubscribe"`.

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*method-type*> | string | Metodo HTTP da usare per la richiesta di sottoscrizione: "GET", "PUT", "POST", "PATCH" o "DELETE" | 
| <*endpoint-subscribe-URL*> | string | URL dell'endpoint a cui inviare la richiesta di sottoscrizione | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*method-type*> | string | Metodo HTTP da usare per la richiesta di annullamento: "GET", "PUT", "POST", "PATCH" o "DELETE" | 
| <*endpoint-unsubscribe-URL*> | string | URL dell'endpoint a cui inviare la richiesta di annullamento | 
| <*body-content*> | string | Qualsiasi contenuto di messaggio da inviare nella richiesta di sottoscrizione o di annullamento | 
| <*authentication-method*> | Oggetto JSON | Il metodo usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

*Outputs* 

| Elemento | Type | Descrizione |
|---------|------|-------------| 
| headers | Oggetto JSON | Intestazioni dalla risposta | 
| Corpo | Oggetto JSON | Il corpo dalla risposta | 
| Codice di stato | Integer | Il codice di stato della risposta | 
|||| 

*Esempio*

Questo trigger crea una sottoscrizione all'endpoint specificato, fornisce un URL di callback univoco e resta in attesa dei nuovi articoli di tecnologia pubblicati.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger Recurrence  

Questo trigger viene eseguito in base alla pianificazione delle ricorrenze specificata e offre un modo semplice di creare un flusso di lavoro eseguito regolarmente. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*time-unit*> | string | Unità di tempo per la frequenza di attivazione del trigger: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Valore che specifica quanto spesso il trigger viene attivato in base alla frequenza, ovvero il numero di unità di tempo di attesa fino alla successiva attivazione del trigger <p>Ecco gli intervalli minimo e massimo: <p>- Month: 1-16 mesi </br>- Day: 1-500 giorni </br>- Hour: 1-12.000 ore </br>- Minute: 1-72.000 minuti </br>- Second: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Month", la ricorrenza è ogni 6 mesi. | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | string | Data e ora di inizio in questo formato: <p>AAAA-MM-GGThh:mm:ss se si specifica un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si specifica un fuso orario <p>Ad esempio, per il 18 settembre 2017 alle 14.00, specificare "2017-09-18T14:00:00" e indicare un fuso orario, ad esempio l'ora solare del Pacifico, oppure specificare "2017-09-18T14:00:00Z" senza fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si specifica un fuso orario, è necessario aggiungere la lettera "Z" alla fine senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non viene attivato prima dell'ora di inizio. Per altre informazioni sulle date e le ore di inizio, vedere [Creare e pianificare attività eseguite regolarmente](../connectors/connectors-native-recurrence.md). | 
| <*time-zone*> | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Specificare il fuso orario che si vuole applicare. | 
| <*one-or-more-hour-marks*> | Intero o matrice di intero | Se si specifica "Day" o "Week" per `frequency`, è possibile specificare uno o più numeri interi compresi tra 0 e 23, separati da virgole, come ore del giorno in cui eseguire il flusso di lavoro. <p>Ad esempio, se si specifica "10", "12" e "14", gli indicatori di ora corrisponderanno alle 10.00, alle 12.00 e alle 14.00. | 
| <*one-or-more-minute-marks*> | Intero o matrice di intero | Se si specifica "Day" o "Week" per `frequency`, è possibile specificare uno o più numeri interi compresi tra 0 e 59, separati da virgole, come minuti dell'ora in cui eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. | 
| weekDays | Stringa o matrice di stringhe | Se si specifica "Week" per `frequency`, è possibile definire uno o più giorni, separati da virgole, in cui eseguire il flusso di lavoro: "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" e "Sunday" | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

*Esempio 1*

Questo trigger di ricorrenza di base viene eseguito ogni giorno:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Esempio 2*

È possibile specificare una data e un'ora di inizio per l'attivazione del trigger. Questo trigger di ricorrenza inizia alla data specificata e quindi viene attivato ogni giorno:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Esempio 3*

Questo trigger di ricorrenza inizia il 9 settembre 2017 alle 14.00 e viene attivato ogni settimana il lunedì alle 10.30, 12.30 e 14.30, ora solare Pacifico:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Per altre informazioni ed esempi per questo trigger, vedere [Creare e pianificare attività eseguite regolarmente](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger di richiesta

Questo trigger fa in modo che l'app per la logica possa essere chiamata mediante la creazione di un endpoint in grado di accettare le richieste in ingresso. Per questo trigger fornire uno schema JSON che descrive e convalida il payload o gli input che il trigger riceve dalla richiesta in ingresso. Lo schema semplifica il riferimento alle proprietà del trigger da parte delle azioni più recenti nel flusso di lavoro. 

Per chiamare questo trigger, è necessario usare l'API `listCallbackUrl` che viene descritta nell'[API REST del servizio di flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows). Per informazioni su come usare questo trigger come endpoint HTTP, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*property-name*> | string | Il nome di una proprietà nello schema JSON che descrive il payload | 
| <*property-type*> | string | Il tipo della proprietà | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*method-type*> | string | Metodo che le richieste in ingresso devono usare per chiamare l'app per la logica: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relative-path-for-accepted-parameter*> | string | Percorso relativo per il parametro che l'URL dell'endpoint può accettare | 
| <*required-properties*> | Array | Una o più proprietà che richiedono valori. | 
| <*max-runs*> | Integer | Per impostazione predefinita, le istanze del flusso di lavoro dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Quando nell'app per la logica è già in esecuzione il numero massimo di istanze che è possibile modificare in base alla proprietà `runtimeConfiguration.concurrency.runs`, eventuali nuove esecuzioni vengono inserite in questa coda fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

*Esempio*

Questo trigger specifica che una richiesta in ingresso deve usare il metodo HTTP POST per chiamare il trigger e include uno schema che convalida l'input della richiesta in ingresso: 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Condizioni di trigger

Esclusivamente per i trigger, è possibile includere una matrice con una o più espressioni per le condizioni che determinano se il flusso di lavoro deve essere eseguito o meno. Per aggiungere la proprietà `conditions` a un trigger nell'app per la logica, aprire l'app per la logica nell'editor della visualizzazione codice.

Ad esempio, è possibile specificare che un trigger venga attivato solo quando un sito Web restituisce un errore interno del server facendo riferimento al codice di stato del trigger nella proprietà `conditions`:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Per impostazione predefinita, un trigger viene attivato solo dopo la ricezione di una risposta "200 OK". Quando un'espressione fa riferimento a un codice di stato di un trigger, il comportamento predefinito del trigger viene sostituito. Pertanto, se si vuole che il trigger venga attivato per più di un codice di stato, ad esempio "200" e "201", è necessario includere questa espressione come condizione: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Esecuzioni multiple del trigger

Se il trigger restituisce una matrice che deve essere elaborata dall'app per la logica, a volte un ciclo "foreach" può impiegare troppo tempo per elaborare ogni elemento della matrice. In alternativa, è possibile usare la proprietà **SplitOn** nel trigger per *eseguire il debatch* della matrice. Il debatching suddivide gli elementi della matrice e avvia una nuova istanza dell'app per la logica, eseguita per ogni elemento della matrice. Questo approccio è utile, ad esempio, quando si vuole eseguire il polling di un endpoint che può restituire più elementi nuovi tra gli intervalli di polling.
Per informazioni sul numero massimo di elementi della matrice che la proprietà **SplitOn** può elaborare in un'unica esecuzione dell'app per la logica, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Non è possibile usare **SplitOn** con un modello di risposta sincrona. Qualsiasi flusso di lavoro che usa **SplitOn** e include un'azione di risposta viene eseguito in modo asincrono e invia immediatamente una risposta `202 ACCEPTED`.

Se il file Swagger del trigger descrive un payload che è una matrice, la proprietà **SplitOn** viene automaticamente aggiunta al trigger. In caso contrario, aggiungere questa proprietà all'interno del payload della risposta che include la matrice di cui si vuole eseguire il debatch. 

*Esempio*

Si supponga che un'API restituisca questa risposta: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
L'app per la logica richiede solo il contenuto della matrice in `Rows`, in modo da poter creare un trigger come in questo esempio:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Se si usa il comando `SplitOn`, non è possibile ottenere le proprietà esterne alla matrice. Di conseguenza, per questo esempio non è possibile ottenere la proprietà `status` nella risposta restituita dall'API.
> 
> Per evitare un errore se la proprietà `Rows` non esiste, questo esempio usa l'operatore `?`.

La definizione del flusso di lavoro può ora usare `@triggerBody().name` per ottenere i valori `name` che sono `"customer-name-one"` dalla prima esecuzione e `"customer-name-two"` dalla seconda. Gli output del trigger saranno simili a questi esempi:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Panoramica delle azioni

Le app per la logica di Azure forniscono vari tipi di azione, ognuna con input diversi che definiscono il comportamento univoco di un'azione. 

Le azioni hanno questi elementi di livello superiore, anche se alcuni sono facoltativi:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------|
| <*action-name*> | string | Il nome dell'azione | 
| <*action-type*> | string | Tipo di azione, ad esempio "Http" o "ApiConnection"| 
| <*input-name*> | string | Il nome di un input che definisce il comportamento dell'azione | 
| <*input-value*> | Vari | Il valore di input, che può essere una stringa, un numero intero, un oggetto JSON e così via | 
| <*previous-trigger-or-action-status*> | Oggetto JSON | Il nome e lo stato risultante per il trigger o l'azione che devo essere eseguiti immediatamente prima di poter eseguire questa azione corrente | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------|
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere Criteri di ripetizione dei tentativi. | 
| <*runtime-config-options*> | Oggetto JSON | Per alcune azioni, è possibile modificare il comportamento dell'azione in fase di esecuzione impostando le proprietà `runtimeConfiguration`. Per altre informazioni vedere [Impostazioni di configurazione di runtime](#runtime-config-options). | 
| <*operation-option*> | string | È possibile modificare il comportamento predefinito di alcune azioni impostando la proprietà `operationOptions`. Per altre informazioni, vedere [Opzioni relative alle operazioni](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Elenco dei tipi di azione

Di seguito sono riportati alcuni tipi di azioni di uso comune: 

* [Tipi di azioni predefinite](#built-in-actions) come in questi e altri esempi: 

  * [**HTTP** ](#http-action) per chiamate a endpoint su HTTP o HTTPS

  * [**Response**](#response-action) per rispondere alle richieste

  * [**Function** ](#function-action) per chiamare le Funzioni di Azure

  * Azioni di operazioni sui dati, ad esempio [**Join**](#join-action), [**Compose**](#compose-action), [**Table**](#table-action), [**Select**](#select-action) e altre che creano o trasformano i dati da vari input

  * [**Workflow**](#workflow-action) per chiamare un altro flusso di lavoro per l'app per la logica

* [Tipi di azione gestiti dall'API](#managed-api-actions), ad esempio [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) che chiama vari connettori e API gestite da Microsoft, ad esempio, bus di servizio di Azure, Office 365 Outlook, Power BI, Archiviazione BLOB di Azure, OneDrive, GitHub e altro ancora

* [Tipi di azione di controllo del flusso di lavoro](#control-workflow-actions), ad esempio [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action) e [**Until**](#until-action) che contengono altre azioni che consentono di organizzare l'esecuzione del flusso di lavoro

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Azioni predefinite

| Tipo di azione | Descrizione | 
|-------------|-------------| 
| [**Componi**](#compose-action) | Crea un singolo output dagli input, che possono essere di vari tipi. | 
| [**Function**](#function-action) | Chiama una funzione di Azure. | 
| [**HTTP**](#http-action) | Chiama un endpoint HTTP. | 
| [**Join**](#join-action) | Crea una stringa da tutti gli elementi in una matrice e separa gli elementi con un carattere delimitatore specificato. | 
| [**Parse JSON**](#parse-json-action) | Crea token semplici da usare dalle proprietà nel contenuto JSON. È quindi possibile fare riferimento a tali proprietà includendo i token nell'app per la logica. | 
| [**Query**](#query-action) | Crea una matrice di elementi in un'altra matrice in base a una condizione o a un filtro. | 
| [**Response**](#response-action) | Crea una risposta a una richiesta o a una chiamata in ingresso. | 
| [**Select**](#select-action) | Crea una matrice con gli oggetti JSON mediante la trasformazione degli elementi di un'altra matrice basata sul mapping specificato. | 
| [**Table**](#table-action) | Crea una tabella CSV o HTML da una matrice. | 
| [**Terminate**](#terminate-action) | Arresta un flusso di lavoro in esecuzione attiva. | 
| [**Wait**](#wait-action) | Sospende il flusso di lavoro per la durata specificata o fino alla data e ora specificate. | 
| [**Workflow**](#workflow-action) | Consente di annidare un flusso di lavoro all'interno di un altro flusso di lavoro. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Azioni delle API gestite

| Tipo di azione | Descrizione | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Richiama un endpoint HTTP usando un'[API gestita da Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funziona come HTTP Webhook, ma usa un'[API gestita da Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Azioni di controllo del flusso di lavoro

Queste azioni consentono di controllare l'esecuzione del flusso di lavoro e includono altre azioni. Dall'esterno di un'azione di controllo del flusso di lavoro è possibile fare riferimento direttamente ad azioni all'interno di tale azione di controllo di flusso di lavoro. Ad esempio, se si dispone di un'azione `Http` all'interno di un ambito, è possibile fare riferimento all'espressione `@body('Http')` da qualsiasi posizione nel flusso di lavoro. Tuttavia, le azioni esistenti all'interno di un'azione di controllo del flusso di lavoro possono essere solo "eseguite dopo" altre azioni che sono nella stessa struttura di controllo del flusso di lavoro.

| Tipo di azione | Descrizione | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Esegue le stesse azioni in un ciclo per ogni elemento in una matrice. | 
| [**If**](#if-action) | Esegue le azioni a seconda se la condizione specificata è true o false. | 
| [**Scope**](#scope-action) | Esegue le azioni in base allo stato di gruppo da un set di azioni. | 
| [**Switch**](#switch-action) | Esegue le azioni organizzate in casi quando i valori delle espressioni, oggetti o token corrispondono ai valori specificati per ogni caso. | 
| [**Until**](#until-action) | Esegue le azioni in un ciclo fino a quando la condizione specificata è true. | 
|||  

## <a name="actions---detailed-reference"></a>Azioni - Riferimento dettagliato

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Azione APIConnection

Questa azione invia una richiesta HTTP a un'[API gestita da Microsoft](../connectors/apis-list.md) e richiede le informazioni relative all'API e ai parametri oltre a un riferimento a una connessione valida. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*action-name*> | string | Il nome dell'azione fornito dal connettore | 
| <*api-name*> | string | Il nome dell'API gestita da Microsoft usata per la connessione | 
| <*method-type*> | string | Metodo HTTP per chiamare l'API: "GET", "PUT", "POST", "PATCH" o "DELETE" | 
| <*api-operation*> | string | L'operazione API da chiamare | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | Oggetto JSON | Altre proprietà di input che si applicano a questa azione specifica | 
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella chiamata API. <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*other-action-specific-properties*> | Oggetto JSON | Le altre proprietà che si applicano a questa azione specifica | 
|||| 

*Esempio*

Questa definizione descrive l'azione**Send an email** per il connettore Outlook Office 365 che è un'API gestita da Microsoft: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Azione ApiConnectionWebhook

Questa azione invia una richiesta di sottoscrizione su HTTP a un endpoint usando un'[API gestita da Microsoft](../connectors/apis-list.md), fornisce un *URL di callback* a cui l'endpoint può inviare una risposta e attende che l'endpoint risponda. Per altre informazioni, vedere [Sottoscrizioni agli endpoint](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Alcuni valori, ad esempio <*method-type*>, sono disponibili per gli oggetti `"subscribe"` e `"unsubscribe"`.

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*action-name*> | string | Il nome dell'azione fornito dal connettore | 
| <*method-type*> | string | Metodo HTTP da usare per la sottoscrizione o l'annullamento della sottoscrizione da un endpoint: "GET", "PUT", "POST", "PATCH" o "DELETE" | 
| <*api-subscribe-URL*> | string | L'URI da usare per la sottoscrizione all'API | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | string | L'URI da usare per annullare la sottoscrizione all'API | 
| <*header-content*> | Oggetto JSON | Le eventuali intestazioni da inviare nella richiesta <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Oggetto JSON | Gli eventuali messaggi da inviare nella richiesta | 
| <*authentication-method*> | Oggetto JSON | Il metodo usato dalla richiesta per l'autenticazione. Per altre informazioni, vedere [Autenticazione in uscita dell'Utilità di pianificazione](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella chiamata API <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*other-action-specific-input-properties*> | Oggetto JSON | Altre proprietà di input che si applicano a questa azione specifica | 
| <*other-action-specific-properties*> | Oggetto JSON | Le altre proprietà che si applicano a questa azione specifica | 
|||| 

È anche possibile specificare i limiti per un'azione **ApiConnectionWebhook** allo stesso modo dei [limiti asincroni HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Azione compose

Questa azione crea un singolo output da più input, incluse le espressioni. Sia l'output che gli input possono essere di qualsiasi tipo tra quelli supportati in modo nativo dalle app per la logica di Azure, ad esempio matrici, oggetti JSON, XML e oggetti binari.
È quindi possibile usare l'output dell'azione in altre azioni. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Obbligatorio* 

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*inputs-to-compose*> | Qualsiasi | Gli input per la creazione di un singolo output | 
|||| 

*Esempio 1*

Questa definizione di azione unisce `abcdefg ` con uno spazio finale e il valore `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Ecco l'output che verrà creato con questa azione:

`abcdefg 1234`

*Esempio 2*

Questa definizione di azione unisce una variabile di stringa che contiene `abcdefg` e una variabile integer che contiene `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Ecco l'output che verrà creato con questa azione:

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Azione delle funzioni

Questa azione chiama una [funzione di Azure](../azure-functions/functions-create-first-azure-function.md) creata in precedenza.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------|  
| <*Azure-function-ID*> | string | ID risorsa per la funzione di Azure che si vuole chiamare. Di seguito è riportato il formato per questo valore:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | string | Metodo HTTP per chiamare la funzione: "GET", "PUT", "POST", "PATCH" o "DELETE" <p>Se non è specificato, il metodo predefinito è "POST". | 
||||

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------|  
| <*header-content*> | Oggetto JSON | Le eventuali intestazioni da inviare con la chiamata <p>Ad esempio, per impostare il linguaggio e il tipo in una richiesta: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Oggetto JSON | Gli eventuali messaggi da inviare nella richiesta | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella chiamata API <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*other-action-specific-input-properties*> | Oggetto JSON | Altre proprietà di input che si applicano a questa azione specifica | 
| <*other-action-specific-properties*> | Oggetto JSON | Le altre proprietà che si applicano a questa azione specifica | 
||||

Quando si salva l'app per la logica, il motore di App per la logica esegue questi controlli sulla funzione cui viene fatto riferimento:

* Il flusso di lavoro deve avere accesso alla funzione.

* Il flusso di lavoro può usare solo un trigger HTTP standard o un trigger Webhook JSON generico. 

  Il motore di App per la logica ottiene e memorizza nella cache l'URL del trigger, che viene usato in fase di esecuzione. Pertanto, se qualsiasi operazione invalida l'URL memorizzato nella cache, l'azione **Function** non riesce in fase di esecuzione. Per risolvere questo problema, salvare di nuovo l'app per la logica in modo che ottenga e memorizzi nuovamente nella cache l'URL del trigger.

* Per la funzione non deve essere definita alcuna route.

* Sono consentiti solo i livelli di autorizzazione "funzione" e "anonimo". 

*Esempio*

Questa definizione di azione chiama la funzione "GetProductID" creata in precedenza:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Azione HTTP

Questa azione invia una richiesta all'endpoint specificato e controlla la risposta per determinare se il flusso di lavoro debba o meno essere eseguito. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*method-type*> | string | Metodo da usare per inviare la richiesta: "GET", "PUT", "POST", "PATCH" o "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | string | Endpoint HTTP o HTTPS da chiamare. Dimensione massima della stringa: 2 KB | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*header-content*> | Oggetto JSON | Le eventuali intestazioni da inviare con la richiesta <p>Ad esempio, per impostare il linguaggio e il tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Oggetto JSON | Gli eventuali messaggi da inviare nella richiesta | 
| <*retry-behavior*> | Oggetto JSON | Consente di personalizzare il comportamento per la ripetizione degli errori intermittenti, che hanno il codice di stato 408, 429 e 5XX e tutte le eccezioni di connettività. Per altre informazioni, vedere il [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Oggetto JSON | Tutti i parametri di query da includere nella richiesta <p>Ad esempio, l'oggetto `"queries": { "api-version": "2018-01-01" }` aggiunge `?api-version=2018-01-01` alla chiamata. | 
| <*other-action-specific-input-properties*> | Oggetto JSON | Altre proprietà di input che si applicano a questa azione specifica | 
| <*other-action-specific-properties*> | Oggetto JSON | Le altre proprietà che si applicano a questa azione specifica | 
|||| 

*Esempio*

Questa definizione di azioni ottiene le notizie più recenti inviando una richiesta all'endpoint specificato:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Azione join

Questa azione crea una stringa da tutti gli elementi in una matrice e li separa con il carattere delimitatore specificato. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*matrice*> | Array | La matrice o l'espressione che fornisce gli elementi di origine. Se si specifica un'espressione, racchiuderla tra virgolette doppie. | 
| <*delimiter*> | Stringa di caratteri singoli | Il carattere che separa ciascun elemento nella stringa | 
|||| 

*Esempio*

Si supponga di avere una variabile "myIntegerArray" creata in precedenza che contiene questa matrice di numeri interi: 

`[1,2,3,4]` 

Questa definizione di azione ottiene i valori dalla variabile usando la funzione `variables()` in un'espressione e crea la stringa con questi valori separati da virgole: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Azione analisi JSON

Questa azione consente di creare campi o *token* intuitivi dalle proprietà nel contenuto JSON. È quindi possibile accedere a tali proprietà nell'app per la logica usando i token. Ad esempio, quando si intende usare l'output JSON dei servizi come il bus di servizio di Azure e Azure Cosmos DB, è possibile includere questa azione nell'app per la logica in modo che si possa fa riferimento più facilmente ai dati nell'output. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*JSON-source*> | Oggetto JSON | Il contenuto JSON da analizzare | 
| <*JSON-schema*> | Oggetto JSON | Lo schema JSON che descrive il contenuto JSON sottostante che l'azione usa per l'analisi dell'origine del contenuto JSON. <p>**Suggerimento**: in Progettazione App per la logica è possibile fornire lo schema oppure fornire un payload di esempio in modo che l'azione possa generare lo schema. | 
|||| 

*Esempio*

Questa definizione di azioni crea i token che è possibile usare nel flusso di lavoro delle app per la logica, ma solo nelle azioni in esecuzione a seguito dell'azione **Parse JSON**: 

`FirstName`, `LastName` e `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

In questo esempio, la proprietà "content" specifica il contenuto JSON per l'azione da analizzare. È anche possibile fornire il contenuto JSON come payload di esempio per la generazione dello schema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

La proprietà "schema" specifica lo schema JSON usato per descrivere il contenuto JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Azione di query

Questa azione crea una matrice da elementi in un'altra matrice in base a una condizione o a un filtro specificati.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*matrice*> | Array | La matrice o l'espressione che fornisce gli elementi di origine. Se si specifica un'espressione, racchiuderla tra virgolette doppie. |
| <*condition-or-filter*> | string | La condizione usata per filtrare gli elementi nella matrice di origine <p>**Nota**: se nessun valore soddisfa la condizione, il risultato è una matrice vuota. |
|||| 

*Esempio*

Questa definizione di azioni crea una matrice che contiene valori maggiori del valore specificato, ovvero due:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Azione di risposta  

Questa azione crea il payload per la risposta a una richiesta HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*response-status-code*> | Integer | Codice di stato HTTP inviato per rispondere alla richiesta in ingresso. Il codice predefinito è "200 OK", ma può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx, ma non con 3xxx. | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*response-headers*> | Oggetto JSON | Una o più intestazioni da includere con la risposta | 
| <*response-body*> | Vari | Il corpo della risposta, che può essere una stringa, un oggetto JSON o anche il contenuto binario di un'azione precedente | 
|||| 

*Esempio*

Questa definizione di azioni crea una risposta a una richiesta HTTP con il codice di stato specificato, il corpo e le intestazioni del messaggio:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Restrizioni*

Diversamente da altre azioni, l'azione **Response** ha delle particolari restrizioni: 

* Il flusso di lavoro può usare l'azione **Response** solo quando il flusso di lavoro inizia con un trigger di richiesta HTTP, vale a dire che il flusso di lavoro deve essere attivato da una richiesta HTTP.

* Il flusso di lavoro può usare l'azione **Response** in qualsiasi punto *tranne* all'interno dei cicli **Foreach** e **Until**, inclusi i cicli sequenziali e i rami paralleli. 

* La richiesta HTTP originale ottiene risposta dal flusso di lavoro solo quando tutte le azioni necessarie per l'azione **Response** vengono completate entro il [limite di timeout della richiesta HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Tuttavia, se il flusso di lavoro chiama un'altra app per la logica come un flusso di lavoro annidato, il flusso di lavoro padre attende fino al termine del flusso di lavoro annidato, indipendentemente da quanto tempo passa prima che questo venga completato.

* Quando il flusso di lavoro usa l'azione **Response** e un modello di risposta sincrona, il flusso di lavoro non può usare anche il comando **splitOn** nella definizione del trigger dal momento che tale comando crea più esecuzioni. Controllare questo caso quando viene usato il metodo PUT e, se è true, restituisce una risposta di "richiesta non valida".

  Altrimenti se il flusso di lavoro usa il comando **splitOn** e un'azione **Response**, il flusso di lavoro viene eseguito in modo asincrono e restituisce immediatamente una risposta "202 ACCEPTED".

* Quando l'esecuzione del flusso di lavoro raggiunge l'azione **Response**, ma la richiesta in ingresso ha già ricevuto una risposta, l'azione **Response** è contrassegnata come "Failed" a causa del conflitto. E di conseguenza, l'esecuzione dell'app per la logica viene anche contrassegnata con lo stato "Failed".

<a name="select-action"></a>

### <a name="select-action"></a>Seleziona azione

Questa azione crea una matrice con gli oggetti JSON mediante la trasformazione degli elementi di un'altra matrice basata sul mapping specificato. La matrice di output e la matrice di origine hanno sempre lo stesso numero di elementi. Anche se è possibile modificare il numero di oggetti nella matrice di output, è possibile aggiungere o rimuovere proprietà e i relativi valori tra tali oggetti. La proprietà `select` consente di specificare almeno una coppia chiave-valore che definisce il mapping per trasformare gli elementi nella matrice di origine. Una coppia chiave-valore rappresenta una proprietà e il relativo valore tra tutti gli oggetti nella matrice di output. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Obbligatorio* 

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*matrice*> | Array | La matrice o l'espressione che fornisce gli elementi di origine. Assicurarsi di racchiudere un'espressione tra virgolette doppie. <p>**Nota**: se la matrice di origine è vuota, l'azione crea una matrice vuota. | 
| <*key-name*> | string | Il nome della proprietà assegnato al risultato da <*expression*> <p>Per aggiungere una nuova proprietà a tutti gli oggetti nella matrice di output, fornire un <*key-name*> per tale proprietà e <*expression*> per il valore della proprietà. <p>Per rimuovere una proprietà da tutti gli oggetti nella matrice, omettere <*key-name*> per quella proprietà. | 
| <*expression*> | string | L'espressione che trasforma l'elemento nella matrice di origine e assegna il risultato a <*key-name*> | 
|||| 

L'azione **Select** crea una matrice come output, in modo che qualsiasi azione che intenda usare l'output deve accettare una matrice o è necessario convertire la matrice nel tipo che accetta l'azione consumer. Per convertire la matrice di output in una stringa, ad esempio, è possibile passare tale matrice all'azione **Compose** e quindi fare riferimento all'output dell'azione **Compose** nelle altre azioni.

*Esempio*

Questa definizione di azioni crea una matrice di oggetti JSON da una matrice di numeri interi. L'azione esegue l'iterazione attraverso la matrice di origine, ottiene tutti i valori integer usando l'espressione `@item()` e assegna ogni valore alla proprietà "`number`" in ogni oggetto JSON: 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Ecco la matrice che verrà creata con questa azione:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Per usare questa matrice di output in altre azioni, passare questo output in un'azione **Compose**:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

È quindi possibile usare l'output dell'azione **Compose** nelle altre azioni, ad esempio, l'azione **Office 365 Outlook - Send an email**:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>azione Tabella

Questa azione crea una tabella CSV o HTML da una matrice. Per le matrici con oggetti JSON, questa azione crea automaticamente le intestazioni di colonna dai nomi delle proprietà degli oggetti. Per le matrici con altri tipi di dati, è necessario specificare le intestazioni e i valori di colonna. Ad esempio, questa matrice include le proprietà "ID" e "Product_Name" che questa azione può usare per i nomi delle intestazioni di colonna:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Obbligatorio* 

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <CSV *or* HTML>| string | Il formato della tabella da creare | 
| <*matrice*> | Array | La matrice o l'espressione che fornisce gli elementi di origine per la tabella <p>**Nota**: se la matrice di origine è vuota, l'azione crea una tabella vuota. | 
|||| 

*Facoltativo*

Per specificare o personalizzare le intestazioni e i valori di colonna usare la matrice `columns`. Quando le coppie `header-value` hanno lo stesso nome di intestazione, i relativi valori vengono visualizzati nella stessa colonna con quel nome di intestazione. In caso contrario, ogni intestazione univoca definisce una colonna univoca.

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*column-name*> | string | Il nome dell'intestazione di una colonna | 
| <*column-value*> | Qualsiasi | Il valore nella colonna | 
|||| 

*Esempio 1*

Si supponga di avere una variabile "myItemArray" creata in precedenza che attualmente contiene questa matrice: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Questa definizione di azione crea una tabella CSV dalla variabile "myItemArray". L'espressione usata dalla proprietà `from` ottiene la matrice da "myItemArray" usando la funzione `variables()`: 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Ecco la tabella CSV che verrà creata con questa azione: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Esempio 2*

Questa definizione di azione crea una tabella HTML dalla variabile "myItemArray". L'espressione usata dalla proprietà `from` ottiene la matrice da "myItemArray" usando la funzione `variables()`: 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Ecco la tabella HTML che verrà creata con questa azione: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Mele</td></tr><tr><td>1</td><td>Arance</td></tr></tbody></table>

*Esempio 3*

Questa definizione di azione crea una tabella HTML dalla variabile "myItemArray". Tuttavia, questo esempio sostituisce i nomi di intestazione di colonna predefiniti con "Stock_ID" e "Description" e aggiunge la parola "Organic" ai valori nella colonna "Description".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Ecco la tabella HTML che verrà creata con questa azione: 

<table><thead><tr><th>Stock_ID</th><th>Descrizione</th></tr></thead><tbody><tr><td>0</td><td>Mele biologiche</td></tr><tr><td>1</td><td>Arance biologiche</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Azione terminate

Questa azione arresta l'esecuzione dell'istanza del flusso di lavoro dell'app per la logica, annulla tutte le azioni in corso, ignora tutte le restanti azioni e restituisce lo stato specificato. Ad esempio, è possibile usare l'azione **Terminate** quando l'app per la logica deve chiudersi completamente a causa di uno stato di errore. Questa azione non influisce sulle azioni già completate e non può apparire all'interno dei cicli **Foreach** e **Until**, inclusi i cicli sequenziali. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*status*> | string | Stato da restituire per l'esecuzione: "Failed", "Cancelled" o "Succeeded" |
|||| 

*Facoltativo*

Le proprietà per l'oggetto "runStatus" si applicano solo quando la proprietà "runStatus" è impostata sullo stato "Failed".

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*error-code-or-name*> | string | Il codice o il nome dell'errore |
| <*error-message*> | string | Il messaggio o il testo che descrive l'errore e le azioni che l'utente dell'app può eseguire | 
|||| 

*Esempio*

Questa definizione di azione arresta un'esecuzione del flusso di lavoro, imposta lo stato di esecuzione su "Failed" e restituisce lo stato, un codice di errore e un messaggio di errore:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Azione wait  

Questa azione sospende l'esecuzione del flusso di lavoro per l'intervallo specificato o fino all'ora specificata, ma non entrambe le cose. 

*Intervallo specificato*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Orario specificato*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*number-of-units*> | Integer | Per l'azione **Delay**, il numero di unità da attendere | 
| <*interval*> | string | Per l'azione **Delay**, l'intervallo di attesa: "Second", "Minute", "Hour", "Day", "Week", "Month" | 
| <*date-time-stamp*> | string | Per l'azione **Delay Until**, la data e l'ora per riprendere l'esecuzione. Questo valore deve usare la [data e ora in formato UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Esempio 1*

Questa definizione di azione sospende il flusso di lavoro per 15 minuti:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Esempio 2*

Questa definizione di azione sospende il flusso di lavoro fino all'orario specificato:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Azione workflow

Questa azione chiama un'altra app per la logica creata in precedenza, ovvero è possibile includere e riusare altri flussi di lavoro di app per la logica. È anche possibile usare gli output dall'app per la logica figlio o *nidificata* nelle azioni che seguono l'app per la logica nidificata, a condizione che l'app per la logica figlio restituisca una risposta.

Il motore delle app per la logica controlla l'accesso al trigger che si intende chiamare, quindi verificare che sia possibile accedere a tale trigger. Inoltre, l'app per la logica nidificata deve soddisfare questi criteri:

* Un trigger rende richiamabile l'app per la logica nidificata, ad esempio un trigger [Request](#request-trigger) o [HTTP](#http-trigger)

* La stessa sottoscrizione di Azure dell'app per la logica padre

* Per usare gli output dell'app per la logica nidificata nell'app per la logica padre, l'app per la logica nidificata deve avere un'azione [Response](#response-action) 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | string | Il nome dell'app per la logica che si vuole richiamare | 
| <*trigger-name*> | string | Il nome del trigger nell'app per la logica nidificata che si vuole richiamare | 
| <*Azure-subscription-ID*> | string | L'ID sottoscrizione di Azure per l'app per la logica nidificata |
| <*Azure-resource-group*> | string | Il nome del gruppo di risorse di Azure dell'app per la logica nidificata |
| <*nested-logic-app-name*> | string | Il nome dell'app per la logica che si vuole richiamare |
||||

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------|  
| <*header-content*> | Oggetto JSON | Le eventuali intestazioni da inviare con la chiamata | 
| <*body-content*> | Oggetto JSON | Gli eventuali messaggi da inviare con la chiamata | 
||||

*Outputs*

Gli output di questa azione variano a seconda dell'azione Response dell'app per la logica nidificata. Se l'app per la logica nidificata non include un'azione Response, gli output sono vuoti.

*Esempio*

Dopo che l'azione "Start_search" viene completata correttamente, questa definizione di azioni del flusso di lavoro chiama un'altra app per la logica denominata "Get_product_information", che passa gli input specificati: 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Dettagli delle azioni di controllo del flusso di lavoro

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Azione foreach

Questa azione di esecuzione a ciclo continuo scorre una matrice ed esegue azioni per ogni elemento. Per impostazione predefinita, il ciclo "for each" viene eseguito in parallelo fino a un numero massimo di cicli. Per questo limite, vedere [Limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Informazioni su [come creare cicli "for each"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Obbligatorio* 

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*action-1...n*> | string | I nomi delle azioni in esecuzione in ogni elemento della matrice | 
| <*action-definition-1...n*> | Oggetto JSON | Le definizioni delle azioni in esecuzione | 
| <*for-each-expression*> | string | L'espressione che fa riferimento a ogni elemento nella matrice specificata | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*count*> | Integer | Per impostazione predefinita, le iterazioni del ciclo "for each" vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare questo limite impostando un nuovo valore <*count*>, vedere [Modificare la concorrenza del ciclo "for each"](#change-for-each-concurrency). | 
| <*operation-option*> | string | Per eseguire un ciclo "for each" in modo sequenziale anziché in parallelo, impostare <*operation-option*> su `Sequential` o <*count*> su `1` ma non entrambi. Per altre informazioni, vedere [Eseguire il ciclo "for each" in modo sequenziale](#sequential-for-each). | 
|||| 

*Esempio*

Questo ciclo "for each" Invia un messaggio e-mail per ogni elemento della matrice, che contiene gli allegati da un messaggio e-mail in arrivo. Il ciclo invia un messaggio e-mail, incluso l'allegato, a una persona che esamina l'allegato.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Per specificare solo una matrice che viene passata come output del trigger, questa espressione ottiene la matrice <*array-name*> dal corpo del trigger. Per evitare un errore se la matrice non esiste l'espressione usa l'operatore `?`:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Azione If

Questa azione, ovvero un'*istruzione condizionale*, valuta un'espressione che rappresenta una condizione ed esegue un ramo diverso a seconda che la condizione sia true o false. Se la condizione è true, viene contrassegnata con lo stato "Succeeded". Informazioni su [Come creare istruzioni condizionali](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*condition*> | Oggetto JSON | La condizione, che può essere un'espressione, da valutare | 
| <*action-1*> | Oggetto JSON | L'azione da eseguire quando il valore di <*condition*> restituisce true | 
| <*action-definition*> | Oggetto JSON | La definizione dell'azione | 
| <*action-2*> | Oggetto JSON | L'azione da eseguire quando il valore di <*condition*> restituisce false | 
|||| 

Le azioni negli oggetti `actions` o `else` ottengono questi stati:

* "Succeeded" quando vengono eseguite e riescono
* "Failed" quando vengono eseguite e non riescono
* "Skipped" quando il rispettivo ramo non viene eseguito

*Esempio*

Questa condizione specifica che quando la variabile integer ha un valore maggiore di zero, il flusso di lavoro controlla un sito Web. Se la variabile è zero o inferiore a zero, il flusso di lavoro controlla un altro sito Web.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>In che modo le condizioni usano le espressioni

Ecco alcuni esempi che mostrano come è possibile usare espressioni nelle condizioni:
  
| JSON | Risultato | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | Solo per espressioni booleane, la condizione passa per qualsiasi valore che restituisce true. <p>Per convertire altri tipi in valori in booleano, usare la funzione `empty()` o `equals()`. | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | Per le funzioni di confronto, l'azione viene eseguita solo quando l'output di <*action*> è maggiore del valore <*threshold*>. | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | Per le funzioni per la logica e la creazione di espressioni booleane nidificate, l'azione viene eseguita quando l'output di <*action*> è maggiore del valore <*threshold*> o inferiore a 100. | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | È possibile usare funzioni di matrice per controllare se una matrice contiene elementi. L'azione viene eseguita quando la matrice `errors` è vuota. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Azione scope

Questa azione raggruppa logicamente le azioni in *ambiti*, che ottengono un proprio stato al termine dell'esecuzione delle azioni nell'ambito. È quindi possibile usare lo stato dell'ambito per determinare se sono in esecuzione altre azioni. Informazioni su [come creare gli ambiti](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------|  
| <*inner-action-1...n*> | Oggetto JSON | Una o più azioni da eseguire all'interno dell'ambito |
| <*action-inputs*> | Oggetto JSON | Gli input per ogni azione |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Azione switch

Questa azione, nota anche come *istruzione switch*, organizza le altre azioni in *casi* e assegna un valore a ogni caso ad eccezione del caso predefinito, se presente. Quando viene eseguito il flusso di lavoro, l'azione **Switch** confronta il valore da un'espressione, un oggetto o un token rispetto ai valori specificati per ogni caso. Se l'azione **Switch** trova un caso corrispondente, il flusso di lavoro esegue solo le azioni per il caso specifico. Ogni volta che viene eseguita l'azione **Switch** esiste un solo caso corrispondente oppure non ci sono corrispondenze. Se non ci sono corrispondenze l'azione **Switch** esegue le azioni predefinite. Informazioni su [come creare le istruzioni switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Obbligatorio*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Variabile | Espressione, oggetto JSON o token da valutare | 
| <*action-name*> | string | Il nome dell'azione da eseguire per il caso corrispondente | 
| <*action-definition*> | Oggetto JSON | La definizione dell'azione da eseguire per il caso corrispondente | 
| <*matching-value*> | Variabile | Il valore da confrontare con il risultato valutato | 
|||| 

*Facoltativo*

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*default-action-name*> | string | Il nome dell'azione predefinita da eseguire quando non è presente alcun caso corrispondente | 
| <*default-action-definition*> | Oggetto JSON | La definizione dell'azione da eseguire quando non è presente alcun caso corrispondente | 
|||| 

*Esempio*

Questa definizione di azione stabilisce se la persona che risponde all'e-mail di richiesta di approvazione ha selezionato l'opzione "Approve" o "Reject". In base a questa scelta l'azione **Switch** esegue le azioni per il caso corrispondente, che prevede l'invio di un'altra e-mail al risponditore ma con una formulazione diversa in ogni caso. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Azione Until

Questa azione del ciclo contiene azioni che vengono eseguite fino a che la condizione specificata è true. Il ciclo verifica la condizione come ultimo passaggio dopo l'esecuzione di tutte le altre azioni. È possibile includere più di un'azione nell'oggetto `"actions"` e l'azione deve definire almeno un limite. Informazioni su come [creare cicli "until"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Valore | Type | Descrizione | 
|-------|------|-------------| 
| <*action-name*> | string | Il nome per l'azione da eseguire all'interno del ciclo | 
| <*action-type*> | string | Il tipo di azione da eseguire | 
| <*action-inputs*> | Vari | Gli input per l'azione da eseguire | 
| <*condition*> | string | La condizione o espressione da valutare dopo completamento dell'esecuzione di tutte le azioni nel ciclo | 
| <*loop-count*> | Integer | Limite sul numero massimo di cicli che l'azione può eseguire. Il valore predefinito `count` è 60. | 
| <*loop-timeout*> | string | Il limite sulla durata massima di esecuzione del ciclo. Il valore predefinito `timeout` è `PT1H`, che è il [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) obbligatorio. |
|||| 

*Esempio*

Questa definizione di azione del ciclo invia una richiesta HTTP all'URL specificato fino a quando non viene soddisfatta una delle seguenti condizioni: 

* La richiesta ottiene una risposta con codice di stato "200 OK".
* Il ciclo è stato eseguito 60 volte.
* Il ciclo è stato eseguito per un'ora.

```json
 "Run_until_loop_succeeds_or_expires": {
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
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhook e sottoscrizioni

I trigger e le azioni basati su Webhook non controllano regolarmente gli endpoint, ma attendono invece eventi o dati specifici presso tali endpoint. Queste azioni e trigger *effettuano una sottoscrizione* agli endpoint, fornendo un *URL di callback* a cui l'endpoint può inviare le risposte.

La chiamata `subscribe` viene effettuata ogni volta che il flusso di lavoro viene modificato, ad esempio quando vengono rinnovate le credenziali o cambiano i parametri di input di un trigger o di un'azione. Questa chiamata usa gli stessi parametri delle azioni HTTP standard. 

La chiamata `unsubscribe` viene effettuata automaticamente quando un'operazione rende il trigger o l'azione non validi, ad esempio:

* Eliminazione o disabilitazione del trigger. 
* Eliminazione o disabilitazione del flusso di lavoro. 
* Eliminazione o disabilitazione della sottoscrizione. 

Per supportare queste chiamate, l'espressione `@listCallbackUrl()` restituisce un "URL di callback" univoco per questo trigger o azione. Questo URL rappresenta un identificatore univoco per gli endpoint che usano l'API REST del servizio. I parametri per questa funzione sono gli stessi del trigger o dell'azione di webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Modificare la durata asincrona

Per i trigger e azioni, è possibile limitare la durata del modello asincrono a un intervallo di tempo specifico aggiungendo la proprietà `limit.timeout`. In questo modo, se l'azione non è terminata quando è trascorso l'intervallo, lo stato dell'azione è contrassegnato come `Cancelled` con il codice `ActionTimedOut`. La proprietà `timeout` usa il [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Impostazioni configurazione di runtime

È possibile modificare il comportamento di runtime predefinito dei trigger e delle azioni con queste proprietà `runtimeConfiguration` nella definizione del trigger o dell'azione.

| Proprietà | Type | Descrizione | Trigger o azione | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Modifica il [*limite predefinito*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) sul numero di istanze dell'app per la logica che è possibile eseguire contemporaneamente o in parallelo. Questo valore può limitare il numero di richieste ricevute dai sistemi back-end. <p>L'impostazione della proprietà `runs` su `1` funziona allo stesso modo dell'impostazione della proprietà `operationOptions` su `SingleInstance`. È possibile impostare una delle proprietà, ma non entrambe. <p>Per modificare il limite predefinito, vedere [Modificare la concorrenza dei trigger](#change-trigger-concurrency) oppure [Attivazione sequenziale delle istanze](#sequential-trigger). | Tutti i trigger | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Modifica il [*limite predefinito*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) sul numero di istanze dell'app per la logica che possono attendere l'esecuzione quando nell'app per la logica è già in esecuzione il numero massimo di istanze simultanee. È possibile modificare il limite di concorrenza nella proprietà `concurrency.runs`. <p>Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](#change-waiting-runs). | Tutti i trigger | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Modifica il [*limite predefinito*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) sul numero di iterazioni del ciclo "for each" che è possibile eseguire contemporaneamente o in parallelo. <p>L'impostazione della proprietà `repetitions` su `1` funziona allo stesso modo dell'impostazione della proprietà `operationOptions` su `SingleInstance`. È possibile impostare una delle proprietà, ma non entrambe. <p>Per modificare il limite predefinito, vedere [Modificare la concorrenza "for each"](#change-for-each-concurrency) oppure [Eseguire i cicli "for each" in sequenza](#sequential-for-each). | Azione: <p>[Foreach](#foreach-action) | 
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opzioni relative alle operazioni

È possibile modificare il comportamento predefinito dei trigger e delle azioni con la proprietà `operationOptions` nella definizione del trigger o dell'azione.

| Opzione dell'operazione | Type | Descrizione | Trigger o azione | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | string | Eseguire le azioni basate su HTTP in modo sincrono, anziché in modo asincrono. <p><p>Per impostare questa opzione, vedere [Eseguire le azioni in modo sincrono](#asynchronous-patterns). | Azioni: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Risposta](#response-action) | 
| `OptimizedForHighThroughput` | string | Modifica il [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) sul numero di esecuzioni di azioni per 5 minuti al [limite massimo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Per impostare questa opzione, vedere [Esecuzione in modalità velocità effettiva elevata](#run-high-throughput-mode). | Tutte le azioni | 
| `Sequential` | string | Esegue le iterazioni del ciclo "for each" una alla volta, anziché tutte contemporaneamente in parallelo. <p>L'opzione funziona allo stesso modo dell'impostazione della proprietà `runtimeConfiguration.concurrency.repetitions` su `1`. È possibile impostare una delle proprietà, ma non entrambe. <p><p>Per impostare questa opzione, vedere [Eseguire il ciclo "for each" in modo sequenziale](#sequential-for-each).| Azione: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | string | Esegue il trigger per ogni istanza dell'app per la logica in modo sequenziale e attende la fine dell'esecuzione attiva precedente prima di attivare la successiva istanza di app per la logica. <p><p>L'opzione funziona allo stesso modo dell'impostazione della proprietà `runtimeConfiguration.concurrency.runs` su `1`. È possibile impostare una delle proprietà, ma non entrambe. <p>Per impostare questa opzione, vedere [Attivazione delle istanze in modo sequenziale](#sequential-trigger). | Tutti i trigger | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Modifica della concorrenza dei trigger

Per impostazione predefinita, le istanze dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pertanto, ogni istanza del trigger viene attivata prima della fine dell'esecuzione dell'istanza dell'app per la logica precedente. Questo limite consente di controllare il numero di richieste ricevute dai sistemi back-end. 

Per modificare il limite predefinito, è possibile usare l'editor della visualizzazione codice o Progettazione app per la logica perché la modifica delle impostazioni di concorrenza tramite la finestra di progettazione aggiunge o aggiorna la proprietà `runtimeConfiguration.concurrency.runs` nella definizione del trigger sottostante e viceversa. Questa proprietà controlla il numero massimo di istanze dell'app per la logica che è possibile eseguire in parallelo. 

> [!NOTE] 
> Se si imposta l'esecuzione sequenziale del trigger tramite la finestra di progettazione o l'editor della visualizzazione codice, non impostare la proprietà `operationOptions` del trigger su `SingleInstance` nell'editor della visualizzazione codice. Altrimenti si verifica un errore di convalida. Per altre informazioni, vedere [Attivazione delle istanze in modo sequenziale](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Modifica in visualizzazione codice 

Nella definizione sottostante del trigger, aggiungere o aggiornare la proprietà `runtimeConfiguration.concurrency.runs` su un valore compreso tra `1` e `50` (inclusi).

Di seguito è riportato un esempio che limita le esecuzioni simultanee a 10 istanze:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Modifica in Progettazione app per la logica

1. Nell'angolo superiore destro del trigger scegliere il pulsante con i puntini di sospensione (...), quindi scegliere **Impostazioni**.

2. In **Controllo concorrenza** attivare l'opzione **Limite** impostandola su **Sì**. 

3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) sul valore desiderato. Per eseguire l'app per la logica in modo sequenziale, trascinare il dispositivo di scorrimento e impostarlo su **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Modifica della concorrenza "for each"

Per impostazione predefinita, le iterazioni del ciclo "for each" vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Per modificare il limite predefinito, è possibile usare l'editor della visualizzazione codice o Progettazione app per la logica perché la modifica delle impostazioni di concorrenza tramite la finestra di progettazione aggiunge o aggiorna la proprietà `runtimeConfiguration.concurrency.repetitions` nella definizione dell'azione "for each" sottostante e viceversa. Questa proprietà controlla il numero massimo di iterazioni che è possibile eseguire in parallelo.

> [!NOTE] 
> Se si imposta l'esecuzione sequenziale dell'azione "for each" tramite la finestra di progettazione o l'editor della visualizzazione codice, non impostare la proprietà `operationOptions` dell'azione su `Sequential` nell'editor della visualizzazione codice. Altrimenti si verifica un errore di convalida. Per altre informazioni, vedere [Eseguire il ciclo "for each" in modo sequenziale](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Modifica in visualizzazione codice 

Nella definizione sottostante di "for each" aggiungere o aggiornare la proprietà `runtimeConfiguration.concurrency.repetitions` su un valore compreso tra `1` e `50` (inclusi). 

Di seguito è riportato un esempio che limita le esecuzioni simultanee a 10 iterazioni:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Modifica in Progettazione app per la logica

1. Nell'angolo superiore destro dell'azione **For each** scegliere il pulsante con puntini di sospensione (...) e quindi **Impostazioni**.

2. In **Controllo concorrenza** attivare l'opzione **Controllo concorrenza** impostandola su **Sì**. 

3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) sul valore desiderato. Per eseguire l'app per la logica in modo sequenziale, trascinare il dispositivo di scorrimento e impostarlo su **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Modifica del limite delle esecuzioni in attesa

Per impostazione predefinita, le istanze dell'app per la logica vengono eseguite contemporaneamente o in parallelo fino al [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ogni istanza del trigger viene attivata prima della fine dell'esecuzione dell'istanza dell'app per la logica precedentemente attiva. Sebbene sia possibile [modificare questo limite predefinito](#change-trigger-concurrency), quando il numero di istanze dell'app per la logica raggiunge il nuovo limite di concorrenza, tutte le altre nuove istanze devono attendere per l'esecuzione. 

Il numero di esecuzioni che possono rimanere in attesa ha anche un [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), che è possibile modificare. Tuttavia, dopo che l'app per la logica raggiunge il limite di esecuzioni di attesa, il motore di App per la logica non accetta più nuove esecuzioni. I trigger di richiesta e webhook restituiscono errori 429 e i trigger ricorrenti iniziano a ignorare i tentativi di polling.

Per modificare il limite predefinito per le esecuzioni in attesa, nella definizione del trigger sottostante aggiungere la proprietà `runtimeConfiguration.concurency.maximumWaitingRuns` con un valore compreso tra `0` e `100`. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Attivare le istanze in sequenza

Per eseguire ciascuna istanza di app per la logica solo dopo il completamento dell'esecuzione dell'istanza precedente, impostare l'esecuzione del trigger in modo sequenziale. È possibile usare l'editor della visualizzazione codice o Progettazione app per la logica perché la modifica delle impostazioni di concorrenza tramite la finestra di progettazione aggiunge o aggiorna anche la proprietà `runtimeConfiguration.concurrency.runs` nella definizione del trigger sottostante e viceversa. 

> [!NOTE] 
> Quando si imposta l'esecuzione sequenziale di un trigger tramite la finestra di progettazione o l'editor della visualizzazione codice, non impostare la proprietà `operationOptions` del trigger su `Sequential` nell'editor della visualizzazione codice. Altrimenti si verifica un errore di convalida. 

#### <a name="edit-in-code-view"></a>Modifica in visualizzazione codice

Nella definizione del trigger, impostare una di queste proprietà, ma non entrambe. 

Impostare la proprietà `runtimeConfiguration.concurrency.runs` su `1`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-o-*

Impostare la proprietà `operationOptions` su `SingleInstance`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Modifica in Progettazione app per la logica

1. Nell'angolo superiore destro del trigger scegliere il pulsante con i puntini di sospensione (...), quindi scegliere **Impostazioni**.

2. In **Controllo concorrenza** attivare l'opzione **Limite** impostandola su **Sì**. 

3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) sul numero `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Esecuzione dei cicli "for each" in modo sequenziale

Per eseguire un'iterazione del ciclo "for each" solo al termine dell'esecuzione dell'iterazione precedente, impostare l'azione "for each" per l'esecuzione sequenziale. È possibile usare l'editor della visualizzazione codice o Progettazione app per la logica perché la modifica della concorrenza dell'azione tramite la finestra di progettazione aggiunge o aggiorna anche la proprietà `runtimeConfiguration.concurrency.repetitions` nella definizione dell'azione sottostante e viceversa. 

> [!NOTE] 
> Quando si imposta l'esecuzione sequenziale di un'azione "for each" tramite la finestra di progettazione o l'editor della visualizzazione codice, non impostare la proprietà `operationOptions` dell'azione su `Sequential` nell'editor della visualizzazione codice. Altrimenti si verifica un errore di convalida. 

#### <a name="edit-in-code-view"></a>Modifica in visualizzazione codice

Nella definizione dell'azione impostare una di queste proprietà ma non entrambe. 

Impostare la proprietà `runtimeConfiguration.concurrency.repetitions` su `1`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-o-*

Impostare la proprietà `operationOptions` su `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Modifica in Progettazione app per la logica

1. Nell'angolo superiore destro dell'azione **For each**, scegliere il pulsante con puntini di sospensione (...), quindi scegliere **Impostazioni**.

2. In **Controllo concorrenza** attivare l'opzione **Controllo concorrenza** impostandola su **Sì**. 

3. Trascinare il dispositivo di scorrimento **Degree of Parallelism** (Grado di parallelismo) sul numero `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Esecuzione delle azioni in modo sincrono

Per impostazione predefinita, tutte le azioni basate su HTTP seguono il modello di operazione asincrono standard. Questo modello consente di specificare che quando un'azione basata su HTTP invia una richiesta all'endpoint specificato, il server remoto invia una risposta "202 ACCEPTED". Questa risposta indica che il server ha accettato la richiesta per l'elaborazione. Il motore di App per la logica continua a controllare l'URL specificato dall'intestazione del percorso della risposta finché non si arresta l'elaborazione, ovvero se riceve qualsiasi risposta diversa da 202.

Tuttavia, le richieste hanno un limite di timeout in modo che per le azioni a esecuzione prolungata, è possibile disabilitare il comportamento asincrono aggiungendo e impostando la proprietà `operationOptions` su `DisableAsyncPattern` negli input dell'azione.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Esecuzione in modalità velocità effettiva elevata

Per un'esecuzione singola dell'app per la logica, il numero di azioni che vengono eseguite ogni 5 minuti ha un [limite predefinito](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Per aumentare questo limite al [massimo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) possibile impostare la proprietà `operationOptions` su `OptimizedForHighThroughput`. Questa impostazione pone l'app per la logica in modalità "velocità effettiva elevata". 

> [!NOTE]
> La modalità di velocità effettiva elevata è in anteprima. È anche possibile distribuire un carico di lavoro tra più app per la logica, se necessario.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Eseguire l'autenticazione di trigger e azioni HTTP

Gli endpoint HTTP supportano diversi tipi di autenticazione. È possibile configurare l'autenticazione per tali trigger e azioni HTTP:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [Webhook HTTP](../connectors/connectors-native-webhook.md)

Ecco i tipi di autenticazione che è possibile configurare:

* [Autenticazione di base](#basic-authentication)
* [Autenticazione con certificato client](#client-certificate-authentication)
* [Autenticazione OAuth di Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Assicurarsi di proteggere eventuali informazioni riservate gestite dalla definizione del flusso di lavoro dell'app per la logica. Usare i parametri protetti e codificare i dati in base alle esigenze. Per altre informazioni sull'uso e sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticazione di base

Per l'[autenticazione di base](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) usando Azure Active Directory, la definizione di trigger o azione può includere un oggetto JSON `authentication`, che dispone delle proprietà specificate nella tabella seguente. Per accedere ai valori dei parametri durante il runtime, si può usare l'espressione `@parameters('parameterName')` fornita dal [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs). 

| Proprietà | Obbligatoria | Valore | Descrizione | 
|----------|----------|-------|-------------| 
| **type** | Sì | "Basic" | Il tipo di autenticazione da usare, in questo caso, "Basic" | 
| **username** | Sì | "@parameters('userNameParam')" | Il nome utente per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
| **password** | Sì | "@parameters('passwordParam')" | La password per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
||||| 

In questa definizione dell'azione HTTP di esempio, la sezione `authentication` specifica l'autenticazione `Basic`. Per altre informazioni sull'uso e sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Assicurarsi di proteggere eventuali informazioni riservate gestite dalla definizione del flusso di lavoro dell'app per la logica. Usare i parametri protetti e codificare i dati in base alle esigenze. Per altre informazioni sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticazione con certificato client

Per l'[autenticazione basata sul certificato](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) usando Azure Active Directory, la definizione di trigger o azione può includere un oggetto JSON `authentication`, che ha le proprietà specificate nella tabella seguente. Per accedere ai valori dei parametri durante il runtime, si può usare l'espressione `@parameters('parameterName')` fornita dal [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs). Per i limiti sul numero di certificati client che è possibile usare, vedere [Limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md).

| Proprietà | Obbligatoria | Valore | Descrizione |
|----------|----------|-------|-------------|
| **type** | Sì | "ClientCertificate" | Il tipo di autenticazione da usare per i certificati client di Secure Sockets Layer (SSL). Benché siano supportati i certificati autofirmati, non sono supportati i certificati autofirmati per SSL. |
| **pfx** | Sì | "@parameters('pfxParam') | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) |
| **password** | Sì | "@parameters('passwordParam')" | Password per accedere al file PFX. |
||||| 

In questa definizione dell'azione HTTP di esempio, la sezione `authentication` specifica l'autenticazione `ClientCertificate`. Per altre informazioni sull'uso e sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Assicurarsi di proteggere eventuali informazioni riservate gestite dalla definizione del flusso di lavoro dell'app per la logica. Usare i parametri protetti e codificare i dati in base alle esigenze. Per altre informazioni sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Autenticazione OAuth di Azure Active Directory (AD)

Per l'[autenticazione OAuth di Azure Active Directory](../active-directory/develop/authentication-scenarios.md), la definizione di trigger o azione può includere un oggetto JSON `authentication`, che dispone delle proprietà specificate nella tabella seguente. Per accedere ai valori dei parametri durante il runtime, si può usare l'espressione `@parameters('parameterName')` fornita dal [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs).

| Proprietà | Obbligatoria | Value | Descrizione |
|----------|----------|-------|-------------|
| **type** | Sì | `ActiveDirectoryOAuth` | Il tipo di autenticazione da usare, ovvero "ActiveDirectoryOAuth" per OAuth di Azure AD |
| **authority** | No  | <*URL-for-authority-token-issuer*> | L'URL per l'autorità che fornisce il token di autenticazione |
| **tenant** | Sì | <*tenant-ID*> | L'ID tenant per il tenant di Azure AD |
| **audience** | Sì | <*resource-to-authorize*> | La risorsa che si vuole usare per l'autorizzazione, ad esempio `https://management.core.windows.net/` |
| **clientId** | Sì | <*client-ID*> | L'ID client per l'app richiedente l'autorizzazione |
| **credentialType** | Sì | "Certificate" o "Secret" | Il tipo di credenziale client utilizzata per la richiesta di autorizzazione. Tale proprietà e valore non viene visualizzata nella definizione sottostante, ma determina i parametri obbligatori per il tipo di credenziale. |
| **pfx** | Sì, solo per il tipo di credenziale "Certificato" | "@parameters('pfxParam') | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) |
| **password** | Sì, solo per il tipo di credenziale "Certificato" | "@parameters('passwordParam')" | Password per accedere al file PFX. |
| **secret** | Sì, solo per il tipo di credenziale "Segreto" | "@parameters('secretParam')" | Il segreto client per la richiesta dell'autorizzazione |
|||||

In questa definizione dell'azione HTTP di esempio, la sezione `authentication` specifica l'autenticazione `ActiveDirectoryOAuth` e il tipo di credenziale "Secret". Per altre informazioni sull'uso e sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Assicurarsi di proteggere eventuali informazioni riservate gestite dalla definizione del flusso di lavoro dell'app per la logica. Usare i parametri protetti e codificare i dati in base alle esigenze. Per altre informazioni sulla protezione dei parametri, vedere [Proteggere l'app per la logica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Passaggi successivi

* Leggere altre informazioni sul [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md)
