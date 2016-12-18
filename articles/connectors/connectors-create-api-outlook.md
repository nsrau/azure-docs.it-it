---
title: Outlook.com | Documentazione Microsoft
description: "Creare app per la logica in Servizio app di Azure. Il connettore Outlook.com consente di gestire la posta elettronica, i calendari e i contatti. Consente anche di eseguire diverse azioni, ad esempio inviare messaggi, pianificare riunioni, aggiungere contatti e così via."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 13d0a71762d3cba05c3dccb647d0890862307327
ms.openlocfilehash: 9593804ba5baa4ffe4cf49e21b5698c5fd3d157e


---
# <a name="get-started-with-the-outlookcom-connector"></a>Introduzione al connettore Outlook.com
Il connettore Outlook.com consente di gestire la posta elettronica, i calendari e i contatti. Consente anche di eseguire diverse azioni, ad esempio inviare messaggi, pianificare riunioni, aggiungere contatti e così via.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
>
>

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Outlook.com può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Outlook.com sono disponibili le azioni e/o i trigger seguenti:

### <a name="outlookcom-actions"></a>Azioni di Outlook.com
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |Recupera i messaggi di posta elettronica da una cartella. |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |Invia un messaggio di posta elettronica |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |Elimina un messaggio di posta elettronica in base all'ID |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |Segna un messaggio di posta elettronica come già letto |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |Risponde a un messaggio di posta elettronica |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |Recupera l'allegato al messaggio in base all'ID |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |Invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |Invia un messaggio di approvazione e attende una risposta dal destinatario |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |Recupera i calendari. |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |Recupera gli elementi da un calendario. |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |Crea un nuovo evento. |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |Recupera un elemento specifico da un calendario. |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |Elimina un elemento del calendario. |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |Aggiorna parzialmente un elemento del calendario. |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |Recupera le cartelle dei contatti. |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |Recupera i contatti da una cartella Contatti. |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |Crea un nuovo contatto. |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |Recupera un contatto specifico da una cartella Contatti. |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |Elimina un contatto. |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |Aggiorna parzialmente un contatto. |

### <a name="outlookcom-triggers"></a>Trigger di Outlook.com
È possibile ascoltare questi eventi:

| Trigger | Description |
| --- | --- |
| All'avvio imminente di un evento |Attiva un flusso all'avvio di un prossimo evento del calendario. |
| All'arrivo di un nuovo messaggio di posta elettronica |Attiva un flusso quando arriva un nuovo messaggio di posta elettronica. |
| In presenza di nuovi elementi |Attivata quando viene creato un nuovo elemento del calendario. |
| In presenza di elementi aggiornati |Attivata quando viene modificato un elemento del calendario. |

## <a name="create-a-connection-to-outlookcom"></a>Creare una connessione a Outlook.com
Per creare app per la logica con Outlook.com, è prima necessario creare una **connessione** e quindi specificare i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornisce le credenziali di Outlook.com |

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.  
>
>

## <a name="reference-for-outlookcom"></a>Informazioni di riferimento per Outlook.com
Si applica alla versione 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
All'avvio imminente di un evento: attiva un flusso all'avvio di un evento imminente del calendario

```GET: /Events/OnUpcomingEvents```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |yes |query |nessuno |Identificatore univoco del calendario. |
| lookAheadTimeInMinutes |integer |no |query |15 |Tempo di attesa (in minuti) per eventi imminenti |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="getemails"></a>GetEmails
Recupero messaggi di posta elettronica: recupera i messaggi di posta elettronica da una cartella

```GET: /Mail```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |query |Posta in arrivo |Percorso della cartella per recuperare i messaggi di posta elettronica (impostazione predefinita: "Posta in arrivo") |
| top |integer |no |query |10 |Numero di messaggi di posta elettronica da recuperare (valore predefinito: 10) |
| fetchOnlyUnread |boolean |no |query |true |Recuperare solo i messaggi non letti? |
| includeAttachments |boolean |no |query |false |Se impostata su true, insieme al messaggio di posta elettronica saranno recuperati anche gli allegati |
| searchQuery |string |no |query |nessuno |Query di ricerca per filtrare messaggi di posta elettronica. |
| skip |integer |no |query |0 |Numero di messaggi di posta elettronica da ignorare (valore predefinito: 0). |
| skipToken |string |no |query |nessuno |Ignorare il token per recuperare una nuova pagina. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="sendemail"></a>SendEmail
Invio di posta elettronica: invia un messaggio di posta elettronica

```POST: /Mail```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |yes |body |nessuno |Email |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="deleteemail"></a>DeleteEmail
Eliminazione messaggio: elimina un messaggio di posta elettronica in base all'ID

```DELETE: /Mail/{messageId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sì |path |nessuno |ID del messaggio di posta elettronica da eliminare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="markasread"></a>MarkAsRead
Segna come già letto: segna un messaggio di posta elettronica come già letto

```POST: /Mail/MarkAsRead/{messageId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sì |path |nessuno |ID del messaggio da segnare come già letto |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="replyto"></a>ReplyTo
Risposta al messaggio: risponde a un messaggio di posta elettronica

```POST: /Mail/ReplyTo/{messageId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sì |path |nessuno |ID del messaggio di posta elettronica a cui rispondere |
| comment |string |yes |query |nessuno |Commento nella risposta |
| replyAll |boolean |no |query |false |Rispondere a tutti i destinatari. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="getattachment"></a>GetAttachment
Recupero allegato: recupera l'allegato al messaggio in base all'ID

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sì |path |nessuno |ID del messaggio di posta elettronica |
| attachmentId |string |Sì |path |nessuno |ID dell'allegato d scaricare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="onnewemail"></a>OnNewEmail
All'arrivo di un nuovo messaggio di posta elettronica: attiva un flusso quando arriva un nuovo messaggio di posta elettronica

```GET: /Mail/OnNewEmail```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |query |Posta in arrivo |Cartella di posta elettronica da recuperare (impostazione predefinita: Posta in arrivo). |
| to |string |no |query |nessuno |Indirizzi di posta elettronica dei destinatari. |
| from |string |no |query |nessuno |Indirizzo del mittente. |
| importance |string |no |query |Normal |Importanza del messaggio di posta elettronica (Alta, Normale, Bassa) (impostazione predefinita: Normale). |
| fetchOnlyWithAttachment |boolean |no |query |false |Recuperare solo i messaggi di posta elettronica con un allegato. |
| includeAttachments |boolean |no |query |false |Includere gli allegati. |
| subjectFilter |string |no |query |nessuno |Stringa da cercare nell'oggetto |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Accepted |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
Invio messaggio con opzioni: invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni

```POST: /mailwithoptions/$subscriptions```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |yes |body |nessuno |Richiesta di sottoscrizione per i messaggi di posta elettronica con opzioni |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 201 |Sottoscrizione creata |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="sendapprovalmail"></a>SendApprovalMail
Invio messaggio di approvazione: invia un messaggio di approvazione e attende una risposta dal destinatario

```POST: /approvalmail/$subscriptions```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |yes |body |nessuno |Richiesta di sottoscrizione per i messaggi di posta elettronica di approvazione |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 201 |Sottoscrizione creata |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="calendargettables"></a>CalendarGetTables
Recupero calendari: recupera calendari

```GET: /datasets/calendars/tables```

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendargetitems"></a>CalendarGetItems
Recupero eventi: recupera gli elementi da un calendario

```GET: /datasets/calendars/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco del calendario da recuperare. |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendarpostitem"></a>CalendarPostItem
Creazione evento: crea un nuovo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| item | |Sì |body |nessuno |Elemento del calendario da creare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendargetitem"></a>CalendarGetItem
Recupero evento: recupera un elemento specifico da un calendario

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| id |string |Sì |path |nessuno |Identificatore univoco di un elemento del calendario da recuperare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
Eliminazione evento: elimina un elemento del calendario

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| id |string |Sì |path |nessuno |Identificatore univoco dell'elemento del calendario da eliminare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendarpatchitem"></a>CalendarPatchItem
Aggiornamento evento: aggiorna parzialmente un elemento del calendario

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| id |string |Sì |path |nessuno |Identificatore univoco dell'elemento del calendario da aggiornare. |
| item | |Sì |body |nessuno |Elemento del calendario da aggiornare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
In presenza di nuovi elementi: si attiva quando viene creato un nuovo elemento del calendario

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
In presenza di elementi aggiornati: si attiva quando viene modificato un elemento del calendario

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di un calendario. |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactgettables"></a>ContactGetTables
Recupero cartelle contatti: recupera cartelle di contatti

```GET: /datasets/contacts/tables```

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactgetitems"></a>ContactGetItems
Recupero contatti: recupera i contatti da una cartella di contatti

```GET: /datasets/contacts/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco della cartella Contatti da recuperare. |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactpostitem"></a>ContactPostItem
Creazione contatto: crea un nuovo contatto

```POST: /datasets/contacts/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di una cartella Contatti. |
| item | |Sì |body |nessuno |Contatto da creare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactgetitem"></a>ContactGetItem
Recupero contatto: recupera un contatto specifico da una cartella di contatti

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di una cartella Contatti. |
| id |string |Sì |path |nessuno |Identificatore univoco di un contatto da recuperare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactdeleteitem"></a>ContactDeleteItem
Eliminazione contatto: elimina un contatto

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di una cartella Contatti. |
| id |string |Sì |path |nessuno |Identificatore univoco del contatto da eliminare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="contactpatchitem"></a>ContactPatchItem
Aggiornamento contatto: aggiorna parzialmente un contatto

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| tabella |string |Sì |path |nessuno |Identificatore univoco di una cartella Contatti. |
| id |string |Sì |path |nessuno |Identificatore univoco del contatto da aggiornare. |
| item | |Sì |body |nessuno |Contatto da aggiornare. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni degli oggetti
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="object"></a>Oggetto
### <a name="sendmessage"></a>SendMessage
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Attachments |array |No |
| Da |stringa |No |
| Cc |string |No |
| Bcc |stringa |No |
| Oggetto |stringa |Sì |
| Corpo |string |Sì |
| Importance |stringa |No |
| IsHtml |boolean |No |
| To |stringa |Sì |

### <a name="sendattachment"></a>SendAttachment
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| @odata.type |string |No |
| Name |string |Sì |
| ContentBytes |stringa |Sì |

### <a name="receivemessage"></a>ReceiveMessage
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |stringa |No |
| IsRead |boolean |No |
| HasAttachment |boolean |No |
| DateTimeReceived |string |No |
| Attachments |array |No |
| Da |stringa |No |
| Cc |string |No |
| Bcc |stringa |No |
| Oggetto |stringa |Sì |
| Corpo |string |Sì |
| Importance |stringa |No |
| IsHtml |boolean |No |
| To |stringa |Sì |

### <a name="receiveattachment"></a>ReceiveAttachment
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |Sì |
| ContentType |stringa |Sì |
| @odata.type |stringa |No |
| Name |string |Sì |
| ContentBytes |stringa |Sì |

### <a name="digestmessage"></a>DigestMessage
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Oggetto |stringa |Sì |
| Corpo |stringa |No |
| Importance |stringa |No |
| Digest |array |Sì |
| Attachments |array |No |
| To |stringa |Sì |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| tabular |non definito |No |
| BLOB |non definito |No |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |No |
| displayName |stringa |No |
| urlEncoding |stringa |No |
| tableDisplayName |stringa |No |
| tablePluralName |stringa |No |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |No |
| displayName |stringa |No |
| urlEncoding |stringa |No |

### <a name="tablemetadata"></a>TableMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |stringa |No |
| title |string |No |
| x-ms-permission |stringa |No |
| x-ms-capabilitiesx-ms-capabilities |non definito |No |
| schema |non definito |No |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| sortRestrictions |non definito |No |
| filterRestrictions |non definito |No |
| filterFunctions |array |No |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| sortable |boolean |No |
| unsortableProperties |array |No |
| ascendingOnlyProperties |array |No |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| filterable |boolean |No |
| nonFilterableProperties |array |No |
| requiredProperties |array |No |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| NotificationUrl |stringa |No |
| Message |non definito |No |

### <a name="messagewithoptions"></a>MessageWithOptions
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Oggetto |stringa |Sì |
| Opzioni |stringa |Sì |
| Corpo |stringa |No |
| Importance |stringa |No |
| Attachments |array |No |
| To |stringa |Sì |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| resource |stringa |No |
| notificationType |stringa |No |
| notificationUrl |stringa |No |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| NotificationUrl |stringa |No |
| Message |non definito |No |

### <a name="approvalmessage"></a>ApprovalMessage
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Oggetto |stringa |Sì |
| Opzioni |stringa |Sì |
| Corpo |stringa |No |
| Importance |stringa |No |
| Attachments |array |No |
| To |stringa |Sì |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| SelectedOption |stringa |No |

### <a name="tableslist"></a>TablesList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="table"></a>Tabella
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |stringa |No |
| DisplayName |string |No |

### <a name="item"></a>Item
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="calendaritemslist"></a>CalendarItemsList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="calendaritem"></a>CalendarItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="contactitemslist"></a>ContactItemsList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="contactitem"></a>ContactItem
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ItemInternalId |string |No |

### <a name="datasetslist"></a>DataSetsList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="dataset"></a>DataSet
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |stringa |No |
| DisplayName |string |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)



<!--HONumber=Nov16_HO3-->


