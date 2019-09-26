---
title: Usare i comandi e le funzionalità predefiniti del notebook in Azure Cosmos DB
description: Informazioni su come usare i comandi e le funzionalità predefiniti per eseguire operazioni comuni usando i notebook predefiniti di Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310347"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Usare i comandi e le funzionalità predefiniti del notebook in Azure Cosmos DB

I notebook Jupyter incorporati in Azure Cosmos DB consentono di analizzare e visualizzare i dati dall'portale di Azure. Questo articolo descrive come usare le funzionalità e i comandi predefiniti del notebook per eseguire operazioni comuni.

## <a name="install-a-new-package"></a>Installare un nuovo pacchetto
Dopo aver abilitato il supporto per notebook per gli account Azure Cosmos, è possibile aprire un nuovo notebook e installare un pacchetto.

In una nuova cella di codice inserire ed eseguire il codice seguente, sostituendo ``PackageToBeInstalled`` con il pacchetto python desiderato.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Questo pacchetto sarà disponibile per l'uso da qualsiasi notebook nell'account Azure Cosmos. 

## <a name="run-a-sql-query"></a>Eseguire una query SQL

È possibile usare il ``%%sql`` comando Magic per eseguire una [query SQL](sql-query-getting-started.md) su qualsiasi contenitore nell'account. Usare la sintassi:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Sostituire ``{database_id}`` e``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli ``--database`` argomenti ``--container`` e non vengono specificati, la query verrà eseguita nel [database e nel contenitore predefiniti](#set-default-database-for-queries).
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve trovarsi in una nuova riga.

Esempio: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Eseguire ```%%sql?``` in una cella per visualizzare la documentazione della Guida per il comando SQL Magic nel notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Eseguire una query SQL e l'output in un dataframe Pandas

È possibile restituire i risultati di una ``%%sql`` query in un [dataframe Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Usare la sintassi: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Sostituire ``{database_id}`` e``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli ``--database`` argomenti ``--container`` e non vengono specificati, la query verrà eseguita nel [database e nel contenitore predefiniti](#set-default-database-for-queries).
- Sostituire ``{outputDataFrameVar}`` con il nome della variabile dataframe che conterrà i risultati.
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve trovarsi in una nuova riga. 

Esempio:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Imposta database predefinito per le query
È possibile impostare i comandi predefiniti ```%%sql``` del database che utilizzeranno per il notebook. Sostituire ```{database_id}``` con il nome del database.

```bash
%database {database_id}
```
Eseguire ```%database?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="set-default-container-for-queries"></a>Impostazione del contenitore predefinito per le query
È possibile impostare i comandi predefiniti ```%%sql``` del contenitore che utilizzeranno per il notebook. Sostituire ```{container_id}``` con il nome del contenitore.

```bash
%container {container_id}
```
Eseguire ```%container?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="use-the-built-in-python-sdk"></a>Usare l'SDK Python incorporato
La versione 4 dell' [API Azure Cosmos DB Python SDK per SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) è installata e inclusa nell'ambiente notebook per l'account Cosmos.

Usare l' ``cosmos_client`` istanza predefinita per eseguire qualsiasi operazione dell'SDK. 

Esempio:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Vedere [esempi di Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> L'SDK Python incorporato è supportato solo per gli account API SQL (Core). Per altre API, sarà necessario [installare il driver Python pertinente](#install-a-new-package) che corrisponde all'API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Creare un'istanza personalizzata di``cosmos_client``
Per una maggiore flessibilità, è possibile creare un'istanza personalizzata ``cosmos_client`` di per:

- Personalizzare i [criteri di connessione](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Eseguire operazioni su un account Cosmos diverso da quello in cui ci si trova

È possibile accedere alla stringa di connessione e alla chiave primaria dell'account corrente tramite le [variabili di ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Accedere alle variabili dell'endpoint dell'account e della chiave primaria ENV
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Le ``COSMOS_ENDPOINT`` variabili ``COSMOS_KEY`` di ambiente e sono valide solo per l'API SQL. Per altre API, trovare l'endpoint e la chiave nel pannello **stringhe di connessione** o **chiavi** nell'account Cosmos.  

## <a name="next-steps"></a>Passaggi successivi

- Scopri i vantaggi di [Azure Cosmos DB notebook Jupyter](cosmosdb-jupyter-notebooks.md)
- Informazioni sull' [API di Azure Cosmos DB Python SDK per SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
