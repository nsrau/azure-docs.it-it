<properties
pageTitle="Usare l'API di Slack nelle app per la logica | Microsoft Azure"
description="Introduzione all'uso dell'API di Slack (connettore) nelle app per la logica del Servizio app di Microsoft Azure"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Introduzione all'API di Slack

Slack è uno strumento di comunicazione del team, che riunisce tutte le comunicazioni del team in un'unica posizione immediatamente disponibile e individuabile in qualsiasi luogo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con il connettore Slack è possibile:

* Compilare app per la logica

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

È possibile usare il connettore Slack come azione. Non sono disponibili trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Slack sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Slack
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|PostMessage|Pubblicare un messaggio in un canale specificato.|
## Creare una connessione a Slack
Per usare l'API di Slack, creare prima una **connessione**, quindi specificare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di Slack|

Seguire questa procedura per accedere a Slack e completare la configurazione della **connessione** di Slack nell'app per la logica:

1. Selezionare **Recurrence**
2. Selezionare una **frequenza** e immettere un **intervallo**
3. Selezionare **Add an action** ![Configurare Slack][1]  
4. Immettere Slack nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con Slack nel nome
5. Selezionare **Slack - Post message**
6. Selezionare **Sign in to Slack**:
![Configurare Slack][2]
7. Specificare le credenziali di Slack per accedere e autorizzare l'applicazione ![Configurare Slack][3]  
8. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. **Autorizzare** Slack a interagire con l'app per la logica:
![Configurare Slack][5] 
9. Al termine dell'autenticazione si verrà reindirizzati all'app per la logica per completarla tramite la configurazione della sezione **Slack - Get all messages**. Aggiungere altri trigger e azioni necessari.
![Configurare Slack][6]
10. Salvare il lavoro selezionando **Salva** nella barra dei menu visualizzata in alto.


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento sulle API REST di Slack
#### Questa documentazione è relativa alla versione 1.0


### Pubblicare un messaggio in un canale specificato.
**```POST: /chat.postMessage```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|channel|string|yes|query|nessuno|Canale, gruppo privato o canale di messaggistica istantanea per inviare messaggi. Può essere un nome, ad esempio #general o un ID codificato.|
|text|string|yes|query|nessuno|Testo del messaggio da inviare. Per le opzioni di formattazione, vedere https://api.slack.com/docs/formatting.|
|username|string|no|query|nessuno|Nome del robot|
|as\_user|boolean|no|query|nessuno|Passare true per pubblicare il messaggio come utente autenticato, anziché come robot|
|parse|string|no|query|nessuno|Modificare la gestione dei messaggi. Per informazioni dettagliate, vedere https://api.slack.com/docs/formatting.|
|link\_names|integer|no|query|nessuno|Trovare e collegare i nomi dei canali e i nomi utente.|
|unfurl\_links|boolean|no|query|nessuno|Passare true per abilitare la rivelazione del contenuto principale basato su testo.|
|unfurl\_media|boolean|no|query|nessuno|Passare false per disabilitare la rivelazione dei contenuti multimediali.|
|icon\_url|string|no|query|nessuno|URL di un'immagine da usare come icona per questo messaggio|
|icon\_emoji|string|no|query|nessuno|Emoji da usare come icona per questo messaggio|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|408|Timeout richiesta|
|429|Troppe richieste|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|503|Servizio Slack non disponibile|
|504|Timeout gateway|
|default|Operazione non riuscita.|
------



## Definizioni di oggetti: 

 **Message**: messaggio di Yammer

Proprietà obbligatorie per Message:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|id|integer|
|content\_excerpt|string|
|sender\_id|integer|
|replied\_to\_id|integer|
|created\_at|string|
|network\_id|integer|
|message\_type|string|
|sender\_type|string|
|URL|string|
|web\_url|string|
|group\_id|integer|
|body|non definito|
|thread\_id|integer|
|direct\_message|boolean|
|client\_type|string|
|client\_url|string|
|Lingua|string|
|notified\_user\_ids|array|
|privacy|string|
|liked\_by|non definito|
|system\_message|boolean|



 **PostOperationRequest**: indica una richiesta di pubblicazione del connettore Yammer per la pubblicazione in Yammer

Proprietà obbligatorie per PostOperationRequest:

body

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|body|string|
|group\_id|integer|
|replied\_to\_id|integer|
|direct\_to\_id|integer|
|broadcast|boolean|
|topic1|string|
|topic2|string|
|topic3|string|
|topic4|string|
|topic5|string|
|topic6|string|
|topic7|string|
|topic8|string|
|topic9|string|
|topic10|string|
|topic11|string|
|topic12|string|
|topic13|string|
|topic14|string|
|topic15|string|
|topic16|string|
|topic17|string|
|topic18|string|
|topic19|string|
|topic20|string|



 **MessageList: **elenco di messaggi

Proprietà obbligatorie per MessageList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|messages|array|



 **MessageBody**: corpo del messaggio

Proprietà obbligatorie per MessageBody


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|parsed|string|
|plain|string|
|rich|string|



 **LikedBy**: utenti che hanno espresso gradimento

Proprietà obbligatorie per LikedBy


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|count|integer|
|names|array|



 **YammmerEntity**: utenti che hanno espresso gradimento

Proprietà obbligatorie per YammerEntity:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|type|string|
|id|integer|
|full\_name|string|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
## Definizioni di oggetti: 

 **WebResultModel**: risultati della ricerca di siti Web di Bing

Proprietà obbligatorie per WebResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Titolo|string|
|Descrizione|string|
|DisplayUrl|string|
|ID|string|
|FullUrl|string|



 **VideoResultModel**: risultati della ricerca di video di Bing

Proprietà obbligatorie per VideoResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Titolo|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|Runtime|integer|
|Immagine di anteprima|non definito|



 **ThumbnailModel**: proprietà di anteprima dell'elemento multimediale

Proprietà obbligatorie per ThumbnailModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|MediaUrl|string|
|ContentType|string|
|Larghezza|integer|
|Altezza|integer|
|FileSize|integer|



 **ImageResultModel**: risultati della ricerca di immagini di Bing

Proprietà obbligatorie per ImageResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Titolo|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|SourceUrl|string|
|Immagine di anteprima|non definito|



 **NewsResultModel**: risultati della ricerca di notizie di Bing

Proprietà obbligatorie per NewsResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Titolo|string|
|Descrizione|string|
|DisplayUrl|string|
|ID|string|
|Source|string|
|Date|string|



 **SpellResultModel**: risultati dei suggerimenti ortografici di Bing

Proprietà obbligatorie per SpellResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ID|string|
|Valore|string|



 **RelatedSearchResultModel**: risultati di ricerca correlati di Bing

Proprietà obbligatorie per RelatedSearchResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Titolo|string|
|ID|string|
|BingUrl|string|



 **CompositeSearchResultModel**: risultati della ricerca complessa di Bing

Proprietà obbligatorie per CompositeSearchResultModel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|WebResultsTotal|integer|
|ImageResultsTotal|integer|
|VideoResultsTotal|integer|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## Definizioni di oggetti: 

 **PostOperationResponse**: rappresenta la risposta dell'operazione di pubblicazione del connettore Slack per la pubblicazione in Slack

Proprietà obbligatorie per PostOperationResponse:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ok|boolean|
|channel|string|
|ts|string|
|message|non definito|
|error|string|



 **MessageItem**: un messaggio del canale.

Proprietà obbligatorie per MessageItem:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|text|string|
|id|string|
|user|string|
|created|integer|
|is\_user-deleted|boolean|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

<!---HONumber=AcomDC_0413_2016-->