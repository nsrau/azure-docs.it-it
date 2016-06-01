<properties
pageTitle="OneDrive for Business | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi a OneDrive per gestire i file. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introduzione al connettore OneDrive for Business



Il connettore OneDrive for Business può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flusso](http://flows.microsoft.com)  

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore OneDrive for Business può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore OneDrive for Business sono disponibili le azioni e/o i trigger seguenti:

### Azioni di OneDrive for Business
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|Recupera i metadati di un file in OneDrive for Business tramite ID|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|Aggiorna un file in OneDrive for Business|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|Elimina un file da OneDrive for Business|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|Recupera i metadati di un file in OneDrive for Business tramite il percorso|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|Recupera i contenuti di un file in OneDrive for Business tramite il percorso|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|Recupera i contenuti di un file in OneDrive for Business tramite l'ID|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|Carica un file in OneDrive for Business|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|Copia un file in OneDrive for Business|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|Elenca i file in una cartella di OneDrive for Business|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|Elenca i file nella cartella radice di OneDrive for Business|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|Estrae una cartella in OneDrive for Business|
### Trigger di OneDrive for Business
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando un file viene creato|Attiva un flusso quando un nuovo file viene creato in una cartella di OneDrive for Business|
|Quando un file viene modificato|Attiva un flusso quando un file viene modificato in una cartella di OneDrive for Business|


## Creare una connessione a OneDrive for Business
Per creare app per la logica con OneDrive for Business, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornisce le credenziali per OneDrive for Business|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

>[AZURE.INCLUDE [Passaggi per creare una connessione a OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per OneDrive for Business
Si applica alla versione: 1.0

## GetFileMetadata
Recupero dei metadati di un file tramite ID: ottiene i metadati di un file in OneDrive for Business tramite ID

```GET: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Specifica il file|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## UpdateFile
Aggiornamento file: aggiorna un file in OneDrive for Business

```PUT: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Specifica il file da aggiornare|
|body| |sì|body|nessuno|Contenuto del file da aggiornare in OneDrive for Business|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## DeleteFile
Eliminazione file: Elimina un file da OneDrive for Business

```DELETE: /datasets/default/files/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Specifica il file da eliminare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## GetFileMetadataByPath
Recupero dei metadati di un file tramite percorso: ottiene i metadati di un file in OneDrive for Business tramite il percorso

```GET: /datasets/default/GetFileByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file in OneDrive for Business|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## GetFileContentByPath
Recupero dei contenuti di un file tramite percorso: ottiene i contenuti di un file in OneDrive for Business tramite il percorso

```GET: /datasets/default/GetFileContentByPath```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|path|string|yes|query|nessuno|Percorso univoco del file in OneDrive for Business|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## GetFileContent
Recupero dei contenuti di un file tramite ID: ottiene i contenuti di un file in OneDrive for Business tramite ID

```GET: /datasets/default/files/{id}/content```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Specifica il file|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CreateFile
Creazione file: carica un file in OneDrive for Business

```POST: /datasets/default/files```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|nessuno|Percorso della cartella per caricare il file in OneDrive for Business|
|name|string|yes|query|nessuno|Nome del file da creare in OneDrive for Business|
|body| |sì|body|nessuno|Contenuto del file da caricare in OneDrive for Business|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## CopyFile
Copia file: copia un file in OneDrive for Business

```POST: /datasets/default/copyFile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno|URL del file di origine|
|destination|string|yes|query|nessuno|Percorso del file di destinazione in OneDrive for Business, incluso il nome del file di destinazione|
|overwrite|boolean|no|query|false|Sovrascrive il file di destinazione se è impostata su 'true'|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## OnNewFile
Quando un file viene creato: attiva un flusso quando un nuovo file viene creato in una cartella di OneDrive for Business

```GET: /datasets/default/triggers/onnewfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno|Specifica una cartella|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## OnUpdatedFile
Quando un file viene modificato: attiva un flusso quando un file viene modificato in una cartella di OneDrive for Business

```GET: /datasets/default/triggers/onupdatedfile```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderId|string|yes|query|nessuno|Specifica una cartella|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ListFolder
Elenco file in una cartella: elenca i file in una cartella di OneDrive for Business

```GET: /datasets/default/folders/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|id|string|yes|path|nessuno|Specifica la cartella|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ListRootFolder
Elenco cartella radice: elenca i file nella cartella radice di OneDrive for Business

```GET: /datasets/default/folders```

Non sono disponibili parametri per questa chiamata
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## ExtractFolderV2
Estrazione cartella: estrae una cartella in OneDrive for Business

```POST: /datasets/default/extractFolderV2```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|source|string|yes|query|nessuno|Percorso del file di archivio|
|destination|string|yes|query|nessuno|Percorso in OneDrive for Business in cui estrarre il contenuto dell'archivio|
|overwrite|boolean|no|query|false|Sovrascrive i file di destinazione se è impostata su 'true'|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetto 

### DataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabular|non definito|No |
|BLOB|non definito|No |



### TabularDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|No |
|displayName|string|No |
|urlEncoding|string|No |
|tableDisplayName|string|No |
|tablePluralName|string|No |



### BlobDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|No |
|displayName|string|No |
|urlEncoding|string|No |



### BlobMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|string|No |
|Nome|string|No |
|DisplayName|string|No |
|Path|string|No |
|LastModified|string|No |
|Dimensione|numero intero|No |
|MediaType|string|No |
|IsFolder|boolean|No |
|ETag|string|No |
|FileLocator|string|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->