<properties
	pageTitle="Aggiungere il connettore OneDrive nelle app per la logica | Microsoft Azure"
	description="Panoramica del connettore OneDrive con i parametri dell'API REST."
	services="app-servicelogic"    
	documentationCenter=""     
	authors="MandiOhlinger"    
	manager="erikre"    
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/26/2016"
   ms.author="mandia"/>

# Introduzione al connettore OneDrive

Connettersi a OneDrive per gestire i file, ad esempio, caricare, recuperare ed eliminare i file e altro ancora.

Con OneDrive è possibile:

- Creare un flusso di lavoro mediante l'archiviazione di file in OneDrive o aggiornare i file esistenti in OneDrive.
- Usare trigger per avviare il flusso di lavoro quando un file viene creato o aggiornato in OneDrive.
- Usare le azioni per creare un file, eliminarlo e così via. Ad esempio, creare un nuovo file in OneDrive (azione) quando viene ricevuto un nuovo messaggio di posta elettronica di Office 365 con un allegato (trigger).

Questo argomento illustra come usare il connettore OneDrive in un'app per la logica ed elenca i trigger e le azioni.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) e l'articolo su come [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a OneDrive

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a OneDrive, è necessaria innanzitutto una *connessione* a OneDrive. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Pertanto, per creare la connessione a OneDrive, immettere le credenziali dell'account OneDrive.

### Creare la connessione

>[AZURE.INCLUDE [Passaggi per creare una connessione a OneDrive](../../includes/connectors-create-api-onedrive.md)]

## Usare un trigger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Nell'app per la logica digitare "onedrive" per ottenere l'elenco dei trigger:

	![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selezionare **When a file is modified** (Quando un file viene modificato). Se esiste già una connessione, selezionare il pulsante Mostra selezione per selezionare una cartella.

	![](./media/connectors-create-api-onedrive/sample-folder.png)

	Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo argomento elenca i passaggi necessari.

	> [AZURE.NOTE] In questo esempio l'app per la logica viene eseguita quando viene aggiornato un file nella cartella scelta. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un messaggio di posta elettronica al proprio indirizzo. Ad esempio, aggiungere l'azione di Office 365 Outlook *Send an email* (Invia messaggio di posta elettronica) che manda un messaggio di posta elettronica al proprio indirizzo quando un file viene aggiornato.

3. Selezionare il pulsante **Edit** (Modifica) e impostare i valori **Frequency** (Frequenza) e **Interval** (Intervallo). Ad esempio, se si desidera che il trigger esegua il polling ogni 15 minuti, impostare **Frequency** (Frequenza) su **Minute** (Minuto) e **Interval** (Intervallo) su **15**.

	![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.


## Usare un'azione

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.

	![](./media/connectors-create-api-onedrive/add-action.png)

2. Selezionare **Aggiungi un'azione**.

3. Nella casella di testo digitare "onedrive" per ottenere l'elenco di tutte le azioni disponibili.

	![](./media/connectors-create-api-onedrive/onedrive-actions.png)

4. Per questo esempio scegliere **OneDrive - Create file** (OneDrive - Crea file). Se esiste già una connessione, selezionare il **percorso della cartella** in cui inserire il file, immettere il **nome del file** e scegliere il **contenuto del file** desiderato:

	![](./media/connectors-create-api-onedrive/sample-action.png)

	Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo argomento descrive queste proprietà.

	> [AZURE.NOTE] In questo esempio creiamo un nuovo file in una cartella di OneDrive. Per creare il file di OneDrive è possibile usare l'output di un altro trigger. Ad esempio aggiungere il trigger di Office 365 Outlook *When a new email arrives* (Quando arriva un nuovo messaggio di posta elettronica). Quindi aggiungere l'azione di OneDrive *Create file* (Crea file) che usa i campi Attachments (Allegati) e Content-Type (Tipo di contenuto) in un ciclo ForEach per creare il nuovo file in OneDrive.
	> 
	> ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.


## Dettagli tecnici

## Trigger

|Trigger | Descrizione|
|--- | ---|
|[Quando un file viene creato](connectors-create-api-onedrive.md#when-a-file-is-created)|Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella.|
|[Quando un file viene modificato](connectors-create-api-onedrive.md#when-a-file-is-modified)|Questa operazione attiva un flusso quando viene modificato un file in una cartella.|


## Azioni

|Azione|Descrizione|
|--- | ---|
|[Ottenere i metadati del file](connectors-create-api-onedrive.md#get-file-metadata)|Questa operazione ottiene i metadati per un file.|
|[Aggiorna file](connectors-create-api-onedrive.md#update-file)|Questa operazione aggiorna un file.|
|[Elimina file](connectors-create-api-onedrive.md#delete-file)|Questa operazione elimina un file.|
|[Recupera metadati di file tramite percorso](connectors-create-api-onedrive.md#get-file-metadata-using-path)|Questa operazione ottiene i metadati di un file usando il percorso.|
|[Ottieni contenuto di file tramite percorso](connectors-create-api-onedrive.md#get-file-content-using-path)|Questa operazione ottiene il contenuto di un file usando il percorso.|
|[Ottieni contenuto di file](connectors-create-api-onedrive.md#get-file-content)|Questa operazione ottiene il contenuto di un file.|
|[Crea file](connectors-create-api-onedrive.md#create-file)|Questa operazione crea un file.|
|[Copia file](connectors-create-api-onedrive.md#copy-file)|Questa operazione copia un file in OneDrive.|
|[Elenca i file nella cartella](connectors-create-api-onedrive.md#list-files-in-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.|
|[Elenca i file nella cartella radice](connectors-create-api-onedrive.md#list-files-in-root-folder)|Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice.|
|[Estrai archivio in una cartella](connectors-create-api-onedrive.md#extract-archive-to-folder)|Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.|

### Informazioni dettagliate sulle azioni

In questa sezione sono riportati i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.


#### Ottenere i metadati del file
Questa operazione ottiene i metadati per un file.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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


#### Aggiorna file
Questa operazione aggiorna un file.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|
|body*|Contenuto del file|Contenuto del file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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


#### Elimina file
Questa operazione elimina un file.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Recupera metadati di file tramite percorso
Questa operazione ottiene i metadati di un file usando il percorso.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Selezionare un file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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


#### Ottieni contenuto di file tramite percorso
Questa operazione ottiene il contenuto di un file usando il percorso.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|path*|Percorso del file|Selezionare un file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.


#### Ottieni contenuto di file
Questa operazione ottiene il contenuto di un file.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|File|Selezionare un file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Crea file
Questa operazione crea un file.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderPath*|Percorso della cartella|Seleziona una cartella|
|name*|Nome file|Nome del file|
|body*|Contenuto del file|Contenuto del file|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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


#### Copia file
Questa operazione copia un file in OneDrive.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|URL di origine|URL del file di origine|
|destination*|Percorso del file di destinazione|Percorso del file di destinazione, incluso il nome del file di destinazione|
|overwrite|Sovrascrivere?|Sovrascrive il file di destinazione se è impostata su 'true'|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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


#### Quando un file viene creato
Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderId*|Cartella|Seleziona una cartella|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Quando un file viene modificato
Questa operazione attiva un flusso quando viene modificato un file in una cartella.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|folderId*|Cartella|Seleziona una cartella|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Elenca i file nella cartella
Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|id*|Cartella|Seleziona una cartella|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Dimensione|integer|
|MediaType|string||
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|


#### Elenca i file nella cartella radice
Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice.

Non sono disponibili parametri per questa chiamata.


##### Dettagli dell'output
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

#### Estrai archivio in una cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|source*|Percorso del file di archivio di origine|Percorso del file di archivio|
|destination*|Percorso della cartella di destinazione|Percorso in cui estrarre il contenuto dell'archivio|
|overwrite|Sovrascrivere?|Sovrascrive i file di destinazione se è impostata su 'true'|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
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

La tabella seguente riporta le risposte alle azioni e ai trigger e le descrizioni delle risposte:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell'[elenco delle API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->