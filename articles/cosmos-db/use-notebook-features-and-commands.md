---
title: Usare funzionalità e comandi di blocco appunti incorporati in Azure Cosmos DB (anteprima)Use built-in notebook commands and features in Azure Cosmos DB (preview)
description: Informazioni su come usare funzionalità e comandi incorporati per eseguire operazioni comuni usando i blocchi appunti predefiniti di Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513400"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Usare funzionalità e comandi di blocco appunti incorporati in Azure Cosmos DB (anteprima)Use built-in notebook commands and features in Azure Cosmos DB (preview)

I blocchi appunti Jupyter incorporati in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure.Built-in Jupyter notebooks in Azure Cosmos DB enable you to analyze and visualize your data from the Azure portal. Questo articolo descrive come usare le funzionalità e i comandi predefiniti del notebook per eseguire operazioni comuni.

## <a name="install-a-new-package"></a>Installare un nuovo pacchetto
Dopo aver abilitato il supporto del blocco appunti per gli account Cosmos di Azure, è possibile aprire un nuovo blocco appunti e installare un pacchetto.

In una nuova cella di codice, inserire ``PackageToBeInstalled`` ed eseguire il codice seguente, sostituendo con il pacchetto Python desiderato.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Questo pacchetto sarà disponibile per l'uso da qualsiasi blocco appunti nell'area di lavoro degli account di Azure Cosmos.This package will be available to use from any notebook in the Azure Cosmos account workspace. 

> [!TIP]
> Se il blocco appunti richiede un pacchetto personalizzato, è consigliabile aggiungere una cella nel blocco appunti per installare il pacchetto, poiché i pacchetti vengono rimossi se si [reimposta l'area di lavoro.](#reset-notebooks-workspace)  

## <a name="run-a-sql-query"></a>Eseguire una query SQLRun a SQL query

È possibile ``%%sql`` utilizzare il comando magic per eseguire una [query SQL](sql-query-getting-started.md) su qualsiasi contenitore nell'account. Utilizzare la sintassi:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Sostituire ``{database_id}`` ``{container_id}`` e con il nome del database e del contenitore nel tuo account Cosmos. Se ``--database`` gli ``--container`` argomenti e non vengono forniti, la query verrà eseguita nel database e nel [contenitore predefiniti.](#set-default-database-for-queries)
- È possibile eseguire qualsiasi query SQL valida in Azure Cosmos DB. Il testo della query deve trovarsi in una nuova riga.

Ad esempio: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Eseguire ```%%sql?``` in una cella per visualizzare la documentazione della Guida per il comando sql magic nel blocco appunti.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Eseguire una query SQL e l'output in un frame di dati PandasRun a SQL query and output to a Pandas DataFrame

È possibile restituire i ``%%sql`` risultati di una query in un [oggetto Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Utilizzare la sintassi: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Sostituire ``{database_id}`` ``{container_id}`` e con il nome del database e del contenitore nel tuo account Cosmos. Se ``--database`` gli ``--container`` argomenti e non vengono forniti, la query verrà eseguita nel database e nel [contenitore predefiniti.](#set-default-database-for-queries)
- Sostituire ``{outputDataFrameVar}`` con il nome della variabile Frame di dati che conterrà i risultati.
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
## <a name="upload-json-items-to-a-container"></a>Caricare elementi JSON in un contenitoreUpload JSON items to a container
È possibile ``%%upload`` usare il comando magico per caricare dati da un file JSON in un contenitore Cosmos di Azure specificato. Utilizzare il comando seguente per caricare gli elementi:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Sostituire ``{database_id}`` ``{container_id}`` e con il nome del database e del contenitore nell'account Cosmos di Azure. Se ``--database`` gli ``--container`` argomenti e non vengono forniti, la query verrà eseguita nel database e nel [contenitore predefiniti.](#set-default-database-for-queries)
- Sostituire ``{url_location_of_file}`` con il percorso del file JSON. Il file deve essere una matrice di oggetti JSON validi e deve essere accessibile tramite Internet pubblico.

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
Con le statistiche di output, è possibile calcolare le RU effettive utilizzate per caricare gli elementi. Ad esempio, se 25.000 RU sono state utilizzate in 38 secondi, la RU/s effettiva è 25.000 RU / 38 secondi , 658 RU/s.

## <a name="set-default-database-for-queries"></a>Impostare il database predefinito per le query
È possibile impostare ```%%sql``` i comandi di database predefiniti da utilizzare per il blocco appunti. Sostituire ```{database_id}``` con il nome del database.

```bash
%database {database_id}
```
Eseguire ```%database?``` in una cella per visualizzare la documentazione nel blocco appunti.

## <a name="set-default-container-for-queries"></a>Impostare il contenitore predefinito per le querySet default container for queries
È possibile impostare ```%%sql``` i comandi contenitore predefiniti da utilizzare per il blocco appunti. Sostituire ```{container_id}``` con il nome del contenitore.

```bash
%container {container_id}
```
Eseguire ```%container?``` in una cella per visualizzare la documentazione nel blocco appunti.

## <a name="use-built-in-nteract-data-explorer"></a>Utilizzare Esplora dati nteract integrato
È possibile utilizzare l'Esplora [dati nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporato per filtrare e visualizzare un frame di dati. Per abilitare questa funzione, ``True`` ``pd.options.display.max_rows`` impostare l'opzione ``pd.options.display.html.table_schema`` su ``pd.options.display.max_rows`` ``None`` e sul valore desiderato (è possibile impostare su per visualizzare tutti i risultati).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![esplora dati nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Usare l'SDK di Python incorporato
La versione 4 [dell'API Di Azure Cosmos DB Python SDK per SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) è installata e inclusa nell'ambiente del blocco appunti per l'account Azure Cosmos.

Utilizzare ``cosmos_client`` l'istanza predefinita per eseguire qualsiasi operazione SDK. 

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
> Python SDK incorporato è supportato solo per gli account API SQL (Core). Per altre API, è necessario [installare il driver Python pertinente](#install-a-new-package) che corrisponde all'API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Creare un'istanza personalizzata di``cosmos_client``
Per una maggiore flessibilità, è ``cosmos_client`` possibile creare un'istanza personalizzata di per:

- Personalizzare i [criteri di connessione](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Eseguire operazioni su un account Cosmos di Azure diverso da quello in cui ci si trova

È possibile accedere alla stringa di connessione e alla chiave primaria dell'account corrente tramite le variabili di [ambiente.](#access-the-account-endpoint-and-primary-key-env-variables) 

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
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Accedere all'endpoint dell'account e alle variabili della chiave primaria
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Le ``COSMOS_ENDPOINT`` ``COSMOS_KEY`` variabili di ambiente e sono applicabili solo per l'API SQL. Per altre API, trovare l'endpoint e la chiave nel pannello **Stringhe di connessione** o Chiavi nell'account Azure Cosmos.For other APIs, find the endpoint and key in the Connection Strings or **Keys** blade in your Azure Cosmos account.  

## <a name="reset-notebooks-workspace"></a>Reimpostare l'area di lavoro dei blocchi appunti
Per ripristinare le impostazioni predefinite dell'area di lavoro blocchi appunti, selezionare **Reimposta area di lavoro** sulla barra dei comandi. Questo rimuoverà tutti i pacchetti installati personalizzati e riavvierà il server Jupyter. I blocchi appunti, i file e le risorse di Azure Cosmos non saranno interessati.  

![Reimpostare l'area di lavoro dei blocchi appunti](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri i vantaggi dei [notebook Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Informazioni su [Azure Cosmos DB Python SDK per l'API SQLLearn](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) about the Azure Cosmos DB Python SDK for SQL API
