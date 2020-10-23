---
title: Esempi Python dell'API SQL per Azure Cosmos DB
description: Esempi Python in GitHub per attività comuni in Azure Cosmos DB, tra cui operazioni CRUD.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ba4382e03e6fc1acfd606a375a062c05581f712c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278218"
---
# <a name="azure-cosmos-db-python-examples"></a>Esempi Python per Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Esempi di .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Esempi di .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Esempi di Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Esempi di Spring Data V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Il repository [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) di GitHub include soluzioni di esempio che effettuano operazioni CRUD e altre operazioni comuni su risorse di Azure Cosmos DB. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Python di esempio.
* Collegamenti al contenuto di riferimento sulle API correlato.

## <a name="prerequisites"></a>Prerequisiti

- Un account Cosmos DB. Le opzioni disponibili sono le seguenti:
    * All'interno di una sottoscrizione di Azure attiva:
        * [Creare un account Azure gratuito](https://azure.microsoft.com/free) o usare una sottoscrizione esistente 
        * [Crediti mensili di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Livello gratuito di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/optimize-dev-test#azure-cosmos-db-free-tier)
    * Senza una sottoscrizione di Azure attiva:
        * [Provare Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/), un ambiente di test con validità di 30 giorni.
        * [Emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 o 3.5.3+](https://www.python.org/downloads/), con il file eseguibile `python` in `PATH`.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>Esempi di database

L'esempio [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) di Python mostra come eseguire le attività seguenti. Per saperne di più sui database di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Leggere un database in base all'ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Eseguire query sul database](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Elencare database per un account](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Eliminare un database](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Esempi di contenitore

L'esempio [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) di Python mostra come eseguire le attività seguenti. Per saperne di più sulle raccolte di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Eseguire query per trovare un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Creare un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Elencare tutti i contenitori in un database](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Ottenere un contenitore in base all'ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Gestire la velocità effettiva con provisioning del contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Eliminare un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Esempi di elementi

L'esempio [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) di Python mostra come eseguire le attività seguenti. Per saperne di più sui documenti di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare elementi in un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Leggere un elemento in base all'ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Leggere tutti gli elementi in un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Eseguire query su un elemento in base all'ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Sostituire un elemento](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Upsert di un elemento](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Eliminare un elemento](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Ottenere il feed delle modifiche degli elementi di un contenitore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Esempi di indicizzazione

L'esempio [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) di Python mostra come eseguire le attività seguenti. Per saperne di più sull'indicizzazione in Azure Cosmos DB prima di passare agli esempi seguenti, vedere gli articoli concettuali su [criteri di indicizzazione](index-policy.md), [tipi di indicizzazione](index-types.md) e [percorsi di indicizzazione](index-paths.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Escludere un elemento specifico dall'indice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Usare l'indicizzazione manuale con elementi specifici indicizzati](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [Escludere percorsi dall'indicizzazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definire i percorsi da escludere nella proprietà `IndexingPolicy` |
| [Usare gli indici di intervallo sulle stringhe](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Definire i criteri di indicizzazione con indici di intervallo per il tipo di dati stringa. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Eseguire una trasformazione dell'indice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (usare i criteri di indicizzazione aggiornati)|
| [Usare le analisi quando nel percorso esiste solo l'indice hash](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Impostare `enable_scan_in_query=True` e `enable_cross_partition_query=True` quando si eseguono query sugli elementi |
