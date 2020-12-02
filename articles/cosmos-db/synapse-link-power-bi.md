---
title: Power BI e pool SQL senza server per analizzare i dati Azure Cosmos DB con il collegamento sinapsi
description: Informazioni su come creare un database del pool SQL senza server e visualizzazioni sul collegamento sinapsi per Azure Cosmos DB, eseguire query sui contenitori di Azure Cosmos DB e quindi compilare un modello con Power BI su tali viste.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.openlocfilehash: 959070ca431c3397779a2a22c16f03b3adebbb35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444512"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-preview-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Usare Power BI e il pool SQL sinapsi senza server (anteprima) per analizzare i dati Azure Cosmos DB con il collegamento sinapsi 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In questo articolo si apprenderà come creare un database del pool SQL senza server e le visualizzazioni sul collegamento sinapsi per Azure Cosmos DB. Si eseguirà una query sui contenitori di Azure Cosmos DB e quindi si compilerà un modello con Power BI su tali viste per riflettere tale query.

In questo scenario si utilizzeranno dati fittizi sulle vendite di prodotti Surface in un negozio di vendita al dettaglio partner. Si analizzeranno i ricavi per ogni negozio in base alla vicinanza a famiglie di grandi dimensioni e all'effetto della pubblicità per una settimana specifica. In questo articolo vengono create due visualizzazioni denominate **RetailSales** e **StoreDemographics** e una query tra di esse. È possibile ottenere i dati del prodotto di esempio da questo repository [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) .

> [!IMPORTANT]
> Il supporto del pool SQL senza server di sinapsi per il collegamento a sinapsi di Azure per Azure Cosmos DB è attualmente in fase di anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di creare le risorse seguenti:

* [Creare un account Azure Cosmos DB di tipo SQL (Core) o MongoDB.](create-cosmosdb-resources-portal.md)

* Abilitare il collegamento a sinapsi di Azure per l' [account Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Creare un database all'interno dell'account Azure Cosmos e due contenitori in cui è [abilitato l'archivio analitico.](configure-synapse-link.md#create-analytical-ttl)

* Caricare i dati dei prodotti nei contenitori di Azure Cosmos come descritto in questo notebook di inserimento [dati batch](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Creare un'area di lavoro sinapsi](../synapse-analytics/quickstart-create-workspace.md) denominata **SynapseLinkBI**.

* [Connettere il database di Azure Cosmos all'area di lavoro sinapsi](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Creazione di un database e di viste

Dall'area di lavoro sinapsi passare alla scheda **sviluppo** , selezionare l' **+** icona e selezionare **script SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Aggiungere uno script SQL all'area di lavoro di sinapsi Analytics":::

Ogni area di lavoro viene fornita con un endpoint SQL senza server. Dopo aver creato uno script SQL, dalla barra degli strumenti nella parte superiore connettersi a **built-in**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Abilitare lo script SQL per l'uso dell'endpoint SQL senza server nell'area di lavoro":::

Creare un nuovo database, denominato **RetailCosmosDB**, e una vista SQL sui contenitori abilitati per il collegamento sinapsi. Il seguente comando Mostra come creare un database:

```sql
-- Create database
Create database RetailCosmosDB
```

Successivamente, creare più viste tra i contenitori di Azure Cosmos abilitati per il collegamento sinapsi diversi. Le visualizzazioni consentiranno di usare T-SQL per aggiungere ed eseguire query Azure Cosmos DB dati che si siedono in contenitori diversi.  Quando si creano le visualizzazioni, assicurarsi di selezionare il database **RetailCosmosDB** .

Negli script seguenti viene illustrato come creare visualizzazioni in ogni contenitore. Per semplicità, si userà la funzionalità di [inferenza dello schema automatica](analytical-store-introduction.md#analytical-schema) del pool SQL senza server tramite i contenitori abilitati per il collegamento sinapsi:


### <a name="retailsales-view"></a>Visualizzazione RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Assicurarsi di inserire l'area Azure Cosmos DB e la chiave primaria nello script SQL precedente. Tutti i caratteri nel nome dell'area devono essere in lettere minuscole senza spazi. Diversamente dagli altri parametri del `OPENROWSET` comando, il parametro del nome del contenitore deve essere specificato senza virgolette.

### <a name="storedemographics-view"></a>Visualizzazione StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

A questo punto, eseguire lo script SQL selezionando il comando **Run (Esegui** ).

## <a name="query-the-views"></a>Eseguire query sulle viste

Ora che le due visualizzazioni sono state create, è possibile definire la query per unire le due visualizzazioni come segue:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Selezionare **Esegui** che fornisce la seguente tabella come risultato:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Risultati della query dopo l'aggiunta delle viste StoreDemographics e RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Viste dei modelli sui contenitori con Power BI

Aprire quindi il desktop Power BI e connettersi all'endpoint SQL senza server attenendosi alla procedura seguente:

1. Aprire l'applicazione Power BI Desktop. Selezionare **recuperare i dati** e selezionare **altro**.

1. Scegliere **Azure sinapsi Analytics (SQL DW)** dall'elenco di opzioni di connessione.

1. Immettere il nome dell'endpoint SQL in cui si trova il database. Immettere `SynapseLinkBI-ondemand.sql.azuresynapse.net` nel campo **Server** . In questo esempio,  **SynapseLinkBI** è il nome dell'area di lavoro. Sostituirlo se è stato specificato un nome diverso per l'area di lavoro. Selezionare **query diretta** per la modalità di connettività dei dati e quindi **OK**.

1. Selezionare il metodo di autenticazione preferito, ad esempio Azure AD.

1. Selezionare il database **RetailCosmosDB** e le visualizzazioni **RetailSales**, **StoreDemographics** .

1. Selezionare **carica** per caricare le due visualizzazioni in modalità query diretta.

1. Selezionare **modello** per creare una relazione tra le due visualizzazioni tramite la colonna **storeId** .

1. Trascinare la colonna **StoreId** dalla vista **RetailSales** alla colonna **StoreId** nella vista **StoreDemographics** .

1. Selezionare la relazione molti-a-uno (*: 1) perché sono presenti più righe con lo stesso ID di archivio nella vista **RetailSales** . **StoreDemographics** dispone di una sola riga dell'ID di archivio (si tratta di una tabella delle dimensioni).

Passare quindi alla finestra del **report** e creare un report per confrontare l'importanza relativa della dimensione domestica con i ricavi medi per negozio in base alla rappresentazione distribuita dei ricavi e dell'indice LargeHH:

1. Selezionare **grafico a dispersione**.

1. Trascinare **LargeHH** dalla visualizzazione **StoreDemographics** all'asse X.

1. Trascinare e rilasciare i **ricavi** dalla visualizzazione **RetailSales** nell'asse Y. Selezionare **media** per ottenere le vendite medie per prodotto per negozio e per settimana.

1. Trascinare e rilasciare **ProductCode** dalla visualizzazione **RetailSales** nella legenda per selezionare una linea di prodotti specifica.
Dopo aver scelto queste opzioni, viene visualizzato un grafico simile allo screenshot seguente:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Report che confronta l'importanza relativa delle dimensioni domestiche con i ricavi medi per negozio":::

## <a name="next-steps"></a>Passaggi successivi

[Usare T-SQL per eseguire query sui dati Azure Cosmos DB usando il collegamento sinapsi di Azure](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Usare un pool SQL senza server per [analizzare i set di dati aperti di Azure e visualizzare i risultati in Azure sinapsi Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
