<properties
pageTitle="Usare l'API di SharePoint Online nelle app per la logica o di PowerApps| Microsoft Azure"
description="Introduzione all'uso dell'API di SharePoint Online del Servizio app di Azure nelle app per la logica e nelle app di PowerApps."
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/26/2016"
ms.author="deonhe"/>

# Introduzione all'API di SharePoint Online

Il provider di connessione di SharePoint fornisce un'API da usare con gli elenchi di SharePoint.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con SharePoint è possibile:

* Compilare app per la logica
* Compilare app di PowerApps  

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

L'API di SharePoint include trigger e può essere usata come azione. Tutte le API supportano i dati nei formati JSON e XML.

Nell'API di SharePoint sono disponibili le azioni e/o i trigger seguenti:

### Azioni di SharePoint
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|GetFileMetadata|Usata per ottenere un file di metadati nella raccolta documenti|
|UpdateFile|Usata per aggiornare un file nella raccolta documenti|
|DeleteFile|Usata per eliminare un file dalla raccolta documenti|
|GetFileMetadataByPath|Usata per ottenere un file di metadati nella raccolta documenti|
|GetFileContentByPath|Usata per ottenere un file nella raccolta documenti|
|GetFileContent|Usata per ottenere un file nella raccolta documenti|
|CreateFile|Usata per caricare un file nella raccolta documenti|
|CopyFile|Usata per copiare un file nella raccolta documenti|
|ExtractFolderV2|Usata per estrarre una cartella nella raccolta documenti|
|PostItem|Crea un nuovo elemento in un elenco di SharePoint|
|GetItem|Recupera un singolo elemento da un elenco di SharePoint|
|DeleteItem|Elimina un elemento da un elenco di SharePoint|
|PatchItem|Aggiorna un elemento in un elenco di SharePoint|
### Trigger di SharePoint
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|OnNewFile|Quando un nuovo file viene creato in una cartella di SharePoint, attiva un flusso|
|OnUpdatedFile|Quando un file viene modificato in una cartella di SharePoint, attiva un flusso|
|GetOnNewItems|Quando un nuovo elemento viene creato in un elenco di SharePoint|
|GetOnUpdatedItems|Quando un elemento esistente viene modificato in un elenco di SharePoint|


## Creare una connessione a SharePoint
Per usare l'API SharePoint, creare prima una **connessione**, quindi indicare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di SharePoint|

Per connettersi a **SharePoint Online**, è necessario indicare la propria identità (nome utente e password, credenziali smart card e così via) in SharePoint Online. Dopo l'autenticazione, è possibile usare l'API di SharePoint Online nella propria app per la logica.

Durante la progettazione dell'app per la logica, seguire questa procedura per accedere a SharePoint e creare la **connessione** da usare nell'app per la logica:

1. Nella casella di ricerca digitare SharePoint e attendere che la ricerca restituisca tutte le voci con SharePoint nel nome: ![Configurare SharePoint][1]  
2. Selezionare **SharePoint Online - When a file is created**   
3. Selezionare **Sign in to SharePoint Online**: ![Configurare SharePoint][2]    
4. Specificare le credenziali di SharePoint per accedere ed eseguire l'autenticazione con SharePoint ![Configurare SharePoint][3]     
5. Al termine dell'autenticazione si verrà reindirizzati all'app per la logica per completarla usando i dati di configurazione della finestra di dialogo **When a file is created** di SharePoint. ![Configurare SharePoint][4]  
6. È quindi possibile aggiungere altri trigger e azioni necessari per completare l'app per la logica.   
7. Salvare il lavoro selezionando **Salva** nella barra dei menu visualizzata in alto.  

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica, app di PowerApps o in entrambe.

## Informazioni di riferimento sull'API REST di SharePoint
#### Questa documentazione è relativa alla versione 1.0


### Usata per ottenere un file di metadati nella raccolta documenti
**```GET: /datasets/{dataset}/files/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|nessuno|Identificatore univoco del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per aggiornare un file nella raccolta documenti
**```PUT: /datasets/{dataset}/files/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|nessuno|Identificatore univoco del file|
|body| |sì|body|nessuno|Contenuto del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per eliminare un file dalla raccolta documenti
**```DELETE: /datasets/{dataset}/files/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|nessuno|Identificatore univoco del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per ottenere un file di metadati nella raccolta documenti
**```GET: /datasets/{dataset}/GetFileByPath```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|query|nessuno|Percorso del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per ottenere un file nella raccolta documenti
**```GET: /datasets/{dataset}/GetFileContentByPath```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|query|nessuno|Percorso del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per ottenere un file nella raccolta documenti
**```GET: /datasets/{dataset}/files/{id}/content```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|nessuno|Identificatore univoco del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per caricare un file nella raccolta documenti
**```POST: /datasets/{dataset}/files```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|folderPath|string|yes|query|nessuno|Percorso della cartella.|
|name|string|yes|query|nessuno|Nome del file|
|body| |sì|body|nessuno|Contenuto del file|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per copiare un file nella raccolta documenti
**```POST: /datasets/{dataset}/copyFile```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|source|string|yes|query|nessuno|Percorso del file di origine|
|destination|string|yes|query|nessuno|Percorso del file di destinazione|
|overwrite|boolean|no|query|false|Sovrascrivere o non sovrascrivere un file esistente|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Quando un nuovo file viene creato in una cartella di SharePoint, attiva un flusso
**```GET: /datasets/{dataset}/triggers/onnewfile```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint|
|folderId|string|yes|query|nessuno|Identificatore univoco della cartella in SharePoint|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Quando un file viene modificato in una cartella di SharePoint, attiva un flusso
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint|
|folderId|string|yes|query|nessuno|Identificatore univoco della cartella in SharePoint|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Usata per estrarre una cartella nella raccolta documenti
**```POST: /datasets/{dataset}/extractFolderV2```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, Ad esempio http://contoso.sharepoint.com/sites/mysite|
|source|string|yes|query|nessuno|Percorso del file di origine|
|destination|string|yes|query|nessuno|Percorso della cartella di destinazione|
|overwrite|boolean|no|query|false|Sovrascrivere o non sovrascrivere un file esistente|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Quando un nuovo elemento viene creato in un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Quando un elemento esistente viene modificato in un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Crea un nuovo elemento in un elenco di SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|item| |yes|body|nessuno|Elemento da creare|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Recupera un singolo elemento da un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|id|integer|sì|path|nessuno|Identificatore univoco dell'elemento da recuperare|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Elimina un elemento da un elenco di SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|id|integer|sì|path|nessuno|Identificatore univoco dell'elemento da eliminare|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



### Aggiorna un elemento in un elenco di SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|nessuno|Nome dell'elenco SharePoint|
|id|integer|sì|path|nessuno|Identificatore univoco dell'elemento da aggiornare|
|item| |yes|body|nessuno|Elemento con le proprietà modificate|


### Ecco le risposte possibili:

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|
------



## Definizioni di oggetti: 

 **DataSetsMetadata**:

Proprietà obbligatorie per DataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|tabular|non definito|
|BLOB|non definito|



 **TabularDataSetsMetadata**:

Proprietà obbligatorie per TabularDataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|una sezione source|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Proprietà obbligatorie per BlobDataSetsMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|una sezione source|string|
|displayName|string|
|urlEncoding|string|



 **BlobMetadata**:

Proprietà obbligatorie per BlobMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
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



 **Object**:

Proprietà obbligatorie per Object:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|



 **TableMetadata**:

Proprietà obbligatorie per TableMetadata:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|non definito|



 **DataSetsList**:

Proprietà obbligatorie per DataSetsList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **DataSet**:

Proprietà obbligatorie per DataSet:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Nome|string|
|DisplayName|string|



 **Tabella**:

Proprietà obbligatorie per Table:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|Nome|string|
|DisplayName|string|



 **Item**:

Proprietà obbligatorie per Item:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|ItemInternalId|string|



 **ItemsList**:

Proprietà obbligatorie per ItemList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|



 **TablesList**:

Proprietà obbligatorie per TablesList:


Nessuna delle proprietà è obbligatoria.


**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|value|array|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) [Creare un'app di PowerApps](../power-apps/powerapps-get-started-azure-portal.md)

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0413_2016-->