---
title: Creare un dashboard in tempo reale usando Azure Cosmos DB, Azure Analysis Services e Power BI
description: Informazioni su come creare un dashboard Meteo attivo in Power BI usando Azure Cosmos DB e Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376593"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Creare un dashboard in tempo reale usando Azure Cosmos DB e Power BI

Questo articolo descrive i passaggi necessari per creare un dashboard Meteo attivo in Power BI usando Azure Cosmos DB e Azure Analysis Services. Il dashboard Power BI Visualizza i grafici per visualizzare le informazioni in tempo reale sulla temperatura e la pioggia in un'area.

## <a name="reporting-scenarios"></a>Scenari di Reporting

Sono disponibili diversi modi per configurare i dashboard di Reporting sui dati archiviati in Azure Cosmos DB. A seconda dei requisiti di obsolescenza e della dimensione dei dati, nella tabella seguente viene descritta la configurazione di Reporting per ogni scenario:


|Scenario |Configurazione |
|---------|---------|
|1. Generazione di report ad hoc (nessun aggiornamento)    |  [Power BI Azure Cosmos DB connettore con la modalità di importazione](powerbi-visualize.md)       |
|2. Generazione di report ad hoc con aggiornamento periodico   |  [Power BI Azure Cosmos DB connettore con la modalità di importazione (aggiornamento periodico pianificato)](powerbi-visualize.md)       |
|3. Creazione di report su set di dati di grandi dimensioni (< 10 GB)     |  Power BI Azure Cosmos DB connettore con aggiornamento incrementale       |
|4. Report in tempo reale su set di dati di grandi dimensioni    |  Power BI connettore Azure Analysis Services con query diretta + Azure Analysis Services (Azure Cosmos DB Connector)       |
|5. Creazione di report sui dati dinamici con aggregazioni     |  [Power BI connettore Spark con Direct query + Azure Databricks + Cosmos DB connettore Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Creazione di report sui dati dinamici con aggregazioni su set di dati di grandi dimensioni   |  Power BI connettore Azure Analysis Services con Direct query + Azure Analysis Services + Azure Databricks + Cosmos DB Spark Connector.       |

Gli scenari 1 e 2 possono essere facilmente configurati con il connettore Azure Cosmos DB Power BI. Questo articolo descrive le configurazioni per gli scenari 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI con aggiornamento incrementale

Power BI dispone di una modalità in cui è possibile configurare l'aggiornamento incrementale. Questa modalità Elimina la necessità di creare e gestire Azure Analysis Services partizioni. È possibile configurare l'aggiornamento incrementale per filtrare solo gli aggiornamenti più recenti nei set di impostazioni di grandi dimensioni. Questa modalità funziona tuttavia solo con Power BI Premium servizio con un limite del set di dati di 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services offre una piattaforma completamente gestita come servizio che ospita modelli di dati di livello aziendale nel cloud. È possibile caricare set di dati di grandi dimensioni da Azure Cosmos DB in Azure Analysis Services. Per evitare l'esecuzione di query sull'intero set di dati, i set di dati possono essere suddivisi in partizioni Azure Analysis Services, che possono essere aggiornate in modo indipendente a frequenze diverse.

## <a name="power-bi-incremental-refresh"></a>Power BI aggiornamento incrementale

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Inserire i dati meteorologici in Azure Cosmos DB

Configurare una pipeline di inserimento per caricare [i dati meteo](https://catalog.data.gov/dataset/local-weather-archive) in Azure Cosmos DB. È possibile configurare un processo di [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) per caricare periodicamente i dati meteo più recenti in Azure Cosmos DB usando l'origine http e il sink di Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Connetti Power BI a Azure Cosmos DB

1. **Connettere l'account Azure Cosmos per Power bi** aprire il Power bi desktop e usare il connettore di Azure Cosmos DB per selezionare il database e il contenitore corretti.

   ![Connettore Power BI di Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Configurare l'aggiornamento incrementale** : seguire la procedura descritta in [aggiornamento incrementale con Power bi](/power-bi/service-premium-incremental-refresh) articolo per configurare l'aggiornamento incrementale per il set di dati. Aggiungere i parametri **RangeStart** e **RangeEnd** come illustrato nello screenshot seguente:

   ![Configurare i parametri di intervallo](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Poiché per il set di dati è presente una colonna data in formato testo, i parametri **RangeStart** e **RangeEnd** devono essere trasformati in modo da utilizzare il filtro seguente. Nel riquadro **Editor avanzato** modificare la query aggiungere il testo seguente per filtrare le righe in base ai parametri RangeStart e rangeend:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   A seconda della colonna e del tipo di dati presenti nel set di dati di origine, è possibile modificare di conseguenza i campi RangeStart e RangeEnd

   
   |Proprietà  |Tipo di dati  |Applica filtro  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration. TotalSeconds (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) e [_ts] < Duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Data (ad esempio:-2019-08-19)     |   String      | [Document. date] > DateTime. ToText (RangeStart, "AAAA-MM-GG") e [Document. date] < DateTime. ToText (RangeEnd, "AAAA-MM-GG")        |
   |Data (ad esempio:-2019-08-11 12:00:00)   |  String       |  [Document. date] > DateTime. ToText (RangeStart, "aaaa-mm-GG HH: mm: SS") e [Document. date] < DateTime. ToText (RangeEnd, "aaaa-mm-GG HH: mm: SS")       |


1. **Definire i criteri di aggiornamento** -definire i criteri di aggiornamento passando alla scheda **aggiornamento incrementale** nel menu di **scelta rapida** per la tabella. Impostare i criteri di aggiornamento per aggiornare **ogni giorno** e archiviare i dati dell'ultimo mese.

   ![Definire i criteri di aggiornamento](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignorare l'avviso che indica che *la query M non può essere confermata per essere ripiegata*. Il connettore Azure Cosmos DB esegue il riduzioni delle query di filtro.

1. **Caricare i dati e generare i report** : usando i dati caricati in precedenza, creare i grafici per segnalare la temperatura e le precipitazioni.

   ![Caricare dati e generare report](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Pubblicare il report in Power bi Premium** : poiché l'aggiornamento incrementale è una funzionalità solo Premium, la finestra di dialogo pubblica consente solo la selezione di un'area di lavoro sulla capacità Premium. Il primo aggiornamento potrebbe richiedere più tempo per importare i dati cronologici. Gli aggiornamenti dei dati successivi sono molto più rapidi perché utilizzano l'aggiornamento incrementale.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Inserire i dati meteorologici in Azure Cosmos DB 

Configurare una pipeline di inserimento per caricare [i dati meteo](https://catalog.data.gov/dataset/local-weather-archive) in Azure Cosmos DB. È possibile configurare un processo di Azure Data Factory (ADF) per caricare periodicamente i dati meteo più recenti in Azure Cosmos DB usando l'origine HTTP e il sink di Cosmos DB.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Connetti Azure Analysis Services all'account Azure Cosmos

1. **Creare un nuovo cluster** - [di Azure Analysis Services creare un'istanza di Azure Analysis Services](../analysis-services/analysis-services-create-server.md) nella stessa area dell'account Azure Cosmos e del cluster databricks.

1. **Creare un nuovo progetto Analysis Services tabulare in Visual Studio** -  [installare il SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) e creare un Analysis Services progetto tabulare in Visual Studio.

   ![Crea Azure Analysis Services progetto](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Scegliere l'istanza dell' **area di lavoro integrata** e impostare il livello di compatibilità su **SQL Server 2017/Azure Analysis Services (1400)**

   ![Progettazione di modelli tabulari Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Aggiungere l'origine dati Azure Cosmos DB** : passare a **modelli**> **origini** > dati**nuova origine dati** e aggiungere l'origine dati Azure Cosmos DB, come illustrato nello screenshot seguente:

   ![Aggiungi origine dati Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Connettersi a Azure Cosmos DB fornendo l' **URI dell'account**, il **nome del database**e il nome del **contenitore**. È ora possibile visualizzare i dati di Azure Cosmos container importati in Power BI.

   ![Anteprima Azure Cosmos DB dati](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Costruire il modello di Analysis Services** : aprire l'editor di query, eseguire le operazioni necessarie per ottimizzare il set di dati caricato:

   * Estrarre solo le colonne correlate al meteo (temperatura e pioggia)

   * Estrarre le informazioni relative al mese dalla tabella. Questi dati sono utili per la creazione di partizioni, come descritto nella sezione successiva.

   * Converte le colonne di temperatura in numero

   L'espressione M risultante è la seguente:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Inoltre, modificare il tipo di dati delle colonne temperature in Decimal per assicurarsi che questi valori possano essere tracciati in Power BI.

1. **Creare partizioni di analisi di Azure** : creare partizioni in Azure Analysis Services per dividere il set di dati in partizioni logiche che possono essere aggiornate in modo indipendente e a frequenze diverse. In questo esempio vengono create due partizioni che dividono il set di dati nei dati del mese più recente e tutti gli altri elementi.

   ![Creazione di partizioni di Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Creare le due partizioni seguenti in Azure Analysis Services:

   * **Ultimo mese** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Cronologici** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Distribuire il modello nel Analysis server di Azure** : fare clic con il pulsante destro del mouse sul progetto Azure Analysis Services e scegliere **Distribuisci**. Aggiungere il nome del server nel riquadro delle **proprietà del server di distribuzione** .

   ![Distribuisci modello di Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Configurare gli aggiornamenti della partizione e le unioni** : Azure Analysis Services consente l'elaborazione indipendente delle partizioni. Poiché si vuole che la partizione **Month più recente** venga aggiornata costantemente con i dati più recenti, impostare l'intervallo di aggiornamento su 5 minuti. Non è necessario aggiornare i dati nella partizione cronologica. Inoltre, è necessario scrivere del codice per consolidare la partizione month più recente nella partizione cronologica e creare una nuova partizione month più recente.


## <a name="connect-power-bi-to-analysis-services"></a>Connetti Power BI a Analysis Services

1. **Connettersi al Analysis server di Azure usando il connettore di database Azure Analysis Services** -scegliere la **modalità Live** e connettersi all'istanza di Azure Analysis Services come illustrato nello screenshot seguente:

   ![Ottenere dati da Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Caricare i dati e generare report** : usando i dati caricati in precedenza, creare grafici per segnalare la temperatura e la piovosità. Poiché si sta creando una connessione dinamica, le query devono essere eseguite sui dati nel modello di Azure Analysis Services distribuito nel passaggio precedente. I grafici di temperatura verranno aggiornati entro cinque minuti dopo il caricamento dei nuovi dati in Azure Cosmos DB.

   ![Caricare i dati e generare report](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Power BI, vedere [Introduzione a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Connetti Qlik Sense per Azure Cosmos DB e visualizzare i dati](visualize-qlik-sense.md)