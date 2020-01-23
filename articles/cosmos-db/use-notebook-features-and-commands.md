---
title: Usare i comandi e le funzionalità predefiniti del notebook in Azure Cosmos DB (anteprima)
description: Informazioni su come usare i comandi e le funzionalità predefiniti per eseguire operazioni comuni usando i notebook predefiniti di Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513400"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Usare i comandi e le funzionalità predefiniti del notebook in Azure Cosmos DB (anteprima)

I notebook Jupyter incorporati in Azure Cosmos DB consentono di analizzare e visualizzare i dati dall'portale di Azure. Questo articolo descrive come usare le funzionalità e i comandi predefiniti del notebook per eseguire operazioni comuni.

## <a name="install-a-new-package"></a>Installare un nuovo pacchetto
Dopo aver abilitato il supporto per notebook per gli account Azure Cosmos, è possibile aprire un nuovo notebook e installare un pacchetto.

In una nuova cella di codice inserire ed eseguire il codice seguente, sostituendo ``PackageToBeInstalled`` con il pacchetto python desiderato.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Questo pacchetto sarà disponibile per l'uso da qualsiasi notebook nell'area di lavoro dell'account Azure Cosmos. 

> [!TIP]
> Se il notebook richiede un pacchetto personalizzato, è consigliabile aggiungere una cella nel notebook per installare il pacchetto, poiché i pacchetti vengono rimossi se si [Reimposta l'area di lavoro](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Eseguire una query SQL

È possibile usare il comando ``%%sql`` Magic per eseguire una [query SQL](sql-query-getting-started.md) su qualsiasi contenitore nell'account. Usare la sintassi:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita nel [database e nel contenitore predefiniti](#set-default-database-for-queries).
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve trovarsi in una nuova riga.

Ad esempio: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Eseguire ```%%sql?``` in una cella per visualizzare la documentazione della Guida per il comando SQL Magic nel notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Eseguire una query SQL e l'output in un dataframe Pandas

È possibile restituire i risultati di una query di ``%%sql`` in un [Dataframe Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Usare la sintassi: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita nel [database e nel contenitore predefiniti](#set-default-database-for-queries).
- Sostituire ``{outputDataFrameVar}`` con il nome della variabile dataframe che conterrà i risultati.
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve trovarsi in una nuova riga. 

Ad esempio:

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
## <a name="upload-json-items-to-a-container"></a>Caricare elementi JSON in un contenitore
È possibile usare il comando ``%%upload`` Magic per caricare dati da un file JSON in un contenitore Azure Cosmos specificato. Usare il comando seguente per caricare gli elementi:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Azure Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita nel [database e nel contenitore predefiniti](#set-default-database-for-queries).
- Sostituire ``{url_location_of_file}`` con il percorso del file JSON. Il file deve essere una matrice di oggetti JSON validi e deve essere accessibile attraverso la rete Internet pubblica.

Ad esempio:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Con le statistiche di output, è possibile calcolare le UR/sec valide usate per caricare gli elementi. Se, ad esempio, 25.000 UR sono state utilizzate più di 38 secondi, il Ur/s effettivo è 25.000 UR/38 secondi = 658 ur/sec.

## <a name="set-default-database-for-queries"></a>Imposta database predefinito per le query
È possibile impostare il database predefinito ```%%sql``` i comandi utilizzati per il notebook. Sostituire ```{database_id}``` con il nome del database.

```bash
%database {database_id}
```
Eseguire ```%database?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="set-default-container-for-queries"></a>Impostazione del contenitore predefinito per le query
È possibile impostare il contenitore predefinito ```%%sql``` comandi da usare per il notebook. Sostituire ```{container_id}``` con il nome del contenitore.

```bash
%container {container_id}
```
Eseguire ```%container?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="use-built-in-nteract-data-explorer"></a>Usare Esplora dati nteract predefiniti
È possibile usare [Esplora dati nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporato per filtrare e visualizzare un frame di dati. Per abilitare questa funzionalità, impostare l'opzione ``pd.options.display.html.table_schema`` su ``True`` e ``pd.options.display.max_rows`` sul valore desiderato. è possibile impostare ``pd.options.display.max_rows`` su ``None`` per visualizzare tutti i risultati.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![Esplora dati nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Usare l'SDK Python incorporato
La versione 4 dell' [API Azure Cosmos DB Python SDK per SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) è installata e inclusa nell'ambiente notebook per l'account Azure Cosmos.

Usare l'istanza predefinita di ``cosmos_client`` per eseguire qualsiasi operazione dell'SDK. 

Ad esempio:

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>Creare un'istanza personalizzata di ``cosmos_client``
Per una maggiore flessibilità, è possibile creare un'istanza personalizzata di ``cosmos_client`` per:

- Personalizzare i [criteri di connessione](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Eseguire operazioni su un account Azure Cosmos diverso da quello in cui ci si trova

È possibile accedere alla stringa di connessione e alla chiave primaria dell'account corrente tramite le [variabili di ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Accedere alle variabili dell'endpoint dell'account e della chiave primaria ENV
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Le variabili di ambiente ``COSMOS_ENDPOINT`` e ``COSMOS_KEY`` sono valide solo per l'API SQL. Per altre API, trovare l'endpoint e la chiave nel pannello **stringhe di connessione** o **chiavi** nell'account Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Reimposta area di lavoro notebook
Per reimpostare l'area di lavoro notebook sulle impostazioni predefinite, selezionare **Reimposta area di lavoro** sulla barra dei comandi. I pacchetti installati personalizzati vengono rimossi e il server Jupyter verrà riavviato. I notebook, i file e le risorse di Azure Cosmos non saranno interessati.  

![Reimposta area di lavoro notebook](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri i vantaggi di [Azure Cosmos DB notebook Jupyter](cosmosdb-jupyter-notebooks.md)
- Informazioni sull' [API di Azure Cosmos DB Python SDK per SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
