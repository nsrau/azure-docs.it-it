---
title: Notebook di Azure Synapse Studio
description: Questa esercitazione offre una panoramica su come creare un dashboard di Power BI usando Apache Spark e un pool SQL serverless.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: ea8fcb602f49dec61187260e08d3ccd1b148cee8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95918939"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Esercitazione: Creare un report di Power BI con Apache Spark e Azure Synapse Analytics

Le organizzazioni hanno spesso l'esigenza di elaborare grandi quantità di dati prima di rispondere alle richieste dei principali stakeholder aziendali. Questa esercitazione illustra come sfruttare le esperienze integrate in Azure Synapse Analytics per elaborare i dati con Apache Spark e in seguito distribuirli agli utenti finali tramite Power BI e SQL serverless.

## <a name="before-you-begin"></a>Prima di iniziare
- [Area di lavoro di Azure Synapse Analytics](../quickstart-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come risorsa archiviazione predefinita. 
- Area di lavoro di Power BI e Power BI Desktop per visualizzare i dati. Per i dettagli, vedere [Creare un'area di lavoro di Power BI](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces) e [Installare Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Servizio collegato per connettere le aree di lavoro di Azure Synapse Analytics e Power BI. Per i dettagli, vedere [Collegamento a un'area di lavoro di Power BI](../quickstart-power-bi.md)
- Pool di Apache Spark serverless nell'area di lavoro di Synapse Analytics. Per i dettagli, vedere [Creare un pool di Apache Spark serverless](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati
In questo esempio si userà Apache Spark per eseguire un'analisi sui dati relativi alle mance offerte per le corse dei taxi di New York. I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse in taxi, incluse informazioni su ogni corsa, l'ora di partenza e di arrivo, i percorsi, i costi e altri attributi interessanti.

1. Eseguire le righe seguenti per creare un dataframe di Spark incollando il codice in una nuova cella. I dati vengono recuperati tramite l'API dei set di dati aperti di Azure. Il pull di tutti questi dati genera circa 1,5 miliardi righe. L'esempio di codice seguente usa start_date e end_date per applicare un filtro che restituisce un singolo mese di dati.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Usando Apache Spark SQL, verrà creato un database denominato NycTlcTutorial. Questo database verrà usato per archiviare i risultati dell'elaborazione dati.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Successivamente, verranno usate le operazioni del dataframe Spark per elaborare i dati. Nel codice seguente verranno eseguite queste trasformazioni:
   1. Rimozione delle colonne non necessarie.
   2. Rimozione dei valori outlier/non corretti tramite filtro.
   3. Creazione di nuove funzionalità, come ```tripTimeSecs``` e ```tipped``` per un'ulteriore analisi.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Infine, il dataframe verrà salvato usando il metodo ```saveAsTable``` di Apache Spark. In questo modo in seguito sarà possibile eseguire query e connettersi alla stessa tabella usando pool SQL serverless.
   
## <a name="query-data-using-serverless-sql-pools"></a>Eseguire query sui dati usando pool SQL serverless
Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle tra i pool di Apache Spark serverless (anteprima) e il pool SQL serverless (anteprima). Questa opzione è disponibile tramite la funzionalità di [gestione di metadati condivisi](../metadata/overview.md) di Synapse. Di conseguenza, i database creati da Spark e le relative tabelle basate su Parquet diventano visibili nel pool SQL serverless dell'area di lavoro.

Per eseguire query sulla tabella di Apache Spark con il pool SQL serverless:
   1. Dopo aver salvato la tabella di Apache Spark, passare alla scheda **Dati**.
   
   2. In **Aree di lavoro** individuare la tabella di Apache Spark appena creata, selezionare **New SQL script** (Nuovo script SQL) e quindi **Select TOP 100 rows** (Seleziona le prime 100 righe). 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="Query SQL." border="true":::

   3. È possibile continuare ad affinare la query o anche visualizzare i risultati usando le opzioni dei grafici di SQL.

## <a name="connect-to-power-bi"></a>Connettersi a Power BI
Il pool SQL serverless verrà ora connesso all'area di lavoro di Power BI. Una volta connessa l'area di lavoro, sarà possibile creare report di Power BI sia direttamente da Azure Synapse Analytics sia da Power BI Desktop.

>[!Note]
> Prima di iniziare, è necessario configurare un servizio collegato per l'[area di lavoro di Power BI](../quickstart-power-bi.md) e scaricare [Power BI Desktop](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces).  

Per connettere il pool SQL serverless all'area di lavoro di Power BI:

1.  Passare alla scheda **Set di dati di Power BI** e selezionare l'opzione **+ Nuovo set di dati**. Al prompt, scaricare il file con estensione pbids dal database di Analisi SQL da usare come origine dati. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Set di dati Power BI." border="true":::

2.  Aprire il file con Power BI Desktop per creare un set di dati. Dopo aver aperto il file, connettersi al database di SQL Server usando l'**account Microsoft** e l'opzione **Importa**. 
   

## <a name="create-a-power-bi-report"></a>Creare un report di Power BI
1. In Power BI Desktop è ora possibile aggiungere un grafico **key influencers** al report.
   
   1. Trascinare la colonna di valori medi *tipAmount* sull'asse **Analizza**.
   
   2. Trascinare le colonne **weekdayString**, **tripDistance** (valori medi) e **tripTimeSecs** (valori medi) sull'asse **Spiega in base a**. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Nella scheda Home di Power BI Desktop selezionare **Pubblica** e quindi **Salva** per salvare le modifiche. Immettere un nome file e salvare questo report nell'*area di lavoro NycTaxiTutorial*.
   
3. È anche possibile creare visualizzazioni di Power BI all'interno dell'area di lavoro di Azure Synapse Analytics. A questo scopo, passare alla scheda **Sviluppo** nell'area di lavoro di Azure Synapse e aprire la scheda Power BI. Qui è possibile selezionare il report e continuare a creare altre visualizzazioni. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Area di lavoro di Azure Synapse Analytics." border="true":::

Per altre informazioni su come creare un set di dati tramite SQL serverless e connettersi a Power BI, vedere questa esercitazione su [come connettersi a Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di visualizzazione dati di Azure Synapse Analytics, vedere i documenti e le esercitazioni seguenti:
   - [Visualizzare dati con pool di Apache Spark serverless](../spark/apache-spark-data-visualization-tutorial.md)
   - [Panoramica della visualizzazione dati con i pool di Apache Spark](../spark/apache-spark-data-visualization.md)