---
title: Informazioni su come usare il connettore SharePoint Online nelle app per la logica | Microsoft Docs
description: Creare app per la logica con il connettore SharePoint Online per la gestione degli elenchi SharePoint.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe

---
# Introduzione al connettore SharePoint Online
Usare il connettore SharePoint Online per gestire gli elenchi SharePoint.

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a SharePoint Online
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione a SharePoint Online
> [!INCLUDE [Passaggi per creare una connessione a SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## Usare un trigger di SharePoint Online
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Passaggi per creare un trigger di SharePoint Online](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## Usare un'azione di SharePoint Online
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Passaggi per creare un'azione di SharePoint Online](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger di SharePoint Online
SharePoint supporta i trigger seguenti:

| Trigger | Descrizione |
| --- | --- |
| [Quando un file viene creato](connectors-create-api-sharepointonline.md#when-a-file-is-created) |Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella di SharePoint. |
| [Quando un file viene modificato](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |Questa operazione attiva un flusso quando viene modificato un file in una cartella di SharePoint. |
| [Quando viene creato un nuovo elemento](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |Questa operazione attiva un flusso quando viene creato un nuovo elemento in un elenco SharePoint. |
| [Quando un elemento esistente viene modificato](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |Questa operazione attiva un flusso quando viene modificato un elemento esistente in un elenco SharePoint. |

## Azioni di SharePoint Online
SharePoint supporta le azioni seguenti:

| Azione | Descrizione |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-sharepointonline.md#get-file-metadata) |Questa operazione recupera i metadati del file tramite l'ID del file. |
| [Aggiorna file](connectors-create-api-sharepointonline.md#update-file) |Questa operazione aggiorna il contenuto del file. |
| [Elimina file](connectors-create-api-sharepointonline.md#delete-file) |Questa operazione elimina un file. |
| [Recupera metadati di file tramite percorso](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |Questa operazione recupera i metadati del file tramite il percorso del file. |
| [Ottieni contenuto di file tramite percorso](connectors-create-api-sharepointonline.md#get-file-content-using-path) |Questa operazione recupera il contenuto del file tramite il percorso del file. |
| [Ottieni contenuto di file](connectors-create-api-sharepointonline.md#get-file-content) |Questa operazione recupera il contenuto del file tramite l'ID del file. |
| [Crea file](connectors-create-api-sharepointonline.md#create-file) |Questa operazione carica un file su un sito di SharePoint. |
| [Copia file](connectors-create-api-sharepointonline.md#copy-file) |Questa operazione copia un file su un sito di SharePoint. |
| [Elenca cartella](connectors-create-api-sharepointonline.md#list-folder) |Questa operazione recupera i file contenuti in una cartella di SharePoint. |
| [Elenca cartella radice](connectors-create-api-sharepointonline.md#list-root-folder) |Questa operazione recupera i file contenuti nella cartella radice di SharePoint. |
| [Estrai cartella](connectors-create-api-sharepointonline.md#extract-folder) |Questa operazione estrae un file di archivio in una cartella di SharePoint (ad esempio un file ZIP). |
| [Ottieni elementi](connectors-create-api-sharepointonline.md#get-items) |Questa operazione recupera gli elementi da un elenco SharePoint. |
| [Creare elemento](connectors-create-api-sharepointonline.md#create-item) |Questa operazione crea un nuovo elemento in un elenco SharePoint. |
| [Ottieni elemento](connectors-create-api-sharepointonline.md#get-item) |Questa operazione recupera un singolo elemento mediante il relativo ID da un elenco SharePoint. |
| [Elimina elemento](connectors-create-api-sharepointonline.md#delete-item) |Questa operazione elimina un elemento da un elenco SharePoint. |
| [Aggiorna elemento](connectors-create-api-sharepointonline.md#update-item) |Questa operazione aggiorna un elemento in un elenco SharePoint. |
| [Ottieni valori di entità](connectors-create-api-sharepointonline.md#get-entity-values) |Questa operazione recupera i valori possibili per un'entità SharePoint. |
| [Ottieni elenchi](connectors-create-api-sharepointonline.md#get-lists) |Questa operazione recupera elenchi SharePoint da un sito. |

### Informazioni dettagliate sulle azioni
Di seguito sono indicati i dettagli per le azioni e i trigger di questo connettore con le relative risposte:

### Ottieni metadati file
Questa operazione recupera i metadati del file tramite l'ID del file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |stringa |

### Aggiorna file
Questa operazione aggiorna il contenuto del file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |
| body* |File Content |Contenuto del file |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Elimina file
Questa operazione elimina un file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### Recupera metadati di file tramite percorso
Questa operazione recupera i metadati del file tramite il percorso del file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| path* |Percorso del file |Selezionare un file |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Ottieni contenuto di file tramite percorso
Questa operazione recupera il contenuto del file tramite il percorso del file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| path* |Percorso del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### Ottieni contenuto di file
Questa operazione recupera il contenuto del file tramite l'ID del file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Selezionare un file |

* indica che la proprietà è obbligatoria

### Crea file
Questa operazione carica un file su un sito di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| folderPath* |Folder Path |Selezionare un file |
| name* |Nome file |Nome del file |
| body* |File Content |Contenuto del file |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Copia file
Questa operazione copia un file su un sito di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| source* |Percorso file origine |Percorso del file di origine |
| destination* |Percorso file di destinazione |Percorso del file di destinazione |
| overwrite |Flag di sovrascrittura |Se sovrascrivere o meno il file di destinazione se esiste |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Quando un file viene creato
Questa operazione attiva un flusso quando viene creato un nuovo file in una cartella di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint |
| folderId* |ID cartella |Seleziona una cartella |

* indica che la proprietà è obbligatoria

### Quando un file viene modificato
Questa operazione attiva un flusso quando viene modificato un file in una cartella di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint |
| folderId* |ID cartella |Seleziona una cartella |

* indica che la proprietà è obbligatoria

### Elenca cartella
Questa operazione recupera i file contenuti in una cartella di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| id* |Identificatore del file |Identificatore univoco della cartella |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Elenca cartella radice
Questa operazione recupera i file contenuti nella cartella radice di SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Estrai cartella
Questa operazione estrae un file di archivio in una cartella di SharePoint (ad esempio un file ZIP).

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| source* |Percorso file origine |Percorso del file di origine |
| destination* |Percorso cartella di destinazione |Percorso della cartella di destinazione |
| overwrite |Flag di sovrascrittura |Se sovrascrivere o meno il file di destinazione se esiste |

* indica che la proprietà è obbligatoria

#### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

### Quando viene creato un nuovo elemento
Questa operazione attiva un flusso quando viene creato un nuovo elemento in un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### Quando un elemento esistente viene modificato
Questa operazione attiva un flusso quando viene modificato un elemento esistente in un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### Ottieni elementi
Questa operazione recupera gli elementi da un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### Dettagli output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### Creare elemento
Questa operazione crea un nuovo elemento in un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| item* |Item |Elemento da creare |

* indica che la proprietà è obbligatoria

#### Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### Ottieni elemento
Questa operazione recupera un singolo elemento mediante il relativo ID da un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da recuperare |

* indica che la proprietà è obbligatoria

#### Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### Elimina elemento
Questa operazione elimina un elemento da un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da eliminare |

* indica che la proprietà è obbligatoria

### Aggiorna elemento
Questa operazione aggiorna un elemento in un elenco SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |
| table* |Nome elenco |Nome dell'elenco SharePoint |
| id* |ID |Identificatore univoco dell'elemento da aggiornare |
| item* |Item |Elemento con le proprietà modificate |

* indica che la proprietà è obbligatoria

#### Dettagli output
Item

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### Ottieni valori di entità
Questa operazione recupera i valori possibili per un'entità SharePoint.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |URL del sito di SharePoint |URL del sito di SharePoint |
| table* |Nome tabella |Nome tabella |
| id* |ID entità |ID entità |

* indica che la proprietà è obbligatoria

#### Dettagli output
### Ottieni elenchi
Questa operazione recupera elenchi SharePoint da un sito.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| dataset* |Site URL |URL del sito di SharePoint (ad esempio: http://contoso.sharepoint.com/sites/mysite) |

* indica che la proprietà è obbligatoria

#### Dettagli output
TablesList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

## Risposte HTTP
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

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
