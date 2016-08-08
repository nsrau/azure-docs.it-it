<properties
    pageTitle="Aggiungere il connettore Office 365 Outlook nelle app per la logica | Microsoft Azure"
    description="Creare app per la logica con il connettore Office 365 per consentire l'interazione con Office 365. Ad esempio, per creare, modificare e aggiornare contatti ed elementi del calendario."
    services=""    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/26/2016"
ms.author="mandia"/>

# Guida introduttiva al connettore Outlook di Office 365 

Il connettore Office 365 Outlook consente l'interazione con Outlook in Office 365. Usare questo connettore per creare, modificare e aggiornare i contatti e gli elementi del calendario e anche per ottenere, inviare e rispondere ai messaggio di posta elettronica.

Con Office 365 Outlook è possibile:

- Creare il flusso di lavoro usando le funzionalità di posta elettronica e calendario in Office 365.
- Usare i trigger per avviare il flusso di lavoro quando è presente un nuovo messaggio di posta elettronica, quando viene aggiornato un elemento del calendario e altro ancora.
- Usare azioni per inviare un messaggio di posta elettronica, creare un nuovo evento del calendario e altro ancora. Ad esempio, quando è presente un nuovo oggetto in Salesforce (trigger), inviare un messaggio di posta elettronica a Office 365 Outlook (azione).

Questo argomento illustra come usare il connettore Office 365 Outlook in un'app per la logica ed elenca i trigger e le azioni.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) e l'articolo su come [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a Office 365

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a Office 365 Outlook, è necessaria innanzitutto una *connessione* a Office 365. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Pertanto, per creare la connessione a Office 365 Outlook, immettere le credenziali dell'account Office 365 Outlook.


## Creare la connessione

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Office 365](../../includes/connectors-create-api-office365-outlook.md)]

## Usare un trigger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Nell'app per la logica digitare "office 365" per ottenere l'elenco dei trigger:

	![](./media/connectors-create-api-office365-outlook/office365-trigger.png)

2. Selezionare **Office 365 Outlook - When an upcoming event is starting soon** (Office 365 Outlook - Quando un evento sta per essere avviato). Se esiste già una connessione, selezionare un calendario dall'elenco a discesa.

	![](./media/connectors-create-api-office365-outlook/sample-calendar.png)

	Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-office365-outlook.md#create-the-connection) di questo argomento elenca i passaggi necessari.

	> [AZURE.NOTE] In questo esempio l'app per la logica viene eseguita quando viene aggiornato un evento del calendario. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un SMS al proprio cellulare. Ad esempio, aggiungere l'azione di Twilio *Send message* (Invia messaggio) che invia un SMS 15 minuti prima dell'avvio dell'evento del calendario.

3. Selezionare il pulsante **Edit** (Modifica) e impostare i valori **Frequency** (Frequenza) e **Interval** (Intervallo). Ad esempio, se si desidera che il trigger esegua il polling ogni 15 minuti, impostare **Frequency** (Frequenza) su **Minute** (Minuto) e **Interval** (Intervallo) su **15**.

	![](./media/connectors-create-api-office365-outlook/calendar-settings.png)

4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.


## Usare un'azione

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.

	![](./media/connectors-create-api-office365-outlook/add-action.png)

2. Selezionare **Aggiungi un'azione**.

3. Nella casella di testo digitare "office 365" per ottenere l'elenco di tutte le azioni disponibili.

	![](./media/connectors-create-api-office365-outlook/office365-actions.png)

4. Nel nostro esempio scegliere **Office 365 Outlook - Create contact** (Office 365 Outlook - Crea contatto). Se esiste già una connessione, scegliere l'**ID cartella**, il **nome** e le altre proprietà:

	![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)

	Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-office365-outlook.md#create-the-connection) di questo argomento descrive queste proprietà.

	> [AZURE.NOTE] In questo esempio si crea un nuovo contatto in Office 365 Outlook. Per creare il contatto è possibile usare l'output di un altro trigger. Ad esempio, aggiungere il trigger di SalesForce *When an object is created* (Quando viene creato un oggetto). Aggiungere quindi l'azione di Office 365 Outlook *Create contact* (Crea contatto) che usa i campi di SalesForce per creare il nuovo contatto in Office 365.

5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.


## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger di Office 365

|Trigger | Descrizione|
|--- | ---|
|[When an upcoming event is starting soon (Quando un evento sta per essere avviato)](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon)|Questa operazione attiva un flusso all'avvio di un prossimo evento del calendario.|
|[When a new email arrives](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) (Quando arriva un nuovo messaggio di posta elettronica)|Questa operazione attiva un flusso quando arriva un nuovo messaggio di posta elettronica|
|[When a new event is created](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) (Quando viene creato un nuovo evento)|Questa operazione attiva un flusso quando viene creato un nuovo evento in un calendario.|
|[When an event is modified](connectors-create-api-office365-outlook.md#when-an-event-is-modified) (Quando un evento viene modificato)|Questa operazione attiva un flusso quando viene modificato un evento in un calendario.|


## Azioni di Office 365

|Azione|Descrizione|
|--- | ---|
|[Ottenere messaggi di posta elettronica](connectors-create-api-office365-outlook.md#get-emails)|Questa operazione recupera messaggi di posta elettronica da una cartella.|
|[Inviare un messaggio di posta elettronica](connectors-create-api-office365-outlook.md#send-an-email)|Questa operazione invia un messaggio di posta elettronica.|
|[Eliminare i messaggi di posta elettronica](connectors-create-api-office365-outlook.md#delete-email)|Questa operazione elimina un messaggio di posta elettronica in base all'ID.|
|[Contrassegnare come già letto](connectors-create-api-office365-outlook.md#mark-as-read)|Questa operazione segna un messaggio di posta elettronica come già letto.|
|[Rispondere a un messaggio di posta elettronica](connectors-create-api-office365-outlook.md#reply-to-email)|Questa operazione risponde a un messaggio di posta elettronica.|
|[Ottenere l'allegato](connectors-create-api-office365-outlook.md#get-attachment)|Questa operazione recupera un allegato di posta elettronica in base all'ID.|
|[Inviare posta elettronica con opzioni](connectors-create-api-office365-outlook.md#send-email-with-options)|Questa operazione invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni.|
|[Inviare messaggio di posta elettronica di approvazione](connectors-create-api-office365-outlook.md#send-approval-email)|Questa operazione invia un messaggio di approvazione e attende una risposta dal destinatario.|
|[Ottenere calendari](connectors-create-api-office365-outlook.md#get-calendars)|Questa operazione elenca i calendari disponibili.|
|[Ottenere eventi](connectors-create-api-office365-outlook.md#get-events)|Questa operazione recupera eventi da un calendario.|
|[Creare un evento](connectors-create-api-office365-outlook.md#create-event)|Questa operazione crea un nuovo evento in un calendario.|
|[Ottenere un evento](connectors-create-api-office365-outlook.md#get-event)|Questa operazione recupera un evento specifico da un calendario.|
|[Eliminare un evento](connectors-create-api-office365-outlook.md#delete-event)|Questa operazione elimina un nuovo evento da un calendario.|
|[Aggiornare un evento](connectors-create-api-office365-outlook.md#update-event)|Questa operazione aggiorna un nuovo evento in un calendario.|
|[Ottenere cartelle dei contatti](connectors-create-api-office365-outlook.md#get-contact-folders)|Questa operazione elenca le cartelle di contatti disponibili.|
|[Ottenere contatti](connectors-create-api-office365-outlook.md#get-contacts)|Questa operazione recupera contatti da una cartella di contatti.|
|[Creare un contatto](connectors-create-api-office365-outlook.md#create-contact)|Questa operazione crea contatti in una cartella di contatti.|
|[Ottenere un contatto](connectors-create-api-office365-outlook.md#get-contact)|Questa operazione recupera un contatto specifico da una cartella di contatti.|
|[Eliminare un contatto](connectors-create-api-office365-outlook.md#delete-contact)|Questa operazione elimina un contatto da una cartella di contatti.|
|[Aggiornare un contatto](connectors-create-api-office365-outlook.md#update-contact)|Questa operazione aggiorna un contatto in una cartella di contatti.|

### Dettagli sui trigger e le azioni

In questa sezione sono riportati i dettagli relativi a ogni trigger e azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.

#### When an upcoming event is starting soon (Quando un evento sta per essere avviato)
Questa operazione attiva un flusso all'avvio di un prossimo evento del calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Identificatore univoco del calendario.|
|lookAheadTimeInMinutes|Tempo di attesa|Tempo di attesa (in minuti) per eventi imminenti|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarItemsList: elenco degli elementi del calendario

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|value|array|Elenco di elementi del calendario|


#### Ottenere messaggi di posta elettronica
Questa operazione recupera messaggi di posta elettronica da una cartella.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderPath|Folder Path|Percorso della cartella per recuperare i messaggi di posta elettronica (impostazione predefinita: "Posta in arrivo")|
|top|Top|Numero di messaggi di posta elettronica da recuperare (valore predefinito: 10)|
|fetchOnlyUnread|Recupera solo i messaggi non letti|Recuperare solo i messaggi non letti?|
|includeAttachments|Includere gli allegati|Se impostata su true, insieme al messaggio di posta elettronica saranno recuperati anche gli allegati|
|searchQuery|Query di ricerca|Query di ricerca per filtrare messaggi di posta elettronica.|
|skip|Skip|Numero di messaggi di posta elettronica da ignorare (valore predefinito: 0).|
|skipToken|Salta il token|Ignorare il token per recuperare una nuova pagina.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
ReceiveMessage: ricevere un messaggio di posta elettronica

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|Da|string|Da|
|To|string|To|
|Oggetto|string|Oggetto|
|Corpo|string|Corpo|
|Importance|string|Importance|
|HasAttachment|boolean|Presenta un allegato|
|ID|string|ID del messaggio|
|IsRead|boolean|È stato letto|
|DateTimeReceived|string|Data e ora di ricezione|
|Attachments|array|Attachments|
|Cc|string|Specificare gli indirizzi di posta elettronica separati da punto e virgola come someone@contoso.com|
|Bcc|string|Specificare gli indirizzi di posta elettronica separati da punto e virgola come someone@contoso.com|
|IsHtml|boolean|È HTML|


#### Inviare un messaggio di posta elettronica
Questa operazione invia un messaggio di posta elettronica.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|emailMessage*|Email|Email|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Eliminare i messaggi di posta elettronica
Questa operazione elimina un messaggio di posta elettronica in base all'ID.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|messageId*|ID del messaggio|ID del messaggio di posta elettronica da eliminare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Contrassegnare come già letto
Questa operazione segna un messaggio di posta elettronica come già letto.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|messageId*|ID del messaggio|ID del messaggio da segnare come già letto|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### Rispondere a un messaggio di posta elettronica
Questa operazione risponde a un messaggio di posta elettronica.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|messageId*|ID del messaggio|ID del messaggio di posta elettronica a cui rispondere|
|comment*|Commento|Commento nella risposta|
|replyAll|Rispondi a tutti|Rispondere a tutti i destinatari.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### Ottenere l'allegato
Questa operazione recupera un allegato di posta elettronica in base all'ID.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|messageId*|ID del messaggio|ID del messaggio di posta elettronica|
|attachmentId*|ID dell'allegato|ID dell'allegato d scaricare.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### When a new email arrives (Quando arriva un nuovo messaggio di posta elettronica)
Questa operazione attiva un flusso quando arriva un nuovo messaggio di posta elettronica.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderPath|Folder Path|Cartella di posta elettronica da recuperare (impostazione predefinita: Posta in arrivo).|
|to|To|Indirizzi di posta elettronica dei destinatari.|
|from|Da|Indirizzo del mittente.|
|importance|Importance|Importanza del messaggio di posta elettronica (Alta, Normale, Bassa) (impostazione predefinita: Normale).|
|fetchOnlyWithAttachment|Presenta allegati|Recuperare solo i messaggi di posta elettronica con un allegato.|
|includeAttachments|Includere gli allegati|Includere gli allegati.|
|subjectFilter|Filtro dell'oggetto|Stringa da cercare nell'oggetto|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
TriggerBatchResponse[ReceiveMessage]

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|


#### Inviare posta elettronica con opzioni
Questa operazione invia un messaggio di posta elettronica con più opzioni e attende che il destinatario risponda con una delle opzioni.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|optionsEmailSubscription*|Richiesta di sottoscrizione per i messaggi di posta elettronica con opzioni|Richiesta di sottoscrizione per i messaggi di posta elettronica con opzioni|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
SubscriptionResponse: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|id|string|ID della sottoscrizione|
|resource|string|Risorsa della richiesta di sottoscrizione|
|notificationType|string|Tipo di notifica|
|notificationUrl|string|URL di notifica|


#### Inviare messaggio di posta elettronica di approvazione
Questa operazione invia un messaggio di approvazione e attende una risposta dal destinatario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|approvalEmailSubscription*|Richiesta di sottoscrizione per i messaggi di posta elettronica di approvazione|Richiesta di sottoscrizione per i messaggi di posta elettronica di approvazione|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
SubscriptionResponse: modello per la sottoscrizione del messaggio di posta elettronica di approvazione

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|id|string|ID della sottoscrizione|
|resource|string|Risorsa della richiesta di sottoscrizione|
|notificationType|string|Tipo di notifica|
|notificationUrl|string|URL di notifica|


#### Ottenere calendari
Questa operazione elenca i calendari disponibili.

Non sono disponibili parametri per questa chiamata.

##### Dettagli dell'output
TablesList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|


#### Ottenere eventi
Questa operazione recupera eventi da un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|$filter|Query di filtro|Una query di filtro ODATA per limitare gli elementi restituiti|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarEventList: l'elenco degli elementi del calendario

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|value|array|Elenco di elementi del calendario|


#### Creare un evento
Questa operazione crea un nuovo evento in un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|item*|Item|Evento da creare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarEvent: classe di modello evento calendario specifico del connettore.

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco dell'evento.|
|Partecipanti|array|Elenco dei partecipanti per l'evento.|
|Corpo|non definito|Il corpo del messaggio associato all'evento.|
|BodyPreview|string|L'anteprima del messaggio associato all'evento.|
|Categorie|array|Le categorie associate all'evento.|
|ChangeKey|string|Identifica la versione dell'oggetto evento. Ogni volta che l'evento viene modificato, cambia anche ChangeKey.|
|DateTimeCreated|string|Data e ora di creazione dell'evento.|
|DateTimeLastModified|string|Data e ora dell'ultima modifica dell'evento.|
|End|string|Ora di fine dell'evento.|
|EndTimeZone|string|Specifica il fuso orario dell'ora di fine della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|HasAttachments|boolean|Impostata su true se l'evento presenta allegati.|
|Importance|string|L'importanza dell'evento: bassa, normale o alta.|
|IsAllDay|boolean|Impostata su true se l'evento dura tutto il giorno.|
|IsCancelled|boolean|Impostata su true se l'evento è stato annullato.|
|IsOrganizer|boolean|Impostata su true se il mittente del messaggio è anche l'organizzatore.|
|Percorso|non definito|La località dell'evento.|
|Organizzatore|non definito|L'organizzatore dell'evento.|
|Ricorrenza|non definito|Il criterio di ricorrenza dell'evento.|
|Reminder|integer|Il tempo in minuti del promemoria prima dell'inizio dell'evento.|
|ResponseRequested|boolean|Impostata su true se il mittente desidera una risposta quando l'evento viene accettato o rifiutato.|
|ResponseStatus|non definito|Indica il tipo di risposta inviato per rispondere a un messaggio dell'evento.|
|SeriesMasterId|string|Identificatore univoco per il tipo di evento master della serie.|
|ShowAs|string|Mostra lo stato libero o occupato.|
|Inizia|string|L'ora di inizio dell'evento.|
|StartTimeZone|string|Specifica il fuso orario dell'ora di inizio della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|Oggetto|string|Oggetto dell'evento.|
|Tipo|string|Il tipo di evento: singola istanza, occorrenza, eccezione o master della serie.|
|WebLink|string|L'anteprima del messaggio associato all'evento.|


#### Ottenere un evento
Questa operazione recupera un evento specifico da un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|id*|ID elemento|Seleziona un evento|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarEvent: classe di modello evento calendario specifico del connettore.

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco dell'evento.|
|Partecipanti|array|Elenco dei partecipanti per l'evento.|
|Corpo|non definito|Il corpo del messaggio associato all'evento.|
|BodyPreview|string|L'anteprima del messaggio associato all'evento.|
|Categorie|array|Le categorie associate all'evento.|
|ChangeKey|string|Identifica la versione dell'oggetto evento. Ogni volta che l'evento viene modificato, cambia anche ChangeKey.|
|DateTimeCreated|string|Data e ora di creazione dell'evento.|
|DateTimeLastModified|string|Data e ora dell'ultima modifica dell'evento.|
|End|string|Ora di fine dell'evento.|
|EndTimeZone|string|Specifica il fuso orario dell'ora di fine della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|HasAttachments|boolean|Impostata su true se l'evento presenta allegati.|
|Importance|string|L'importanza dell'evento: bassa, normale o alta.|
|IsAllDay|boolean|Impostata su true se l'evento dura tutto il giorno.|
|IsCancelled|boolean|Impostata su true se l'evento è stato annullato.|
|IsOrganizer|boolean|Impostata su true se il mittente del messaggio è anche l'organizzatore.|
|Percorso|non definito|La località dell'evento.|
|Organizzatore|non definito|L'organizzatore dell'evento.|
|Ricorrenza|non definito|Il criterio di ricorrenza dell'evento.|
|Reminder|integer|Il tempo in minuti del promemoria prima dell'inizio dell'evento.|
|ResponseRequested|boolean|Impostata su true se il mittente desidera una risposta quando l'evento viene accettato o rifiutato.|
|ResponseStatus|non definito|Indica il tipo di risposta inviato per rispondere a un messaggio dell'evento.|
|SeriesMasterId|string|Identificatore univoco per il tipo di evento master della serie.|
|ShowAs|string|Mostra lo stato libero o occupato.|
|Inizia|string|L'ora di inizio dell'evento.|
|StartTimeZone|string|Specifica il fuso orario dell'ora di inizio della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|Oggetto|string|Oggetto dell'evento.|
|Tipo|string|Il tipo di evento: singola istanza, occorrenza, eccezione o master della serie.|
|WebLink|string|L'anteprima del messaggio associato all'evento.|


#### Eliminare un evento
Questa operazione elimina un nuovo evento da un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|id*|ID|Seleziona un evento|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### Aggiornare un evento
Questa operazione aggiorna un nuovo evento in un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|id*|ID|Seleziona un evento|
|item*|Item|Evento da aggiornare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarEvent: classe di modello evento calendario specifico del connettore.

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco dell'evento.|
|Partecipanti|array|Elenco dei partecipanti per l'evento.|
|Corpo|non definito|Il corpo del messaggio associato all'evento.|
|BodyPreview|string|L'anteprima del messaggio associato all'evento.|
|Categorie|array|Le categorie associate all'evento.|
|ChangeKey|string|Identifica la versione dell'oggetto evento. Ogni volta che l'evento viene modificato, cambia anche ChangeKey.|
|DateTimeCreated|string|Data e ora di creazione dell'evento.|
|DateTimeLastModified|string|Data e ora dell'ultima modifica dell'evento.|
|End|string|Ora di fine dell'evento.|
|EndTimeZone|string|Specifica il fuso orario dell'ora di fine della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|HasAttachments|boolean|Impostata su true se l'evento presenta allegati.|
|Importance|string|L'importanza dell'evento: bassa, normale o alta.|
|IsAllDay|boolean|Impostata su true se l'evento dura tutto il giorno.|
|IsCancelled|boolean|Impostata su true se l'evento è stato annullato.|
|IsOrganizer|boolean|Impostata su true se il mittente del messaggio è anche l'organizzatore.|
|Percorso|non definito|La località dell'evento.|
|Organizzatore|non definito|L'organizzatore dell'evento.|
|Ricorrenza|non definito|Il criterio di ricorrenza dell'evento.|
|Reminder|integer|Il tempo in minuti del promemoria prima dell'inizio dell'evento.|
|ResponseRequested|boolean|Impostata su true se il mittente desidera una risposta quando l'evento viene accettato o rifiutato.|
|ResponseStatus|non definito|Indica il tipo di risposta inviato per rispondere a un messaggio dell'evento.|
|SeriesMasterId|string|Identificatore univoco per il tipo di evento master della serie.|
|ShowAs|string|Mostra lo stato libero o occupato.|
|Inizia|string|L'ora di inizio dell'evento.|
|StartTimeZone|string|Specifica il fuso orario dell'ora di inizio della riunione. Questo valore deve corrispondere a quello definito in Windows (esempio: "Ora solare Pacifico").|
|Oggetto|string|Oggetto dell'evento.|
|Tipo|string|Il tipo di evento: singola istanza, occorrenza, eccezione o master della serie.|
|WebLink|string|L'anteprima del messaggio associato all'evento.|


#### When a new event is created (Quando viene creato un nuovo evento)
Questa operazione attiva un flusso quando viene creato un nuovo evento in un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|$filter|Query di filtro|Una query di filtro ODATA per limitare gli elementi restituiti|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarItemsList: elenco degli elementi del calendario

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|value|array|Elenco di elementi del calendario|


#### When an event is modified (Quando un evento viene modificato)
Questa operazione attiva un flusso quando viene modificato un evento in un calendario.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID calendario|Selezionare un calendario|
|$filter|Query di filtro|Una query di filtro ODATA per limitare gli elementi restituiti|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
CalendarItemsList: elenco degli elementi del calendario


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|value|array|Elenco di elementi del calendario|


#### Ottenere cartelle dei contatti
Questa operazione elenca le cartelle di contatti disponibili.

Non sono disponibili parametri per questa chiamata.

##### Dettagli dell'output
TablesList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|


#### Ottenere contatti
Questa operazione recupera contatti da una cartella di contatti.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID cartella|Identificatore univoco della cartella Contatti da recuperare.|
|$filter|Query di filtro|Una query di filtro ODATA per limitare gli elementi restituiti|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
ContactList: l'elenco dei contatti

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|value|array|Elenco di contatti|


#### Creare un contatto
Questa operazione crea contatti in una cartella di contatti.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID cartella|Selezionare una cartella di contatti|
|item*|Item|Contatto da creare.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Contact: contatto

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco del contatto.|
|ParentFolderId|string|L'ID della cartella principale del contatto|
|Data di nascita|string|La data di nascita del contatto.|
|FileAs|string|Il nome con cui il contatto è registrato.|
|DisplayName|string|Il nome visualizzato del contatto.|
|GivenName|string|Il nome del contatto.|
|Initials|string|Le iniziali del contatto.|
|MiddleName|string|Il secondo nome del contatto.|
|NickName|string|Il nomignolo del contatto.|
|Surname|string|Il cognome del contatto.|
|Titolo|string|Il titolo del contatto.|
|Generation|string|La generazione del contatto.|
|EmailAddresses|array|Gli indirizzi di posta elettronica del contatto.|
|ImAddresses|array|Gli indirizzi di messaggistica istantanea del contatto.|
|JobTitle|string|Il titolo lavorativo del contatto.|
|CompanyName|string|Il nome dell'azienda del contatto.|
|Department|string|Il reparto del contatto.|
|OfficeLocation|string|La posizione dell'ufficio del contatto.|
|Profession|string|La professione del contatto.|
|BusinessHomePage|string|La home page dell'azienda del contatto.|
|AssistantName|string|Il nome dell'assistente del contatto.|
|Manager|string|Il nome del manager del contatto.|
|HomePhones|array|I numeri di telefono dell'abitazione del contatto.|
|BusinessPhones|array|I numeri di telefono aziendali del contatto|
|MobilePhone1|string|Il numero di cellulare del contatto.|
|HomeAddress|non definito|L'indirizzo dell'abitazione del contatto.|
|BusinessAddress|non definito|L'indirizzo dell'ufficio del contatto.|
|OtherAddress|non definito|Altri indirizzi del contatto.|
|YomiCompanyName|string|Il nome dell'azienda del contatto nella fonetica giapponese.|
|YomiGivenName|string|Il nome del contatto nella fonetica giapponese.|
|YomiSurname|string|Il cognome del contatto nella fonetica giapponese|
|Categorie|array|Le categorie associate al contatto.|
|ChangeKey|string|Identifica la versione dell'oggetto evento|
|DateTimeCreated|string|L'ora di creazione del contatto.|
|DateTimeLastModified|string|L'ora di modifica del contatto.|


#### Ottenere un contatto
Questa operazione recupera un contatto specifico da una cartella di contatti.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID cartella|Selezionare una cartella di contatti|
|id*|ID elemento|Identificatore univoco di un contatto da recuperare.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Contact: contatto

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco del contatto.|
|ParentFolderId|string|L'ID della cartella principale del contatto|
|Data di nascita|string|La data di nascita del contatto.|
|FileAs|string|Il nome con cui il contatto è registrato.|
|DisplayName|string|Il nome visualizzato del contatto.|
|GivenName|string|Il nome del contatto.|
|Initials|string|Le iniziali del contatto.|
|MiddleName|string|Il secondo nome del contatto.|
|NickName|string|Il nomignolo del contatto.|
|Surname|string|Il cognome del contatto.|
|Titolo|string|Il titolo del contatto.|
|Generation|string|La generazione del contatto.|
|EmailAddresses|array|Gli indirizzi di posta elettronica del contatto.|
|ImAddresses|array|Gli indirizzi di messaggistica istantanea del contatto.|
|JobTitle|string|Il titolo lavorativo del contatto.|
|CompanyName|string|Il nome dell'azienda del contatto.|
|Department|string|Il reparto del contatto.|
|OfficeLocation|string|La posizione dell'ufficio del contatto.|
|Profession|string|La professione del contatto.|
|BusinessHomePage|string|La home page dell'azienda del contatto.|
|AssistantName|string|Il nome dell'assistente del contatto.|
|Manager|string|Il nome del manager del contatto.|
|HomePhones|array|I numeri di telefono dell'abitazione del contatto.|
|BusinessPhones|array|I numeri di telefono aziendali del contatto|
|MobilePhone1|string|Il numero di cellulare del contatto.|
|HomeAddress|non definito|L'indirizzo dell'abitazione del contatto.|
|BusinessAddress|non definito|L'indirizzo dell'ufficio del contatto.|
|OtherAddress|non definito|Altri indirizzi del contatto.|
|YomiCompanyName|string|Il nome dell'azienda del contatto nella fonetica giapponese.|
|YomiGivenName|string|Il nome del contatto nella fonetica giapponese.|
|YomiSurname|string|Il cognome del contatto nella fonetica giapponese|
|Categorie|array|Le categorie associate al contatto.|
|ChangeKey|string|Identifica la versione dell'oggetto evento|
|DateTimeCreated|string|L'ora di creazione del contatto.|
|DateTimeLastModified|string|L'ora di modifica del contatto.|


#### Eliminare un contatto
Questa operazione elimina un contatto da una cartella di contatti.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID cartella|Selezionare una cartella di contatti|
|id*|ID|Identificatore univoco del contatto da eliminare.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### Aggiornare un contatto
Questa operazione aggiorna un contatto in una cartella di contatti.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|ID cartella|Selezionare una cartella di contatti|
|id*|ID|Identificatore univoco del contatto da aggiornare.|
|item*|Item|Contatto da aggiornare.|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Contact: contatto

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|ID|string|L'identificatore univoco del contatto.|
|ParentFolderId|string|L'ID della cartella principale del contatto|
|Data di nascita|string|La data di nascita del contatto.|
|FileAs|string|Il nome con cui il contatto è registrato.|
|DisplayName|string|Il nome visualizzato del contatto.|
|GivenName|string|Il nome del contatto.|
|Initials|string|Le iniziali del contatto.|
|MiddleName|string|Il secondo nome del contatto.|
|NickName|string|Il nomignolo del contatto.|
|Surname|string|Il cognome del contatto.|
|Titolo|string|Il titolo del contatto.|
|Generation|string|La generazione del contatto.|
|EmailAddresses|array|Gli indirizzi di posta elettronica del contatto.|
|ImAddresses|array|Gli indirizzi di messaggistica istantanea del contatto.|
|JobTitle|string|Il titolo lavorativo del contatto.|
|CompanyName|string|Il nome dell'azienda del contatto.|
|Department|string|Il reparto del contatto.|
|OfficeLocation|string|La posizione dell'ufficio del contatto.|
|Profession|string|La professione del contatto.|
|BusinessHomePage|string|La home page dell'azienda del contatto.|
|AssistantName|string|Il nome dell'assistente del contatto.|
|Manager|string|Il nome del manager del contatto.|
|HomePhones|array|I numeri di telefono dell'abitazione del contatto.|
|BusinessPhones|array|I numeri di telefono aziendali del contatto|
|MobilePhone1|string|Il numero di cellulare del contatto.|
|HomeAddress|non definito|L'indirizzo dell'abitazione del contatto.|
|BusinessAddress|non definito|L'indirizzo dell'ufficio del contatto.|
|OtherAddress|non definito|Altri indirizzi del contatto.|
|YomiCompanyName|string|Il nome dell'azienda del contatto nella fonetica giapponese.|
|YomiGivenName|string|Il nome del contatto nella fonetica giapponese.|
|YomiSurname|string|Il cognome del contatto nella fonetica giapponese|
|Categorie|array|Le categorie associate al contatto.|
|ChangeKey|string|Identifica la versione dell'oggetto evento|
|DateTimeCreated|string|L'ora di creazione del contatto.|
|DateTimeLastModified|string|L'ora di modifica del contatto.|



## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell'[elenco delle API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->