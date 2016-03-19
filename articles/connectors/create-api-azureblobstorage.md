<properties
	pageTitle="Aggiungere l'API di archiviazione BLOB di Azure alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di archiviazione BLOB di Azure con i parametri dell'API REST"
	services=""
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introduzione all'API di archiviazione BLOB di Azure
Connettersi a un BLOB di Azure per gestire file in un contenitore BLOB, ad esempio la creazione, l'eliminazione di file e così via. L'API di archiviazione BLOB di Azure può essere usata da:

- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione schema di 2014-12-01-preview, fare clic su [connettore BLOB di Archiviazione di Azure](../app-service-logic/app-service-logic-connector-azurestorageblob.md).

Con l'archiviazione BLOB di Azure è possibile:

- Creare il flusso aziendale in base ai dati ottenuti dal BLOB. 
- Usare azioni che consentono di creare un file, recuperarne il contenuto e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile cercare "urgente" in un file del BLOB e quindi inviare tutti i file con "urgente" in un messaggio di posta elettronica tramite Office 365. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'archiviazione BLOB di Azure include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
| Nessuno. | <ul><li>Crea file</li><li>Copia file</li><li>Elimina file</li><li>Estrai archivio in una cartella</li><li>Recupera contenuto di file</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file</li><li>Recupera metadati di file tramite</li><li>Aggiorna file</li></ul> |

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione al BLOB di Azure
Quando si aggiunge questa API alle app per la logica, immettere i valori di account di archiviazione seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Nome dell'account di archiviazione di Azure | sì | Nome dell'account di archiviazione BLOB|
|Chiave di accesso dell'account di archiviazione di Azure | sì | Chiave di accesso dell'account di archiviazione BLOB|

Dopo aver creato la connessione immettere le proprietà del BLOB, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di BLOB in altre app per la logica.
 

## Riferimento all'API REST Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file nell'archivio BLOB di Azure. ```POST: /datasets/default/files```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query| nessuno |Percorso della cartella per caricare il file in un account di archiviazione BLOB di Azure|
|name|string|yes|query|nessuno |Nome del file da creare nell'archivio BLOB di Azure|
|body|string(binary) |sì|body|nessuno|Contenuto del file da creare nell'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Copia file
Copia un file nell'archivio BLOB di Azure. ```POST: /datasets/default/copyFile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno |URL del file di origine|
|destination|string|yes|query| nessuno|Percorso file di destinazione nell'archivio BLOB di Azure, incluso nome file di destinazione|
|overwrite|boolean|no|query|nessuno |Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina file
Elimina un file dall'archivio BLOB di Azure. ```DELETE: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file da eliminare dall'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella
Estrae un file di archivio in una cartella dell'archivio BLOB di Azure, ad esempio con estensione zip. ```POST: /datasets/default/ExtractFolderV2```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query| nessuno|Percorso del file di archivio|
|destination|string|yes|query|nessuno |Percorso dell'archivio BLOB di Azure in cui estrarre i contenuti dell'archivio|
|overwrite|boolean|no|query|nessuno |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file
Recupera il contenuto dei file dall'archivio BLOB di Azure tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite percorso
Recupera il contenuto dei file dall'archivio BLOB di Azure tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file nell'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file
Recupera i metadati dei file dall'archivio BLOB di Azure tramite ID file. ```GET: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite percorso
Recupera i metadati dei file dall'archivio BLOB di Azure tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file nell'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna un file in Archivio BLOB di Azure. ```PUT: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file da aggiornare nell'archivio BLOB di Azure|
|body|string(binary) |sì|body|nessuno |Contenuto del file da aggiornare nell'archivio BLOB di Azure|

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

<!---HONumber=AcomDC_0302_2016-->