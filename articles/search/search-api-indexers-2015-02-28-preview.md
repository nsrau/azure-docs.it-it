---
title: 'Operazioni sull''indicizzatore (API REST di Ricerca di Azure: 2015-02-28-Preview) | Documentazione Microsoft'
description: 'Operazioni sull''indicizzatore (API REST di Ricerca di Azure: 2015-02-28-Preview)'
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: eugenesh
ms.openlocfilehash: 356ceb98106d080d8c24dedc3547bee33750156e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Operazioni sull'indicizzatore (API REST di Ricerca di Azure: 2015-02-28-Preview)
> [!NOTE]
> Questo articolo descrive gli indicizzatori nell' [API REST 2015-02-28-Preview](search-api-2015-02-28-preview.md). Questa versione dell'API aggiunge le versioni di anteprima dell'indicizzatore di archiviazione BLOB di Azure con estrazione di documenti, l'indicizzatore di archiviazione tabelle e altri miglioramenti. L'API supporta anche indicizzatori disponibili a livello generale (GA), compresi gli indicizzatori per il Database di SQL Azure, SQL Server in VM di Azure e Azure Cosmos DB.
> 
> 

## <a name="overview"></a>Panoramica
Ricerca di Azure può integrarsi direttamente con alcune origini dati comuni, eliminando la necessità di scrivere codice per l'indicizzazione dei dati. Per impostare questo servizio, è possibile chiamare l'API di Ricerca di Azure in modo da creare e gestire **indicizzatori** e **origini dati**. 

Un **indicizzatore** è una risorsa che connette le origini dati agli indici di ricerca di destinazione. Un indicizzatore viene usato nei modi seguenti: 

* Eseguire una copia occasionale dei dati per popolare un indice.
* Sincronizzare un indice con le modifiche nell'origine dati in base a una pianificazione. La pianificazione fa parte della definizione dell'indicizzatore.
* Chiamare aggiornamenti su richiesta in un indice in base alle esigenze. 

Un **indicizzatore** è utile quando si desidera eseguire aggiornamenti regolari a un indice. È possibile impostare una pianificazione inline come parte di una definizione di indicizzatore oppure eseguire una pianificazione su richiesta usando l'operazione di [esecuzione di un indicizzatore](#RunIndexer). 

Un' **origine dati** specifica i dati da indicizzare, le credenziali per accedere ai dati e i criteri che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati, ad esempio righe modificate o eliminate in una tabella di database. L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Sono attualmente supportate le origini dati seguenti:

* **Database SQL di Azure** e **SQL Server in Macchine virtuali di Azure**. Per una procedura dettagliata, vedere [questo articolo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 
* **Azure Cosmos DB**. Per una procedura dettagliata, vedere [questo articolo](search-howto-index-documentdb.md). 
* **Archiviazione BLOB di Azure**, inclusi i seguenti formati di documenti: PDF, Microsoft Office (DOCX/DOC, XLS/XSLX, PPTX/PPT, MSG), HTML, XML, ZIP e file di testo normale (incluso JSON). Per una procedura dettagliata, vedere [questo articolo](search-howto-indexing-azure-blob-storage.md).
* **Archivio tabelle di Azure**. Per una procedura dettagliata, vedere [questo articolo](search-howto-indexing-azure-tables.md).

La possibilità di aggiungere il supporto per altre origini dati è in fase di valutazione. Per contribuire a questa decisione, è possibile fornire commenti e suggerimenti nell'apposito [forum di Ricerca di Azure](http://feedback.azure.com/forums/263029-azure-search).

Vedere [Limiti del servizio](search-limits-quotas-capacity.md) per i limiti massimi relativi alle risorse di origine dati e dell'indicizzatore.

## <a name="typical-usage-flow"></a>Flusso di utilizzo tipico
È possibile creare e gestire le origini dati e gli indicizzatori tramite semplici richieste HTTP (POST, GET, PUT, DELETE) su una risorsa `data source` o `indexer` specifica.

Per l'impostazione dell'indicizzazione automatica è in genere necessario un processo in quattro fasi:

1. Identificare l'origine dati contenente i dati da indicizzare. Tenere presente che è possibile che Ricerca di Azure non supporti tutti i tipi di dati presenti nell'origine dati. Per un elenco dei [tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx) vedere l'apposito articolo.
2. Creare un indice di Ricerca di Azure con uno schema compatibile con l'origine dati.
3. Creare un'origine dati di Ricerca di Azure, come descritto nella sezione [Creare un'origine dati](#CreateDataSource).
4. Creare un indicizzatore di ricerca di Azure, come descritto in [Creare un indicizzatore](#CreateIndexer).

È consigliabile pianificare la creazione di un indicizzatore per ogni combinazione di indice di destinazione e origine dati. È possibile avere più indicizzatori che svolgono operazioni di scrittura nello stesso indice e riutilizzare la stessa origine dati per più indicizzatori. Tuttavia, un indicizzatore può utilizzare solo un'origine dati alla volta e può scrivere solo su un singolo indice. 

Dopo aver creato un indicizzatore, è possibile ottenere il relativo stato di esecuzione mediante l'operazione per [ottenere lo stato di un indicizzatore](#GetIndexerStatus) . È inoltre possibile eseguire un indicizzatore in qualsiasi momento (anziché o oltre a eseguirlo periodicamente in base a una pianificazione) usando l'operazione di [esecuzione di un indicizzatore](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Creare un'origine dati
Nella ricerca di Azure, un'origine dati viene utilizzata con gli indicizzatori, fornendo le informazioni di connessione per l'aggiornamento dei dati ad hoc o pianificato di un indice di destinazione. È possibile creare una nuova origine dati in Ricerca di Azure mediante una richiesta HTTP POST.

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare una richiesta PUT e specificare il nome dell'origine dati nell'URI. Se l'origine dati non esiste, verrà creata.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> Il numero massimo di origini dati consentite varia in base al piano tariffario. Nel servizio gratuito sono consentite fino a 3 origini dati. Nel servizio standard sono consentite 50 origini dati. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti del servizio](search-limits-quotas-capacity.md) .
> 
> 

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **creazione di un'origine dati** può essere costruita mediante un metodo POST o PUT. Se si usa il metodo POST, è necessario specificare nel corpo della richiesta il nome e la definizione dell'origine dati. Nel caso di PUT, il nome fa parte dell'URL. Se l'origine dati non esiste, viene creata. Se esiste già, viene aggiornata in base alla nuova definizione. 

Il nome dell'origine dati deve essere scritto in caratteri minuscoli, iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può includere qualsiasi lettera, numero e trattino, purché i trattini non siano consecutivi. Per informazioni dettagliare, vedere [Regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative. 

* `Content-Type`: richiesto. Impostare il valore su `application/json`
* `api-key`: richiesto. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **creazione di un'origine dati** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query. 

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere sia il nome del servizio sia `api-key` dal dashboard servizi nel [portale di Azure](https://portal.azure.com/). Per informazioni, vedere [Creare un servizio di ricerca nel portale](search-create-service-portal.md) .

<a name="CreateDataSourceRequestSyntax"></a>
**Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione dell'origine dati, che include il tipo di origine dati, le credenziali per leggere i dati, oltre a criteri facoltativi di rilevamento delle modifiche dei dati e dell'eliminazione dei dati usati per identificare in modo efficiente i dati modificati o eliminati nell'origine dati, quando vengono usati con un indicizzatore pianificato periodicamente. 

La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è riportata una richiesta di esempio.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

La richiesta contiene le proprietà seguenti: 

* `name`: richiesto. nome dell'origine dati. Il nome di un'origine dati deve contenere solo lettere minuscole, cifre o trattini, non può iniziare o terminare con trattini e deve avere una lunghezza massima di 128 caratteri.
* `description`: descrizione facoltativa. 
* `type`: richiesto. Deve essere uno dei tipi di origine dati supportati:
  * `azuresql` - database SQL di Azure e SQL Server in macchine virtuali di Azure
  * `documentdb` - Azure Cosmos DB
  * `azureblob` - Archiviazione BLOB di Azure
  * `azuretable` - Archivio tabelle di Azure
* `credentials`:
  * La proprietà `connectionString` obbligatoria specifica la stringa di connessione per l'origine dati. Il formato della stringa di connessione dipende dal tipo di origine dati: 
    * Per SQL Azure, si tratta della solita stringa di connessione di SQL Server. Se si usa il portale di Azure per recuperare la stringa di connessione, usare l'opzione `ADO.NET connection string`.
    * Per Azure Cosmos DB, la stringa di connessione deve essere nel formato seguente: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Tutti i valori sono obbligatori e sono reperibili nel [portale di Azure](https://portal.azure.com/).  
    * Per l'archiviazione BLOB e l'archiviazione tabelle di Azure, questa è la stringa di connessione dell'account di archiviazione. Il formato è descritto [qui](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). È richiesto un protocollo HTTPS per l'endpoint.  
* `container`, elemento obbligatorio: specifica i dati da indicizzare mediante le proprietà `name` e `query`: 
  * `name`, elemento obbligatorio:
    * SQL Azure: specifica la tabella o la vista. È possibile usare nomi completi di schema, ad esempio `[dbo].[mytable]`.
    * DocumentDB: specifica la raccolta. 
    * Archiviazione BLOB di Azure: specifica il contenitore di archiviazione.
    * Archiviazione tabelle di Azure: consente di specificare il nome della colonna della tabella. 
  * `query`, elemento facoltativo:
    * DocumentDB: consente di specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che può essere indicizzato da Ricerca di Azure.  
    * Archiviazione BLOB di Azure: consente di specificare una cartella virtuale all'interno del contenitore BLOB. Ad esempio, per il percorso BLOB `mycontainer/documents/blob.pdf` è possibile usare `documents` come cartella virtuale.
    * Archiviazione tabelle di Azure: consente di specificare una query che filtra il set di righe da importare.
    * SQL Azure: la query non è supportata. Se questa funzionalità è necessaria, votare per [questo suggerimento](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* Le proprietà facoltative `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` sono descritte di seguito.

<a name="DataChangeDetectionPolicies"></a>
**Criteri di rilevamento delle modifiche dei dati**

Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. I criteri supportati variano in base al tipo di origine dati. Le sezioni seguenti descrivono ognuno di questi criteri. 

***Criteri di rilevamento delle modifiche con limite massimo*** 

Usare questi criteri quando l'origine dati contiene una colonna o una proprietà che soddisfa le condizioni seguenti:

* Tutti gli inserimenti specificano un valore per la colonna. 
* Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna. 
* Il valore di questa colonna aumenta in base alla modifica apportata.
* Le query che usano una clausola di filtro simile a `WHERE [High Water Mark Column] > [Current High Water Mark Value]` possono essere eseguite in modo efficiente.

Ad esempio, quando si usano origini dati di SQL Azure, una colonna `rowversion` indicizzata è il candidato ideale da usare per i criteri con limite massimo. 

Questi criteri possono essere specificati come indicato di seguito:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

Quando si usano origini dati di Azure Cosmos DB, è necessario usare la proprietà `_ts` specificata da Azure Cosmos DB. 

Quando si usano le origini dati BLOB di Azure, Ricerca di Azure usa automaticamente criteri di rilevamento modifiche con limite massimo basati sull'ultimo timestamp modificato del BLOB. L'utente non deve quindi specificare personalmente questi criteri.   

***Criteri di rilevamento delle modifiche integrati di SQL***

Se il database SQL supporta il [rilevamento delle modifiche](https://msdn.microsoft.com/library/bb933875.aspx), è consigliabile usare i criteri di rilevamento delle modifiche integrati di SQL. Questi criteri favoriscono il rilevamento delle modifiche più efficiente e consentono a Ricerca di Azure di identificare le righe eliminate senza dover includere nello schema una colonna di "eliminazione temporanea" esplicita.

Il rilevamento delle modifiche integrato è supportata a partire dalle seguenti versioni di database di SQL Server: 

* SQL Server 2008 R2 e versioni successive, se si usa SQL Server nelle VM di Azure.
* Database SQL di Azure V12, se si utilizza il database SQL di Azure SQL.  

Quando si usano i criteri di rilevamento delle modifiche integrati di SQL, non specificare criteri di rilevamento dell'eliminazione dei dati separati, perché questi ultimi includono il supporto predefinito per l'identificazione delle righe eliminate. 

Questi criteri possono essere usati solo con le tabelle, ma non con le visualizzazioni. Prima di poter usare questi criteri, è necessario abilitare il rilevamento delle modifiche per la tabella in uso. Per istruzioni, vedere [Abilitare e disabilitare il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb964713.aspx) .    

Quando si struttura la richiesta di **creazione di un'origine dati** , è possibile specificare i criteri di rilevamento delle modifiche integrati di SQL come segue:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Criteri di rilevamento dell'eliminazione dei dati**

Scopo dei criteri di rilevamento dell'eliminazione dei dati è quello di identificare in modo efficace gli elementi di dati eliminati. Attualmente, gli unici criteri supportati sono i criteri `Soft Delete`, che consente di identificare gli elementi eliminati in base al valore di un `soft delete` colonna o proprietà dell'origine dati. Questi criteri possono essere specificati come indicato di seguito:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> Sono supportate solo le colonne con valori di stringa, interi o booleani. Il valore usato come `softDeleteMarkerValue` deve essere una stringa, anche se la colonna corrispondente contiene valori interi o booleani. Se ad esempio il valore visualizzato nell'origine dati è 1, usare `"1"` come `softDeleteMarkerValue`.    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**Esempi del corpo della richiesta**

Se si intende usare l'origine dati con un indicizzatore eseguito in una pianificazione, questo esempio illustra come specificare i criteri di rilevamento delle modifiche e dell'eliminazione dei dati: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Se si intende usare solo l'origine dati per una copia occasionale dei dati, è possibile omettere i criteri:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato. 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>Aggiornare un'origine dati
È possibile aggiornare un'origine dati esistente mediante una richiesta HTTP PUT. È necessario specificare il nome dell'origine dati da aggiornare nell'URI della richiesta:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. In [Controllo delle API di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) sono presenti informazioni dettagliate sulle versioni alternative.

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Richiesta**

La sintassi del corpo della richiesta è analoga a quella indicata nell'operazione di [creazione di un'origine dati](#CreateDataSourceRequestSyntax).

Alcune proprietà non possono essere aggiornate in un'origine dati esistente. Ad esempio, non è possibile modificare il tipo di un'origine dati esistente.  

Se non si vuole modificare la stringa di connessione per un'origine dati esistente, è possibile specificare il valore letterale `<unchanged>` per la stringa di connessione. Questo accorgimento è utile nelle situazioni in cui è necessario aggiornare un'origine dati ma non si dispone dell'accesso alla stringa di connessione poiché si tratta di dati con esigenze particolari a livello di sicurezza.

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato se è stata creata una nuova origine dati, e il codice di stato 204 - Nessun contenuto se è stata aggiornata un'origine dati esistente.

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>Elencare le origini dati
L'operazione per **elencare le origini dati** restituisce un elenco delle origini dati disponibili in Ricerca di Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 200 - OK.

Di seguito è riportato il corpo di una risposta di esempio:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Si noti che è possibile filtrare la risposta fino a visualizzare solo le proprietà a cui si è interessati. Ad esempio, se si vuole solo un elenco di nomi di origini dati, usare l'opzione di query `$select` di OData:

    GET /datasources?api-version=205-02-28&$select=name

In questo caso, la risposta dell'esempio precedente sarà simile alla seguente: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Questa è una tecnica utile per risparmiare larghezza di banda, se nel servizio di ricerca sono presenti numerose origini dati.

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>Ottenere un'origine dati
L'operazione per **ottenere un'origine dati** ottiene la definizione dell'origine dati da Ricerca di Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

La risposta è simile agli esempi nell'operazione di [creazione di un'origine dati](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> Non impostare l'intestazione della richiesta `Accept` su `application/json;odata.metadata=none` quando si chiama questa API, in quanto tale operazione comporterebbe l'omissione dell'attributo `@odata.type` dalla risposta e sarebbe impossibile distinguere tra i criteri di rilevamento dell'eliminazione dei dati e i criteri di rilevamento della modifica dei dati di tipi diversi. 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>Eliminare un'origine dati
L'operazione di **eliminazione di un'origine dati** rimuove un'origine dati da Ricerca di Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Se alcuni indicizzatori fanno riferimento all'origine dati da eliminare, l'operazione di eliminazione continuerà comunque. Questi indicizzatori, tuttavia, effettueranno una transizione a uno stato di errore all'esecuzione successiva.  
> 
> 

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>Creare un indicizzatore
È possibile creare un nuovo indicizzatore in Ricerca di Azure mediante una richiesta HTTP POST.

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare una richiesta PUT e specificare il nome dell'origine dati nell'URI. Se l'origine dati non esiste, verrà creata.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> Il numero massimo di indicizzatori consentiti varia in base al piano tariffario. Nel servizio gratuito sono consentiti fino a 3 indicizzatori. Nel servizio standard sono consentiti 50 indicizzatori. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti del servizio](search-limits-quotas-capacity.md) .
> 
> 

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

<a name="CreateIndexerRequestSyntax"></a>
**Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione di indicizzatore, che specifica l'origine dati e l'indice di destinazione per l'indicizzazione, nonché una pianificazione e pianificazioni di indicizzazione facoltativi. 

La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è riportata una richiesta di esempio.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Pianificazione dell'indicizzatore**

Facoltativamente, un indicizzatore può specificare una pianificazione. Se è presente una pianificazione, l'indicizzatore verrà eseguito periodicamente in base alla pianificazione. La pianificazione ha gli attributi seguenti:

* `interval`: richiesto. Valore di durata che specifica un intervallo o un periodo per l'esecuzione dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `"P[nD][T[nH][nM]]"`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore. 
* `startTime`: richiesto. Data e ora UTC di inizio dell'esecuzione dell'indicizzatore. 

**Parametri dell'indicizzatore**

Un indicizzatore può facoltativamente specificare diversi parametri che ne influenzano il comportamento. Tutti i parametri sono facoltativi.  

* `maxFailedItems` : numero di elementi per cui l'indicizzazione può avere esito negativo prima che l'indicizzatore venga considerato in errore. Il valore predefinito è 0. Le informazioni sugli elementi non riusciti vengono restituite dall'operazione per [ottenere lo stato di un indicizzatore](#GetIndexerStatus) . 
* `maxFailedItemsPerBatch` : numero di elementi per cui l'indicizzazione può avere esito negativo in ogni batch prima che l'indicizzatore venga considerato in errore. Il valore predefinito è 0.
* `base64EncodeKeys`: specifica se le chiavi del documento saranno codificate in base 64. Ricerca di Azure impone restrizioni sui caratteri che possono essere presenti in una chiave del documento. Tuttavia, i valori nei dati di origine possono contenere caratteri non validi. Se è necessario indicizzare questi valori come chiavi del documento, questo contrassegno può essere impostato su true. Il valore predefinito è `false`.
* `batchSize`: specifica il numero di elementi che vengono letti dall'origine dati e indicizzati in un batch unico per migliorare le prestazioni. Il valore predefinito dipende dal tipo di origine dati: 1000 per SQL Azure e Azure Cosmos DB e 10 per archiviazione BLOB di Azure.

**Mapping dei campi**

È possibile usare il mapping dei campi per associare un nome di campo nell'origine dati a un nome di campo diverso nell'indice di destinazione. Si consideri ad esempio una tabella di origine con un campo `_id`. Ricerca di Azure non consente un nome di campo che inizia con un carattere di sottolineatura, quindi il campo deve essere rinominato. Questa operazione può essere eseguita usando la proprietà `fieldMappings` dell'indicizzatore nel modo seguente: 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

È possibile specificare più mapping dei campi. 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Per i nomi dei campi di origine e destinazione non viene fatta distinzione tra maiuscole e minuscole.

<a name="FieldMappingFunctions"></a>
***Funzioni del mapping dei campi***

Il mapping dei campi può essere usato anche per trasformare i valori del campo di origine mediante le *funzioni di mapping*.

Attualmente è supportata solo una di queste funzioni: `jsonArrayToStringCollection`. Questa funzione analizza un campo contenente una stringa formattata come matrice JSON in un campo Collection(Edm.String) nell'indice di destinazione. È destinata in particolare all'uso con l'indicizzatore di SQL Azure, poiché SQL non ha un tipo di dati nativo per le raccolte. È possibile usare questa funzione come descritto di seguito: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Ad esempio, se il campo di origine contiene la stringa `["red", "white", "blue"]`, il campo di destinazione di tipo `Collection(Edm.String)` verrà popolato con i tre valori `"red"`, `"white"` e `"blue"`.

Si noti che la proprietà `targetFieldName` è facoltativa. Se omessa, viene usato il valore `sourceFieldName`. 

<a name="CreateIndexerRequestExamples"></a>
**Esempi del corpo della richiesta**

L'esempio seguente crea un indicizzatore che copia i dati dalla tabella a cui fa riferimento l'origine dati `ordersds` all'indice `orders` in base a una pianificazione che inizia l'1 gennaio 2015 UTC e viene eseguita ogni ora. Ogni chiamata all'indicizzatore avrà esito positivo se l'indicizzazione non ha esito negativo per più di 5 elementi in ogni batch e per più di 10 elementi in totale. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato.

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>Aggiornare un indicizzatore
È possibile aggiornare un indicizzatore esistente mediante una richiesta HTTP PUT. Nell'URI della richiesta viene specificato il nome dell'indicizzatore da aggiornare:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione corrente è `2015-02-28`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Richiesta**

La sintassi del corpo della richiesta è analoga a quella dell'operazione di [creazione di un indicizzatore](#CreateIndexerRequestSyntax).

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato se è stato creato un nuovo indicizzatore, e il codice di stato 204 - Nessun contenuto se è stato aggiornato un indicizzatore esistente.

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>Elencare gli indicizzatori
L'operazione per **elencare gli indicizzatori** restituisce l'elenco di indicizzatori in Ricerca di Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. [Controllo delle versioni di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) sono presenti informazioni dettagliate sulle versioni alternative.

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 200 - OK.

Di seguito è riportato il corpo di una risposta di esempio:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Si noti che è possibile filtrare la risposta fino a visualizzare solo le proprietà a cui si è interessati. Ad esempio, se si vuole solo un elenco di nomi di indicizzatori, usare l'opzione di query `$select` di OData:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

In questo caso, la risposta dell'esempio precedente sarà simile alla seguente: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Questa è una tecnica utile per risparmiare larghezza di banda, se nel servizio di ricerca sono presenti numerosi indicizzatori.

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>Ottenere un indicizzatore
L'operazione per **ottenere un indicizzatore** recupera la definizione dell'indicizzatore da Ricerca di Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

La risposta è simile agli esempi nella richiesta di [creazione di un indicizzatore](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>Eliminare un indicizzatore
L'operazione di **eliminazione di un indicizzatore** rimuove un indicizzatore da Ricerca di Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando viene eliminato un indicizzatore, le esecuzioni dell'indicizzatore in corso verranno completate, ma non verranno pianificate altre esecuzioni. I tentativi di utilizzare un indicizzatore inesistente restituiscono un codice di stato HTTP 404 Pagina non trovata. 

L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>esecuzione di un indicizzatore
Oltre a essere eseguito periodicamente in base a una pianificazione, un indicizzatore può anche essere chiamato su richiesta tramite l'operazione di **esecuzione di un indicizzatore** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 202 Accettato.

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>ottenere lo stato di un indicizzatore
L'operazione per **ottenere lo stato di un indicizzatore** recupera la cronologia di esecuzione e lo stato corrente di un indicizzatore: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Il corpo della risposta contiene informazioni sullo stato di integrità globale dell'indicizzatore, l'ultima chiamata all'indicizzatore, nonché la cronologia delle chiamate recenti, se presenti. 

Un corpo della risposta di esempio è simile al seguente: 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**Stato dell'indicizzatore**

Lo stato dell'indicizzatore può avere uno dei valori seguenti:

* `running` indica che l'indicizzatore viene eseguito normalmente. Si noti che è possibile che si verifichino comunque errori nelle esecuzioni dell'indicizzatore. È quindi consigliabile controllare anche la proprietà `lastResult`. 
* `error` indica che nell'indicizzatore si è verificato un errore che non può essere corretto senza un intervento da parte dell'utente. È ad esempio possibile che le credenziali dell'origine dati siano scadute o che lo schema dell'origine dati o dell'indice di destinazione sia cambiato in modo significativo. 

**Risultato dell'esecuzione dell'indicizzatore**

Il risultato dell'esecuzione dell'indicizzatore contiene informazioni su una singola esecuzione dell'indicizzatore. Il risultato più recente viene esposto come proprietà `lastResult` dello stato dell'indicizzatore. Altri risultati recenti, se presenti, vengono restituiti come proprietà `executionHistory` dello stato dell'indicizzatore. 

Il risultato dell'esecuzione dell'indicizzatore contiene le proprietà seguenti: 

* `status`: stato di questa esecuzione. Per informazioni dettagliate, vedere [Stato di esecuzione dell'indicizzatore](#IndexerExecutionStatus) più avanti. 
* `errorMessage`: messaggio di errore per un'esecuzione non riuscita. 
* `startTime`: ora UTC di avvio dell'esecuzione.
* `endTime`: ora UTC di fine dell'esecuzione. Questo valore non viene impostato se l'esecuzione è ancora in corso.
* `errors`: array di errori a livello di elemento, se presenti. Ogni voce contiene una chiave di documento (proprietà `key`) e un messaggio di errore (proprietà `errorMessage`). 
* `itemsProcessed`: numero di elementi di origine dati (ad esempio, righe di tabella) che l'indicizzatore ha tentato di indicizzare durante questa esecuzione. 
* `itemsFailed`: numero di elementi non riusciti durante questa esecuzione.  
* `initialTrackingState`: sempre `null` per la prima esecuzione dell'indicizzatore o se i criteri di rilevamento delle modifiche dei dati non sono abilitati sull'origine dati usata. Se i criteri sono abilitati, nelle esecuzioni successive questo valore indica il primo (minimo) valore di rilevamento delle modifiche elaborato da questa esecuzione. 
* `finalTrackingState`: sempre `null` se i criteri di rilevamento delle modifiche dei dati non sono abilitati sull'origine dati usata. In caso contrario, indica l'ultimo (massimo) valore di rilevamento delle modifiche elaborato correttamente da questa esecuzione. 

<a name="IndexerExecutionStatus"></a>
**Stato di esecuzione dell'indicizzatore**

Lo stato di esecuzione dell'indicizzatore acquisisce lo stato di una singola esecuzione dell'indicizzatore. I valori possibili sono i seguenti:

* `success` indica che l'esecuzione dell'indicizzatore è stata completata correttamente.
* `inProgress` indica che l'esecuzione dell'indicizzatore è in corso. 
* `transientFailure` indica che l'esecuzione dell'indicizzatore ha avuto esito negativo. Per informazioni dettagliate, vedere la proprietà `errorMessage` . L'errore può o potrebbe non richiedere l'intervento di correzione -, ad esempio, correggere l'incompatibilità dello schema tra l'origine dati e l'indice di destinazione richiede un intervento da parte dell'utente, mentre un tempo di inattività temporaneo dell'origine dati non richiede alcun intervento. Le chiamate all'indicizzatore continuano in base alla pianificazione, se presente. 
* `persistentFailure` indica che si è verificato un errore dell'indicizzatore che richiede un intervento da parte dell'utente. Le esecuzioni pianificate dell'indicizzatore vengono arrestate. Dopo aver risolto il problema, usare l'API di reimpostazione di un indicizzatore per riavviare le esecuzioni pianificate. 
* `reset` indica che l'indicizzatore è stato reimpostato tramite una chiamata all'API di reimpostazione di un indicizzatore (vedere la sezione seguente). 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>Reimpostare un indicizzatore
L'operazione di **reimpostazione di un indicizzatore** reimposta lo stato di rilevamento delle modifiche associato all'indicizzatore. Questo consente di attivare la reindicizzazione da zero, ad esempio se è cambiato lo schema dell'origine dati, oppure di modificare i criteri di rilevamento delle modifiche per un'origine dati associata all'indicizzatore.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

L'elemento `api-version` è obbligatorio. La versione di anteprima è `2015-02-28-Preview`. 

L'elemento `api-key` deve essere una chiave amministratore, invece di una chiave di query Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). [Creare un servizio di ricerca nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure
<table style="font-size:12">
<tr>
<td>Tipo di dati SQL</td>    
<td>Tipi di campi dell'indice di destinazione consentiti</td>
<td>Note</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Ricerca di Azure non supporta la conversione di tipi decimali in Edm.Double, perché in tal caso si perderebbe la precisione
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Vedere la sezione [Funzioni del mapping dei campi](#FieldMappingFunctions) in questo documento per informazioni dettagliate sulla trasformazione di una colonna stringa in un campo Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>Sono supportate solo le istanze geografiche di tipo POINT con SRID 4326 (ossia l'impostazione predefinita)</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Le colonne di versione di riga non possono essere archiviate nell'indice di ricerca, ma possono essere usate per il rilevamento modifiche</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image<br>XML, geometry, tipi CLR</td>
<td>N/D</td>
<td>Non supportate</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati di Ricerca di Azure
<table style="font-size:12">
<tr>
<td>Tipo di dati JSON</td>    
<td>Tipi di campi dell'indice di destinazione consentiti</td>
<td>Note</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Numeri integrali</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Numeri a virgola mobile</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Matrici di tipi primitivi, ad esempio "a", "b", "c"</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Stringhe che rappresentano date</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Oggetti punto GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Gli oggetti punto GeoJSON sono oggetti JSON nel formato seguente: { "type" : "Point", "coordinates" : [long, lat] } </td>
</tr>
<tr>
<td>Altri oggetti JSON</td>
<td>N/D</td>
<td>Non supportati. Ricerca di Azure attualmente supporta solo tipi primitivi e raccolte di stringhe</td>
</tr>
</table>
