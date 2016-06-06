<properties
pageTitle="Aggiungere il connettore Twilio alle app per la logica | Microsoft Azure"
description="Panoramica del connettore Twilio con i parametri dell'API REST"
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
ms.author="mandia"/>

# Introduzione al connettore Twilio

Connettersi a Twilio per inviare e ricevere messaggi SMS, MMS e IP globali.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Twilio è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Twilio. 
- Usare le azioni per ottenere un messaggio, elencare i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando si ottiene un nuovo messaggio di Twilio, è possibile usarlo come flusso di lavoro del bus di servizio. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Il connettore Twilio include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
|Nessuno| <ul><li>Get Message</li><li>List Messages</li><li>Send Message</li></ul>|

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Twilio
Quando si aggiunge questo connettore alle app per la logica, immettere i valori di Twilio seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Account ID|Sì|Immettere l'ID dell'account Twilio|
|Access Token|Sì|Immettere Il token di accesso di Twilio|

>[AZURE.INCLUDE [Passaggi per creare una connessione a Twilio](../../includes/connectors-create-api-twilio.md)]

In mancanza di un token di accesso, vedere [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) per crearne uno.


>[AZURE.TIP] È possibile usare la stessa connessione di Twilio in altre app per la logica.

## Informazioni di riferimento sulle API REST Swagger
#### Questa documentazione è relativa alla versione 1.0

### Ottieni messaggio
Restituisce un singolo messaggio specificato dall'ID messaggio indicato. ```GET: /Messages/{MessageId}.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|MessageId|string|yes|path|nessuno|ID del messaggio|

### Risposta
|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|404|Messaggio non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Elenca messaggi
Restituisce un elenco di messaggi associati all'account. ```GET: /Messages.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|To|string|no|query|nessuno|Al numero di telefono|
|Da|string|no|query|nessuno|Dal numero di telefono|
|DateSent|string|no|query|nessuno|Mostra solo i messaggi inviati in questa data (in formato GMT), specificata come AAAA-MM-GG. Ad esempio: DateSent = 2009-07-06. È inoltre possibile specificare la disuguaglianza, ad esempio DateSent<=AAAA-MM-GG per i messaggi che sono stati inviati a mezzanotte o prima in una data e DateSent>=AAAA-MM-GG per i messaggi inviati a mezzanotte o dopo in una data.|
|PageSize|integer|no|query|50|Numero di risorse da restituire in ogni pagina dell'elenco. Il valore predefinito è 50|
|Page|integer|no|query|0|Numero di pagina. Il valore predefinito è 0.|

### Risposta
|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|



### Invia messaggio
Invia un nuovo messaggio a un numero di cellulare. ```POST: /Messages.json```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|sendMessageRequest| |sì|body|nessuno|Messaggio da inviare|

### Risposta
|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|Bad Request|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Definizioni oggetto

#### SendMessageRequest: modello di richiesta per l'operazione di invio del messaggio

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|from|string|sì|
|to|string|sì|
|body|string|sì|
|media\_url|array|no|
|status\_callback|string|no|
|messaging\_service\_sid|string|no|
|application\_sid|string|no|
|max\_price|string|no|


#### Message: modello per il messaggio

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|body|string|no|
|from|string|no|
|to|string|no|
|status|string|no|
|sid|string|no|
|account\_sid|string|no|
|api\_version|string|no|
|num\_segments|string|no|
|num\_media|string|no|
|date\_created|string|no|
|date\_sent|string|no|
|date\_updated|string|no|
|direction|string|no|
|error\_code|string|no|
|error\_message|string|no|
|price|string|no|
|price\_unit|string|no|
|Uri|string|no|
|subresource\_uris|array|no|
|messaging\_service\_sid|string|no|

#### MessageList: modello di risposta per l'operazione di elenco dei messaggi

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|Uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|
|total|integer|no|
|previous\_page\_uri|string|no|

#### IncomingPhoneNumberList: modello di risposta per l'operazione di elenco dei messaggi

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|incoming\_phone\_numbers|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|Uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|


#### AddIncomingPhoneNumberRequest: modello di richiesta per l'operazione di aggiunta dei numeri in ingresso

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|PhoneNumber|string|sì|
|AreaCode|string|no|
|FriendlyName|string|no|


#### IncomingPhoneNumber: numero di telefono in ingresso

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|sid|string|no|
|account\_sid|string|no|
|date\_created|string|no|
|date\_updated|string|no|
|capabilities|non definito|no|
|status\_callback|string|no|
|status\_callback\_method|string|no|
|api\_version|string|no|


#### Capabilities: funzionalità dei numeri di telefono

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|mms|boolean|no|
|sms|boolean|no|
|voice|boolean|no|

#### AvailablePhoneNumbers: numeri di telefono disponibili

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|lata|string|no|
|latitudine|string|no|
|longitudine|string|no|
|postal\_code|string|no|
|rate\_center|string|no|
|region|string|no|
|MMS|boolean|no|
|SMS|boolean|no|
|voice|boolean|no|


#### UsageRecords: classe dei record di utilizzo

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|category|string|no|
|utilizzo|string|no|
|usage\_unit|string|no|
|description|string|no|
|price|number|no|
|price\_unit|string|no|
|count|string|no|
|count\_unit|string|no|
|start\_date|string|no|
|end\_date|string|no|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0525_2016-->