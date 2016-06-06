<properties
    pageTitle="Aggiungere il connettore di archiviazione BLOB di Azure alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore di archiviazione BLOB di Azure con i parametri dell'API REST"
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
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Introduzione al connettore di archiviazione BLOB di Azure
Connettersi a un BLOB di Azure per gestire file in un contenitore BLOB, ad esempio la creazione, l'eliminazione di file e così via. Il connettore di archiviazione BLOB di Azure può essere usato da:

- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con l'archiviazione BLOB di Azure è possibile:

- Creare il flusso aziendale in base ai dati ottenuti dal BLOB. 
- Usare azioni che consentono di creare un file, ottenerne il contenuto e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile cercare "urgente" in un file del BLOB e quindi inviare tutti i file con "urgente" in un messaggio di posta elettronica tramite Office 365. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'archiviazione BLOB di Azure include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
| Nessuno. | <ul><li>Create file</li><li>Copy file</li><li>Delete file</li><li>Extract archive to folder</li><li>Get file content</li><li>Get file content using path</li><li>Get file metadata</li><li>Get file metadata using path</li><li>Update file</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione al BLOB di Azure

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione all'archiviazione BLOB di Azure](../../includes/connectors-create-api-azureblobstorage.md)]

Dopo aver creato la connessione immettere le proprietà del BLOB, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di BLOB in altre app per la logica.
 

## Informazioni di riferimento sulle API REST Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file locale nell'archiviazione BLOB di Azure. ```POST: /datasets/default/files```

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
Copia un file nell'archiviazione BLOB di Azure. ```POST: /datasets/default/copyFile```

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
Elimina un file dall'archiviazione BLOB di Azure. ```DELETE: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file da eliminare dall'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella
Estrae un file di archivio in una cartella dell'archiviazione BLOB di Azure, ad esempio con estensione zip. ```POST: /datasets/default/ExtractFolderV2```

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


### Ottieni contenuto di file
Recupera i contenuti dei file dall'archiviazione BLOB di Azure tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite percorso
Recupera i contenuti dei file dall'archiviazione BLOB di Azure tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file nell'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file
Recupera i metadati dei file dall'archiviazione BLOB di Azure tramite l'ID del file. ```GET: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file tramite percorso
Recupera i metadati dei file dall'archiviazione BLOB di Azure tramite il percorso. ```GET: /datasets/default/GetFileByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file nell'archivio BLOB di Azure|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna un file locale nell'archiviazione BLOB di Azure. ```PUT: /datasets/default/files/{id}```

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

<!---HONumber=AcomDC_0525_2016-->