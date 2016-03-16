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
ms.date="02/25/2016"
ms.author="mandia"/>

# Guida introduttiva all'API Outlook di Office 365 

Connettersi a Outlook di Office 365 per ricevere la posta elettronica, rispondere a un messaggio di posta elettronica, aggiornare il calendario, aggiornare i contatti e molto altro. È possibile usare l'API Outlook di Office 365 da:

- PowerApps 
- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [API di Office 365](../app-service-logic/app-service-logic-connector-office365.md).

Con Outlook di Office 365 è possibile:

- Creare il flusso di business in base ai dati ottenuti da Outlook di Office 365. 
- Usare un trigger in presenza di un nuovo messaggio di posta elettronica, quando si crea un nuovo contatto e altro ancora.
- Usare azioni per rispondere a un messaggio di posta elettronica, creare un nuovo evento del calendario e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, in presenza di un nuovo oggetto in Salesforce, si può prendere questo oggetto e aggiornare i contatti Outlook di Office 365. 
- Aggiungere l'API Outlook di Office 365 a PowerApps Enterprise in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, leggere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere l'articolo su come [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

L'API Outlook di Office 365 dispone dei trigger e delle azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>All'avvio imminente di un evento</li><li>All'arrivo di un nuovo messaggio di posta elettronica</li><li>In presenza di nuovi elementi</li><li>In presenza di elementi aggiornati</li></ul>| <ul><li>Creare un contatto</li><li>Creare un evento</li><li>Inviare un messaggio di posta elettronica di approvazione</li><li>Inviare un messaggio di posta elettronica</li><li>Eliminare un contatto</li><li>Eliminare un messaggio di posta elettronica</li><li>Eliminare un evento</li><li>Ottenere l'allegato</li><li>Ottenere calendari</li><li>Ottenere un contatto</li><li>Ottenere cartelle dei contatti</li><li>Ottenere contatti</li><li>Ottenere messaggi di posta elettronica</li><li>Ottenere un evento</li><li>Ottenere eventi</li><li>Contrassegnare come già letto</li><li>All'avvio imminente di un evento</li><li>All'avvio di un nuovo messaggio di posta elettronica</li><li>In presenza di nuovi elementi</li><li>In presenza di elementi aggiornati</li><li>Rispondere al messaggio</li><li>Inviare un messaggio di posta elettronica con opzioni</li><li>Aggiornare un contatto</li><li>Aggiornare un evento</li></ul> |

Tutte le API supportano dati nei formati JSON e XML.


## Creare una connessione a Office 365

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge questa API a PowerApps Enterprise, immettere la **chiave app** e i valori di **chiave privata app** dell'applicazione di Office 365 Azure Active Directory (AAD). Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione Office 365. Se non si dispone di un'applicazione Office 365, è possibile seguire la procedura riportata sotto per creare l'applicazione:

1. Nel [portale di Azure][5] aprire **Active Directory** e selezionare il nome del tenant dell'organizzazione.
2. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**:  
![Applicazioni tenant ADD][7]

3. In **Aggiungi applicazione**:

	1. Immettere un **Nome** per l'applicazione.  
	2. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.  

	![Aggiungere l'applicazione ADD - info app][8]

6. In **Proprietà app**:

	1. Immettere l'**URL ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su _https://login.windows.net_.
	2. Immettere un **URI ID APP** valido per l'app.  
	3. Selezionare **OK**.  

	![Aggiungere l'applicazione ADD - proprietà app][9]

7. Al termine viene visualizzata la nuova applicazione AAD. Selezionare **Configura**:  
![App ADD Contoso][10]

8. Nella sezione _OAuth 2_ impostare l'**URL di risposta** secondo il valore dell'URL di reindirizzamento mostrato nel momento in cui è stata aggiunta l'API Outlook di Office 365 nel portale di Azure. Selezionare **Aggiungi applicazione**:  
![Configurare l'applicazione ADD Contoso][11]

9. In **Autorizzazioni per altre applicazioni** selezionare **Office 365 Exchange Online**, quindi **OK**:  
![Delegato app Contoso][12]

	Nella pagina di configurazione, notare che _Office 365 Exchange Online_ viene aggiunto all’elenco _Autorizzazioni per altre applicazioni_.

10. Per **Office 365 Exchange Online** selezionare **Autorizzazioni delegate**, quindi le autorizzazioni seguenti:

	- Lettura e scrittura dei contatti dell'utente
	- Lettura dei contatti dell'utente
	- Lettura e scrittura dei calendari dell'utente
	- Lettura dei calendari dell’utente
	- Invio di messaggi di posta elettronica come utente
	- Lettura e scrittura della posta dell'utente
	- Lettura della posta utente

	![Autorizzazioni delegate app Contoso][13]

Viene creata una nuova applicazione Azure Active Directory È possibile copiare e incollare la **chiave app** e i valori della **chiave privata app** nella configurazione di API Outlook di Office 365 nel portale di Azure.

Per alcune utili informazioni sulle applicazioni AAD vedere [Come vengono aggiunte le applicazioni in Azure AD e perché](../active-directory/active-directory-how-applications-are-added.md).

### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario accedere all'account Outlook di Office 365 e consentire alle app per la logica di connettersi all'account.

1. Accedere all'account Outlook di Office 365.
2. Consentire alle app per la logica di connettersi e di usare l'account Office 365. 

Dopo aver creato la connessione, immettere le proprietà Outlook di Office 365, ad esempio il percorso della cartella della posta in arrivo o il messaggio di posta elettronica. Le **Informazioni di riferimento sulle API REST** in questo argomento illustrano tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione Outlook di Office 365 in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.


### All'avvio imminente di un evento 
Per attivare un flusso all'avvio di un evento del calendario imminente.  
```GET: /Events/OnUpcomingEvents```

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
Per recuperare i messaggi di posta elettronica da una cartella.  
```GET: /Mail```

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
Per inviare un messaggio di posta elettronica.  
```POST: /Mail```

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
Per eliminare un messaggio di posta elettronica in base all'ID.  
```DELETE: /Mail/{messageId}```

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
Per contrassegnare un messaggio di posta elettronica come già letto.  
```POST: /Mail/MarkAsRead/{messageId}```

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
Per rispondere a un messaggio di posta elettronica.  
```POST: /Mail/ReplyTo/{messageId}```

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
Per recuperare l'allegato al messaggio in base all'ID.  
```GET: /Mail/{messageId}/Attachments/{attachmentId}```

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
Per attivare un flusso all'arrivo di un nuovo messaggio di posta elettronica.  
```GET: /Mail/OnNewEmail```

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
Per inviare un messaggio di posta elettronica con più opzioni e attendere che il destinatario risponda con una delle opzioni.  
```POST: /mailwithoptions/$subscriptions```

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
Per inviare un messaggio di approvazione e attendere una risposta dal destinatario A.  
```POST: /approvalmail/$subscriptions```

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
Per recuperare i calendari.  
```GET: /datasets/calendars/tables```

Non sono disponibili parametri per questa chiamata.

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Ottenere eventi 
Per recuperare gli elementi da un calendario.  
```GET: /datasets/calendars/tables/{table}/items```

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
Per creare un nuovo evento.  
```POST: /datasets/calendars/tables/{table}/items```

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
Per recuperare un elemento specifico da un calendario.  
```GET: /datasets/calendars/tables/{table}/items/{id}```

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
Per eliminare un elemento del calendario.  
```DELETE: /datasets/calendars/tables/{table}/items/{id}```

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
Per aggiornare parzialmente un elemento del calendario.  
```PATCH: /datasets/calendars/tables/{table}/items/{id}```

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
Si attiva quando viene creato un nuovo elemento del calendario.  
```GET: /datasets/calendars/tables/{table}/onnewitems```

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
Si attiva quando viene modificato un elemento del calendario.  
```GET: /datasets/calendars/tables/{table}/onupdateditems```

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
Per recuperare le cartelle dei contatti.  
```GET: /datasets/contacts/tables```

Non sono disponibili parametri per questa chiamata.

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottenere contatti 
Per recuperare i contatti da una cartella di contatti.  
```GET: /datasets/contacts/tables/{table}/items```

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
Per creare un nuovo contatto.  
```POST: /datasets/contacts/tables/{table}/items```

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
Per recuperare un contatto specifico da una cartella di contatti.  
```GET: /datasets/contacts/tables/{table}/items/{id}```

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
Per eliminare un contatto.  
```DELETE: /datasets/contacts/tables/{table}/items/{id}```

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
Per aggiornare parzialmente un contatto.  
```PATCH: /datasets/contacts/tables/{table}/items/{id}```

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
Dopo aver aggiunto l'API di Office 365 a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0302_2016-->

