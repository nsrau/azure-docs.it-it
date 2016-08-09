<properties
pageTitle="Informazioni su come usare il connettore FTP nelle app per la logica | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi a un server FTP per gestire i file. Nel server FTP è possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file."
services="app-servicelogic"	
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
ms.date="07/22/2016"
ms.author="deonhe"/>

# Introduzione al connettore FTP

Usare il connettore FTP per monitorare, gestire e creare file in un server FTP.

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a FTP

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione a FTP

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a FTP](../../includes/connectors-create-api-ftp.md)]

## Usare un trigger FTP

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.IMPORTANT]Il connettore FTP richiede un server FTP che sia accessibile da Internet e sia configurato per operare con la modalità passiva. Il connettore FTP, inoltre, **non è compatibile con FTPS implicito (FTP su SSL)**. Il connettore FTP supporta solo FTPS esplicito (FTP su SSL).

Questo esempio illustrerà come usare il trigger **FTP - When a file is added or modified** (FTP - Quando un file viene aggiunto o modificato) per avviare un flusso di lavoro dell'app per la logica quando viene aggiunto o modificato un file in un server FTP. In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella FTP per nuovi file di ordini dei clienti. È quindi possibile usare un'azione connettore FTP, come **Get file content** (Ottieni contenuto di file) per recuperare il contenuto dell'ordine a scopo di elaborazione successiva e di archiviazione nel database degli ordini.

1. Immettere *sftp* nella casella di ricerca della finestra di progettazione dell'app per la logica, quindi selezionare il trigger **FTP - When a file is added or modified** (FTP - Quando un file viene aggiunto o modificato) ![Immagine di trigger FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png) Si apre il controllo **When a file is added or modified** (Quando un file viene aggiunto o modificato) ![Immagine di trigger FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)
- Selezionare **...** sul lato destro del controllo. Si apre il controllo di selezione della cartella ![Immagine di trigger FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)
- Selezionare **>** (freccia destra) e individuare la cartella da monitorare per rilevare i file nuovi o modificati. Selezionare la cartella e notare che ora è visualizzata nel controllo **Folder** (Cartella). ![Immagine di trigger FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)


A questo punto, l’app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro quando un file viene modificato o creato nella cartella FTP specificata.

>[AZURE.NOTE]Affinché sia funzionale, l’app per la logica deve contenere almeno un trigger e un’azione. Seguire i passaggi nella sezione successiva per aggiungere un'azione.



## Usare un'azione FTP

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Dopo aver aggiunto un trigger, seguire questi passaggi per aggiungere un'azione che otterrà il contenuto del file nuovo o modificato individuato dal trigger.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che recupera il contenuto del file sul server FTP
- Selezionare il collegamento **Aggiungi un’azione**. ![Immagine di azione FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)
- Immettere *FTP* per cercare tutte le azioni correlate a FTP.
- Selezionare **FTP - Get file content** (FTP - Ottieni contenuto di file) come azione da eseguire quando viene individuato un file nuovo o modificato nella cartella FTP. ![Immagine di azione FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png) Si apre il controllo **Get file content** (Ottieni contenuto di file). **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account sul server FTP, se non lo si è già fatto in precedenza. ![Immagine di azione FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)
- Selezionare il controllo **File** (lo spazio bianco sotto **FILE***). In questo caso è possibile usare le varie proprietà dal file nuovo o modificato individuato sul server FTP.
- Selezionare l'opzione **File content** (Contenuto del file). ![Immagine di azione FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)
-  Il controllo viene aggiornato e indica che l'azione **FTP - Get file content** (FTP - Ottieni contenuto di file) recupererà il *contenuto del file* nuovo o modificato sul server FTP. ![Immagine di azione FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)
- Salvare il lavoro e poi aggiungere un file nella cartella FTP per testare il flusso di lavoro.

A questo punto l'app per la logica è stata configurata con un trigger per monitorare una cartella su un server FTP e avviare il flusso di lavoro quando viene individuato un nuovo file o un file modificato sul server FTP stesso.

L'app per la logica è stata configurata con un'azione per ottenere il contenuto del file nuovo o modificato.

È ora possibile aggiungere un'altra azione, come [SQL Server - Insert row](./connectors-create-api-sqlazure.md#insert-row) (SQL Server - Inserisci riga), per inserire il contenuto del file nuovo o modificato in una tabella di database SQL.

## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger FTP

FTP supporta i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[When a file is added or modified](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) (Quando un file viene aggiunto o modificato)|Questa operazione attiva un flusso quando viene aggiunto o modificato un file in una cartella.|


## Azioni FTP

FTP supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Ottenere i metadati del file](connectors-create-api-ftp.md#get-file-metadata)|Questa operazione ottiene i metadati per un file.|
|[Aggiorna file](connectors-create-api-ftp.md#update-file)|Questa operazione aggiorna un file.|
|[Elimina file](connectors-create-api-ftp.md#delete-file)|Questa operazione elimina un file.|
|[Recupera metadati di file tramite percorso](connectors-create-api-ftp.md#get-file-metadata-using-path)|Questa operazione ottiene i metadati di un file usando il percorso.|
|[Ottieni contenuto di file tramite percorso](connectors-create-api-ftp.md#get-file-content-using-path)|Questa operazione ottiene il contenuto di un file usando il percorso.|
|[Ottieni contenuto di file](connectors-create-api-ftp.md#get-file-content)|Questa operazione ottiene il contenuto di un file.|
|[Crea file](connectors-create-api-ftp.md#create-file)|Questa operazione crea un file.|
|[Copia file](connectors-create-api-ftp.md#copy-file)|Questa operazione copia un file su un server FTP.|
|[Elenca i file nella cartella](connectors-create-api-ftp.md#list-files-in-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.|
|[Elenca i file nella cartella radice](connectors-create-api-ftp.md#list-files-in-root-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice.|
|[Estrai cartella](connectors-create-api-ftp.md#extract-folder)|Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.|
### Informazioni dettagliate sulle azioni

Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:



### Ottieni metadati file
Questa operazione ottiene i metadati per un file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

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
Questa operazione aggiorna un file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|
|body*|Contenuto del file|Contenuto del file|

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
|id*|File|Selezionare un file|

* indica che la proprietà è obbligatoria




### Recupera metadati di file tramite percorso
Questa operazione ottiene i metadati di un file usando il percorso.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Selezionare un file|

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
Questa operazione ottiene il contenuto di un file usando il percorso.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Selezionare un file|

* indica che la proprietà è obbligatoria




### Ottieni contenuto di file
Questa operazione ottiene il contenuto di un file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

* indica che la proprietà è obbligatoria




### Crea file
Questa operazione crea un file.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderPath*|Percorso della cartella|Seleziona una cartella|
|name*|Nome file|Nome del file|
|body*|Contenuto del file|Contenuto del file|

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




### Copia file
Questa operazione copia un file su un server FTP.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|URL di origine|URL del file di origine|
|destination*|Percorso del file di destinazione|Percorso del file di destinazione, incluso il nome del file di destinazione|
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
|folderId*|Cartella|Seleziona una cartella|

* indica che la proprietà è obbligatoria




### Elenca i file nella cartella
Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|Cartella|Seleziona una cartella|

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
Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice.


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

<!---HONumber=AcomDC_0727_2016-->