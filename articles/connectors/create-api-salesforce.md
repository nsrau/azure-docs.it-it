<properties
pageTitle="Aggiungere l'API di Salesforce a PowerApps Enterprise e alle app per la logica | Microsoft Azure"
description="Panoramica dell'API di Salesforce con i parametri dell'API REST."
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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introduzione all'API di Salesforce
Connettersi a Salesforce per creare oggetti, recuperarli e così via.

L'API di Salesforce può essere usata dalle app di PowerApps Enterprise e dalle app per la logica.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica. Per la versione schema 2014-12-01 di anteprima, fare clic sull'[API di Salesforce](../app-service-logic/app-service-logic-connector-salesforce.md).

Con Salesforce è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Salesforce. 
- Usare i trigger quando un oggetto viene creato o aggiornato.
- Usare le azioni per creare un oggetto, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo oggetto in Salesforce, è possibile inviare un messaggio di posta elettronica tramite Office 365.
- Aggiungere l'API di Salesforce a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di Salesforce include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>Quando viene creato un oggetto</li><li>Quando viene modificato un oggetto</li></ul> | <ul><li>Crea oggetto</li><li>Recupera oggetti</li><li>Quando viene creato un oggetto</li><li>Quando viene modificato un oggetto</li><li>Elimina oggetto</li><li>Recupera oggetto</li><li>Recupera tipi di oggetto (SObjects)</li><li>Aggiorna oggetto</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a Salesforce 

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge Salesforce a PowerApps Enterprise, si immettono i valori di **chiave app** e **segreto app** dell'applicazione di Salesforce. Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione Salesforce. Se non si ha un'applicazione Salesforce, è possibile usare la procedura seguente per creare l'applicazione:

1. [Accedere alla home page per gli sviluppatori Salesforce][5], selezionare il profilo e quindi **il programma di installazione**: ![Home page di Salesforce][6]

3. Selezionare **Crea** e selezionare **App**. Nella pagina **Apps** selezionare **New** in **Connected Apps**: ![Creazione app Salesforce][7]

4. In **Nuova App connessa**:

	1. Immettere il valore per **Connected App Name**.  
	2. Immettere il valore per **API Name**.  
	3. Immettere il valore per **Contact Email**.  
	4. In _API (Enable OAuth Settings)_ selezionare **Enable OAuth Settings** e impostare **Callback URL** sul valore visualizzato quando si aggiunge la nuova API di Salesforce nel portale di Azure.  

5. In _Ambiti OAuth selezionati_ aggiungere i seguenti ambiti agli **Ambiti OAuth selezionati**:

	- Accedere e gestire i dati Chatter (chatter\_api)
	- Accedere e gestire i dati (api)
	- Consentire l'accesso all'identificatore univoco (openid)
	- Eseguire richieste per conto dell’utente in qualsiasi momento (refresh\_token, offline\_access)

6. **Salvare** le modifiche: ![Nuova app di Salesforce][8]

A questo punto, copiare e incollare i valori di **ID client** e **chiave app** nella configurazione di Salesforce nel portale di Azure.

### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Salesforce.

1. Accedere all'account Salesforce.
2. Consentire alle app per la logica di connettersi e usare l'account Salesforce. 

Dopo aver creato la connessione immettere le proprietà di Salesforce, ad esempio nome tabella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione in altre app per la logica.

## Informazioni di riferimento sull'API REST
#### Questa documentazione è relativa alla versione 1.0


### Crea oggetto
Crea un oggetto Salesforce. ```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|item| |sì|body|nessuno|Oggetto Salesforce da creare|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Recupera oggetto
Recupera un oggetto Salesforce. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|sì|path|nessuno|Identificatore univoco dell'oggetto Salesforce da recuperare|

### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Elimina oggetto
Elimina un oggetto Salesforce. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|sì|path|nessuno|Identificatore univoco dell'oggetto Salesforce da eliminare|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Aggiorna oggetto
Aggiorna un oggetto Salesforce. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|id|string|sì|path|nessuno|Identificatore univoco dell'oggetto Salesforce da aggiornare|
|item| |sì|body|nessuno|Oggetto Salesforce con le proprietà modificate|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando viene creato un oggetto
Quando viene creato un oggetto in Salesforce attiva un flusso. ```GET: /datasets/default/tables/{table}/onnewitems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando viene modificato un oggetto 
Quando viene modificato un oggetto in Salesforce attiva un flusso. ```GET: /datasets/default/tables/{table}/onupdateditems```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|table|string|sì|path|nessuno|Tipo di oggetto SObject di Salesforce, ad esempio: 'Lead'|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

### Risposta
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
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|source|string|no|
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


#### Table

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
Dopo aver aggiunto l'API di Salesforce a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://developer.salesforce.com
[6]: ./media/create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/create-api-salesforce/salesforce-create-app.png
[8]: ./media/create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0224_2016-->