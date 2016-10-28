<properties
pageTitle="Informazioni su come usare il connettore SFTP nelle app per la logica | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi all'API SFTP per inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/20/2016"
ms.author="deonhe"/>

# Introduzione al connettore SFTP

Usare il connettore SFTP per accedere a un account SFTP al fine di inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file.

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a SFTP

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione a SFTP

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a SFTP](../../includes/connectors-create-api-sftp.md)]

## Usare un trigger SFTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Questo esempio illustrerà come usare il trigger **SFTP - When a file is added or modified** (SFTP - Quando un file viene aggiunto o modificato) per avviare un flusso di lavoro dell'app logica quando viene aggiunto o modificato un file in un server SFTP. Nell'esempio si apprenderà inoltre come aggiungere una condizione che controlla il contenuto del file nuovo o modificato e prende la decisione di estrarre il file se il relativo contenuto indica che l'estrazione è necessaria per poter usare il contenuto. Infine si apprenderà come aggiungere un'azione per estrarre il contenuto di un file e inserire il contenuto estratto in una cartella sul server SFTP.

In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. È quindi possibile usare un'azione connettore SFTP, come **Get file content** (Ottieni contenuto di file) per recuperare il contenuto dell'ordine a scopo di elaborazione successiva e di archiviazione nel database degli ordini.

>[AZURE.INCLUDE [Passaggi per creare un trigger SFTP](../../includes/connectors-create-api-sftp-trigger.md)]

## Aggiungere una condizione

>[AZURE.INCLUDE [Passaggi per aggiungere una condizione](../../includes/connectors-create-api-sftp-condition.md)]

## Usare un'azione SFTP

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Passaggi per creare un'azione SFTP](../../includes/connectors-create-api-sftp-action.md)]


## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger SFTP

SFTP supporta i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[When a file is added or modified](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) (Quando un file viene aggiunto o modificato)|Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella.|


## Azioni SFTP

SFTP supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Ottieni metadati file](connectors-create-api-sftp.md#get-file-metadata)|Questa operazione recupera i metadati del file tramite l'ID del file.|
|[Aggiorna file](connectors-create-api-sftp.md#update-file)|Questa operazione aggiorna il contenuto del file.|
|[Elimina file](connectors-create-api-sftp.md#delete-file)|Questa operazione elimina un file.|
|[Recupera metadati di file tramite percorso](connectors-create-api-sftp.md#get-file-metadata-using-path)|Questa operazione recupera i metadati del file tramite il percorso del file.|
|[Ottieni contenuto di file tramite percorso](connectors-create-api-sftp.md#get-file-content-using-path)|Questa operazione recupera il contenuto del file tramite il percorso del file.|
|[Ottieni contenuto di file](connectors-create-api-sftp.md#get-file-content)|Questa operazione recupera il contenuto del file tramite l'ID del file.|
|[Crea file](connectors-create-api-sftp.md#create-file)|Questa operazione carica un file su un server SFTP.|
|[Copia file](connectors-create-api-sftp.md#copy-file)|Questa operazione copia un file su un server SFTP.|
|[Elenca i file nella cartella](connectors-create-api-sftp.md#list-files-in-folder)|Questa operazione recupera i file contenuti in una cartella.|
|[Elenca i file nella cartella radice](connectors-create-api-sftp.md#list-files-in-root-folder)|Questa operazione recupera i file nella cartella radice.|
|[Estrai cartella](connectors-create-api-sftp.md#extract-folder)|Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.|
### Informazioni dettagliate sulle azioni

Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:



### Ottieni metadati file
Questa operazione recupera i metadati del file tramite l'ID del file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Specifica il file|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|stringa|




### Aggiorna file
Questa operazione aggiorna il contenuto del file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Specifica il file|
|body*|Contenuto del file|Contenuto del file da aggiornare|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### Elimina file
Questa operazione elimina un file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Specifica il file|

* indica che la proprietà è obbligatoria




### Recupera metadati di file tramite percorso
Questa operazione recupera i metadati del file tramite il percorso del file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Percorso univoco del file|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### Ottieni contenuto di file tramite percorso
Questa operazione recupera il contenuto del file tramite il percorso del file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Percorso univoco del file|

* indica che la proprietà è obbligatoria




### Ottieni contenuto di file
Questa operazione recupera il contenuto del file tramite l'ID del file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Specifica il file|

* indica che la proprietà è obbligatoria




### Crea file
Questa operazione carica un file su un server SFTP.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderPath*|Percorso della cartella|Percorso univoco della cartella|
|name*|Nome file|Nome del file|
|body*|Contenuto del file|Contenuto del file da creare|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


|| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### Copia file
Questa operazione copia un file su un server SFTP.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|Percorso file origine|Percorso del file di origine|
|destination*|Percorso file di destinazione|Percorso del file di destinazione, incluso il nome del file|
|overwrite|Sovrascrivere?|Sovrascrive il file di destinazione se è impostata su 'true'|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### When a file is added or modified (Quando un file viene aggiunto o modificato)
Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderId*|Cartella|Specifica una cartella|

* indica che la proprietà è obbligatoria




### Elenca i file nella cartella
Questa operazione recupera i file contenuti in una cartella.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|Cartella|Specifica la cartella|

* indica che la proprietà è obbligatoria



#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### Elenca i file nella cartella radice
Questa operazione recupera i file nella cartella radice.


Non sono disponibili parametri per questa chiamata

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|




### Estrai cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|Percorso del file di archivio di origine|Percorso del file di archivio|
|destination*|Percorso cartella di destinazione|Percorso della cartella di destinazione|
|overwrite|Sovrascrivere?|Sovrascrive i file di destinazione se è impostata su 'true'|

* indica che la proprietà è obbligatoria



#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|



## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|
|default|Operazione non riuscita.|







## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
