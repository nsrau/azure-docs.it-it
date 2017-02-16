---
title: Usare il connettore SharePoint Online nelle app per la logica | Documentazione Microsoft
description: Introduzione all&quot;uso del connettore SharePoint Online nelle app per la logica
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Introduzione al connettore SharePoint Online
Il connettore SharePoint consente di utilizzare gli elenchi in SharePoint.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Informazioni su trigger e azioni
Il connettore SharePoint può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML. 

Nel connettore SharePoint sono disponibili le azioni e/o i trigger seguenti:

### <a name="sharepoint-actions"></a>Azioni di SharePoint
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| GetFileMetadata |Usata per recuperare un file di metadati nella raccolta documenti |
| UpdateFile |Usata per aggiornare un file nella raccolta documenti |
| DeleteFile |Usata per eliminare un file dalla raccolta documenti |
| GetFileMetadataByPath |Usata per recuperare un file di metadati nella raccolta documenti |
| GetFileContentByPath |Usata per recuperare un file nella raccolta documenti |
| GetFileContent |Usata per recuperare un file nella raccolta documenti |
| CreateFile |Usata per caricare un file nella raccolta documenti |
| CopyFile |Usata per copiare un file nella raccolta documenti |
| ExtractFolderV2 |Usata per estrarre una cartella nella raccolta documenti |
| PostItem |Crea un nuovo elemento in un elenco di SharePoint |
| GetItem |Recupera un singolo elemento da un elenco di SharePoint |
| DeleteItem |Elimina un elemento da un elenco di SharePoint |
| PatchItem |Aggiorna un elemento in un elenco di SharePoint |

### <a name="sharepoint-triggers"></a>Trigger di SharePoint
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| OnNewFile |Quando un nuovo file viene creato in una cartella di SharePoint, attiva un flusso |
| OnUpdatedFile |Quando un file viene modificato in una cartella di SharePoint, attiva un flusso |
| GetOnNewItems |Quando un nuovo elemento viene creato in un elenco di SharePoint |
| GetOnUpdatedItems |Quando un elemento esistente viene modificato in un elenco di SharePoint |

## <a name="create-a-connection-to-sharepoint"></a>Creare una connessione a SharePoint
Per usare il connettore SharePoint, creare prima una **connessione** , quindi indicare i dettagli di queste proprietà: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornire le credenziali di SharePoint |

Per connettersi a **SharePoint Online** è necessario specificare la propria identità, completa di nome utente, password, credenziali smart card e così via, in SharePoint Online. Dopo l'autenticazione, è possibile usare il connettore SharePoint Online nella propria app per la logica. 

Dall'applicazione di progettazione delle app per la logica, seguire questi passaggi per accedere a SharePoint e creare la connessione **connessione** da usare con l'app per la logica:

1. Immettere SharePoint nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con SharePoint nel nome:    
   ![Configurare SharePoint][1]  
2. Selezionare **SharePoint Online - Quando un file viene creato**   
3. Selezionare **Sign in to SharePoint Online** (Accedi a SharePoint Online):   
   ![Configurare SharePoint][2]    
4. Fornire le credenziali di SharePoint per accedere ed eseguire l'autenticazione con SharePoint    
   ![Configurare SharePoint][3]     
5. Al termine dell'autenticazione si verrà reindirizzati all'app per la logica per completarla tramite la configurazione della finestra di dialogo **Quando un file viene creato** di SharePoint.          
   ![Configurare SharePoint][4]  
6. È quindi possibile aggiungere altri trigger e azioni necessari per completare l'app per la logica.   
7. Salvare il lavoro selezionando **Salva** nella barra dei menu in alto.  

## <a name="sharepoint-rest-api-reference"></a>Informazioni di riferimento sull'API REST di SharePoint
#### <a name="this-documentation-is-for-version-10"></a>Questa documentazione è relativa alla versione 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Usata per recuperare un file di metadati nella raccolta documenti
**```GET: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| id |string |Sì |path |nessuno |Identificatore univoco del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>Usata per aggiornare un file nella raccolta documenti
**```PUT: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| id |string |Sì |path |nessuno |Identificatore univoco del file |
| body | |Sì |body |nessuno |Contenuto del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>Usata per eliminare un file dalla raccolta documenti
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| id |string |Sì |path |nessuno |Identificatore univoco del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Usata per recuperare un file di metadati nella raccolta documenti
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| path |string |yes |query |nessuno |Percorso del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Usata per recuperare un file nella raccolta documenti
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| path |string |yes |query |nessuno |Percorso del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Usata per recuperare un file nella raccolta documenti
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| id |string |Sì |path |nessuno |Identificatore univoco del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>Usata per caricare un file nella raccolta documenti
**```POST: /datasets/{dataset}/files```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |yes |query |nessuno |Percorso della cartella. |
| Nome |string |yes |query |nessuno |Nome del file |
| body | |Sì |body |nessuno |Contenuto del file |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>Usata per copiare un file nella raccolta documenti
**```POST: /datasets/{dataset}/copyFile```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| una sezione source |string |yes |query |nessuno |Percorso del file di origine |
| destination |string |yes |query |nessuno |Percorso del file di destinazione |
| overwrite |boolean |no |query |false |Sovrascrivere o non sovrascrivere un file esistente |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>Quando un nuovo file viene creato in una cartella di SharePoint, attiva un flusso
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint |
| folderId |string |yes |query |nessuno |Identificatore univoco della cartella in SharePoint |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>Quando un file viene modificato in una cartella di SharePoint, attiva un flusso
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint |
| folderId |string |yes |query |nessuno |Identificatore univoco della cartella in SharePoint |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>Usata per estrarre una cartella nella raccolta documenti
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, Ad esempio, http://contoso.sharepoint.com/sites/mysite |
| una sezione source |string |yes |query |nessuno |Percorso del file di origine |
| destination |string |yes |query |nessuno |Percorso della cartella di destinazione |
| overwrite |boolean |no |query |false |Sovrascrivere o non sovrascrivere un file esistente |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>Quando un nuovo elemento viene creato in un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>Quando un elemento esistente viene modificato in un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| $skip |integer |no |query |Nessuno |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |integer |no |query |Nessuno |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |
| $filter |string |no |query |Nessuno |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |string |no |query |Nessuno |Query orderBy ODATA per specificare l'ordine degli elementi |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>Crea un nuovo elemento in un elenco di SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| item | |Sì |body |nessuno |Elemento da creare |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>Recupera un singolo elemento da un elenco di SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| id |integer |Sì |path |nessuno |Identificatore univoco dell'elemento da recuperare |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>Elimina un elemento da un elenco di SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| id |integer |Sì |path |nessuno |Identificatore univoco dell'elemento da eliminare |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>Aggiorna un elemento in un elenco di SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |nessuno |URL del sito di SharePoint, ad esempio http://contoso.sharepoint.com/sites/mysite |
| tabella |string |Sì |path |nessuno |Nome dell'elenco SharePoint |
| id |integer |Sì |path |nessuno |Identificatore univoco dell'elemento da aggiornare |
| item | |Sì |body |nessuno |Elemento con le proprietà modificate |

### <a name="here-are-the-possible-responses"></a>Ecco le risposte possibili:
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

- - -
## <a name="object-definitions"></a>Definizioni di oggetti:
 **DataSetsMetadata**:

Proprietà obbligatorie per DataSetsMetadata:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| tabular |non definito |
| BLOB |non definito |

 **TabularDataSetsMetadata**:

Proprietà obbligatorie per TabularDataSetsMetadata:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

Proprietà obbligatorie per BlobDataSetsMetadata:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

Proprietà obbligatorie per BlobMetadata:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
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

 **Object**:

Proprietà obbligatorie per Object:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
|  | |

 **TableMetadata**:

Proprietà obbligatorie per TableMetadata:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| Nome |string |
| title |string |
| x-ms-permission |string |
| schema |non definito |

 **DataSetsList**:

Proprietà obbligatorie per DataSetsList:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| value |array |

 **DataSet**:

Proprietà obbligatorie per DataSet:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| Nome |string |
| displayName |string |

 **Tabella**:

Proprietà obbligatorie per Table:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| Nome |string |
| displayName |string |

 **Item**:

Proprietà obbligatorie per Item:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

Proprietà obbligatorie per ItemList:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| value |array |

 **TablesList**:

Proprietà obbligatorie per TablesList:

Nessuna delle proprietà è obbligatoria. 

**Tutte le proprietà**: 

| Nome | Tipo di dati |
| --- | --- |
| value |array |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


