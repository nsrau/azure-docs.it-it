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
   ms.date="08/18/2016"
   ms.author="mandia"/>  

# Introduzione al connettore Box
Connettersi a Box per creare ed eliminare file e così via.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Box è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Box.
- Usare i trigger quando un file viene creato o aggiornato.
- Usare le azioni per copiare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando un file viene modificato in Box, è possibile usare il file e la posta elettronica tramite Office 365.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Box include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>Quando viene creato un file</li><li>Quando viene modificato un file</li></ul> | <ul><li>Crea file</li><li>Quando viene creato un file</li><li>Copia file</li><li>Elimina file</li><li>Estrai archivio nella cartella</li><li>Ottieni contenuto file in base all'ID</li><li>Ottieni contenuto file in base al percorso</li><li>Ottieni metadati file in base all'ID</li><li>Ottieni metadati file in base al percorso</li><li>Aggiorna file</li><li>Quando viene modificato un file</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Box
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Box.

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Box](../../includes/connectors-create-api-box.md)]

Dopo aver creato la connessione, immettere le proprietà di Box. Il **riferimento all'API REST** in questo articolo descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Box in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file in Box. ```POST: /datasets/default/files```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|folderPath|stringa|Sì|query|None |Percorso della cartella per caricare il file in Box|
|name|stringa|Sì|query|None |Nome del file da creare in Box|
|body|string(binary) |Sì|body|None |Contenuto del file da creare in Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene creato
Attiva un flusso quando viene creato un nuovo file in una cartella di Box. ```GET: /datasets/default/triggers/onnewfile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|stringa|Sì|query|None |Identificatore univoco della cartella in Box.|

#### Response
|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Copia file
Copia un file in Box. ```POST: /datasets/default/copyFile```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|una sezione source|string|Sì|query|None |URL del file di origine|
|destination|stringa|Sì|query| None|Percorso file di destinazione in Box, incluso nome file di destinazione|
|overwrite|boolean|No|query| None|Sovrascrive il file di destinazione se è impostata su 'true'|

#### Response
|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina file
Elimina un file da Box. ```DELETE: /datasets/default/files/{id}```


| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|stringa|Sì|path|None |Identificatore univoco del file da eliminare da Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Estrai archivio in una cartella
Estrae un file di archivio in una cartella di Box, ad esempio un file ZIP. ```POST: /datasets/default/extractFolderV2```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|una sezione source|stringa|Sì|query| |Percorso del file di archivio|
|destination|stringa|Sì|query| |Percorso in Box in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|No|query| |Sovrascrive i file di destinazione se è impostata su 'true'|

#### Response
|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite ID
Recupera il contenuto di file da Box in base all'ID. ```GET: /datasets/default/files/{id}/content```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|string|Sì|path|None |Identificatore univoco del file in Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni contenuto di file tramite percorso
Recupera il contenuto di file da Box in base al percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|stringa|Sì|query|None |Percorso univoco del file in Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera metadati di file tramite ID
Recupera i metadati di file da Box in base all'ID file. ```GET: /datasets/default/files/{id}```

| Name|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|id|string|Sì|path| None|Identificatore univoco del file in Box|

#### Response
|Name|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Ottieni metadati di file tramite percorso
Recupera i metadati di file da Box in base al percorso. ```GET: /datasets/default/GetFileByPath```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|stringa|Sì|query|None |Percorso univoco del file in Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna file
Aggiorna un file in Box. ```PUT: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|stringa|Sì|path| None|Identificatore univoco del file da aggiornare in Box|
|body|string(binary) |Sì|body|None |Contenuto del file da aggiornare in Box|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Quando un file viene modificato
Attiva un flusso quando viene modificato un file in una cartella di Box. ```GET: /datasets/default/triggers/onupdatedfile```

| Name|Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|stringa|Sì|query|None |Identificatore univoco della cartella in Box.|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetti

#### DataSetsMetadata

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|

#### TabularDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|stringa|no|
|displayName|stringa|no|
|urlEncoding|stringa|no|
|tableDisplayName|stringa|no|
|tablePluralName|stringa|no|

#### BlobDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|stringa|no|
|displayName|string|no|
|urlEncoding|stringa|no|

#### BlobMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|string|no|
|Nome|stringa|no|
|DisplayName|stringa|no|
|Path|string|no|
|LastModified|stringa|no|
|Dimensione|integer|no|
|MediaType|stringa|no|
|IsFolder|boolean|no|
|ETag|stringa|no|
|FileLocator|stringa|no|

## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0824_2016-->