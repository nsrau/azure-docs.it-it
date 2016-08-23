<properties
pageTitle="Dropbox | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Connettersi a Dropbox per gestire i file. In Dropbox è possibile eseguire diverse azioni, ad esempio caricare, aggiornare, ottenere ed eliminare file."
services="logic-apps"	
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
ms.date="07/15/2016"
ms.author="deonhe"/>

# Introduzione al connettore Dropbox

Connettersi a Dropbox per gestire i file. In Dropbox è possibile eseguire diverse azioni, ad esempio caricare, aggiornare, ottenere ed eliminare file.

Per usare [qualsiasi connettore](./apis-list.md) è prima necessario creare un'app per la logica. Per iniziare, [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a Dropbox

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a Dropbox creare prima di tutto una *connessione* a Dropbox. Per creare una connessione è necessario indicare le credenziali usate normalmente per accedere al servizio a cui connettersi. Nell'esempio relativo a Dropbox saranno quindi necessarie le credenziali dell'account di Dropbox per creare la connessione a Dropbox. [Altre informazioni sulle connessioni]()

### Creare una connessione a Dropbox

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Dropbox](../../includes/connectors-create-api-dropbox.md)]

## Usare un trigger Dropbox

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

In questo esempio si userà il trigger **Quando viene creato un file**. Con questo trigger viene chiamata l'azione **Ottieni contenuto file in base al percorso** di Dropbox.

1. Immettere *dropbox* nella casella di ricerca della finestra di progettazione di app per la logica, quindi selezionare il trigger **Dropbox - Quando viene creato un file**.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)
  
2. Selezionare la cartella in cui tenere traccia della creazione di file. Selezionare... (identificato nella casella rossa) e passare alla cartella che si vuole selezionare per l'input del trigger.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)

## Usare un'azione Dropbox

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ora che il trigger è stato aggiunto, seguire questi passaggi per aggiungere un'azione che ottiene il contenuto del nuovo file.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione da eseguire quando viene creato un nuovo file.  
 
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)

2. Selezionare **Aggiungi un'azione**. Verrà aperta la casella di ricerca per cercare l'azione da eseguire.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)

3. Immettere *dropbox* per cercare le azioni correlate a Dropbox.

4. Selezionare **Dropbox - Ottieni contenuto file in base al percorso** come azione da eseguire quando viene creato un nuovo file nella cartella di Dropbox selezionata. Si aprirà il blocco di controllo azione. Verrà richiesto di autorizzare l'app per la logica ad accedere all'account di Dropbox, se non è stato fatto in precedenza.  

 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  

5. Selezionare... sul lato destro del controllo **Percorso file** e passare al percorso file da usare. In alternativa, usare il token **file path** per rendere più rapida la creazione di app per la logica.  
 ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  

6. Salvare il lavoro e creare un nuovo file in Dropbox per attivare il flusso di lavoro.

## Dettagli tecnici

Di seguito sono indicati i dettagli su trigger, azioni e risposte supportati dalla connessione:

## Trigger di Dropbox

Il connettore Dropbox ha i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[Quando un file viene creato](connectors-create-api-dropbox.md#when-a-file-is-created)|Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella.|
|[Quando un file viene modificato](connectors-create-api-dropbox.md#when-a-file-is-modified)|Questa operazione attiva un flusso quando viene modificato un file in una cartella.|

## Azioni di Dropbox

Il connettore Dropbox ha le azioni seguenti:

|Azione|Descrizione|
|--- | ---|
|[Ottenere i metadati del file](connectors-create-api-dropbox.md#get-file-metadata)|Questa operazione ottiene i metadati per un file.|
|[Aggiorna file](connectors-create-api-dropbox.md#update-file)|Questa operazione aggiorna un file.|
|[Elimina file](connectors-create-api-dropbox.md#delete-file)|Questa operazione elimina un file.|
|[Recupera metadati di file tramite percorso](connectors-create-api-dropbox.md#get-file-metadata-using-path)|Questa operazione ottiene i metadati di un file usando il percorso.|
|[Ottieni contenuto di file tramite percorso](connectors-create-api-dropbox.md#get-file-content-using-path)|Questa operazione ottiene il contenuto di un file usando il percorso.|
|[Ottieni contenuto di file](connectors-create-api-dropbox.md#get-file-content)|Questa operazione ottiene il contenuto di un file.|
|[Crea file](connectors-create-api-dropbox.md#create-file)|Questa operazione crea un file.|
|[Copia file](connectors-create-api-dropbox.md#copy-file)|Questa operazione copia un file in Dropbox.|
|[Elenca i file nella cartella](connectors-create-api-dropbox.md#list-files-in-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.|
|[Elenca i file nella cartella radice](connectors-create-api-dropbox.md#list-files-in-root-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice.|
|[Estrai archivio in una cartella](connectors-create-api-dropbox.md#extract-archive-to-folder)|Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.|

### Informazioni dettagliate sulle azioni

Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:


### Ottieni metadati file
Questa operazione ottiene i metadati per un file.

                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|
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
|---|---|
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
|---|---|
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
|---|---|
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
Questa operazione copia un file in Dropbox.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|URL di origine|URL del file di origine|
|destination*|Percorso del file di destinazione|Percorso del file di destinazione, incluso il nome del file di destinazione|
|overwrite|Sovrascrivere?|Sovrascrive il file di destinazione se è impostata su 'true'|

* indica che la proprietà è obbligatoria

#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|
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




### Quando un file viene creato
Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderId*|Cartella|Seleziona una cartella|

* indica che la proprietà è obbligatoria




### Quando un file viene modificato
Questa operazione attiva un flusso quando viene modificato un file in una cartella.


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
|---|---|
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
|---|---|
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




### Estrai archivio in una cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|Percorso del file di archivio di origine|Percorso del file di archivio|
|destination*|Percorso della cartella di destinazione|Percorso in cui estrarre il contenuto dell'archivio|
|overwrite|Sovrascrivere?|Sovrascrive i file di destinazione se è impostata su 'true'|

* indica che la proprietà è obbligatoria



#### Dettagli output

BlobMetadata


| Nome proprietà | Tipo di dati |
|---|---|
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

| Nome | Descrizione |
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

<!---HONumber=AcomDC_0803_2016-->
