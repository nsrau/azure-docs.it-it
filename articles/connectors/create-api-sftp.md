<properties
	pageTitle="Aggiungere l'API di SFTP alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di SFTP con i parametri dell'API REST."
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

# Introduzione all'API di SFTP
Connettersi a un server SFTP per gestire i file. È possibile eseguire diverse attività sul server SFTP, ad esempio caricare file, eliminarli e così via. L'API di SFTP può essere usata da:

- App per la logica

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione schema 2014-12-01-preview, fare clic sul [connettore di SFTP](../app-service-logic/app-service-logic-connector-sftp.md).

Con SFTP è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da SFTP. 
- Usare un trigger quando un file viene aggiornato.
- Usare azioni per crear file, eliminarli e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile ottenere il contenuto di un file e quindi aggiornare un database SQL. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger e azioni
Per l'API di SFTP sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
<ul><li>Quando un file viene creato o modificato</li></ul> | <ul><li>Crea file</li><li>Copia file</li><li>Elimina file</li><li>Estrai cartella</li><li>Recupera contenuto di file</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file</li><li>Recupera metadati di file tramite percorso</li><li>Aggiorna file</li><li>Quando un file viene creato o modificato </li></ul>

Tutte le API supportano i dati nei formati JSON e XML.


## Creare una connessione a SFTP
Quando si aggiunge questa API alle app per la logica, immettere i valori seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Host Server Address| Sì | Immettere il nome di dominio completo (FQDN) o l'indirizzo IP del server SFTP.|
|Nome utente| Sì | Immettere il nome utente per la connessione al server SFTP.|
|Password | Sì | Immettere la password del nome utente.|
|SSH Server Host Key Finger Print | Sì | Immettere l'ID digitale della chiave host pubblica per il server SSH. <br/><br/>In genere, la chiave viene fornita dall'amministratore del server. Per ottenere l'ID digitale della chiave è inoltre possibile usare gli strumenti ```WinSCP``` o ```ssh-keygen-g3 -F```. | 

Dopo aver creato la connessione immettere le proprietà di SFTP, ad esempio il percorso del file o della cartella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di SFTP in altre app per la logica.


## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file in SFTP. ```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|nessuno |Percorso univoco della cartella in SFTP|
|name|string|yes|query| nessuno|Nome del file|
|body|string(binary) |sì|body|nessuno |Contenuto del file da creare in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Copia file
Copia un file in SFTP. ```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query| nessuno|Percorso del file di origine|
|destination|string|yes|query|nessuno |Percorso del file di destinazione, incluso il nome del file|
|overwrite|boolean|no|query|nessuno|Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Elimina file 
Elimina un file in SFTP. ```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Estrai cartella
Estrae un file di archivio in una cartella tramite SFTP, ad esempio con estensione zip. ```POST: /datasets/default/extractFolderV2```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno |Percorso del file di archivio|
|destination|string|yes|query|nessuno |Percorso della cartella di destinazione|
|overwrite|boolean|no|query|nessuno|Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Recupera contenuto di file
Recupera il contenuto dei file da SFTP tramite ID. ```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera contenuto di file tramite percorso
Recupera il contenuto dei file da SFTP tramite percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query| nessuno|Percorso univoco del file in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file 
Recupera i metadati dei file da SFTP tramite ID file. ```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path| nessuno|Identificatore univoco del file in SFTP|

#### Risposta
| Nome | Descrizione |
| --- | --- |
| 200 | OK | 
| default | Operazione non riuscita.


### Recupera metadati di file tramite percorso
Recupera i metadati dei file da SFTP tramite percorso. ```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno |Percorso univoco del file in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna il contenuto dei file tramite SFTP. ```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno |Identificatore univoco del file in SFTP|
|body|string(binary) |sì|body| nessuno|Contenuto del file da aggiornare in SFTP|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene creato o modificato 
Quando un file viene modificato in SFTP, attiva un flusso. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno |Identificatore univoco della cartella|

#### Risposta
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
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

| Nome | Tipo di dati | Obbligatorio|
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