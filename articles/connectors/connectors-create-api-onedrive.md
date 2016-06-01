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
ms.date="05/12/2016"
ms.author="mandia"/>

# Introduzione all'API di OneDrive

Connettersi a OneDrive per gestire i file, ad esempio, caricare, recuperare ed eliminare i file e altro ancora. L'API di OneDrive può essere usata da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con OneDrive è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da OneDrive. 
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per creare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in OneDrive, è possibile inviare tale file come messaggio di posta elettronica tramite Office 365.
- Aggiungere l'API di OneDrive a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, accedere alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di OneDrive include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>When a file is created</li><li>When a file is modified</li></ul> | <ul><li>Create file</li><li>List files in a folder</li><li>When a file is created</li><li>Copy file</li><li>Delete file</li><li>Extract folder</li><li>Get file content using id</li><li>Get file content using path</li><li>Get file metadata using id</li><li>Get file metadata using path</li><li>List root folder</li><li>Update file</li><li>When a file is modified</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a OneDrive

Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a OneDrive.

1. Accedere all'account OneDrive.
2. Consentire alle app per la logica di connettersi e usare OneDrive. 

>[AZURE.INCLUDE [Passaggi per creare una connessione a OneDrive](../../includes/connectors-create-api-onedrive.md)]

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
Attiva un flusso quando un nuovo file viene creato in una cartella di OneDrive. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno|Identificatore univoco della cartella in OneDrive.|


### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Quando un file viene modificato in una cartella di OneDrive, attiva un flusso
Attiva un flusso quando un file viene modificato in una cartella di OneDrive. ```GET: /datasets/default/triggers/onupdatedfile```

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

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

[5]: https://account.live.com/developers/applications/create
[6]: ./media/connectors-create-api-onedrive/onedrive-new-app.png
[7]: ./media/connectors-create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0518_2016-->