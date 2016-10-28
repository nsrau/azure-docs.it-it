<properties
pageTitle="RSS | Microsoft Azure"
description="Creare app per la logica in Servizio app di Azure. Il connettore RSS consente agli utenti di pubblicare e recuperare elementi di feed. Consente anche di attivare operazioni quando viene pubblicato un nuovo elemento nel feed."
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

# Introduzione al connettore RSS
RSS è un formato di diffusione nel Web molto usato per la pubblicazione di contenuti aggiornati di frequente, come post di blog e notizie. Molti autori di contenuti offrono feed RSS per consentire agli utenti di eseguire la sottoscrizione. È possibile usare il connettore RSS per recuperare informazioni sui feed e attivare flussi quando vengono pubblicati nuovi elementi in un feed RSS.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore RSS può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore RSS sono disponibili le azioni e/o i trigger seguenti:

### Azioni di RSS
È possibile eseguire queste azioni:

|Azione|Description|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Ottiene tutti gli elementi del feed RSS.|
### Trigger di RSS
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando viene pubblicato un nuovo elemento di feed|Attiva un flusso di lavoro quando viene pubblicato un nuovo feed|


## Creare una connessione a RSS

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Yammer](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per RSS
Si applica alla versione 1.0

## OnNewFeed
Quando viene pubblicato un nuovo elemento di feed: attiva un flusso di lavoro quando viene pubblicato un nuovo feed

```GET: /OnNewFeed```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|feedUrl|string|yes|query|nessuno|URL del feed|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## ListFeedItems
Elenco di tutti gli elementi del feed RSS: ottiene tutti gli elementi del feed RSS.

```GET: /ListFeedItems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|feedUrl|stringa|yes|query|nessuno|URL del feed|

#### Response

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


## Definizioni oggetti 

### TriggerBatchResponse[FeedItem]


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### FeedItem


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|Sì |
|title|string|Sì |
|content|stringa|Sì |
|links|array|No |
|updatedOn|string|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->