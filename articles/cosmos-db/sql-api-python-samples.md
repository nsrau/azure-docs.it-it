---
title: Esempi Python dell'API SQL per Azure Cosmos DB | Microsoft Docs
description: Esempi Python in GitHub per attività comuni in Azure Cosmos DB, tra cui operazioni CRUD.
keywords: Esempi di Python
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 2eb585876f91526633fb2844e67ba734648b38e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389537"
---
# <a name="azure-cosmos-db-python-examples"></a>Esempi Python per Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Esempi di .NET](sql-api-dotnet-samples.md)
> * [Esempi di Java](sql-api-java-samples.md)
> * [Esempi di Java Async](sql-api-async-java-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Il repository [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) di GitHub include soluzioni di esempio che eseguono operazioni CRUD e altre operazioni comuni su risorse di Azure Cosmos DB. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Python di esempio. 
* Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- È possibile [attivare i benefici della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): con la sottoscrizione Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

È anche necessario [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Ogni esempio è indipendente e le operazioni di installazione e pulizia sono eseguite automaticamente. Gli esempi effettuano più chiamate a [document_client.CreateCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient#createcollection). Ogni volta che questa operazione viene eseguita, viene fatturata un'ora di servizio per la sottoscrizione. Per altre informazioni sulla fatturazione di Azure Cosmos DB, vedere [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Esempi di database
Il file [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) del progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) illustra come eseguire le attività seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdatabase) |
| [Leggere un database in base all'ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabase) |
| [Elencare database per un account](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabases) |
| [Eliminare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletedatabase) |

## <a name="collection-examples"></a>Esempi di raccolta
Il file [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) del progetto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) illustra come eseguire le attività seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createcollection) |
| [Leggere un elenco di raccolte in un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollections) |
| [Ottenere una raccolta in base all'ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollection) |
| [Modifica della velocità effettiva di una raccolta](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replaceoffer)|
| [Eliminare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletecollection) |

## <a name="document-examples"></a>Esempi di documento
Il file [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) del progetto [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) illustra come eseguire le attività seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un documento](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdocument) |
| [Creare una raccolta di documenti](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python) |
| [Leggere un documento in base all'ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocument) |
| [Leggere tutti i documenti in una raccolta](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocuments) |

## <a name="indexing-examples"></a>Esempi di indicizzazione
Il file [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) del progetto [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) illustra come eseguire le attività seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Usare l'indicizzazione manuale anziché automatica](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](https://docs.microsoft.com/python/api/pydocumentdb.documents?view=azure-python) |
| [Escludere percorsi di documenti specificati dall'indice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Escludere un documento dall'indice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Impostare la modalità di indicizzazione](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingmode?view=azure-python) |
| [Usare gli indici di intervallo sulle stringhe](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Eseguire una trasformazione dell'indice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replacecollection) |

## <a name="query-examples"></a>Esempi di query
I progetti di esempio mostrano anche come eseguire le attività di query seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Eseguire query in un account per un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydatabases) |
| [Eseguire query per documenti](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydocuments) |
| [Forzare un'operazione di analisi dell'intervallo su un percorso indicizzato hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](https://docs.microsoft.com/python/api/pydocumentdb.http_constants.httpheaders?view=azure-python#enablescaninquery) |
