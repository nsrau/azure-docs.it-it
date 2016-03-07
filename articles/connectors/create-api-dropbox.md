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
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Introduzione all'API di Dropbox 
Connettersi a Dropbox per gestire file, ad esempio creare i file, recuperarli e così via.

L'API di Dropbox può essere usata dalle app di PowerApps Enterprise e dalle app per la logica.

Con Dropbox è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Dropbox. 
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per creare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile inviare tale file come messaggio di posta elettronica tramite Office 365.
- Aggiungere l'API di Dropbox a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Dropbox include i trigger e le azioni seguenti.

Trigger | Azioni
--- | ---
<ul><li>Quando un file viene creato</li><li>Quando un file viene modificato</li></ul> | <ul><li>Crea file</li><li>Quando un file viene creato</li><li>Copia file</li><li>Elimina file</li><li>Estrai archivio in una cartella</li><li>Recupera contenuto di file tramite ID</li><li>Recupera file tramite percorso</li><li>Recupera metadati di file tramite ID</li><li>Recupera metadati di file tramite percorso</li><li>Aggiorna file</li><li>Quando un file viene modificato</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a Dropbox

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge Dropbox a PowerApps Enterprise, si immettono i valori di **chiave app** e **segreto app** dell'applicazione di Dropbox. Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione Dropbox. Se non si ha un'applicazione Dropbox, è possibile usare la procedura seguente per creare l'applicazione:

1. Accedere a [Dropbox][1].
2. Visitare il sito per sviluppatori Dropbox e selezionare **My Apps**: ![Sito per sviluppatori Dropbox][8]  
3. Selezionare **Crea app**: ![Creare app Dropbox][9]  
4. In **Crea una nuova app sulla piattaforma Dropbox**:  

	1. In **Choose an API** selezionare **Dropbox API**.
	2. In **Choose the type of access you need** selezionare **Full Dropbox**.  
	3. Immettere un nome per l'app.  

	![Creare app Dropbox pagina 1][10]

5. Nella pagina delle impostazioni dell’app:

	1. In **OAuth 2** immettere il valore dell'**URL di reindirizzamento** visualizzato quando si aggiunge l'API di Dropbox nel portale di Azure. Selezionare **Aggiungi**.  
	2. Selezionare il collegamento **Show** per visualizzare il **segreto dell'app**:  

	![Creare app Dropbox pagina 2][11]

A questo punto, copiare e incollare i valori di **ID client** e **chiave app** nella configurazione di Dropbox nel portale di Azure.

### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Dropbox.

1. Accedere all'account Dropbox.
2. Selezionare **Authorize** e consentire alle app per la logica di connettersi e usare Dropbox. 

Dopo aver creato la connessione immettere le proprietà di Dropbox, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Dropbox in altre app per la logica.

## Riferimento all'API REST Swagger

### Crea file    
Carica un file in Dropbox. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|sì|query|nessuno |Percorso della cartella per caricare il file in Dropbox|
|name|string|sì|query|nessuno |Nome del file da creare in Dropbox|
|body|string(binary) |sì|body|nessuno |Contenuto del file da creare in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene creato    
Quando un nuovo file viene creato in una cartella di Dropbox, attiva un flusso. ```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|sì|query|nessuno |Identificatore univoco della cartella in Dropbox.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Copia file    
Copia un file in Dropbox. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|sì|query|nessuno |URL del file di origine|
|destination|string|sì|query| nessuno|Percorso file di destinazione in Dropbox, incluso nome file di destinazione|
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
|id|string|sì|path|nessuno|Identificatore univoco del file da eliminare da Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella    
Estrae un file di archivio in una cartella di Dropbox, ad esempio con estensione zip. **```POST: /datasets/default/extractFolderV2```**

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|sì|query|nessuno |Percorso del file di archivio|
|destination|string|sì|query|nessuno |Percorso in Dropbox in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|no|query|nessuno |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite ID    
Recupera i contenuti del file da Dropbox tramite l'ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite percorso    
Recupera i contenuti del file da Dropbox tramite il percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|sì|query|nessuno |Percorso univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite ID    
Recupera i metadati dei file da Dropbox tramite l'ID del file. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite percorso    
Recupera i metadati dei file da Dropbox tramite il percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|sì|query|nessuno |Percorso univoco del file in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file    
Aggiorna un file in Dropbox. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path| nessuno|Identificatore univoco del file da aggiornare in Dropbox|
|body|string(binary) |sì|body|nessuno |Contenuto del file da aggiornare in Dropbox|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene modificato    
Quando un file viene modificato in una cartella di Dropbox, attiva un flusso. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|sì|query|nessuno |Identificatore univoco della cartella in Dropbox.|

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
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|source|string|no|
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
Dopo aver aggiunto l'API di Dropbox a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0224_2016-->