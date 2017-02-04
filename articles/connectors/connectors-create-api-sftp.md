---
title: Informazioni su come usare il connettore SFTP nelle app per la logica | Documentazione Microsoft
description: "Creare app per la logica con Servizio app di Azure. Connettersi all&quot;API SFTP per inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2977404fb408ea5301c88caa7ce6767a906ca9c7


---
# <a name="get-started-with-the-sftp-connector"></a>Introduzione al connettore SFTP
Usare il connettore SFTP per accedere a un account SFTP al fine di inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file.  

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Connettersi a SFTP
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-sftp"></a>Creare una connessione a SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Usare un trigger SFTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Questo esempio spiega come usare il trigger **SFTP - Quando viene aggiunto o modificato un file** per avviare un flusso di lavoro dell'app per la logica quando viene aggiunto o modificato un file in un server SFTP. Nell'esempio si apprenderà anche come aggiungere una condizione che controlla il contenuto del file nuovo o modificato e decide di estrarre il file se il relativo contenuto indica che il file deve essere estratto prima di usare il contenuto. Infine si apprenderà come aggiungere un'azione per estrarre il contenuto di un file e inserire il contenuto estratto in una cartella sul server SFTP. 

In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti.  È quindi possibile usare un'azione connettore SFTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviarlo nel database degli ordini.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Add a condition
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Usare un'azione SFTP
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## <a name="sftp-triggers"></a>Trigger SFTP
SFTP supporta i trigger seguenti:  

| Trigger | Descrizione |
| --- | --- |
| [Quando viene aggiunto o modificato un file](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella. |

## <a name="sftp-actions"></a>Azioni SFTP
SFTP supporta le azioni seguenti:

| Azione | Description |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-sftp.md#get-file-metadata) |Questa operazione recupera i metadati del file tramite l'ID del file. |
| [Aggiorna file](connectors-create-api-sftp.md#update-file) |Questa operazione aggiorna il contenuto del file. |
| [Elimina file](connectors-create-api-sftp.md#delete-file) |Questa operazione elimina un file. |
| [Ottieni metadati file in base al percorso](connectors-create-api-sftp.md#get-file-metadata-using-path) |Questa operazione recupera i metadati del file tramite il percorso del file. |
| [Ottieni contenuto file in base al percorso](connectors-create-api-sftp.md#get-file-content-using-path) |Questa operazione recupera il contenuto del file tramite il percorso del file. |
| [Ottieni contenuto file](connectors-create-api-sftp.md#get-file-content) |Questa operazione recupera il contenuto del file tramite l'ID del file. |
| [Crea file](connectors-create-api-sftp.md#create-file) |Questa operazione carica un file su un server SFTP. |
| [Copia file](connectors-create-api-sftp.md#copy-file) |Questa operazione copia un file su un server SFTP. |
| [Elenca file nella cartella](connectors-create-api-sftp.md#list-files-in-folder) |Questa operazione recupera i file contenuti in una cartella. |
| [Elenca file nella cartella radice](connectors-create-api-sftp.md#list-files-in-root-folder) |Questa operazione recupera i file nella cartella radice. |
| [Estrai cartella](connectors-create-api-sftp.md#extract-folder) |Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:

### <a name="get-file-metadata"></a>Ottenere i metadati del file
Questa operazione recupera i metadati del file tramite l'ID del file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Specifica il file |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### <a name="update-file"></a>Aggiorna file
Questa operazione aggiorna il contenuto del file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Specifica il file |
| body* |Contenuto del file |Contenuto del file da aggiornare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### <a name="delete-file"></a>Elimina file
Questa operazione elimina un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Specifica il file |

* indica che la proprietà è obbligatoria

### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Questa operazione recupera i metadati del file tramite il percorso del file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Percorso univoco del file |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### <a name="get-file-content-using-path"></a>Recupera contenuto di file tramite percorso
Questa operazione recupera il contenuto del file tramite il percorso del file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Percorso univoco del file |

* indica che la proprietà è obbligatoria

### <a name="get-file-content"></a>Recupera contenuto di file
Questa operazione recupera il contenuto del file tramite l'ID del file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Specifica il file |

* indica che la proprietà è obbligatoria

### <a name="create-file"></a>Crea file
Questa operazione carica un file su un server SFTP. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderPath* |Percorso della cartella |Percorso univoco della cartella |
| name* |Nome file |Nome del file |
| body* |Contenuto del file |Contenuto del file da creare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

|  | Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string | |
| Nome |string | |
| displayName |string | |
| path |string | |
| LastModified |string | |
| Dimensione |integer | |
| MediaType |string | |
| IsFolder |boolean | |
| ETag |string | |
| FileLocator |string | |

### <a name="copy-file"></a>Copia file
Questa operazione copia un file su un server SFTP. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| source* |Percorso file origine |Percorso del file di origine |
| destination* |Percorso file di destinazione |Percorso del file di destinazione, incluso il nome del file |
| overwrite |Sovrascrivere? |Sovrascrive il file di destinazione se è impostata su 'true' |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |stringa |

### <a name="when-a-file-is-added-or-modified"></a>When a file is added or modified (Quando un file viene aggiunto o modificato)
Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderId* |Cartella |Specifica una cartella |

* indica che la proprietà è obbligatoria

### <a name="list-files-in-folder"></a>Elenca i file nella cartella
Questa operazione recupera i file contenuti in una cartella. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |Cartella |Specifica la cartella |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### <a name="list-files-in-root-folder"></a>Elenca i file nella cartella radice
Questa operazione recupera i file nella cartella radice. 

Non sono disponibili parametri per questa chiamata

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### <a name="extract-folder"></a>Estrai cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| source* |Percorso del file di archivio di origine |Percorso del file di archivio |
| destination* |Percorso cartella di destinazione |Percorso della cartella di destinazione |
| overwrite |Sovrascrivere? |Sovrascrive i file di destinazione se è impostata su 'true' |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| id |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

## <a name="http-responses"></a>Risposte HTTP
Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP: 

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


