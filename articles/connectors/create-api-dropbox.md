<properties
	pageTitle="Aggiungere l'API di Dropbox a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di Dropbox con i parametri dell'API REST."
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Introduzione all'API di Dropbox 
Connettersi a Dropbox per gestire file, ad esempio creare i file, recuperarli e così via. L'API di Dropbox può essere usata da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic sul [connettore Dropbox](../app-service-logic/app-service-logic-connector-dropbox.md).


Con Dropbox è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Dropbox. 
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per creare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile inviare tale file come messaggio di posta elettronica tramite Office 365.
- Aggiungere l'API di Dropbox a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, accedere alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Dropbox include i trigger e le azioni seguenti.

Trigger | Azioni
--- | ---
<ul><li>Quando un file viene creato</li><li>Quando un file viene modificato</li></ul> | <ul><li>Crea file</li><li>Quando un file viene creato</li><li>Copia file</li><li>Elimina file</li><li>Estrai archivio in una cartella</li><li>Ottieni contenuto di file tramite ID</li><li>Ottieni file tramite percorso</li><li>Ottieni metadati di file tramite ID</li><li>Ottieni metadati di file tramite percorso</li><li>Aggiorna file</li><li>Quando un file viene modificato</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a Dropbox

Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Dropbox.

1. Accedere all'account Dropbox.
2. Selezionare **Autorizza** e consentire alle app per la logica di connettersi e usare Dropbox. 

Dopo aver creato la connessione immettere le proprietà di Dropbox, ad esempio nome file o percorso cartella. Tali proprietà vengono descritte nelle **Informazioni di riferimento sulle API REST** in questo argomento.

>[AZURE.TIP] È possibile usare la stessa connessione di Dropbox in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file    
Carica un file in Dropbox. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|nessuno |Percorso della cartella per caricare il file in Dropbox|
|name|string|yes|query|nessuno |Nome del file da creare in Dropbox|
|body|string(binary) |sì|body|nessuno |Contenuto del file da creare in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene creato    
Attiva un flusso quando un nuovo file viene creato in una cartella di Dropbox. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno |Identificatore univoco della cartella in Dropbox.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Copia file    
Copia un file in Dropbox. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno |URL del file di origine|
|destination|string|yes|query| nessuno|Percorso file di destinazione in Dropbox, incluso nome file di destinazione|
|overwrite|boolean|no|query|nessuno |Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina file    
Elimina un file da Dropbox. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file da eliminare da Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella    
Estrae un file di archivio in una cartella di Dropbox, ad esempio con estensione zip. **```POST: /datasets/default/extractFolderV2```**

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno |Percorso del file di archivio|
|destination|string|yes|query|nessuno |Percorso in Dropbox in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|no|query|nessuno |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite ID    
Recupera il contenuto dei file da Dropbox tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite percorso    
Recupera il contenuto dei file da Dropbox tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite ID    
Recupera i metadati dei file da Dropbox tramite ID file. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite percorso    
Recupera i metadati dei file da Dropbox tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file    
Aggiorna un file in Dropbox. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path| nessuno|Identificatore univoco del file da aggiornare in Dropbox|
|body|string(binary) |sì|body|nessuno |Contenuto del file da aggiornare in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene modificato    
Attiva un flusso quando un file viene modificato in una cartella di Dropbox. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno |Identificatore univoco della cartella in Dropbox.|

#### Risposta
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


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0309_2016-->