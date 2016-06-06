<properties
    pageTitle="Aggiungere il connettore Outlook di Office 365 a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Outlook di Office 365 con i parametri dell'API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="mandia"/>

# Guida introduttiva al connettore Outlook di Office 365 

Connettersi a Outlook di Office 365 per ricevere la posta elettronica, rispondere a un messaggio di posta elettronica, aggiornare il calendario, aggiornare i contatti e molto altro. Il connettore Outlook di Office 365 può essere usato da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Outlook di Office 365 è possibile:

- Creare il flusso di business in base ai dati ottenuti da Outlook di Office 365. 
- Usare un trigger in presenza di un nuovo messaggio di posta elettronica, quando si crea un nuovo contatto e altro ancora.
- Usare azioni per rispondere a un messaggio di posta elettronica, creare un nuovo evento del calendario e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, in presenza di un nuovo oggetto in Salesforce, si può prendere questo oggetto e aggiornare i contatti Outlook di Office 365. 
- Aggiungere il connettore Outlook di Office 365 a PowerApps Enterprise. in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise, vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore Outlook di Office 365 dispone dei trigger e delle azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>On event starting soon</li><li>On new email</li><li>On new items</li><li>On updated items</li></ul>| <ul><li>Create contact</li><li>Create event</li><li>Send approval email</li><li>Send email</li><li>Delete contact</li><li>Delete email</li><li>Delete event</li><li>Get attachment</li><li>Get calendars</li><li>Get contact</li><li>Get contact folders</li><li>Get contacts</li><li>Get emails</li><li>Get event</li><li>Get events</li><li>Mark as read</li><li>On event starting soon</li><li>On new email</li><li>On new items</li><li>On updated items</li><li>Reply to message</li><li>Send email with options</li><li>Update contact</li><li>Update event</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.


## Creare una connessione a Office 365

Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Outlook di Office 365 e consentire alle app per la logica di connettersi all'account.

1. Accedere all'account Outlook di Office 365.
2. Consentire alle app per la logica di connettersi e di usare l'account Office 365. 

Dopo aver creato la connessione, immettere le proprietà Outlook di Office 365, ad esempio il percorso della cartella della posta in arrivo o il messaggio di posta elettronica. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione Outlook di Office 365 in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.


### All'avvio imminente di un evento 
Attiva un flusso all'avvio di un evento imminente del calendario. ```GET: /Events/OnUpcomingEvents```

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


### Invia messaggio di posta elettronica 
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




### Ottenere calendari 
Recupera i calendari. ```GET: /datasets/calendars/tables```

Non sono disponibili parametri per questa chiamata.

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Ottenere eventi 
Recupera gli elementi da un calendario. ```GET: /datasets/calendars/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco del calendario da recuperare.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


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


### In presenza di nuovi elementi 
Si attiva quando viene creato un nuovo elemento del calendario. ```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### In presenza di elementi aggiornati 
Si attiva quando viene modificato un elemento del calendario. ```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di un calendario.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottenere cartelle dei contatti 
Recupera le cartelle dei contatti. ```GET: /datasets/contacts/tables```

Non sono disponibili parametri per questa chiamata.

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottenere contatti 
Recupera i contatti da una cartella dei contatti. ```GET: /datasets/contacts/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco della cartella Contatti da recuperare.|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


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


### Ottenere un contatto 
Recupera un contatto specifico da una cartella dei contatti. ```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Identificatore univoco di una cartella Contatti.|
|id|string|yes|path|nessuno|Identificatore univoco di un contatto da recuperare.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


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


## Definizioni degli oggetti

#### TriggerBatchResponse[IDictionary[String,Object]]

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### SendMessage: inviare un messaggio di posta elettronica

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Attachments|array|no|
|Da|string|no|
|Cc|string|no|
|Bcc|string|no|
|Oggetto|string|yes|
|Corpo|string|yes|
|Importance|string|no|
|IsHtml|boolean|no|
|To|string|yes|

#### SendAttachment: allegato

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|@odata.type|string|no|
|Nome|string|yes|
|ContentBytes|string|yes|


#### ReceiveMessage: ricevere un messaggio di posta elettronica

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|string|no|
|IsRead|boolean|no|
|HasAttachment|boolean|no|
|DateTimeReceived|string|no|
|Attachments|array|no|
|Da|string|no|
|Cc|string|no|
|Bcc|string|no|
|Oggetto|string|yes|
|Corpo|string|yes|
|Importance|string|no|
|IsHtml|boolean|no|
|To|string|yes|


#### ReceiveAttachment: ricevere un allegato

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|string|yes|
|ContentType|string|yes|
|@odata.type|string|no|
|Nome|string|no|
|ContentBytes|string|yes|


#### DigestMessage: inviare un messaggio di posta elettronica

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Oggetto|string|yes|
|Corpo|string|no|
|Importance|string|no|
|Digest|array|yes|
|Attachments|array|no|
|To|string|yes|

#### TriggerBatchResponse[ReceiveMessage]

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|


#### TabularDataSetsMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|


#### OptionsEmailSubscription: modello per la sottoscrizione di opzioni di posta elettronica

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|NotificationUrl|string|no|
|Message|non definito|no|

#### MessageWithOptions: messaggio di posta elettronica con opzioni dell'utente. Si tratta del messaggio previsto come parte dell'input utente.

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Oggetto|string|yes|
|Opzioni|string|yes|
|Corpo|string|no|
|Importance|string|no|
|Attachments|array|no|
|To|string|yes|

#### SubscriptionResponse: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|id|string|no|
|resource|string|no|
|notificationType|string|no|
|notificationUrl|string|no|


#### ApprovalEmailSubscription: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|NotificationUrl|string|no|
|Message|non definito|no|


#### ApprovalMessage: messaggio di posta elettronica di approvazione. Si tratta del messaggio previsto come parte dell'input utente.

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Oggetto|string|yes|
|Opzioni|string|yes|
|Corpo|string|no|
|Importance|string|no|
|Attachments|array|no|
|To|string|yes|

#### ApprovalEmailResponse: risposta al messaggio di posta elettronica di approvazione

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|SelectedOption|string|no|

#### TablesList

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### Tabella

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|


#### Item

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|


#### CalendarItemsList: elenco degli elementi del calendario

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### CalendarItem: rappresenta un elemento della tabella del calendario

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|


#### ContactItemsList: elenco degli elementi dei contatti

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### ContactItem: rappresenta un elemento della tabella dei contatti

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|


#### DataSetsList

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### DataSet

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/connectors-create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0525_2016-->