<properties
pageTitle="Aggiungere il connettore Yammer alle app per la logica | Microsoft Azure"
description="Panoramica del connettore Yammer con i parametri dell'API REST"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introduzione al connettore Yammer

Connettersi a Yammer per accedere alle conversazioni della rete dell'organizzazione.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Yammer è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Yammer. 
- Usare i trigger quando c'è un nuovo messaggio in un gruppo o un feed che si sta seguendo.
- Usare le azioni per pubblicare un messaggio, ottenere tutti i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene visualizzato un nuovo messaggio, è possibile inviare un messaggio di posta elettronica tramite Office 365.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Yammer include i trigger e le azioni seguenti.

Trigger | Azioni
--- | ---
<ul><li>When there is a new message in a group</li><li>When there is a new message in my Following feed</li></ul>| <ul><li>Get all messages</li><li>Gets messages in a group</li><li>Gets the messages from my Following feed</li><li>Post message</li><li>When there is a new message in a group</li><li>When there is a new message in my Following feed</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Yammer
Per usare il connettore Yammer, creare prima una **connessione**, quindi specificare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Specificare le credenziali di Yammer|

>[AZURE.INCLUDE [Passaggi per creare una connessione a Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento sulle API REST Yammer
Questa documentazione è relativa alla versione 1.0


### Ottenere tutti i messaggi pubblici nella rete Yammer dell'utente connesso
Corrisponde a "Tutte" le conversazioni nell'interfaccia Web di Yammer. ```GET: /messages.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|older\_then|integer|no|query|nessuno|Restituisce i messaggi meno recenti rispetto all'ID del messaggio specificato come stringa numerica. Risulta utile per l'impaginazione dei messaggi. Ad esempio, se si stanno attualmente visualizzando 20 messaggi e il meno recente è il numero 2912, è possibile aggiungere "?older\_than=2912" alla richiesta per ottenere i 20 messaggi precedenti a quelli visualizzati.|
|newer\_then|integer|no|query|nessuno|Restituisce i messaggi più recenti rispetto all'ID del messaggio specificato come stringa numerica. Risulta utile per il polling dei nuovi messaggi. Se si cercano i messaggi e il messaggio più recente restituito è il numero 3516, è possibile eseguire una richiesta con il parametro "?newer\_than=3516" per garantire che non si ottengono copie duplicate dei messaggi già presenti nella pagina.|
|limit|integer|no|query|nessuno|Restituisce solo il numero specificato di messaggi.|
|page|integer|no|query|nessuno|Ottenere la pagina specificata. Se i dati restituiti sono maggiori del limite, è possibile usare questo campo per accedere alle pagine successive|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|408|Timeout richiesta|
|429|Troppe richieste|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|503|Servizio Yammer non disponibile|
|504|Timeout gateway|
|default|Operazione non riuscita.|


### Pubblica un messaggio in un gruppo o nel feed generale dell'organizzazione
Se viene indicato l'ID gruppo, il messaggio verrà pubblicato nel gruppo specificato, altrimenti verrà pubblicato nel feed generale dell'organizzazione. ```POST: /messages.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|input| |sì|body|nessuno|Pubblica la richiesta del messaggio|


### Risposta

|Nome|Descrizione|
|---|---|
|201|Data di creazione|



## Definizioni oggetti

#### Message: messaggio di Yammer

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|id|integer|no|
|content\_excerpt|string|no|
|sender\_id|integer|no|
|replied\_to\_id|integer|no|
|created\_at|string|no|
|network\_id|integer|no|
|message\_type|string|no|
|sender\_type|string|no|
|URL|string|no|
|web\_url|string|no|
|group\_id|integer|no|
|body|non definito|no|
|thread\_id|integer|no|
|direct\_message|boolean|no|
|client\_type|string|no|
|client\_url|string|no|
|Lingua|string|no|
|notified\_user\_ids|array|no|
|privacy|string|no|
|liked\_by|non definito|no|
|system\_message|boolean|no|

#### PostOperationRequest: indica una richiesta di pubblicazione per il connettore Yammer per la pubblicazione in Yammer

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|body|string|sì|
|group\_id|integer|no|
|replied\_to\_id|integer|no|
|direct\_to\_id|integer|no|
|broadcast|boolean|no|
|topic1|string|no|
|topic2|string|no|
|topic3|string|no|
|topic4|string|no|
|topic5|string|no|
|topic6|string|no|
|topic7|string|no|
|topic8|string|no|
|topic9|string|no|
|topic10|string|no|
|topic11|string|no|
|topic12|string|no|
|topic13|string|no|
|topic14|string|no|
|topic15|string|no|
|topic16|string|no|
|topic17|string|no|
|topic18|string|no|
|topic19|string|no|
|topic20|string|no|

#### MessageList: elenco di messaggi

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|messages|array|no|


#### MessageBody: corpo del messaggio

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|parsed|string|no|
|plain|string|no|
|rich|string|no|

#### LikedBy: utenti che hanno espresso gradimento

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|count|integer|no|
|names|array|no|

#### YammmerEntity: utenti che hanno espresso gradimento

| Nome | Tipo di dati | Obbligatorio |
|---|---| --- | 
|type|string|no|
|id|integer|no|
|full\_name|string|no|


## Passaggi successivi
[Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0525_2016-->