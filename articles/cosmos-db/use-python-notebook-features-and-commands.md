---
title: Usare i comandi e le funzionalità predefiniti dei notebook Python in Azure Cosmos DB (anteprima)
description: Informazioni su come usare i comandi e le funzionalità predefiniti per eseguire operazioni comuni con i notebook Python predefiniti di Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: 5599a64e812c3006cf4379f98c5a0f0b0cd067e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801852"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Usare i comandi e le funzionalità predefiniti dei notebook Python in Azure Cosmos DB (anteprima)

I notebook Jupyter predefiniti in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure. Questo articolo descrive come usare le funzionalità e i comandi predefiniti per eseguire operazioni comuni nei notebook Python.

## <a name="install-a-new-package"></a>Installare un nuovo pacchetto
Dopo aver abilitato il supporto per notebook per gli account Azure Cosmos, è possibile aprire un nuovo notebook e installare un pacchetto.

In una nuova cella di codice inserire ed eseguire il codice seguente, sostituendo ``PackageToBeInstalled`` con il pacchetto Python desiderato.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Questo pacchetto sarà disponibile per l'uso da qualsiasi notebook nell'area di lavoro dell'account Azure Cosmos. 

> [!TIP]
> Se il notebook richiede un pacchetto personalizzato, è consigliabile aggiungere una cella nel notebook per installare il pacchetto, poiché i pacchetti vengono rimossi se si [reimposta l'area di lavoro](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Eseguire una query SQL

È possibile usare il comando magic ``%%sql`` per eseguire una [query SQL](sql-query-getting-started.md) su qualsiasi contenitore nell'account. Ecco la sintassi:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita [sul database e sul contenitore predefiniti](#set-default-database-for-queries).
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve essere riportato su una nuova riga.

Ad esempio: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Eseguire ```%%sql?``` in una cella per visualizzare la documentazione della guida per il comando sql magic nel notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Eseguire una query SQL e restituire i risultati in un dataframe Pandas

È possibile restituire i risultati di una query ``%%sql`` in un [dataframe Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame). Ecco la sintassi: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita [sul database e sul contenitore predefiniti](#set-default-database-for-queries).
- Sostituire ``{outputDataFrameVar}`` con il nome della variabile DataFrame che conterrà i risultati.
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve essere riportato su una nuova riga. 

Ad esempio:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Impostare il database predefinito per le query
È possibile impostare i comandi ```%%sql``` del database predefinito che verranno usati per il notebook. Sostituire ```{database_id}``` con il nome del database.

```python
%database {database_id}
```
Eseguire ```%database?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="set-default-container-for-queries"></a>Impostare il contenitore predefinito per le query
È possibile impostare i comandi ```%%sql``` del contenitore predefinito che verranno usati per il notebook. Sostituire ```{container_id}``` con il nome del contenitore.

```python
%container {container_id}
```
Eseguire ```%container?``` in una cella per visualizzare la documentazione nel notebook.

## <a name="upload-json-items-to-a-container"></a>Caricare elementi JSON in un contenitore
È possibile usare il comando magic ``%%upload`` per caricare dati da un file JSON in un contenitore Azure Cosmos specificato. Eseguire il comando seguente per caricare gli elementi:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Azure Cosmos. Se gli argomenti ``--database`` e ``--container`` non vengono specificati, la query verrà eseguita [sul database e sul contenitore predefiniti](#set-default-database-for-queries).
- Sostituire ``{url_location_of_file}`` con il percorso del file JSON. Il file deve essere una matrice di oggetti JSON validi e deve essere accessibile attraverso la rete Internet pubblica.

Ad esempio:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Con le statistiche di output, è possibile calcolare il numero effettivo di UR/sec usate per caricare gli elementi. Se, ad esempio, sono state usate 25.000 UR in 38 secondi, il numero effettivo di UR/sec è 25.000 UR/38 secondi = 658 UR/sec.

## <a name="run-another-notebook-in-current-notebook"></a>Eseguire un altro notebook nel notebook corrente 
È possibile usare il comando magic ``%%run`` per eseguire un altro notebook nell'area di lavoro dal notebook corrente. Ecco la sintassi:

```python
%%run ./path/to/{notebookName}.ipynb
```
Sostituire ``{notebookName}`` con il nome del notebook da eseguire. Il notebook deve trovarsi nell'area di lavoro My Notebooks (Notebook personali) corrente. 

## <a name="use-built-in-nteract-data-explorer"></a>Usare lo strumento nteract data explorer predefinito
Per filtrare e visualizzare un dataframe, è possibile usare lo strumento [nteract data explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) predefinito. Per abilitare questa funzionalità, impostare l'opzione ``pd.options.display.html.table_schema`` su ``True`` e ``pd.options.display.max_rows`` sul valore desiderato. È possibile impostare ``pd.options.display.max_rows`` su ``None`` per visualizzare tutti i risultati.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="nteract data explorer":::

## <a name="use-the-built-in-python-sdk"></a>Usare l'SDK Python predefinito
La versione 4 dell'[SDK Python di Azure Cosmos DB per l'API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) è installata e inclusa nell'ambiente di notebook per l'account Azure Cosmos.

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
Vedere gli [esempi dell'SDK Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> L'SDK Python predefinito è supportato solo per gli account API SQL (Core). Per le altre API, sarà necessario [installare il driver di Python pertinente](#install-a-new-package) corrispondente all'API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Creare un'istanza personalizzata di ``cosmos_client``
Per una maggiore flessibilità, è possibile creare un'istanza personalizzata di ``cosmos_client`` per:

- Personalizzare i [criteri di connessione](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview&preserve-view=true)
- Eseguire operazioni su un account Azure Cosmos diverso da quello corrente

È possibile accedere alla stringa di connessione e alla chiave primaria dell'account corrente tramite le [variabili di ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Accedere alle variabili di ambiente della chiave primaria e dell'endpoint dell'account
È possibile accedere alla chiave e all'endpoint predefiniti dell'account in cui si trova il notebook.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> Le variabili ``COSMOS.ENDPOINT`` e ``COSMOS.KEY`` sono valide solo per l'API SQL. Per le altre API, trovare l'endpoint e la chiave nel pannello **Stringhe di connessione** o **Chiavi** nell'account Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Reimpostare l'area di lavoro dei notebook
Per ripristinare le impostazioni predefinite dell'area di lavoro dei notebook, selezionare **Reimposta area di lavoro** sulla barra dei comandi. I pacchetti installati personalizzati verranno rimossi e il server Jupyter verrà riavviato. I notebook, i file e le risorse di Azure Cosmos non saranno interessati.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="nteract data explorer":::

## <a name="next-steps"></a>Passaggi successivi

- Scoprire i vantaggi dei [notebook Jupyter di Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Ottenere informazioni sull'[SDK Python di Azure Cosmos DB per l'API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
