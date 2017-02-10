---
title: Informazioni su come usare il connettore SharePoint Online nelle app per la logica | Documentazione Microsoft
description: Creare app per la logica con il connettore SharePoint Online per la gestione degli elenchi SharePoint.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 3ecf3e30fe2fcb9d6473d7eda450536cddfa97f4


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Introduzione al connettore SharePoint Online
Usare il connettore SharePoint Online per gestire gli elenchi SharePoint.  

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sharepoint-online"></a>Connettersi a SharePoint Online
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-sharepoint-online"></a>Creare una connessione a SharePoint Online
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>Usare un trigger di SharePoint Online
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>Usare un'azione di SharePoint Online
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## <a name="sharepoint-online-triggers"></a>Trigger di SharePoint Online
SharePoint supporta i trigger seguenti:  

| Trigger | Description |
| --- | --- |
| [Quando viene creato un file](connectors-create-api-sharepointonline.md#when-a-file-is-created) |Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella di SharePoint. |
| [Quando viene modificato un file](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |Questa operazione attiva un flusso quando viene modificato un file in una cartella di SharePoint. |
| [Quando viene creato un nuovo elemento](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |Questa operazione attiva un flusso quando viene creato un nuovo elemento in un elenco SharePoint. |
| [Quando viene modificato un elemento esistente](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |Questa operazione attiva un flusso quando viene modificato un elemento esistente in un elenco SharePoint. |

## <a name="sharepoint-online-actions"></a>Azioni di SharePoint Online
SharePoint supporta le azioni seguenti:

| Azione | Description |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-sharepointonline.md#get-file-metadata) |Questa operazione recupera i metadati del file tramite l'ID del file. |
| [Aggiorna file](connectors-create-api-sharepointonline.md#update-file) |Questa operazione aggiorna il contenuto del file. |
| [Elimina file](connectors-create-api-sharepointonline.md#delete-file) |Questa operazione elimina un file. |
| [Ottieni metadati file in base al percorso](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |Questa operazione recupera i metadati del file tramite il percorso del file. |
| [Ottieni contenuto file in base al percorso](connectors-create-api-sharepointonline.md#get-file-content-using-path) |Questa operazione recupera il contenuto del file tramite il percorso del file. |
| [Ottieni contenuto file](connectors-create-api-sharepointonline.md#get-file-content) |Questa operazione recupera il contenuto del file tramite l'ID del file. |
| [Crea file](connectors-create-api-sharepointonline.md#create-file) |Questa operazione carica un file su un sito di SharePoint. |
| [Copia file](connectors-create-api-sharepointonline.md#copy-file) |Questa operazione copia un file su un sito di SharePoint. |
| [Elenca cartella](connectors-create-api-sharepointonline.md#list-folder) |Questa operazione recupera i file contenuti in una cartella di SharePoint. |
| [Elenca cartella radice](connectors-create-api-sharepointonline.md#list-root-folder) |Questa operazione recupera i file contenuti nella cartella radice di SharePoint. |
| [Estrai cartella](connectors-create-api-sharepointonline.md#extract-folder) |Questa operazione estrae un file di archivio in una cartella di SharePoint (ad esempio un file ZIP). |
| [Ottieni elementi](connectors-create-api-sharepointonline.md#get-items) |Questa operazione recupera gli elementi da un elenco SharePoint. |
| [Crea elemento](connectors-create-api-sharepointonline.md#create-item) |Questa operazione crea un nuovo elemento in un elenco SharePoint. |
| [Ottieni elemento](connectors-create-api-sharepointonline.md#get-item) |Questa operazione recupera un singolo elemento mediante il relativo ID da un elenco SharePoint. |
| [Elimina elemento](connectors-create-api-sharepointonline.md#delete-item) |Questa operazione elimina un elemento da un elenco SharePoint. |
| [Aggiorna elemento](connectors-create-api-sharepointonline.md#update-item) |Questa operazione aggiorna un elemento in un elenco SharePoint. |
| [Ottieni valori entità](connectors-create-api-sharepointonline.md#get-entity-values) |Questa operazione recupera i valori possibili per un'entità SharePoint. |
| [Ottieni elenchi](connectors-create-api-sharepointonline.md#get-lists) |Questa operazione recupera elenchi SharePoint da un sito. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:

### <a name="get-file-metadata"></a>Ottenere i metadati del file
Questa operazione recupera i metadati del file tramite l'ID del file. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

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

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |
| body* |File Content |Contenuto del file |

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

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Questa operazione recupera i metadati del file tramite il percorso del file. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
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
Questa operazione recupera il contenuto del file tramite il percorso del file. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| path* |Percorso del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="get-file-content"></a>Recupera contenuto di file
Questa operazione recupera il contenuto del file tramite l'ID del file. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### <a name="create-file"></a>Crea file
Questa operazione carica un file su un sito di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| folderPath* |Folder Path |Selezionare un file |
| name* |Nome file |Nome del file |
| body* |File Content |Contenuto del file |

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
Questa operazione copia un file su un sito di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| source* |Percorso file origine |Percorso del file di origine |
| destination* |Percorso del file di destinazione |Percorso del file di destinazione |
| overwrite |Flag di sovrascrittura |Se sovrascrivere o meno il file di destinazione se esiste |

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

### <a name="when-a-file-is-created"></a>Quando un file viene creato
Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint |
| folderId* |ID cartella |Seleziona una cartella |

* indica che la proprietà è obbligatoria

### <a name="when-a-file-is-modified"></a>Quando un file viene modificato
Questa operazione attiva un flusso quando viene modificato un file in una cartella di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint |
| folderId* |ID cartella |Seleziona una cartella |

* indica che la proprietà è obbligatoria

### <a name="list-folder"></a>Elenca cartella
Questa operazione recupera i file contenuti in una cartella di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Identificatore univoco della cartella |

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

### <a name="list-root-folder"></a>Elenca cartella radice
Questa operazione recupera i file contenuti nella cartella radice di SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |

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

### <a name="extract-folder"></a>Estrai cartella
Questa operazione estrae un file di archivio in una cartella di SharePoint (ad esempio un file ZIP). 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| source* |Percorso file origine |Percorso del file di origine |
| destination* |Percorso cartella di destinazione |Percorso della cartella di destinazione |
| overwrite |Flag di sovrascrittura |Se sovrascrivere o meno il file di destinazione se esiste |

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

### <a name="when-a-new-item-is-created"></a>Quando viene creato un nuovo elemento
Questa operazione attiva un flusso quando viene creato un nuovo elemento in un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="when-an-existing-item-is-modified"></a>Quando un elemento esistente viene modificato
Questa operazione attiva un flusso quando viene modificato un elemento esistente in un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="get-items"></a>Ottieni elementi
Questa operazione recupera gli elementi da un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="create-item"></a>Creare elemento
Questa operazione crea un nuovo elemento in un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| item* |Item |Elemento da creare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |stringa |

### <a name="get-item"></a>Ottieni elemento
Questa operazione recupera un singolo elemento mediante il relativo ID da un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da recuperare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |stringa |

### <a name="delete-item"></a>Elimina elemento
Questa operazione elimina un elemento da un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da eliminare |

* indica che la proprietà è obbligatoria

### <a name="update-item"></a>Aggiorna elemento
Questa operazione aggiorna un elemento in un elenco SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da aggiornare |
| item* |Item |Elemento con le proprietà modificate |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>Ottieni valori di entità
Questa operazione recupera i valori possibili per un'entità SharePoint. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |URL del sito di SharePoint |URL del sito di SharePoint |
| table* |Nome tabella |Nome tabella |
| id* |ID entità |ID entità |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
### <a name="get-lists"></a>Ottieni elenchi
Questa operazione recupera elenchi SharePoint da un sito. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TablesList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

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


