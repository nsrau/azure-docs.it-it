<properties
	pageTitle="Aggiungere l'API di Google Drive a PowerApps o app per la logica | Microsoft Azure"
	description="Panoramica dell'API di Google Drive con i parametri dell'API REST."
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

# Introduzione all'API di Google Drive
Connettersi a Google Drive per creare file, recuperare righe e così via.

L'API di Google Drive può essere usata da PowerApps e dalle app per la logica.

Con Google Drive è possibile:

- Creare il flusso aziendale in base ai dati ottenuti dalla ricerca. 
- Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.
- Aggiungere l'API di Google Drive a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger e azioni
Google Drive include le azioni seguenti. Non sono disponibili trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Crea file</li><li>Inserisci riga</li><li>Copia file</li><li>Elimina file</li><li>Elimina riga</li><li>Estrai archivio in una cartella </li><li>Recupera contenuto di file tramite ID</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file tramite ID</li><li>Recupera metadati di file tramite percorso</li><li>Recupera riga</li><li>Aggiorna file</li><li>Aggiorna riga</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.


## Creare una connessione a Google Drive

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge Google Drive a PowerApps Enterprise, si immettono i valori di **chiave app** e **segreto app** dell'applicazione di Google Drive. Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione Google. Se non si ha un'applicazione Google Drive, è possibile usare la procedura seguente per creare l'applicazione:

1. Accedere a [Google Developers Console][5] e selezionare **Create an empty project**: ![Google Developers Console][6]

2. Immettere le proprietà dell'applicazione e selezionare **Create**.
3. Selezionare **Use Google APIs**: ![Usare le API Google][8]  
4. Nella panoramica selezionare **Drive API**: ![Panoramica dell'API di Google Drive][9]  
5. Selezionare **Abilita API**: ![Abilitare le API di Google Drive][10]  
6. All’abilitazione delle API Drive, scegliere **Credenziali** e selezionare **OAuth 2.0 Client ID**: ![Aggiungere credenziali][12]  
7. Selezionare **Configura schermata di consenso**.
8. In **OAuth consent screen** immettere un **nome prodotto** e selezionare **Save**: ![Configurare la schermata di consenso][13]  
9. Nella pagina di creazione dell’id client:  

	1. In **Application type** selezionare **Web application**.
	2. Immettere un nome per il client..
	3. Immettere il valore dell'URL di reindirizzamento visualizzato quando è stata aggiunta l'API di Google Drive nel portale di Azure.
	4. Selezionare **Create**.  

	![Creare l’id client][14]

11. Vengono visualizzati l'ID client e il segreto client dell'applicazione registrata.

A questo punto, copiare e incollare i valori di **ID client** e **chiave app** nella configurazione dell'API di Google Drive nel portale di Azure.


### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Google Drive.

1. Accedere all'account Google Drive.
2. Selezionare **Authorize** e consentire alle app per la logica di connettersi e usare Google Drive. 

Dopo aver creato la connessione immettere le proprietà di Google Drive, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Google Drive in altre app per la logica.


## Riferimento all'API REST Swagger

### Crea file    
Carica un file in Google Drive. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|sì|query|nessuno |Percorso della cartella per caricare il file in Google Drive|
|name|string|sì|query|nessuno |Nome del file da creare in Google Drive|
|body|string(binary) |sì|body| nessuno|Contenuto del file da creare in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Inserisci riga    
Inserisce una riga in Google Sheet. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|sì|path| nessuno|Identificatore univoco del file di Google Sheet|
|table|string|sì|path|nessuno |Identificatore univoco del foglio di lavoro|
|item|ItemInternalId: string |sì|body|nessuno |Riga da inserire nel foglio specificato|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Copia file    
Copia un file in Google Drive. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|sì|query| nessuno|URL del file di origine|
|destination|string|sì|query|nessuno |Percorso file di destinazione in Google Drive incluso nome file di destinazione|
|overwrite|boolean|no|query|nessuno |Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina file    
Elimina un file da Google Drive. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file da eliminare da Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina riga    
Elimina una riga da Google Sheet. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|sì|path|nessuno |Identificatore univoco del file di Google Sheet|
|table|string|sì|path|nessuno |Identificatore univoco del foglio di lavoro|
|id|string|sì|path|nessuno |Identificatore univoco della riga da eliminare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella    
Estrae un file di archivio in una cartella di Google Drive, ad esempio con estensione zip. ```POST: /datasets/default/extractFolderV2```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|sì|query|nessuno |Percorso del file di archivio|
|destination|string|sì|query|nessuno |Percorso in Google Drive in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|no|query|nessuno |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite ID    
Recupera il contenuto di file da Google Drive tramite l'ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file da recuperare in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite percorso    
Recupera il contenuto di file da Google Drive tramite il percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|sì|query|nessuno |Percorso del file in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite ID    
Recupera i metadati dei file da Google Drive tramite l'ID. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite percorso    
Recupera i metadati dei file da Google Drive tramite il percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|sì|query|nessuno |Percorso del file in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottenere la riga    
Recupera una singola riga da Google Sheet. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|sì|path|nessuno |Identificatore univoco del file di Google Sheet|
|table|string|sì|path|nessuno |Identificatore univoco del foglio di lavoro|
|id|string|sì|path| nessuno|Identificatore univoco della riga da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file    
Aggiorna un file in Google Drive. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|sì|path|nessuno |Identificatore univoco del file da aggiornare in Google Drive|
|body|string(binary) |sì|body| nessuno|Contenuto del file da creare in Google Drive|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna riga    
Aggiorna una riga in Google Sheet. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|sì|path|nessuno |Identificatore univoco del file di Google Sheet|
|table|string|sì|path| nessuno|Identificatore univoco del foglio di lavoro|
|id|string|sì|path|nessuno |Identificatore univoco della riga da aggiornare|
|item|ItemInternalId: string |sì|body|nessuno |Riga con i valori aggiornati|

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

#### TableMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|

#### TablesList

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|

#### Table

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Item

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


## Passaggi successivi
Dopo aver aggiunto l'API di Google Drive a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0224_2016-->