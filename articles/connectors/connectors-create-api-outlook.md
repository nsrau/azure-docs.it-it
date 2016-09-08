<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Creare app per la logica in Servizio app di Azure. Il connettore Outlook.com consente di gestire la posta elettronica, i calendari e i contatti. Consente anche di eseguire diverse azioni, ad esempio inviare messaggi, pianificare riunioni, aggiungere contatti e così via."
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

# Introduzione al connettore Outlook.com

Il connettore Outlook.com consente di gestire la posta elettronica, i calendari e i contatti. Consente anche di eseguire diverse azioni, ad esempio inviare messaggi, pianificare riunioni, aggiungere contatti e così via.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore Outlook.com può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Outlook.com sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Outlook.com
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera i messaggi di posta elettronica da una cartella.|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Invia un messaggio di posta elettronica|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Elimina un messaggio di posta elettronica in base all'ID|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Segna un messaggio di posta elettronica come già letto|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Risponde a un messaggio di posta elettronica|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Recupera l'allegato al messaggio in base all'ID|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Invia un messaggio di approvazione e attende una risposta dal destinatario|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera i calendari.|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera gli elementi da un calendario.|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crea un nuovo evento.|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera un elemento specifico da un calendario.|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Elimina un elemento del calendario.|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Aggiorna parzialmente un elemento del calendario.|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera le cartelle dei contatti.|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera i contatti da una cartella Contatti.|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crea un nuovo contatto.|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera un contatto specifico da una cartella Contatti.|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Elimina un contatto.|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Aggiorna parzialmente un contatto.|
### Trigger di Outlook.com
È possibile ascoltare questi eventi:

|Trigger | Description|
|--- | ---|
|All'avvio imminente di un evento|Attiva un flusso all'avvio di un prossimo evento del calendario.|
|All'arrivo di un nuovo messaggio di posta elettronica|Attiva un flusso quando arriva un nuovo messaggio di posta elettronica.|
|In presenza di nuovi elementi|Attivata quando viene creato un nuovo elemento del calendario.|
|In presenza di elementi aggiornati|Attivata quando viene modificato un elemento del calendario.|


## Creare una connessione a Outlook.com
Per creare app per la logica con Outlook.com, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornisce le credenziali di Outlook.com|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

>[AZURE.INCLUDE [Passaggi per creare una connessione a Outlook.com](../../includes/connectors-create-api-outlook.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per Outlook.com
Si applica alla versione 1.0

## OnUpcomingEvents
All'avvio imminente di un evento: attiva un flusso all'avvio di un evento imminente del calendario

```GET: /Events/OnUpcomingEvents```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|query|nessuno|Identificatore univoco del calendario.|
|lookAheadTimeInMinutes|integer|no|query|15|Tempo di attesa (in minuti) per eventi imminenti|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## GetEmails
Recupero messaggi di posta elettronica: recupera i messaggi di posta elettronica da una cartella

```GET: /Mail```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Posta in arrivo|Percorso della cartella per recuperare i messaggi di posta elettronica (impostazione predefinita: "Posta in arrivo")|
|top|integer|no|query|10|Numero di messaggi di posta elettronica da recuperare (valore predefinito: 10)|
|fetchOnlyUnread|boolean|no|query|true|Recuperare solo i messaggi non letti?|
|includeAttachments|boolean|no|query|false|Se impostata su true, insieme al messaggio di posta elettronica saranno recuperati anche gli allegati|
|searchQuery|string|no|query|nessuno|Query di ricerca per filtrare messaggi di posta elettronica.|
|skip|integer|no|query|0|Numero di messaggi di posta elettronica da ignorare (valore predefinito: 0).|
|skipToken|string|no|query|nessuno|Ignorare il token per recuperare una nuova pagina.|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## SendEmail
Invio di posta elettronica: invia un messaggio di posta elettronica

```POST: /Mail```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|emailMessage| |sì|body|nessuno|Email|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## DeleteEmail
Eliminazione messaggio: elimina un messaggio di posta elettronica in base all'ID

```DELETE: /Mail/{messageId}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|messageId|stringa|yes|path|nessuno|ID del messaggio di posta elettronica da eliminare|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## MarkAsRead
Segna come già letto: segna un messaggio di posta elettronica come già letto

```POST: /Mail/MarkAsRead/{messageId}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio da segnare come già letto|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## ReplyTo
Risposta al messaggio: risponde a un messaggio di posta elettronica

```POST: /Mail/ReplyTo/{messageId}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|messageId|stringa|yes|path|nessuno|ID del messaggio di posta elettronica a cui rispondere|
|comment|string|yes|query|nessuno|Commento nella risposta|
|replyAll|boolean|no|query|false|Rispondere a tutti i destinatari.|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## GetAttachment
Recupero allegato: recupera l'allegato al messaggio in base all'ID

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio di posta elettronica|
|attachmentId|stringa|yes|path|nessuno|ID dell'allegato d scaricare.|

#### Response

|Name|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## OnNewEmail
All'arrivo di un nuovo messaggio di posta elettronica: attiva un flusso quando arriva un nuovo messaggio di posta elettronica

```GET: /Mail/OnNewEmail```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|stringa|no|query|Posta in arrivo|Cartella di posta elettronica da recuperare (impostazione predefinita: Posta in arrivo).|
|to|string|no|query|nessuno|Indirizzi di posta elettronica dei destinatari.|
|from|stringa|no|query|nessuno|Indirizzo del mittente.|
|importance|string|no|query|Normale|Importanza del messaggio di posta elettronica (Alta, Normale, Bassa) (impostazione predefinita: Normale).|
|fetchOnlyWithAttachment|boolean|no|query|false|Recuperare solo i messaggi di posta elettronica con un allegato.|
|includeAttachments|boolean|no|query|false|Includere gli allegati.|
|subjectFilter|string|no|query|nessuno|Stringa da cercare nell'oggetto|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Accepted|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## SendMailWithOptions
Invio messaggio con opzioni: invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni

```POST: /mailwithoptions/$subscriptions```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |sì|body|nessuno|Richiesta di sottoscrizione per i messaggi di posta elettronica con opzioni|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|201|Sottoscrizione creata|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## SendApprovalMail
Invio messaggio di approvazione: invia un messaggio di approvazione e attende una risposta dal destinatario

```POST: /approvalmail/$subscriptions```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |sì|body|nessuno|Richiesta di sottoscrizione per i messaggi di posta elettronica di approvazione|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|201|Sottoscrizione creata|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## CalendarGetTables
Recupero calendari: recupera calendari

```GET: /datasets/calendars/tables```

Non sono disponibili parametri per questa chiamata
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarGetItems
Recupero eventi: recupera gli elementi da un calendario

```GET: /datasets/calendars/tables/{table}/items```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco del calendario da recuperare.|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarPostItem
Creazione evento: crea un nuovo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|item| |yes|body|nessuno|Elemento del calendario da creare.|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarGetItem
Recupero evento: recupera un elemento specifico da un calendario

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco di un elemento del calendario da recuperare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarDeleteItem
Eliminazione evento: elimina un elemento del calendario

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco dell'elemento del calendario da eliminare.|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarPatchItem
Aggiornamento evento: aggiorna parzialmente un elemento del calendario

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco dell'elemento del calendario da aggiornare.|
|item| |yes|body|nessuno|Elemento del calendario da aggiornare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarGetOnNewItems
In presenza di nuovi elementi: si attiva quando viene creato un nuovo elemento del calendario

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CalendarGetOnUpdatedItems
In presenza di elementi aggiornati: si attiva quando viene modificato un elemento del calendario

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactGetTables
Recupero cartelle contatti: recupera cartelle di contatti

```GET: /datasets/contacts/tables```

Non sono disponibili parametri per questa chiamata
#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactGetItems
Recupero contatti: recupera i contatti da una cartella di contatti

```GET: /datasets/contacts/tables/{table}/items```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco della cartella Contatti da recuperare.|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactPostItem
Creazione contatto: crea un nuovo contatto

```POST: /datasets/contacts/tables/{table}/items```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|item| |yes|body|nessuno|Contatto da creare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactGetItem
Recupero contatto: recupera un contatto specifico da una cartella di contatti

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco di un contatto da recuperare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactDeleteItem
Eliminazione contatto: elimina un contatto

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco del contatto da eliminare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ContactPatchItem
Aggiornamento contatto: aggiorna parzialmente un contatto

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco del contatto da aggiornare.|
|item| |yes|body|nessuno|Contatto da aggiornare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetti 

### TriggerBatchResponse[IDictionary[String,Object]]


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### SendMessage


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Attachments|array|No |
|Da|string|No |
|Cc|stringa|No |
|Bcc|stringa|No |
|Oggetto|stringa|Sì |
|Corpo|stringa|Sì |
|Importance|stringa|No |
|IsHtml|boolean|No |
|To|stringa|Sì |



### SendAttachment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|@odata.type|string|No |
|Name|stringa|Sì |
|ContentBytes|string|Sì |



### ReceiveMessage


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|No |
|IsRead|boolean|No |
|HasAttachment|boolean|No |
|DateTimeReceived|string|No |
|Attachments|array|No |
|Da|stringa|No |
|Cc|string|No |
|Bcc|stringa|No |
|Oggetto|string|Sì |
|Corpo|string|Sì |
|Importance|stringa|No |
|IsHtml|boolean|No |
|To|stringa|Sì |



### ReceiveAttachment


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|string|Sì |
|ContentType|stringa|Sì |
|@odata.type|stringa|No |
|Name|stringa|Sì |
|ContentBytes|stringa|Sì |



### DigestMessage


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Corpo|stringa|No |
|Importance|stringa|No |
|Digest|array|Sì |
|Attachments|array|No |
|To|stringa|Sì |



### TriggerBatchResponse[ReceiveMessage]


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### DataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabular|non definito|No |
|BLOB|non definito|No |



### TabularDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|No |
|displayName|stringa|No |
|urlEncoding|stringa|No |
|tableDisplayName|stringa|No |
|tablePluralName|stringa|No |



### BlobDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|stringa|No |
|displayName|string|No |
|urlEncoding|stringa|No |



### TableMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|name|stringa|No |
|title|stringa|No |
|x-ms-permission|stringa|No |
|x-ms-capabilitiesx-ms-capabilities|non definito|No |
|schema|non definito|No |



### TableCapabilitiesMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sortRestrictions|non definito|No |
|filterRestrictions|non definito|No |
|filterFunctions|array|No |



### TableSortRestrictionsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sortable|boolean|No |
|unsortableProperties|array|No |
|ascendingOnlyProperties|array|No |



### TableFilterRestrictionsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|filterable|boolean|No |
|nonFilterableProperties|array|No |
|requiredProperties|array|No |



### OptionsEmailSubscription


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|NotificationUrl|stringa|No |
|Message|non definito|No |



### MessageWithOptions


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Opzioni|stringa|Sì |
|Corpo|stringa|No |
|Importance|stringa|No |
|Attachments|array|No |
|To|stringa|Sì |



### SubscriptionResponse


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|resource|stringa|No |
|notificationType|stringa|No |
|notificationUrl|stringa|No |



### ApprovalEmailSubscription


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|NotificationUrl|stringa|No |
|Message|non definito|No |



### ApprovalMessage


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Oggetto|stringa|Sì |
|Opzioni|stringa|Sì |
|Corpo|stringa|No |
|Importance|stringa|No |
|Attachments|array|No |
|To|string|Sì |



### ApprovalEmailResponse


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|SelectedOption|stringa|No |



### TablesList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### Tabella


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|No |
|DisplayName|stringa|No |



### Item


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|stringa|No |



### CalendarItemsList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### CalendarItem


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|string|No |



### ContactItemsList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### ContactItem


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|stringa|No |



### DataSetsList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### DataSet


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Name|stringa|No |
|DisplayName|string|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->