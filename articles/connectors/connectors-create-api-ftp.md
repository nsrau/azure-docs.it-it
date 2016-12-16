---
title: Informazioni su come usare il connettore FTP nelle app per la logica | Documentazione Microsoft
description: "Creare app per la logica con Servizio app di Azure. Connettersi a un server FTP per gestire i file. Nel server FTP è possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f08180c196a1f573529c9fed52c7ae5c3869da6d


---
# <a name="get-started-with-the-ftp-connector"></a>Introduzione al connettore FTP
Usare il connettore FTP per monitorare, gestire e creare file in un server FTP. 

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Connettersi a FTP
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-ftp"></a>Creare una connessione a FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Usare un trigger FTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

> [!IMPORTANT]
> Il connettore FTP richiede un server FTP che sia accessibile da Internet e sia configurato per operare con la modalità passiva. Il connettore FTP, inoltre, **non è compatibile con FTPS implicito (FTP su SSL)**. Il connettore FTP supporta solo FTPS esplicito (FTP su SSL).  
> 
> 

Questo esempio spiega come usare il trigger **FTP - Quando viene aggiunto o modificato un file** per avviare un flusso di lavoro dell'app per la logica quando viene aggiunto o modificato un file in un server FTP. In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella FTP per nuovi file di ordini dei clienti.  È quindi possibile usare un'azione connettore FTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviarlo nel database degli ordini.

1. Immettere *ftp* nella casella di ricerca della finestra di progettazione delle app per la logica, quindi selezionare il trigger **FTP - Quando viene aggiunto o modificato un file**   
   ![Immagine di trigger FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Il controllo **Quando viene aggiunto o modificato un file** viene visualizzato.  
   ![Immagine di trigger FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Selezionare **...** sul lato destro del controllo. Viene visualizzato il controllo di selezione della cartella.  
   ![Immagine di trigger FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Selezionare **>** (freccia destra) e individuare la cartella da monitorare per rilevare i file nuovi o modificati. Selezionare la cartella e notare che ora è visualizzata nel controllo **Cartella**.  
   ![Immagine di trigger FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

A questo punto, l’app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro quando un file viene modificato o creato nella cartella FTP specificata. 

> [!NOTE]
> Affinché sia funzionale, l'app per la logica deve contenere almeno un trigger e un'azione. Seguire i passaggi nella sezione successiva per aggiungere un'azione.  
> 
> 

## <a name="use-a-ftp-action"></a>Usare un'azione FTP
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

Dopo aver aggiunto un trigger, seguire questi passaggi per aggiungere un'azione che otterrà il contenuto del file nuovo o modificato individuato dal trigger.    

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che recupera il contenuto del file sul server FTP  
2. Selezionare il collegamento **Aggiungi un'azione** .  
   ![Immagine di azione FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Immettere *FTP* per cercare tutte le azioni correlate a FTP.
4. Selezionare **FTP - Recuperare i contenuti del file** come azione da eseguire quando viene individuato un file nuovo o modificato nella cartella FTP.      
   ![Immagine di azione FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Si apre il controllo **Recuperare i contenuti del file**. **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account sul server FTP, se non lo si è già fatto in precedenza.  
   ![Immagine di azione FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Selezionare il controllo **File** (lo spazio vuoto sotto **FILE***). In questo caso è possibile usare le varie proprietà dal file nuovo o modificato individuato sul server FTP.  
6. Selezionare l'opzione **Contenuto file**.  
   ![Immagine di azione FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Il controllo viene aggiornato e indica che l'azione **FTP - Recuperare i contenuti del file** recupererà il *contenuto del file* nuovo o modificato nel server FTP.      
   ![Immagine di azione FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Salvare il lavoro e poi aggiungere un file nella cartella FTP per testare il flusso di lavoro.    

A questo punto l'app per la logica è stata configurata con un trigger per monitorare una cartella su un server FTP e avviare il flusso di lavoro quando viene individuato un nuovo file o un file modificato sul server FTP stesso. 

L'app per la logica è stata configurata con un'azione per ottenere il contenuto del file nuovo o modificato.

È ora possibile aggiungere un'altra azione, come [SQL Server - Inserisci riga](connectors-create-api-sqlazure.md#insert-row), per inserire il contenuto del file nuovo o modificato in una tabella di database SQL.  

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## <a name="ftp-triggers"></a>Trigger FTP
FTP supporta i trigger seguenti:  

| Trigger | Descrizione |
| --- | --- |
| [Quando viene aggiunto o modificato un file](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) |Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella. |

## <a name="ftp-actions"></a>Azioni FTP
FTP supporta le azioni seguenti:

| Azione | Description |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-ftp.md#get-file-metadata) |Questa operazione ottiene i metadati per un file. |
| [Aggiorna file](connectors-create-api-ftp.md#update-file) |Questa operazione aggiorna un file. |
| [Elimina file](connectors-create-api-ftp.md#delete-file) |Questa operazione elimina un file. |
| [Ottieni metadati file in base al percorso](connectors-create-api-ftp.md#get-file-metadata-using-path) |Questa operazione ottiene i metadati di un file usando il percorso. |
| [Ottieni contenuto file in base al percorso](connectors-create-api-ftp.md#get-file-content-using-path) |Questa operazione ottiene il contenuto di un file usando il percorso. |
| [Ottieni contenuto file](connectors-create-api-ftp.md#get-file-content) |Questa operazione ottiene il contenuto di un file. |
| [Crea file](connectors-create-api-ftp.md#create-file) |Questa operazione crea un file. |
| [Copia file](connectors-create-api-ftp.md#copy-file) |Questa operazione copia un file su un server FTP. |
| [Elenca file nella cartella](connectors-create-api-ftp.md#list-files-in-folder) |Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella. |
| [Elenca file nella cartella radice](connectors-create-api-ftp.md#list-files-in-root-folder) |Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice. |
| [Estrai cartella](connectors-create-api-ftp.md#extract-folder) |Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:

### <a name="get-file-metadata"></a>Ottenere i metadati del file
Questa operazione ottiene i metadati per un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |

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
Questa operazione aggiorna un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |
| body* |Contenuto del file |Contenuto del file |

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
| id* |File |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Questa operazione ottiene i metadati di un file usando il percorso. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

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
Questa operazione ottiene il contenuto di un file usando il percorso. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="get-file-content"></a>Recupera contenuto di file
Questa operazione ottiene il contenuto di un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="create-file"></a>Crea file
Questa operazione crea un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderPath* |Percorso della cartella |Seleziona una cartella |
| name* |Nome file |Nome del file |
| body* |Contenuto del file |Contenuto del file |

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

### <a name="copy-file"></a>Copia file
Questa operazione copia un file su un server FTP. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| source* |URL di origine |URL del file di origine |
| destination* |Percorso del file di destinazione |Percorso del file di destinazione, incluso il nome del file di destinazione |
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
| folderId* |Cartella |Seleziona una cartella |

* indica che la proprietà è obbligatoria

### <a name="list-files-in-folder"></a>Elenca i file nella cartella
Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |Cartella |Seleziona una cartella |

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
Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice. 

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
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


