<properties
pageTitle="Usare l'API di Office 365 Video nelle app per la logica | Microsoft Azure"
description="Introduzione all'uso dell'API di Office 365 Video (connettore) nelle app per la logica del Servizio app di Microsoft Azure"
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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Introduzione all'API di Office 365 Video
Connettersi a Office 365 Video per ottenere informazioni su un video di Office 365, recuperare un elenco di video e altro ancora. L'API di Office 365 Video può essere usata da:

- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Questa API non è supportata da alcuna versione precedente dello schema.

Con Office 365 Video è possibile:

- Creare un flusso aziendale basato sui dati ottenuti da Office 365 Video. 
- Usare azioni per controllare lo stato del portale video, recuperare l'elenco di tutti video in un canale e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile usare l'API di ricerca di Bing per cercare video di Office 365 e quindi usare l'API di Office 365 Video per ottenere informazioni relative a un video specifico. Se il video soddisfa le esigenze, è possibile pubblicarlo su Facebook. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Nell'API di Office 365 Users sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
| Nessuno | <ul><li>Controlla stato portale video</li><li>Recupera tutti i canali visualizzabili</li><li>Recupera l'URL di riproduzione del manifesto di Servizi multimediali di Azure per un video</li><li>Recupera il token di connessione per poter accedere a un video da decrittografare</li><li>Recupera informazioni su un particolare video di Office 365</li><li>Elenca tutti i video di Office 365 presenti in un canale</li></ul>

Tutte le API supportano dati nei formati JSON e XML.

## Creare una connessione all'API di Office 365 Video
Quando si aggiunge questa API alle app per la logica, è necessario accedere all'account Office 365 Video e consentire alle app per la logica di connettersi all'account.

1. Accedere all'account Office 365 Video.
2. Consentire alle app per la logica di connettersi e di usare l'account Office 365. 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Video, ad esempio, il nome del tenant o l'ID canale. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Office 365 Video in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Controlla stato portale video 
Controlla lo stato del portale video per vedere se i servizi video sono abilitati.```GET: /{tenant}/IsEnabled```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|



### Recupera tutti i canali visualizzabili 
Recupera tutti i canali a cui l'utente ha accesso in visualizzazione. ```GET: /{tenant}/Channels```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|




### Elenca tutti i video di Office 365 presenti in un canale 
Elenca tutti i video di Office 365 presenti in un canale. ```GET: /{tenant}/Channels/{channelId}/Videos```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID canale da cui devono essere recuperati i video|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|




### Recupera informazioni su un particolare video di Office 365 
Recupera informazioni su un particolare video di Office 365. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|




### Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video 
Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|
|streamingFormatType|string|yes|query|nessuno|Tipo di formato di streaming. 1 - Smooth Streaming o MPEG-DASH. 0 - Streaming HLS|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|




### Recupera il token di connessione per poter accedere a un video da decrittografare 
Recupera il token di connessione per poter accedere a un video da decrittografare. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|


#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


## Definizioni degli oggetti

#### Channel: classe del canale

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|ID|string|no|
|Titolo|string|no|
|Descrizione|string|no|


#### Video 

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|string|no|
|Titolo|string|no|
|Descrizione|string|no|
|CreationDate|string|no|
|Proprietario|string|no|
|ThumbnailUrl|string|no|
|VideoUrl|string|no|
|VideoDuration|integer|no|
|VideoProcessingStatus|integer|no|
|ViewCount|integer|no|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->