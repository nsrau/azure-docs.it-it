<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Creare app per la logica in Servizio app di Azure. Wunderlist fornisce un elenco di attività e uno strumento di gestione attività per aiutare le persone a portare a termine ciò che devono fare. Che si tratti di condividere una lista della spesa con un familiare, lavorare a un progetto o pianificare una vacanza, Wunderlist consente di acquisire, condividere e completare le attività da svolgere in modo semplice. Wunderlist esegue immediatamente la sincronizzazione tra il telefono, il tablet e il computer, per consentire l'accesso a tutte le attività da qualsiasi posizione."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Introduzione al connettore Wunderlist

Wunderlist fornisce un elenco di attività e uno strumento di gestione attività per aiutare le persone a portare a termine ciò che devono fare. Che si tratti di condividere una lista della spesa con un familiare, lavorare a un progetto o pianificare una vacanza, Wunderlist consente di acquisire, condividere e completare le attività da svolgere in modo semplice. Wunderlist esegue immediatamente la sincronizzazione tra il telefono, il tablet e il computer, per consentire l'accesso a tutte le attività da qualsiasi posizione.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore Wunderlist può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Wunderlist sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Wunderlist
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recupera gli elenchi associati all'account|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Crea un elenco|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recupera le attività da un elenco specifico|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Crea un'attività|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recupera le sottoattività da un'attività o un elenco specifico|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Crea una sottoattività all'interno di un'attività specifica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recupera le note per un'attività o un elenco specifico|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Aggiunge una nota a un'attività specifica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recupera i commenti per un'attività o un elenco specifico|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Aggiunge un commento a un'attività specifica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recupera i promemoria per un'attività o un elenco specifico|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Imposta un promemoria|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recupera i file per un'attività o un elenco specifico|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera un elenco specifico|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Elimina un elenco|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Aggiorna un elenco specifico|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera un'attività specifica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Aggiorna un'attività specifica|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Elimina un'attività specifica|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera una sottoattività specifica|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Aggiorna una sottoattività specifica|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Elimina una sottoattività specifica|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recupera una nota specifica|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|Aggiorna una nota specifica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Elimina una nota specifica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recupera un commento specifico di un'attività|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Aggiorna un promemoria specifico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Elimina un promemoria specifico|
### Trigger di Wunderlist
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando un'attività è in scadenza|Attiva un nuovo flusso quando un'attività nell'elenco è in scadenza|
|Quando viene creata una nuova attività|Attiva un nuovo flusso quando viene creata una nuova attività nell'elenco|
|Quando si verifica un promemoria|Attiva un nuovo flusso quando si verifica un promemoria|


## Creare una connessione a Wunderlist
Per creare app per la logica con Wunderlist, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornisce le credenziali di Wunderlist|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.


>[AZURE.INCLUDE [Passaggi per creare una connessione a Wunderlist](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per Wunderlist
Si applica alla versione 1.0

## TriggerTaskDue
Quando un'attività è in scadenza: attiva un nuovo flusso quando un'attività nell'elenco è in scadenza.

```GET: /trigger/tasksdue```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## TriggerTaskNew
Quando viene creata una nuova attività: attiva un nuovo flusso quando viene creata una nuova attività nell'elenco.

```GET: /trigger/tasksnew```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## TriggerReminder
Quando si verifica un promemoria: attiva un nuovo flusso quando si verifica un promemoria.

```GET: /trigger/reminders```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## RetrieveLists
Ottenere elenchi: recupera gli elenchi associati all'account.

```GET: /lists```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateList
Creare un elenco: crea un elenco.

```POST: /lists```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuovo elenco da creare|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|default|Operazione non riuscita.|


## ListTasks
Ottenere attività: recupera le attività da un elenco specifico.

```GET: /tasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|Completata|boolean|no|query|nessuno|Completed|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateTask
Creare un'attività: crea un'attività.

```POST: /tasks```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova attività da creare|

#### Response

|Nome|Description|
|---|---|
|201|Data di creazione|


## ListSubTasks
Ottenere le sottoattività: recupera le sottoattività da un'attività o un elenco specifico.

```GET: /subtasks```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|
|Completata|boolean|no|query|nessuno|Completed|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateSubTask
Creare una sottoattività: crea una sottoattività all'interno di un'attività specifica.

```POST: /subtasks```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova sottoattività da creare|

#### Response

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## ListNotes
Ottenere le note: recupera le note per un'attività o un elenco specifico.

```GET: /notes```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateNote
Creare una nota: aggiunge una nota a un'attività specifica.

```POST: /notes```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova nota da creare|

#### Response

|Nome|Description|
|---|---|
|201|Data di creazione|


## ListComments
Ottenere i commenti di un'attività: recupera i commenti per un'attività o un elenco specifico.

```GET: /task_comments```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateComment
Aggiungere un commento a un'attività: aggiunge un commento a un'attività specifica.

```POST: /task_comments```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuovo commento dell'attività da creare|

#### Response

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## RetrieveReminders
Ottenere i promemoria: recupera i promemoria per un'attività o un elenco specifico.

```GET: /reminders```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateReminder
Impostare un promemoria: imposta un promemoria.

```POST: /reminders```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuovo promemoria da creare|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|default|Operazione non riuscita.|


## RetrieveFiles
Ottenere i file: recupera i file per un'attività o un elenco specifico.

```GET: /files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|task\_id|integer|no|query|nessuno|ID attività|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## GetList
Ottenere un elenco: recupera un elenco specifico.

```GET: /lists/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID elenco|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## DeleteList
Eliminare un elenco: elimina un elenco.

```DELETE: /lists/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID elenco|
|revision|integer|sì|query|nessuno|Revisione|

#### Response

|Nome|Description|
|---|---|
|204|Nessun contenuto|


## UpdateList
Aggiornare un elenco: aggiorna un elenco specifico.

```PATCH: /lists/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID elenco|
|post| |sì|body|nessuno|Dettagli elenco|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## GetTask
Ottenere un'attività: recupera un'attività specifica.

```GET: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## UpdateTask
Aggiornare un'attività: aggiorna un'attività specifica.

```PATCH: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|
|post| |sì|body|nessuno|Dettagli dell'attività|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## DeleteTask
Eliminare un'attività: elimina un'attività specifica.

```DELETE: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|integer|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|
|revision|integer|sì|query|nessuno|Revisione|

#### Response

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetSubTask
Ottenere una sottoattività: recupera una sottoattività specifica.

```GET: /subtasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID sottoattività|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## UpdateSubTask
Aggiornare una sottoattività: aggiorna una sottoattività specifica.

```PATCH: /subtasks/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID sottoattività|
|post| |sì|body|nessuno|Dettagli sottoattività|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## DeleteSubTask
Eliminare una sottoattività: elimina una sottoattività specifica.

```DELETE: /subtasks/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID sottoattività|
|revision|integer|sì|query|nessuno|Revisione|

#### Response

|Name|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetNote
Ottenere una nota: recupera una nota specifica.

```GET: /notes/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID nota|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|


## UpdateNote
Aggiornare una nota: aggiorna una nota specifica.

```PATCH: /notes/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID nota|
|post| |sì|body|nessuno|Dettagli nota|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteNote
Eliminare una nota: elimina una nota specifica.

```DELETE: /notes/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID nota|
|revision|integer|sì|query|nessuno|Revisione|

#### Response

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetComment
Ottenere un commento di un'attività: recupera un commento specifico di un'attività.

```GET: /task_comments/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID commento|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|


## UpdateReminder
Aggiornare un promemoria: aggiorna un promemoria specifico.

```PATCH: /reminders/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID promemoria|
|post| |sì|body|nessuno|Dettagli promemoria|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|


## DeleteReminder
Eliminare un promemoria: elimina un promemoria specifico.

```DELETE: /reminders/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID del promemoria|
|revision|integer|sì|query|nessuno|Revisione|

#### Response

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## Definizioni oggetti 

### Elenco


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|created\_at|stringa|No |
|title|string|No |
|list\_type|string|No |
|type|stringa|No |
|revision|integer|No |



### CreatedList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|created\_at|stringa|No |
|title|string|No |
|revision|integer|No |
|type|stringa|No |



### Attività


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|assignee\_id|integer|No |
|assigner\_id|integer|No |
|created\_at|stringa|No |
|created\_by\_id|integer|No |
|due\_date|string|No |
|list\_id|integer|No |
|revision|integer|No |
|starred|boolean|No |
|title|string|No |



### Subtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|created\_at|stringa|No |
|created\_by\_id|integer|No |
|revision|stringa|No |
|title|string|No |



### Note


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|content|stringa|No |
|created\_at|stringa|No |
|updated\_at|stringa|No |
|revision|integer|No |



### Comment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|revision|integer|No |
|text|stringa|No |
|type|stringa|No |
|created\_at|string|No |



### Reminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|date|string|No |
|task\_id|integer|No |
|revision|integer|No |
|type|stringa|No |
|created\_at|stringa|No |
|updated\_at|stringa|No |



### CreatedReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|date|stringa|No |
|task\_id|integer|No |
|revision|integer|No |
|created\_at|stringa|No |
|updated\_at|string|No |



### File


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|integer|No |
|URL|stringa|No |
|task\_id|integer|No |
|list\_id|integer|No |
|user\_id|integer|No |
|file\_name|string|No |
|content\_type|stringa|No |
|file\_size|integer|No |
|local\_created\_at|string|No |
|created\_at|stringa|No |
|updated\_at|string|No |
|type|stringa|No |
|revision|integer|No |



### NewTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|integer|Sì |
|title|stringa|Sì |
|assignee\_id|integer|No |
|Completata|boolean|No |
|recurrence\_type|stringa|No |
|recurrence\_count|integer|No |
|due\_date|stringa|No |
|starred|boolean|No |



### NewList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|title|stringa|Sì |



### NewSubtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|integer|Sì |
|task\_id|integer|Sì |
|title|stringa|Sì |
|Completata|boolean|No |



### NewNote


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|integer|Sì |
|task\_id|integer|Sì |
|content|string|Sì |



### NewComment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|integer|Sì |
|task\_id|integer|Sì |
|text|stringa|Sì |



### NewReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|integer|Sì |
|task\_id|integer|Sì |
|date|stringa|Sì |



### UpdateTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|integer|No |
|title|stringa|No |
|assignee\_id|integer|No |
|Completata|boolean|No |
|recurrence\_type|stringa|No |
|recurrence\_count|integer|No |
|due\_date|stringa|No |
|starred|boolean|No |



### UpdateList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|integer|No |
|title|string|No |



### UpdateSubtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|integer|No |
|title|stringa|No |
|Completata|boolean|No |



### UpdateNote


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|integer|No |
|content|stringa|No |



### UpdateReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|date|stringa|No |
|revision|integer|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->