---
title: Wunderlist | Documentazione Microsoft
description: "Creare app per la logica in Servizio app di Azure. Wunderlist fornisce un elenco di attività e uno strumento di gestione attività per aiutare le persone a portare a termine ciò che devono fare.  Che si tratti di condividere una lista della spesa con un familiare, lavorare a un progetto o pianificare una vacanza, Wunderlist consente di acquisire, condividere e completare le attività da svolgere in modo semplice. Wunderlist esegue immediatamente la sincronizzazione tra il telefono, il tablet e il computer, per consentire l&quot;accesso a tutte le attività da qualsiasi posizione."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a528a47373d7e9ae2cdc7d62328ed8e83112863


---
# <a name="get-started-with-the-wunderlist-connector"></a>Introduzione al connettore Wunderlist
Wunderlist fornisce un elenco di attività e uno strumento di gestione attività per aiutare le persone a portare a termine ciò che devono fare.  Che si tratti di condividere una lista della spesa con un familiare, lavorare a un progetto o pianificare una vacanza, Wunderlist consente di acquisire, condividere e completare le attività da svolgere in modo semplice. Wunderlist esegue immediatamente la sincronizzazione tra il telefono, il tablet e il computer, per consentire l'accesso a tutte le attività da qualsiasi posizione.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Wunderlist può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Wunderlist sono disponibili le azioni e/o i trigger seguenti:

### <a name="wunderlist-actions"></a>Azioni di Wunderlist
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |Recupera gli elenchi associati all'account |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |Crea un elenco |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |Recupera le attività da un elenco specifico |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |Crea un'attività |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |Recupera le sottoattività da un'attività o un elenco specifico |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |Crea una sottoattività all'interno di un'attività specifica |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |Recupera le note per un'attività o un elenco specifico |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |Aggiunge una nota a un'attività specifica |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |Recupera i commenti per un'attività o un elenco specifico |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |Aggiunge un commento a un'attività specifica |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |Recupera i promemoria per un'attività o un elenco specifico |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |Imposta un promemoria |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |Recupera i file per un'attività o un elenco specifico |
| [GetList](connectors-create-api-wunderlist.md#getlist) |Recupera un elenco specifico |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |Elimina un elenco |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |Aggiorna un elenco specifico |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |Recupera un'attività specifica |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |Aggiorna un'attività specifica |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |Elimina un'attività specifica |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |Recupera una sottoattività specifica |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |Aggiorna una sottoattività specifica |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |Elimina una sottoattività specifica |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |Recupera una nota specifica |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |Aggiorna una nota specifica |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |Elimina una nota specifica |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |Recupera un commento specifico di un'attività |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |Aggiorna un promemoria specifico |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |Elimina un promemoria specifico |

### <a name="wunderlist-triggers"></a>Trigger di Wunderlist
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando un'attività è in scadenza |Attiva un nuovo flusso quando un'attività nell'elenco è in scadenza |
| Quando viene creata una nuova attività |Attiva un nuovo flusso quando viene creata una nuova attività nell'elenco |
| Quando si verifica un promemoria |Attiva un nuovo flusso quando si verifica un promemoria |

## <a name="create-a-connection-to-wunderlist"></a>Creare una connessione a Wunderlist
Per creare app per la logica con Wunderlist, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornisce le credenziali di Wunderlist |

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-wunderlist"></a>Informazioni di riferimento per Wunderlist
Si applica alla versione 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Quando un'attività è in scadenza: attiva un nuovo flusso quando un'attività nell'elenco è in scadenza.

```GET: /trigger/tasksdue```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |

## <a name="triggertasknew"></a>TriggerTaskNew
Quando viene creata una nuova attività: attiva un nuovo flusso quando viene creata una nuova attività nell'elenco.

```GET: /trigger/tasksnew```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |

## <a name="triggerreminder"></a>TriggerReminder
Quando si verifica un promemoria: attiva un nuovo flusso quando si verifica un promemoria.

```GET: /trigger/reminders```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |

## <a name="retrievelists"></a>RetrieveLists
Ottenere elenchi: recupera gli elenchi associati all'account.

```GET: /lists```

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createlist"></a>CreateList
Creare un elenco: crea un elenco.

```POST: /lists```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuovo elenco da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| default |Operazione non riuscita. |

## <a name="listtasks"></a>ListTasks
Ottenere attività: recupera le attività da un elenco specifico.

```GET: /tasks```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| Completata |boolean |no |query |nessuno |Completed |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createtask"></a>CreateTask
Creare un'attività: crea un'attività.

```POST: /tasks```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuova attività da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 201 |Data di creazione |

## <a name="listsubtasks"></a>ListSubTasks
Ottenere le sottoattività: recupera le sottoattività da un'attività o un elenco specifico.

```GET: /subtasks```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |
| Completata |boolean |no |query |nessuno |Completed |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createsubtask"></a>CreateSubTask
Creare una sottoattività: crea una sottoattività all'interno di un'attività specifica.

```POST: /subtasks```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuova sottoattività da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 201 |Data di creazione |

## <a name="listnotes"></a>ListNotes
Ottenere le note: recupera le note per un'attività o un elenco specifico.

```GET: /notes```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createnote"></a>CreateNote
Creare una nota: aggiunge una nota a un'attività specifica.

```POST: /notes```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuova nota da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 201 |Data di creazione |

## <a name="listcomments"></a>ListComments
Ottenere i commenti di un'attività: recupera i commenti per un'attività o un elenco specifico.

```GET: /task_comments```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createcomment"></a>CreateComment
Aggiungere un commento a un'attività: aggiunge un commento a un'attività specifica.

```POST: /task_comments```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuovo commento dell'attività da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 201 |Data di creazione |

## <a name="retrievereminders"></a>RetrieveReminders
Ottenere i promemoria: recupera i promemoria per un'attività o un elenco specifico.

```GET: /reminders```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createreminder"></a>CreateReminder
Impostare un promemoria: imposta un promemoria.

```POST: /reminders```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| post | |sì |body |nessuno |Nuovo promemoria da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| default |Operazione non riuscita. |

## <a name="retrievefiles"></a>RetrieveFiles
Ottenere i file: recupera i file per un'attività o un elenco specifico.

```GET: /files```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| task_id |integer |no |query |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |Bad Request |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="getlist"></a>GetList
Ottenere un elenco: recupera un elenco specifico.

```GET: /lists/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |ID elenco |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
Eliminare un elenco: elimina un elenco.

```DELETE: /lists/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID elenco |
| revision |integer |sì |query |nessuno |Revisione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 204 |Nessun contenuto |

## <a name="updatelist"></a>UpdateList
Aggiornare un elenco: aggiorna un elenco specifico.

```PATCH: /lists/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID elenco |
| post | |sì |body |nessuno |Dettagli elenco |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
Ottenere un'attività: recupera un'attività specifica.

```GET: /tasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| id |integer |Sì |path |nessuno |ID attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
Aggiornare un'attività: aggiorna un'attività specifica.

```PATCH: /tasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| id |integer |Sì |path |nessuno |ID attività |
| post | |sì |body |nessuno |Dettagli dell'attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
Eliminare un'attività: elimina un'attività specifica.

```DELETE: /tasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |sì |query |nessuno |ID elenco |
| id |integer |Sì |path |nessuno |ID attività |
| revision |integer |sì |query |nessuno |Revisione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 204 |Nessun contenuto |

## <a name="getsubtask"></a>GetSubTask
Ottenere una sottoattività: recupera una sottoattività specifica.

```GET: /subtasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |ID sottoattività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
Aggiornare una sottoattività: aggiorna una sottoattività specifica.

```PATCH: /subtasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID sottoattività |
| post | |sì |body |nessuno |Dettagli sottoattività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
Eliminare una sottoattività: elimina una sottoattività specifica.

```DELETE: /subtasks/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID sottoattività |
| revision |integer |sì |query |nessuno |Revisione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 204 |Nessun contenuto |

## <a name="getnote"></a>GetNote
Ottenere una nota: recupera una nota specifica.

```GET: /notes/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |ID nota |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
Aggiornare una nota: aggiorna una nota specifica.

```PATCH: /notes/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID nota |
| post | |sì |body |nessuno |Dettagli nota |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
Eliminare una nota: elimina una nota specifica.

```DELETE: /notes/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID nota |
| revision |integer |sì |query |nessuno |Revisione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 204 |Nessun contenuto |

## <a name="getcomment"></a>GetComment
Ottenere un commento di un'attività: recupera un commento specifico di un'attività.

```GET: /task_comments/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |ID commento |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
Aggiornare un promemoria: aggiorna un promemoria specifico.

```PATCH: /reminders/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID promemoria |
| post | |sì |body |nessuno |Dettagli promemoria |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
Eliminare un promemoria: elimina un promemoria specifico.

```DELETE: /reminders/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sì |path |nessuno |ID del promemoria |
| revision |integer |sì |query |nessuno |Revisione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 204 |Nessun contenuto |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="list"></a>Elenco
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| created_at |stringa |No |
| title |string |No |
| list_type |string |No |
| type |stringa |No |
| revision |integer |No |

### <a name="createdlist"></a>CreatedList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| created_at |stringa |No |
| title |string |No |
| revision |integer |No |
| type |stringa |No |

### <a name="task"></a>Attività
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| assignee_id |integer |No |
| assigner_id |integer |No |
| created_at |stringa |No |
| created_by_id |integer |No |
| due_date |stringa |No |
| list_id |integer |No |
| revision |integer |No |
| starred |boolean |No |
| title |string |No |

### <a name="subtask"></a>Subtask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| created_at |stringa |No |
| created_by_id |integer |No |
| revision |stringa |No |
| title |string |No |

### <a name="note"></a>Note
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| content |stringa |No |
| created_at |stringa |No |
| updated_at |string |No |
| revision |integer |No |

### <a name="comment"></a>Comment
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| revision |integer |No |
| text |stringa |No |
| type |stringa |No |
| created_at |stringa |No |

### <a name="reminder"></a>Reminder
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| date |stringa |No |
| task_id |integer |No |
| revision |integer |No |
| type |stringa |No |
| created_at |stringa |No |
| updated_at |string |No |

### <a name="createdreminder"></a>CreatedReminder
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| date |stringa |No |
| task_id |integer |No |
| revision |integer |No |
| created_at |stringa |No |
| updated_at |string |No |

### <a name="file"></a>File
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |No |
| URL |stringa |No |
| task_id |integer |No |
| list_id |integer |No |
| user_id |integer |No |
| file_name |string |No |
| content_type |stringa |No |
| file_size |integer |No |
| local_created_at |string |No |
| created_at |stringa |No |
| updated_at |string |No |
| type |stringa |No |
| revision |integer |No |

### <a name="newtask"></a>NewTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |integer |Sì |
| title |stringa |Sì |
| assignee_id |integer |No |
| Completata |boolean |No |
| recurrence_type |stringa |No |
| recurrence_count |integer |No |
| due_date |stringa |No |
| starred |boolean |No |

### <a name="newlist"></a>NewList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| title |stringa |Sì |

### <a name="newsubtask"></a>NewSubtask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |integer |Sì |
| task_id |integer |Sì |
| title |stringa |Sì |
| Completata |boolean |No |

### <a name="newnote"></a>NewNote
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |integer |Sì |
| task_id |integer |Sì |
| content |string |Sì |

### <a name="newcomment"></a>NewComment
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |integer |Sì |
| task_id |integer |Sì |
| text |stringa |Sì |

### <a name="newreminder"></a>NewReminder
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |integer |Sì |
| task_id |integer |Sì |
| date |stringa |Sì |

### <a name="updatetask"></a>UpdateTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| revision |integer |No |
| title |string |No |
| assignee_id |integer |No |
| Completata |boolean |No |
| recurrence_type |stringa |No |
| recurrence_count |integer |No |
| due_date |stringa |No |
| starred |boolean |No |

### <a name="updatelist"></a>UpdateList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| revision |integer |No |
| title |string |No |

### <a name="updatesubtask"></a>UpdateSubtask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| revision |integer |No |
| title |string |No |
| Completata |boolean |No |

### <a name="updatenote"></a>UpdateNote
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| revision |integer |No |
| content |stringa |No |

### <a name="updatereminder"></a>UpdateReminder
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| date |stringa |No |
| revision |integer |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


