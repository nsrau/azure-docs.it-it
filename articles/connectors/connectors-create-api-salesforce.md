<properties
pageTitle="Aggiungere il connettore Salesforce a PowerApps Enterprise e alle app per la logica | Microsoft Azure"
description="Panoramica del connettore Salesforce con i parametri dell'API REST."
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
ms.date="05/19/2016"
ms.author="deonhe"/>

# Introduzione al connettore Salesforce 
Connettersi a Salesforce per creare oggetti, recuperarli e così via. Il connettore Salesforce può essere usato da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Salesforce è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Salesforce. 
- Usare i trigger quando un oggetto viene creato o aggiornato.
- Usare le azioni per creare un oggetto, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo oggetto in Salesforce, è possibile inviare un messaggio di posta elettronica tramite Office 365.
- Aggiungere il connettore Salesforce a PowerApps Enterprise, in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise, vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di Salesforce include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>When an object is created</li><li>When an object is modified</li></ul> | <ul><li>Create object</li><li>Get objects</li><li>When an object is created</li><li>When an object is modified</li><li>Delete object</li><li>Get object</li><li>Get object types (SObjects)</li><li>Update object</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Salesforce 

Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Salesforce.

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Salesforce](../../includes/connectors-create-api-salesforce.md)]

Dopo aver creato la connessione immettere le proprietà di Salesforce, ad esempio nome tabella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione in altre app per la logica.

## Informazioni di riferimento sull'API REST
Si applica alla versione 1.0.


### Crea oggetto
Crea un oggetto Salesforce. ```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|item| |yes|body|nessuno|Oggetto Salesforce da creare|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Recupera oggetto
Recupera un oggetto Salesforce. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|yes|path|nessuno|Identificatore univoco dell'oggetto Salesforce da recuperare|

### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Elimina oggetto
Elimina un oggetto Salesforce. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|yes|path|nessuno|Identificatore univoco dell'oggetto Salesforce da eliminare|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Aggiorna oggetto
Aggiorna un oggetto Salesforce. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|yes|path|nessuno|Identificatore univoco dell'oggetto Salesforce da aggiornare|
|item| |yes|body|nessuno|Oggetto Salesforce con le proprietà modificate|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando viene creato un oggetto
Attiva un flusso quando viene creato un oggetto in Salesforce. ```GET: /datasets/default/tables/{table}/onnewitems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando viene modificato un oggetto 
Attiva un flusso quando viene modificato un oggetto in Salesforce. ```GET: /datasets/default/tables/{table}/onupdateditems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



## Definizioni oggetto 

#### DataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|


#### TabularDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|


#### DataSetsList

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|value|array|no|


#### DataSet

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|Nome|string|
|DisplayName|string|no|


#### Tabella

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|


#### Item

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|


#### ItemsList

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|value|array|no|


#### TablesList

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|value|array|no|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).


[5]: https://developer.salesforce.com
[6]: ./media/connectors-create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/connectors-create-api-salesforce/salesforce-create-app.png
[8]: ./media/connectors-create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0525_2016-->