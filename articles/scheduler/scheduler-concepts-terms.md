---
title: Entità, termini e concetti di Utilità di pianificazione di Microsoft Azure | Microsoft Docs
description: Informazioni sui concetti, terminologia e gerarchia di entità di Utilità di pianificazione di Azure, inclusi processi e raccolte di processi
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: get-started-article
ms.date: 08/18/2016
ms.openlocfilehash: 5ed15a58e5b709b003e9f45d04c3654f814aefc7
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334228"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Concetti, terminologia ed entità di Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Gerarchia di entità

L'API REST di Utilità di pianificazione di Microsoft Azure espone e utilizza le entità principali o le risorse:

| Entità | DESCRIZIONE |
|--------|-------------|
| **Processo** | Definisce una singola azione ricorrente, con strategie semplici o complesse per l'esecuzione. Le azioni possono includere HTTP, coda di archiviazione, coda del bus di servizio o richieste di argomento del bus di servizio. | 
| **Raccolta di processi** | Contiene un gruppo di processi e gestisce le impostazioni, le quote e le limitazioni condivise dai processi della raccolta. In quanto proprietario della sottoscrizione di Azure, è possibile creare raccolte di processi e raggruppare i processi in base ai limiti di utilizzo o dell'applicazione. Una raccolta processi presenta questi attributi: <p>- È vincolata a un'area. <br>- Consente di applicare quote in modo che sia possibile limitare l'utilizzo per tutti i processi in una raccolta. <br>- Le quote includono MaxJobs e MaxRecurrence. | 
| **Cronologia processi** | Descrive i dettagli per l'esecuzione del processo, ad esempio, lo stato e i dettagli della risposta. |
||| 

## <a name="entity-management"></a>Gestione delle entità

A livelli elevati, l'API REST di Utilità di pianificazione di Microsoft Azure espone queste operazioni per la gestione delle entità.

### <a name="job-management"></a>Gestione dei processi

Supporta le operazioni per la creazione e modifica di processi. Tutti i processi devono appartenere a una raccolta di processi già esistente e che non viene creata implicitamente. Per altre informazioni, vedere l'articolo relativo all'[API REST di Utilità di pianificazione di Microsoft Azure - Processi](https://docs.microsoft.com/rest/api/scheduler/jobs). Di seguito, l'indirizzo URI per queste operazioni:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`

### <a name="job-collection-management"></a>Gestione delle raccolte di processi

Supporta le operazioni per la creazione e modifica di processi e raccolte di processi, esecuzione del mapping delle quote e impostazioni condivise. Ad esempio, le quote specificano il numero massimo di processi e l'intervallo minimo delle ricorrenze. Per altre informazioni, vedere l'articolo relativo all'[API REST di Utilità di pianificazione di Microsoft Azure - Raccolte di processi](https://docs.microsoft.com/rest/api/scheduler/jobcollections). Di seguito, l'indirizzo URI per queste operazioni:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`

### <a name="job-history-management"></a>Gestione della cronologia dei processi

Supporta l’operazione GET per il recupero di 60 giorni di cronologia di esecuzioni del processo, ad esempio, il tempo di esecuzione del processo e i risultati dell'esecuzione del processo. Include il supporto del parametro della stringa di query per filtrare in base a stato e status. Per altre informazioni, vedere l'articolo relativo all'[API REST di Utilità di pianificazione di Microsoft Azure - Processi - Lista cronologia processi](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory). Di seguito, l'indirizzo URI per questa operazione:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`

## <a name="job-types"></a>Tipi di processo

Utilità di pianificazione di Microsoft Azure supporta più tipi di processo: 

* Processi HTTP, inclusi i processi HTTPS che supportano SSL, nel caso di endpoint per un servizio o un carico di lavoro esistente
* Processi di code di archiviazione per carichi di lavoro che usano le code di archiviazione, ad esempio l'invio di messaggi alle code di archiviazione
* Processi di coda del bus di servizio per i carichi di lavoro che usano le code del bus di servizio
* Processi degli argomenti del bus di servizio per i carichi di lavoro che usano gli argomenti del bus di servizio

## <a name="job-definition"></a>Definizione del processo

A un livello elevato, un processo di Utilità di pianificazione di Microsoft Azure include i seguenti elementi di base:

* L'azione eseguita quando viene si attiva il timer del processo
* Facoltativo: il tempo necessario per eseguire il processo
* Facoltativo: quando e quanto spesso ripetere il processo
* Facoltativo: un'azione di errore da eseguire in caso di esito negativo dell'azione primaria

Il processo include anche i dati forniti dal sistema, ad esempio l'ora successiva di esecuzione pianificata del processo. La definizione di codice del processo è un oggetto in formato JavaScript Object Notation (JSON), con questi elementi:

| Elemento | Obbligatoria | DESCRIZIONE | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | No  | L'ora di inizio per il processo con una differenza di fuso orario in [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) | 
| [**azione**](#action) | Yes | I dettagli per l'azione principale, che può includere un oggetto **errorAction** | 
| [**errorAction**](#error-action) | No  | I dettagli per l’azione secondaria eseguita se l'azione principale ha esito negativo |
| [**recurrence**](#recurrence) | No  | I dettagli, ad esempio frequenza e intervallo per un processo ricorrente | 
| [**retryPolicy**](#retry-policy) | No  | I dettagli per la frequenza con cui ripetere un'azione | 
| [**state**](#state) | Yes | I dettagli per lo stato del processo corrente |
| [**status**](#status) | Yes | I dettagli per lo stato del processo corrente, che viene controllato dal servizio |
||||

Ecco un esempio che illustra una definizione del processo completo per un'azione HTTP. I dettagli più completi degli elementi sono descritti nelle sezioni successive: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

Nell’oggetto **startTime**, è possibile specificare l'ora di inizio e una differenza di fuso orario nel [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>action

Il processo di Utilità di pianificazione di Microsoft Azure esegue un'**azione** primaria in base alla pianificazione specificata. Utilità di pianificazione di Microsoft Azure supporta azioni della coda del bus di servizio, dell'argomento del bus di servizio, della coda di archiviazione e HTTP. Se l'**azione** primaria ha esito negativo, Utilità di pianificazione di Microsoft Azure può eseguire una [**errorAction**](#errorAction) secondaria che gestisce l'errore. L’oggetto dell’**azione** descrive questi elementi:

* Tipo di servizio dell'azione
* Dettagli dell'azione
* **errorAction** alternativa

L'esempio precedente descrive un'azione HTTP. Qui è riportato un esempio di un'azione in coda di archiviazione:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Qui è riportato un esempio di azione di coda del bus di servizio:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Qui è riportato un esempio di azione di argomento del bus di servizio:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Per altre informazioni sui token delle firme di accesso condiviso (SAS), vedere [Autorizzazione con firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Se l'**azione** primaria del processo ha esito negativo, Utilità di pianificazione di Microsoft Azure può eseguire una **errorAction** che gestisce l'errore. Nell'**azione** primaria, è possibile specificare un oggetto della **errorAction** in modo che Utilità di pianificazione di Microsoft Azure possa chiamare un endpoint di gestione degli errori o inviare una notifica all'utente. 

Ad esempio, se si verifica un'emergenza in corrispondenza dell'endpoint primario, è possibile utilizzare **errorAction** per chiamare un endpoint secondario, o per notificare un endpoint di gestione degli errori. 

Proprio come l'**azione** primaria, l'azione di errore può essere semplice o a logica composita, sulla base delle altre azioni. 

<a name="recurrence"></a>

## <a name="recurrence"></a>ricorrenza

Un processo è ricorrente se la definizione JSON del processo include l’oggetto **ricorrenza**, ad esempio:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Proprietà | Obbligatoria | Valore | DESCRIZIONE | 
|----------|----------|-------|-------------| 
| **frequency** | Sì, quando viene utilizzata la **ricorrenza** | "Minute", "Hour", "Day", "Week", "Month", "Year" | L'unità di tempo tra le occorrenze | 
| **interval** | No  | da 1 a 1000 (inclusi) | Un numero intero positivo che determina il numero di unità di tempo tra ogni occorrenza sulla base della **frequenza** | 
| **schedule** | No  | Variabile | I dettagli per le pianificazioni più complesse e avanzate. Vedere **hours**, **minutes**, **weekDays**, **months**, e **monthDays** | 
| **hours** | No  | Da 1 a 24 | Una matrice con l'ora segna quando eseguire il processo | 
| **minutes** | No  | Da 1 a 24 | Una matrice con i minuti segna quando eseguire il processo | 
| **months** | No  | Da 1 a 12 | Una matrice con i mesi segna quando eseguire il processo | 
| **monthDays** | No  | Variabile | Una matrice con i giorni segna quando eseguire il processo | 
| **weekDays** | No  | "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday" | Una matrice con i giorni della settimana segna quando eseguire il processo | 
| **count** | No  | <*none*> | Il numero di ricorrenze. Il valore predefinito prevede la ricorrenza all'infinito. Non è possibile usare sia **count** che **endTime**, ma viene applicata la regola che termina per prima. | 
| **endTime** | No  | <*none*> | Data e ora di arresto della ricorrenza. Il valore predefinito prevede la ricorrenza all'infinito. Non è possibile usare sia **count** che **endTime**, ma viene applicata la regola che termina per prima. | 
||||

Per altre informazioni su questi elementi, vedere [Creare pianificazioni complesse e ricorrenze avanzate](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Nel caso in cui un processo di Utilità di pianificazione di Microsoft Azure abbia esito negativo, è possibile impostare un criterio di ripetizione, che determina se e come Utilità di pianificazione di Microsoft Azure ritenta l'azione. Per impostazione predefinita Utilità di pianificazione di Microsoft Azure tenta di eseguire nuovamente il processo quattro volte, a intervalli di 30 secondi. È possibile rendere più o meno aggressivo questo criterio, ad esempio, questo criterio ripete un'azione due volte al giorno:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Proprietà | Obbligatoria | Valore | DESCRIZIONE | 
|----------|----------|-------|-------------| 
| **retryType** | Yes | **Fixed**, **None** | Determina se sono stati specificati criteri di ripetizione (**fixed**) o no (**none**). | 
| **retryInterval** | No  | PT30S | Specifica l'intervallo e la frequenza tra i tentativi di ripetizione dei tentativi nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Il valore minimo è 15 secondi, mentre il valore massimo è 18 mesi. | 
| **retryCount** | No  | 4 | Specificare il numero di tentativi. Il valore massimo è 20. | 
||||

Per altre informazioni, vedere [Disponibilità e affidabilità elevate](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Lo stato del processo può essere **Attivo**, **Disattivato**, **Completato**, oppure **Con errori**, ad esempio: 

`"state": "Disabled"`

Per modificare i processi allo stato **Attivo** oppure **Disattivato**, è possibile usare l'operazione PUT o PATCH su tali processi.
Tuttavia, se un processo presenta lo stato **Completato** o **Con errori**, non è possibile aggiornare lo stato, benché sia possibile eseguire l'operazione di ELIMINAZIONE del processo. Utilità di pianificazione di Microsoft Azure elimina i processi completati e con errori dopo 60 giorni. 

<a name="status"></a>

## <a name="status"></a>status

Dopo l'avvio di un processo, Utilità di pianificazione di Microsoft Azure restituisce informazioni sullo stato del processo tramite l'oggetto **status**, che è controllato esclusivamente d Utilità di pianificazione di Microsoft Azure. Tuttavia, è possibile trovare l’oggetto **status** all'interno dell'oggetto **processo**. Ecco le informazioni che include lo stato del processo:

* Tempo per l'esecuzione precedente, se presente
* Tempo per la successiva esecuzione pianificata per i processi in corso
* Il numero di esecuzioni di processi
* Il numero di errori, se presenti
* Il numero di errori, se presenti

Ad esempio: 

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>Vedere anche 

* [Informazioni su Utilità di pianificazione di Azure](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità](scheduler-concepts-terms.md)
* [Creare pianificazioni complesse e ricorrenze avanzate](scheduler-advanced-complexity.md)
* [Limiti, quote, valori predefiniti e codici di errore](scheduler-limits-defaults-errors.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler)
* [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)
