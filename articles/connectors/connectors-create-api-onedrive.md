---
title: Aggiungere il connettore OneDrive nelle app per la logica | Documentazione Microsoft
description: Panoramica del connettore OneDrive con i parametri dell&quot;API REST.
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 60c1d307ede5d66f8e92b048581148d6c7112dac
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-onedrive-connector"></a>Introduzione al connettore OneDrive
Connettersi a OneDrive per gestire i file, ad esempio, caricare, recuperare ed eliminare i file e altro ancora. 

Con OneDrive è possibile: 

* Creare un flusso di lavoro mediante l'archiviazione di file in OneDrive o aggiornare i file esistenti in OneDrive. 
* Usare trigger per avviare il flusso di lavoro quando un file viene creato o aggiornato in OneDrive.
* Usare le azioni per creare un file, eliminarlo e così via. Ad esempio, creare un nuovo file in OneDrive (azione) quando viene ricevuto un nuovo messaggio di posta elettronica di Office 365 con un allegato (trigger).

Questo argomento illustra come usare il connettore OneDrive in un'app per la logica ed elenca i trigger e le azioni.

> [!NOTE]
> Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica. 
> 
> 

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Connettersi a OneDrive
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a OneDrive, è necessaria prima di tutto una *connessione* a OneDrive. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Quindi, per creare la connessione a OneDrive, immettere le credenziali dell'account OneDrive.

### <a name="create-the-connection"></a>Creare la connessione
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Usare un trigger
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Nell'app per la logica digitare "onedrive" per ottenere l'elenco dei trigger:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Selezionare **Quando viene modificato un file**. Se esiste già una connessione, selezionare il pulsante Mostra selezione per selezionare una cartella.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo argomento elenca i passaggi necessari. 
   
   > [!NOTE]
   > In questo esempio l'app per la logica viene eseguita quando viene aggiornato un file nella cartella scelta. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un messaggio di posta elettronica al proprio indirizzo. Ad esempio, aggiungere l'azione di Office 365 Outlook *Invia un messaggio di posta elettronica* per ricevere un messaggio di posta elettronica quando un file viene aggiornato. 
   > 
   > 
3. Selezionare il pulsante **Modifica** e impostare i valori **Frequenza** e **Intervallo**. Ad esempio, se si vuole che il trigger esegua il poll ogni 15 minuti, impostare **Frequenza** su **Minuto** e **Intervallo** su **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="use-an-action"></a>Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "onedrive" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. Nell'esempio scegliere **OneDrive - Crea file**. Se esiste già una connessione, selezionare il **percorso della cartella** in cui inserire il file, immettere il **nome del file** e scegliere il **contenuto del file** desiderato:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo argomento descrive queste proprietà. 
   
   > [!NOTE]
   > In questo esempio creiamo un nuovo file in una cartella di OneDrive. Per creare il file di OneDrive è possibile usare l'output di un altro trigger. Ad esempio aggiungere il trigger di Office 365 Outlook *All'arrivo di un nuovo messaggio di posta elettronica*. Quindi aggiungere l'azione di OneDrive *Crea file* che usa i campi Allegati e Content-Type in un ciclo ForEach per creare il nuovo file in OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="technical-details"></a>Dettagli tecnici
## <a name="triggers"></a>Trigger
| Trigger | Description |
| --- | --- |
| [Quando viene creato un file](connectors-create-api-onedrive.md#when-a-file-is-created) |Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella. |
| [Quando viene modificato un file](connectors-create-api-onedrive.md#when-a-file-is-modified) |Questa operazione attiva un flusso quando viene modificato un file in una cartella. |

## <a name="actions"></a>Azioni
| Azione | Description |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-onedrive.md#get-file-metadata) |Questa operazione ottiene i metadati per un file. |
| [Aggiorna file](connectors-create-api-onedrive.md#update-file) |Questa operazione aggiorna un file. |
| [Elimina file](connectors-create-api-onedrive.md#delete-file) |Questa operazione elimina un file. |
| [Ottieni metadati file in base al percorso](connectors-create-api-onedrive.md#get-file-metadata-using-path) |Questa operazione ottiene i metadati di un file usando il percorso. |
| [Ottieni contenuto file in base al percorso](connectors-create-api-onedrive.md#get-file-content-using-path) |Questa operazione ottiene il contenuto di un file usando il percorso. |
| [Ottieni contenuto file](connectors-create-api-onedrive.md#get-file-content) |Questa operazione ottiene il contenuto di un file. |
| [Crea file](connectors-create-api-onedrive.md#create-file) |Questa operazione crea un file. |
| [Copia file](connectors-create-api-onedrive.md#copy-file) |Questa operazione copia un file in OneDrive. |
| [Elenca file nella cartella](connectors-create-api-onedrive.md#list-files-in-folder) |Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella. |
| [Elenca file nella cartella radice](connectors-create-api-onedrive.md#list-files-in-root-folder) |Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice. |
| [Estrai archivio nella cartella](connectors-create-api-onedrive.md#extract-archive-to-folder) |Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
In questa sezione sono riportati i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.

#### <a name="get-file-metadata"></a>Ottenere i metadati del file
Questa operazione ottiene i metadati per un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="update-file"></a>Aggiorna file
Questa operazione aggiorna un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |
| body* |Contenuto del file |Contenuto del file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="delete-file"></a>Elimina file
Questa operazione elimina un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuna.

#### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Questa operazione ottiene i metadati di un file usando il percorso. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="get-file-content-using-path"></a>Recupera contenuto di file tramite percorso
Questa operazione ottiene il contenuto di un file usando il percorso. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuna.

#### <a name="get-file-content"></a>Recupera contenuto di file
Questa operazione ottiene il contenuto di un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |File |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuna.

#### <a name="create-file"></a>Crea file
Questa operazione crea un file. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderPath* |Percorso della cartella |Seleziona una cartella |
| name* |Nome file |Nome del file |
| body* |Contenuto del file |Contenuto del file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="copy-file"></a>Copia file
Questa operazione copia un file in OneDrive. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| source* |URL di origine |URL del file di origine |
| destination* |Percorso del file di destinazione |Percorso del file di destinazione, incluso il nome del file di destinazione |
| overwrite |Sovrascrivere? |Sovrascrive il file di destinazione se è impostata su 'true' |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="when-a-file-is-created"></a>Quando un file viene creato
Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderId* |Cartella |Seleziona una cartella |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuna.

#### <a name="when-a-file-is-modified"></a>Quando un file viene modificato
Questa operazione attiva un flusso quando viene modificato un file in una cartella. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| folderId* |Cartella |Seleziona una cartella |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuna.

#### <a name="list-files-in-folder"></a>Elenca i file nella cartella
Questa operazione ottiene l'elenco dei file e delle sottocartelle in una cartella.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| id* |Cartella |Seleziona una cartella |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="list-files-in-root-folder"></a>Elenca i file nella cartella radice
Questa operazione ottiene l'elenco dei file e delle sottocartelle nella cartella radice. 

Non sono disponibili parametri per questa chiamata.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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

#### <a name="extract-archive-to-folder"></a>Estrai archivio in una cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| source* |Percorso del file di archivio di origine |Percorso del file di archivio |
| destination* |Percorso della cartella di destinazione |Percorso in cui estrarre il contenuto dell'archivio |
| overwrite |Sovrascrivere? |Sovrascrive i file di destinazione se è impostata su 'true' |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
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
La tabella seguente riporta le risposte alle azioni e ai trigger e le descrizioni delle risposte:  

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).


