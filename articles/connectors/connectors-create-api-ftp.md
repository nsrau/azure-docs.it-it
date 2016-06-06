<properties
    pageTitle="Aggiungere il connettore FTP alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore FTP con i parametri dell'API REST"
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

# Introduzione al connettore FTP
Connettersi a un server FTP per gestire i file, ad esempio, caricare i file, eliminare i file e altro. Il connettore FTP può essere usato da:

- App per la logica trattate in questo argomento
- PowerApps. Vedere l'[elenco delle connessioni PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) per l'elenco completo.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con FTP è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da FTP. 
- Usare un trigger quando un file viene aggiornato.
- Usare azioni per creare file, ottenerne il contenuto e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile ottenere il contenuto di un file e quindi aggiornare un database SQL. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger e azioni
Per FTP sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
<ul><li>Gets an updated file</li></ul> | <ul><li>Create file</li><li>Copy file</li><li>Delete file</li><li>Extract folder</li><li>Get file content</li><li>Get file content using path</li><li>Get file metadata</li><li>Get file metadata using path</li><li>Gets an updated file</li><li>Update file</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a FTP


>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a FTP](../../includes/connectors-create-api-ftp.md)]

Dopo aver creato la connessione immettere le proprietà di FTP, ad esempio file di origine o cartella di destinazione. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di FTP in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file nel server FTP. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|nessuno |Percorso della cartella per caricare il file nel server FTP|
|name|string|yes|query| nessuno|Nome del file da creare nel server FTP|
|body| |sì|body|nessuno |Contenuto del file da creare nel server FTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Copia file
Copia un file nel server FTP. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno |URL del file di origine|
|destination|string|yes|query|nessuno |Percorso file di destinazione nel server FTP, incluso nome file di destinazione|
|overwrite|boolean|no|query|nessuno |Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Elimina file 
Elimina un file dal server FTP. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file da eliminare dal server FTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Estrai cartella
Estrae un file di archivio in una cartella del server FTP, ad esempio un file con estensione zip. ```POST: /datasets/default/extractFolderV2```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query| nessuno|Percorso del file di archivio|
|destination|string|yes|query| nessuno|Percorso della cartella di destinazione|
|overwrite|boolean|no|query|nessuno|Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Ottieni contenuto di file
Recupera il contenuto dei file dal server FTP tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite percorso
Recupera il contenuto dei file dal server FTP tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file nel server FTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file 
Recupera i metadati dei file dal server FTP tramite ID file. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Identificatore univoco del file|

#### Risposta
| Nome | Descrizione |
| --- | --- |
| 200 | OK | 
| default | Operazione non riuscita.


### Ottieni metadati di file tramite percorso
Recupera i metadati dei file dal server FTP tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query| nessuno|Percorso univoco del file nel server FTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni un file aggiornato
Ottiene un file aggiornato. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno |ID della cartella in cui cercare un file aggiornato|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file 
Aggiorna un file nel server FTP. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path| nessuno|Identificatore univoco del file da aggiornare nel server FTP|
|body| |sì|body|nessuno |Contenuto del file da aggiornare nel server FTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetto

#### DataSetsMetadata

| Nome | Tipo di dati | Obbligatorio |
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|

#### TabularDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

| Nome | Tipo di dati | Obbligatorio |
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