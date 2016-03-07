<properties
pageTitle="Aggiungere l'API Outlook di Office 365 alle app per la logica | Microsoft Azure"
description="Panoramica dell'API Outlook di Office 365 con i parametri dell'API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introduzione all'API Outlook di Office 365

L'API di Office 365 consente di interagire con Office 365. Ad esempio, per creare, modificare e aggiornare contatti ed elementi del calendario.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [API di Office 365](../app-service-logic/app-service-logic-connector-Office365.md).

È possibile usare Office 365 per:

* Compilare app per la logica
* Compilare app avanzate

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

L'API di Office 365 include trigger e può essere usata come azione. Tutte le API supportano dati nei formati JSON e XML.

 Nell'API di Office 365 sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Office 365
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|GetEmails|Recupera i messaggi di posta elettronica da una cartella.|
|SendEmail|Invia un messaggio di posta elettronica.|
|DeleteEmail|Elimina un messaggio di posta elettronica in base all'ID.|
|MarkAsRead|Contrassegna un messaggio di posta elettronica come già letto.|
|ReplyTo|Risponde a un messaggio di posta elettronica.|
|GetAttachment|Recupera l'allegato al messaggio in base all'ID.|
|SendMailWithOptions|Invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni.|
|SendApprovalMail|Invia un messaggio di approvazione e attende una risposta dal destinatario A.|
|CalendarGetTables|Recupera i calendari.|
|CalendarGetItems|Recupera gli elementi da un calendario.|
|CalendarPostItem|Crea un nuovo evento.|
|CalendarGetItem|Recupera un elemento specifico da un calendario.|
|CalendarDeleteItem|Elimina un elemento del calendario.|
|CalendarPatchItem|Aggiorna parzialmente un elemento del calendario.|
|ContactGetTables|Recupera le cartelle dei contatti.|
|ContactGetItems|Recupera i contatti da una cartella Contatti.|
|ContactPostItem|Crea un nuovo contatto.|
|ContactGetItem|Recupera un contatto specifico da una cartella Contatti.|
|ContactDeleteItem|Elimina un contatto.|
|ContactPatchItem|Aggiorna parzialmente un contatto.|
### Trigger di Office 365
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|OnUpcomingEvents|Attiva un flusso all'avvio di un prossimo evento del calendario.|
|OnNewEmail|Attiva un flusso quando arriva un nuovo messaggio di posta elettronica.|
|CalendarGetOnNewItems|Attivata quando viene creato un nuovo elemento del calendario.|
|CalendarGetOnUpdatedItems|Attivata quando viene modificato un elemento del calendario.|


## Creare una connessione a Office 365
Per usare l'API di Office 365, creare prima di tutto una **connessione**, quindi fornire i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di Office 365|


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento all'API REST di Office 365
#### Questa documentazione è relativa alla versione 1.0


### All'avvio imminente di un evento 


 Attiva un flusso all'avvio di un prossimo evento del calendario. ```GET: /Events/OnUpcomingEvents```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|query|nessuno|Identificatore univoco del calendario.|
|lookAheadTimeInMinutes|integer|no|query|15|Tempo (in minuti) di attesa di eventi imminenti.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Ottenere messaggi di posta elettronica 


 Recupera i messaggi di posta elettronica da una cartella. ```GET: /Mail```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Posta in arrivo|Percorso della cartella per recuperare i messaggi (impostazione predefinita: "Posta in arrivo")|
|top|integer|no|query|10|Numero di messaggi di posta elettronica da recuperare (valore predefinito: 10)|
|fetchOnlyUnread|boolean|no|query|true|Recuperare solo i messaggi non letti?|
|includeAttachments|boolean|no|query|false|Se impostata su true, insieme al messaggio di posta elettronica saranno recuperati anche gli allegati.|
|searchQuery|string|no|query|nessuno|Query di ricerca per filtrare messaggi di posta elettronica.|
|skip|integer|no|query|0|Numero di messaggi di posta elettronica da ignorare (valore predefinito: 0).|
|skipToken|string|no|query|nessuno|Ignorare il token per recuperare una nuova pagina.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Inviare messaggi di posta elettronica 


 Invia un messaggio di posta elettronica. ```POST: /Mail```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|nessuno|Istanza di messaggio di posta elettronica.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Eliminare i messaggi di posta elettronica 


 Elimina un messaggio di posta elettronica in base all'ID. ```DELETE: /Mail/{messageId}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio da eliminare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Contrassegnare come già letto 


 Contrassegna un messaggio di posta elettronica come già letto. ```POST: /Mail/MarkAsRead/{messageId}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio deve essere contrassegnato come letto|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Rispondere al messaggio 


 Risponde a un messaggio di posta elettronica. ```POST: /Mail/ReplyTo/{messageId}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio a cui rispondere.|
|comment|string|yes|query|nessuno|Commento nella risposta|
|replyAll|boolean|no|query|false|Rispondere a tutti i destinatari.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Ottenere l'allegato 


 Recupera l'allegato al messaggio in base all'ID. ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|messageId|string|yes|path|nessuno|ID del messaggio.|
|attachmentId|string|yes|path|nessuno|ID dell'allegato d scaricare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### All'arrivo di un nuovo messaggio di posta elettronica 


 Attiva un flusso quando arriva un nuovo messaggio di posta elettronica. ```GET: /Mail/OnNewEmail```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Posta in arrivo|Cartella di posta elettronica da recuperare (impostazione predefinita: Posta in arrivo).|
|to|string|no|query|nessuno|Indirizzi di posta elettronica dei destinatari.|
|from|string|no|query|nessuno|Indirizzo del mittente.|
|importance|string|no|query|Normal|Importanza del messaggio di posta elettronica (Alta, Normale, Bassa) (impostazione predefinita: Normale).|
|fetchOnlyWithAttachment|boolean|no|query|false|Recuperare solo i messaggi di posta elettronica con un allegato.|
|includeAttachments|boolean|no|query|false|Includere gli allegati.|
|subjectFilter|string|no|query|nessuno|Stringa da cercare nell'oggetto.|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita.|
|202|Accepted|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Inviare posta elettronica con opzioni 


 Invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni. ```POST: /mailwithoptions/$subscriptions```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|nessuno|Richiesta di sottoscrizione per le opzioni di posta elettronica.|


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
------



### Inviare messaggio di posta elettronica di approvazione 


 Invia un messaggio di approvazione e attende una risposta dal destinatario A. ```POST: /approvalmail/$subscriptions```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|nessuno|Richiesta di sottoscrizione per i messaggi di posta elettronica di approvazione.|


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
------



### Ottenere calendari 


 Recupera i calendari. ```GET: /datasets/calendars/tables```

Non sono disponibili parametri per questa chiamata.
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Ottenere eventi 


 Recupera gli elementi da un calendario. ```GET: /datasets/calendars/tables/{table}/items```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco del calendario da recuperare.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0).|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi.|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Creare un evento 


 Crea un nuovo evento. ```POST: /datasets/calendars/tables/{table}/items```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|item| |yes|body|nessuno|Elemento del calendario da creare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Ottenere un evento 


 Recupera un elemento specifico da un calendario. ```GET: /datasets/calendars/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco di un elemento del calendario da recuperare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Eliminare un evento 


 Elimina un elemento del calendario. ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco dell'elemento del calendario da eliminare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Aggiornare un evento 


 Aggiorna parzialmente un elemento del calendario. ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|id|string|yes|path|nessuno|Identificatore univoco dell'elemento del calendario da aggiornare.|
|item| |yes|body|nessuno|Elemento del calendario da aggiornare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### In presenza di nuovi elementi 


 Attivata quando viene creato un nuovo elemento del calendario. ```GET: /datasets/calendars/tables/{table}/onnewitems```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0).|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi.|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### In presenza di elementi aggiornati 


 Attivata quando viene modificato un elemento del calendario. ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0).|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi.|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Ottenere cartelle dei contatti 


 Recupera le cartelle dei contatti. ```GET: /datasets/contacts/tables```

Non sono disponibili parametri per questa chiamata.
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Ottenere contatti 


 Recupera i contatti da una cartella Contatti. ```GET: /datasets/contacts/tables/{table}/items```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco della cartella Contatti da recuperare.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0).|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi.|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Creare un contatto 


 Crea un nuovo contatto. ```POST: /datasets/contacts/tables/{table}/items```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|item| |yes|body|nessuno|Contatto da creare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Ottenere un contatto 


 Recupera un contatto specifico da una cartella Contatti. ```GET: /datasets/contacts/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco di un contatto da recuperare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Eliminare un contatto 


 Elimina un contatto. ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco del contatto da eliminare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Aggiornare un contatto 


 Aggiorna parzialmente un contatto. ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco del contatto da aggiornare.|
|item| |yes|body|nessuno|Contatto da aggiornare.|


#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



## Definizioni di oggetti: 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

Proprietà obbligatorie per TriggerBatchResponse[IDictionary[String,Object]]:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **SendMessage**: invia un messaggio di posta elettronica

Proprietà obbligatorie per SendMessage:

Subject, Body, To

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Attachments|array|
|Da|string|
|Cc|string|
|Bcc|string|
|Oggetto|string|
|Corpo|string|
|Importance|string|
|IsHtml|boolean|
|To|string|



 **SendAttachment**: allegato

Proprietà obbligatorie per SendAttachment:

Name, ContentBytes

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|@odata.type|string|
|Nome|string|
|ContentBytes|string|



 **ReceiveMessage**: riceve un messaggio di posta elettronica

Proprietà obbligatorie per ReceiveMessage:

Subject, Body, To

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ID|string|
|IsRead|boolean|
|HasAttachment|boolean|
|DateTimeReceived|string|
|Attachments|array|
|Da|string|
|Cc|string|
|Bcc|string|
|Oggetto|string|
|Corpo|string|
|Importance|string|
|IsHtml|boolean|
|To|string|



 **ReceiveAttachment**: riceve un allegato

Proprietà obbligatorie per ReceiveAttachment:

Id, ContentType, Name, ContentBytes

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ID|string|
|ContentType|string|
|@odata.type|string|
|Nome|string|
|ContentBytes|string|



 **DigestMessage**: invia un messaggio di posta elettronica

Proprietà obbligatorie per DigestMessage:

Subject, Digest, To

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Oggetto|string|
|Corpo|string|
|Importance|string|
|Digest|array|
|Attachments|array|
|To|string|



 **TriggerBatchResponse[ReceiveMessage]**:

Proprietà obbligatorie per TriggerBatchResponse[ReceiveMessage]:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **DataSetsMetadata**:

Proprietà obbligatorie per DataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|tabular|non definito|
|BLOB|non definito|



 **TabularDataSetsMetadata**:

Proprietà obbligatorie per TabularDataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|una sezione source|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Proprietà obbligatorie per BlobDataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|una sezione source|string|
|displayName|string|
|urlEncoding|string|



 **TableMetadata**:

Proprietà obbligatorie per TableMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|non definito|



 **OptionsEmailSubscription**: modello per la sottoscrizione di opzioni di posta elettronica

Proprietà obbligatorie per OptionsEmailSubscription:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|NotificationUrl|string|
|Message|non definito|



 **MessageWithOptions**: messaggi di posta elettronica con opzioni dell'utente. Si tratta del messaggio previsto come parte dell'input utente.

Proprietà obbligatorie per MessageWithOptions:

Subject, Options, To

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Oggetto|string|
|Opzioni|string|
|Corpo|string|
|Importance|string|
|Attachments|array|
|To|string|



 **SubscriptionResponse**: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

Proprietà obbligatorie per SubscriptionResponse:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|id|string|
|resource|string|
|notificationType|string|
|notificationUrl|string|



 **ApprovalEmailSubscription**: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

Proprietà obbligatorie per ApprovalEmailSubscription:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|NotificationUrl|string|
|Message|non definito|



 **ApprovalMessage**: messaggio di posta elettronica di approvazione. Si tratta del messaggio previsto come parte dell'input utente.

Proprietà obbligatorie per ApprovalMessage:

Subject, Options, To

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Oggetto|string|
|Opzioni|string|
|Corpo|string|
|Importance|string|
|Attachments|array|
|To|string|



 **ApprovalEmailResponse**:risposta al messaggio di posta elettronica di approvazione

Proprietà obbligatorie per ApprovalEmailResponse:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|SelectedOption|string|



 **TablesList**:

Proprietà obbligatorie per TablesList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **Tabella**:

Proprietà obbligatorie per Table:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Nome|string|
|DisplayName|string|



 **Item**:

Proprietà obbligatorie per Item:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ItemInternalId|string|



 **CalendarItemsList**: elenco di elementi del calendario

Proprietà obbligatorie per CalendarItemsList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **CalendarItem**: rappresenta un elemento di tabella del calendario

Proprietà obbligatorie per CalendarItem:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ItemInternalId|string|



 **ContactItemsList**: elenco di contatti

Proprietà obbligatorie per ContactItemsList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **ContactItem**: rappresenta un elemento della tabella dei contatti

Proprietà obbligatorie per ContactItem:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ItemInternalId|string|



 **DataSetsList**:

Proprietà obbligatorie per DataSetsList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **DataSet**:

Proprietà obbligatorie per DataSet:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Nome|string|
|DisplayName|string|


## Passaggi successivi
Dopo aver aggiunto l'API di Office 365 a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->