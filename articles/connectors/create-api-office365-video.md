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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introduzione all'API di Office 365 Video

L'API di Office 365 Video consente di lavorare con canali e video di Office 365.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [Office365 Video Connector](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md)

Con Office 365 Video è possibile:

* Compilare app per la logica

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

È possibile usare l'API di Office 365 Video come azione. Non sono disponibili trigger. Tutte le API supportano dati nei formati JSON e XML.

 Nell'API di Office 365 Video sono disponibili le azioni e/o i trigger seguenti:

### Azioni dell'API di Office 365 Video
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|IsVideoPortalEnabled|Controlla lo stato del portale video per vedere se i servizi video sono abilitati|
|ListViewableChannels|Recupera tutti i canali a cui l'utente ha accesso in visualizzazione|
|ListVideos|Elenca tutti i video di Office 365 presenti in un canale|
|GetVideo|Recupera informazioni su un particolare video di Office 365|
|GetPlaybackUrl|Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video|
|GetStreamingKeyAccessToken|Recupera il token di connessione per poter accedere a un video da decrittografare|
ActionsTableReplaceMeLater # # Creare una connessione all'API di Office 365 Video Per usare l'API di Office 365 Video, creare prima di tutto una **connessione**, quindi indicare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di SharePoint Online|


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento all'API REST di Office 365 Video
#### Questa documentazione è relativa alla versione 1.0


### Controlla lo stato del portale video per vedere se i servizi video sono abilitati
**```GET: /{tenant}/IsEnabled```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Recupera tutti i canali a cui l'utente ha accesso in visualizzazione
**```GET: /{tenant}/Channels```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Elenca tutti i video di Office 365 presenti in un canale
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID canale da cui devono essere recuperati i video|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Recupera informazioni su un particolare video di Office 365
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Recupera l'URL di riproduzione del manifesto dei Servizi multimediali di Azure per un video
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|
|streamingFormatType|string|yes|query|nessuno|Tipo di formato di streaming. 1 - Smooth Streaming o MPEG-DASH. 0 - Streaming HLS|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



### Recupera il token di connessione per poter accedere a un video da decrittografare
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tenant|string|yes|path|nessuno|Il nome del tenant della directory di cui l'utente fa parte|
|channelId|string|yes|path|nessuno|L'ID del canale|
|videoId|string|yes|path|nessuno|L'ID del video|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|404|Non trovato|
|500|Internal Server Error|
|default|Operazione non riuscita.|
------



## Definizioni degli oggetti: 

 **Channel**: Classe del canale

Proprietà obbligatorie per Channel:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ID|string|
|Titolo|string|
|Descrizione|string|



 **Video**: classe del video

Proprietà obbligatorie per Video:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ID|string|
|Titolo|string|
|Descrizione|string|
|CreationDate|string|
|Proprietario|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->