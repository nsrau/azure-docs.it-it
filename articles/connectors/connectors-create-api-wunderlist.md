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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introduzione al connettore Wunderlist



Il connettore Wunderlist può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flusso](http://flows.microsoft.com)

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

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

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## TriggerTaskNew
Quando viene creata una nuova attività: attiva un nuovo flusso quando viene creata una nuova attività nell'elenco.

```GET: /trigger/tasksnew```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## TriggerReminder
Quando si verifica un promemoria: attiva un nuovo flusso quando si verifica un promemoria.

```GET: /trigger/reminders```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|


## RetrieveLists
Ottenere elenchi: recupera gli elenchi associati all'account.

```GET: /lists```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|

#### Risposta

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

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|default|Operazione non riuscita.|


## ListTasks
Ottenere attività: recupera le attività da un elenco specifico.

```GET: /tasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|Completata|boolean|no|query|nessuno|Completed|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateTask
Creare un'attività: crea un'attività.

```POST: /tasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova attività da creare|

#### Risposta

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## ListSubTasks
Ottenere le sottoattività: recupera le sottoattività da un'attività o un elenco specifico.

```GET: /subtasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|
|Completata|boolean|no|query|nessuno|Completed|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateSubTask
Creare una sottoattività: crea una sottoattività all'interno di un'attività specifica.

```POST: /subtasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova sottoattività da creare|

#### Risposta

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## ListNotes
Ottenere le note: recupera le note per un'attività o un elenco specifico.

```GET: /notes```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateNote
Creare una nota: aggiunge una nota a un'attività specifica.

```POST: /notes```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuova nota da creare|

#### Risposta

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## ListComments
Ottenere i commenti di un'attività: recupera i commenti per un'attività o un elenco specifico.

```GET: /task_comments```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|

#### Risposta

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

#### Risposta

|Nome|Descrizione|
|---|---|
|201|Data di creazione|


## RetrieveReminders
Ottenere i promemoria: recupera i promemoria per un'attività o un elenco specifico.

```GET: /reminders```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateReminder
Impostare un promemoria: imposta un promemoria.

```POST: /reminders```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|post| |sì|body|nessuno|Nuovo promemoria da creare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|default|Operazione non riuscita.|


## RetrieveFiles
Ottenere i file: recupera i file per un'attività o un elenco specifico.

```GET: /files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|task\_id|numero intero|no|query|nessuno|ID attività|

#### Risposta

|Nome|Descrizione|
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

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteList
Eliminare un elenco: elimina un elenco.

```DELETE: /lists/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID elenco|
|revision|numero intero|sì|query|nessuno|Revisione|

#### Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## UpdateList
Aggiornare un elenco: aggiorna un elenco specifico.

```PATCH: /lists/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID elenco|
|post| |sì|body|nessuno|Dettagli elenco|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## GetTask
Ottenere un'attività: recupera un'attività specifica.

```GET: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## UpdateTask
Aggiornare un'attività: aggiorna un'attività specifica.

```PATCH: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|
|post| |sì|body|nessuno|Dettagli dell'attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteTask
Eliminare un'attività: elimina un'attività specifica.

```DELETE: /tasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|numero intero|sì|query|nessuno|ID elenco|
|id|integer|sì|path|nessuno|ID attività|
|revision|numero intero|sì|query|nessuno|Revisione|

#### Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetSubTask
Ottenere una sottoattività: recupera una sottoattività specifica.

```GET: /subtasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID sottoattività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## UpdateSubTask
Aggiornare una sottoattività: aggiorna una sottoattività specifica.

```PATCH: /subtasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID sottoattività|
|post| |sì|body|nessuno|Dettagli sottoattività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteSubTask
Eliminare una sottoattività: elimina una sottoattività specifica.

```DELETE: /subtasks/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID sottoattività|
|revision|numero intero|sì|query|nessuno|Revisione|

#### Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetNote
Ottenere una nota: recupera una nota specifica.

```GET: /notes/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID nota|

#### Risposta

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

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteNote
Eliminare una nota: elimina una nota specifica.

```DELETE: /notes/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID nota|
|revision|numero intero|sì|query|nessuno|Revisione|

#### Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## GetComment
Ottenere un commento di un'attività: recupera un commento specifico di un'attività.

```GET: /task_comments/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|ID commento|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## UpdateReminder
Aggiornare un promemoria: aggiorna un promemoria specifico.

```PATCH: /reminders/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID promemoria|
|post| |sì|body|nessuno|Dettagli promemoria|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|


## DeleteReminder
Eliminare un promemoria: elimina un promemoria specifico.

```DELETE: /reminders/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|integer|sì|path|nessuno|ID del promemoria|
|revision|numero intero|sì|query|nessuno|Revisione|

#### Risposta

|Nome|Descrizione|
|---|---|
|204|Nessun contenuto|


## Definizioni oggetti 

### Elenco


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|created\_at|string|No |
|title|string|No |
|list\_type|string|No |
|type|string|No |
|revision|numero intero|No |



### CreatedList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|created\_at|string|No |
|title|string|No |
|revision|numero intero|No |
|type|string|No |



### Attività


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|assignee\_id|numero intero|No |
|assigner\_id|numero intero|No |
|created\_at|string|No |
|created\_by\_id|numero intero|No |
|due\_date|string|No |
|list\_id|numero intero|No |
|revision|numero intero|No |
|starred|boolean|No |
|title|string|No |



### Subtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|task\_id|numero intero|No |
|created\_at|string|No |
|created\_by\_id|numero intero|No |
|revision|string|No |
|title|string|No |



### Note


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|task\_id|numero intero|No |
|content|string|No |
|created\_at|string|No |
|updated\_at|string|No |
|revision|numero intero|No |



### Comment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|task\_id|numero intero|No |
|revision|numero intero|No |
|text|string|No |
|type|string|No |
|created\_at|string|No |



### Reminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|date|string|No |
|task\_id|numero intero|No |
|revision|numero intero|No |
|type|string|No |
|created\_at|string|No |
|updated\_at|string|No |



### CreatedReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|date|string|No |
|task\_id|numero intero|No |
|revision|numero intero|No |
|created\_at|string|No |
|updated\_at|string|No |



### File


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|numero intero|No |
|URL|string|No |
|task\_id|numero intero|No |
|list\_id|numero intero|No |
|user\_id|numero intero|No |
|file\_name|string|No |
|content\_type|string|No |
|file\_size|numero intero|No |
|local\_created\_at|string|No |
|created\_at|string|No |
|updated\_at|string|No |
|type|string|No |
|revision|numero intero|No |



### NewTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|numero intero|Sì |
|title|string|Sì |
|assignee\_id|numero intero|No |
|Completata|boolean|No |
|recurrence\_type|string|No |
|recurrence\_count|numero intero|No |
|due\_date|string|No |
|starred|boolean|No |



### NewList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|title|string|Sì |



### NewSubtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|numero intero|Sì |
|task\_id|numero intero|Sì |
|title|string|Sì |
|Completata|boolean|No |



### NewNote


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|numero intero|Sì |
|task\_id|numero intero|Sì |
|content|string|Sì |



### NewComment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|numero intero|Sì |
|task\_id|numero intero|Sì |
|text|string|Sì |



### NewReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|numero intero|Sì |
|task\_id|numero intero|Sì |
|date|string|Sì |



### UpdateTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|numero intero|No |
|title|string|No |
|assignee\_id|numero intero|No |
|Completata|boolean|No |
|recurrence\_type|string|No |
|recurrence\_count|numero intero|No |
|due\_date|string|No |
|starred|boolean|No |



### UpdateList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|numero intero|No |
|title|string|No |



### UpdateSubtask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|numero intero|No |
|title|string|No |
|Completata|boolean|No |



### UpdateNote


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|revision|numero intero|No |
|content|string|No |



### UpdateReminder


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|date|string|No |
|revision|numero intero|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->