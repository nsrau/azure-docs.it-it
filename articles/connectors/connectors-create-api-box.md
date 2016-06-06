<properties
    pageTitle="Aggiungere il connettore Box alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Box con i parametri dell'API REST"
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

# Introduzione al connettore Box
Connettersi a Box per creare ed eliminare file e così via. Il connettore Box può essere usato da:

- App per la logica trattate in questo argomento
- PowerApps. Vedere l'[elenco delle connessioni PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) per l'elenco completo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Box è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Box. 
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per copiare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando un file viene modificato in Box, è possibile usare il file e la posta elettronica tramite Office 365.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Box include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>When a file is created</li><li>When a file is modified</li></ul> | <ul><li>Create file</li><li>When a file is created</li><li>Copy file</li><li>Delete file</li><li>Extract archive to folder</li><li>Get file content using id</li><li>Get file content using path</li><li>Get file metadata using id</li><li>Get file metadata using path</li><li>Update file</li><li>When a file is modified</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Box
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Box.

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Box](../../includes/connectors-create-api-box.md)]

Dopo aver creato la connessione, immettere le proprietà di Box. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Box in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file in Box. ```POST: /datasets/default/files```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|Sì|query|Nessuno |Percorso della cartella per caricare il file in Box|
|name|string|Sì|query|Nessuno |Nome del file da creare in Box|
|body|string(binary) |Sì|body|Nessuno |Contenuto del file da creare in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene creato
Attiva un flusso quando viene creato un nuovo file in una cartella di Box. ```GET: /datasets/default/triggers/onnewfile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|Sì|query|Nessuno |Identificatore univoco della cartella in Box.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Copia file
Copia un file in Box. ```POST: /datasets/default/copyFile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|Sì|query|Nessuno |URL del file di origine|
|destination|string|Sì|query| Nessuno|Percorso file di destinazione in Box, incluso nome file di destinazione|
|overwrite|boolean|No|query| Nessuno|Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina file
Elimina un file da Box. ```DELETE: /datasets/default/files/{id}```


| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|Sì|path|Nessuno |Identificatore univoco del file da eliminare da Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella
Estrae un file di archivio in una cartella di Box, ad esempio un file con estensione zip. ```POST: /datasets/default/extractFolderV2```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|Sì|query| |Percorso del file di archivio|
|destination|string|Sì|query| |Percorso in Box in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|No|query| |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite ID
Recupera il contenuto dei file da Box tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|Sì|path|Nessuno |Identificatore univoco del file in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite percorso
Recupera il contenuto dei file da Box tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|Sì|query|Nessuno |Percorso univoco del file in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file tramite ID
Recupera i metadati dei file da Box tramite ID file. ```GET: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|Sì|path| Nessuno|Identificatore univoco del file in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file tramite percorso
Recupera i metadati dei file da Box tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|Sì|query|Nessuno |Percorso univoco del file in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna un file in Box. ```PUT: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|Sì|path| Nessuno|Identificatore univoco del file da aggiornare in Box|
|body|string(binary) |Sì|body|Nessuno |Contenuto del file da aggiornare in Box|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene modificato
Attiva un flusso quando viene modificato un file in una cartella di Box. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|Sì|query|Nessuno |Identificatore univoco della cartella in Box.|

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