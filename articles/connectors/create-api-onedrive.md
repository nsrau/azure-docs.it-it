<properties
pageTitle="Aggiungere l'API di OneDrive a PowerApps Enterprise e alle app per la logica | Microsoft Azure"
description="Panoramica dell'API di OneDrive con i parametri dell'API REST."
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
ms.author="mandia"/>

# Introduzione all'API di OneDrive

Connettersi a OneDrive per gestire i file, ad esempio, caricare, recuperare ed eliminare i file e altro ancora. L'API di OneDrive può essere usata da:

- PowerApps 
- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [API di OneDrive](../app-service-logic/app-service-logic-connector-onedrive.md).

Con OneDrive è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da OneDrive. 
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per creare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in OneDrive, è possibile inviare tale file come messaggio di posta elettronica tramite Office 365.
- Aggiungere l'API di OneDrive a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di OneDrive include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>Quando un file viene creato</li><li>Quando un file viene modificato</li></ul> | <ul><li>Crea file</li><li>Elenca file in una cartella</li><li>Quando un file viene creato</li><li>Copia file</li><li>Elimina file</li><li>Estrai cartella</li><li>Recupera contenuto di file tramite ID</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file tramite ID</li><li>Recupera metadati di file tramite percorso</li><li>Elenca cartella radice</li><li>Aggiorna file</li><li>Quando un file viene modificato</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a OneDrive

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge OneDrive a PowerApps Enterprise, si immettono i valori **Chiave app** e **Chiave privata app** dell'applicazione di OneDrive. Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione OneDrive. Se non si ha un'applicazione OneDrive, è possibile usare la procedura seguente per creare l'applicazione:

1. Passare alla [pagina di creazione app][5] in _Centro per sviluppatori di account Microsoft_ e accedere con il proprio _Account Microsoft_.

2. Immettere il **nome dell'applicazione** e accettare il contratto: ![Nuova app di OneDrive][6]

3. Nelle impostazioni:

	1. Selezionare **Impostazioni API**.  
	2. Impostare l'**URL di reindirizzamento** sul valore visualizzato quando si aggiunge la nuova API di OneDrive nel portale di Azure.  
	3. **Salvare** le modifiche.  

	![Impostazioni dell’app API di OneDrive][7]

A questo punto, copiare e incollare i valori di **ID client** e **Chiave privata app** nella configurazione di OneDrive nel portale di Azure.

### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a OneDrive.

1. Accedere all'account OneDrive.
2. Consentire alle app per la logica di connettersi e usare OneDrive. 

Dopo aver creato la connessione immettere le proprietà di OneDrive, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.


### Recupera metadati di file tramite ID
Recupera i metadati di un file in OneDrive tramite ID. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file in OneDrive|

### Risposte
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna un file in OneDrive. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file da aggiornare in OneDrive|
|body| |sì|body|nessuno|Contenuto del file da aggiornare in OneDrive|


### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Elimina file
Elimina un file da OneDrive. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file da eliminare da OneDrive|


### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite percorso
Recupera i metadati di un file in OneDrive tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file in OneDrive|


### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Recupera contenuto di file tramite percorso
Recupera il contenuto dei file in OneDrive tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file in OneDrive|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Recupera contenuto di file tramite ID
Recupera il contenuto dei file in OneDrive tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file in OneDrive|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Crea file
Carica un file in OneDrive. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|nessuno|Percorso della cartella per caricare il file in OneDrive|
|name|string|yes|query|nessuno|Nome del file da creare in OneDrive|
|body| |sì|body|nessuno|Contenuto del file da creare in OneDrive|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Copia file
Copia un file in OneDrive. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno|URL del file di origine|
|destination|string|yes|query|nessuno|Percorso file di destinazione in OneDrive incluso nome file di destinazione|
|overwrite|boolean|no|query|false|Sovrascrive il file di destinazione se è impostata su 'true'|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando un file viene creato
Quando un nuovo file viene creato in una cartella di OneDrive, attiva un flusso. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno|Identificatore univoco della cartella in OneDrive.|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando un file viene modificato in una cartella di OneDrive, attiva un flusso
Quando un file viene modificato in una cartella di OneDrive, attiva un flusso. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno|Identificatore univoco della cartella in OneDrive.|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Estrai cartella
Estrae una cartella in OneDrive. ```POST: /datasets/default/extractFolderV2```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno|Percorso del file di archivio|
|destination|string|yes|query|nessuno|Percorso in OneDrive in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|no|query|false|Sovrascrive i file di destinazione se è impostata su 'true'|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



## Definizioni oggetto

#### DataSetsMetadata

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|


#### TabularDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|



#### BlobMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|string|no|
|Nome|string|no|
|DisplayName|string|no|
|Path|string|no|
|LastModified|string|no|
|Dimensione|integer|no|
|MediaType|string|no|
|IsFolder|boolean|no|
|ETag|string|no|
|FileLocator|string|no|


## Passaggi successivi
Dopo aver aggiunto l'API di OneDrive a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0302_2016-->