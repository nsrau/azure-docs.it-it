---
title: Creare un dashboard in tempo reale usando Azure Cosmos DB, Azure Analysis Services e Power BICreate a real-time dashboard using Azure Cosmos DB, Azure Analysis Services, and Power BI
description: Informazioni su come creare un dashboard meteo in tempo reale in Power BI usando Azure Cosmos DB e Azure Analysis Services.Learn how to create a live weather dashboard in Power BI using Azure Cosmos DB and Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376593"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Creare un dashboard in tempo reale usando Database Cosmos di Azure e Power BICreate a real-time dashboard using Azure Cosmos DB and Power BI

Questo articolo descrive i passaggi necessari per creare un dashboard meteo in tempo reale in Power BI usando Azure Cosmos DB e Azure Analysis Services.This article describes the steps required to create a live weather dashboard in Power BI using Azure Cosmos DB and Azure Analysis Services. Nel dashboard di Power BI verranno visualizzati i grafici per visualizzare informazioni in tempo reale sulla temperatura e le precipitazioni in un'area.

## <a name="reporting-scenarios"></a>Scenari di reporting

Esistono diversi modi per configurare i dashboard di report sui dati archiviati in Azure Cosmos DB. A seconda dei requisiti di stantio e delle dimensioni dei dati, nella tabella seguente viene descritta l'impostazione dei report per ogni scenario:


|Scenario |Configurazione |
|---------|---------|
|1. Generazione di report ad hoc (nessun aggiornamento)    |  [Connettore DB Cosmos di Power BI con modalità di importazionePower BI Azure Cosmos DB connector with import mode](powerbi-visualize.md)       |
|2. Generazione di report ad hoc con aggiornamento periodico   |  [Connettore DB Cosmos di Azure di Power BI con modalità di importazione (aggiornamento periodico pianificato)Power BI Azure Cosmos DB connector with import mode (Scheduled periodic refresh)](powerbi-visualize.md)       |
|3. Creazione di report su set di dati di grandi dimensioni (< 10 GB)     |  Connettore DB Cosmos di Power BI con aggiornamento incrementale       |
|4. Segnalazione in tempo reale su set di dati di grandi dimensioni    |  Connettore di Power BI Azure Analysis Services con query diretta , Azure Analysis Services (connettore database Cosmos di Azure)Power BI Azure Analysis Services connector with direct query - Azure Analysis Services (Azure Cosmos DB connector)       |
|5. Creazione di report sui dati in tempo reale con aggregati     |  [Connettore Di Power BI Spark con query diretta, Azure Databricks e connettore Spark cosmos DB.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Creazione di report sui dati in tempo reale con aggregazioni su set di dati di grandi dimensioni   |  Connettore di Power BI Azure Analysis Services con query diretta, Azure Analysis Services, Azure Databricks e connettore Spark Cosmos DB.       |

Gli scenari 1 e 2 possono essere facilmente configurati usando il connettore Azure Cosmos DB Power BI. In questo articolo vengono descritte le impostazioni per gli scenari 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI con aggiornamento incrementale

Power BI ha una modalità in cui è possibile configurare l'aggiornamento incrementale. Questa modalità elimina la necessità di creare e gestire partizioni di Azure Analysis Services.This mode eliminates the need to create and manage Azure Analysis Services partitions. L'aggiornamento incrementale può essere impostato per filtrare solo gli aggiornamenti più recenti in set di dati di grandi dimensioni. Tuttavia, questa modalità funziona solo con il servizio Power BI Premium con una limitazione del set di dati di 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Connettore di Power BI Azure Analysis - Azure Analysis Services

Azure Analysis Services offre una piattaforma completamente gestita come servizio che ospita modelli di dati di livello enterprise nel cloud. Set di dati massicce possono essere caricati da Database Cosmos di Azure in Azure Analysis Services.Massive data sets can be loaded from Azure Cosmos DB into Azure Analysis Services. Per evitare di eseguire sempre query sull'intero set di dati, i set di dati possono essere suddivisi in partizioni di Azure Analysis Services, che possono essere aggiornate in modo indipendente a frequenze diverse.

## <a name="power-bi-incremental-refresh"></a>Aggiornamento incrementale di Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Inserire i dati meteo in Azure Cosmos DBIngest weather data into Azure Cosmos DB

Configurare una pipeline di inserimento per caricare i [dati meteo](https://catalog.data.gov/dataset/local-weather-archive) in Azure Cosmos DB. È possibile configurare un processo di [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) per caricare periodicamente i dati meteo più recenti in Azure Cosmos DB usando il sink di origine HTTP e cosmos DB.You can set up an Azure Data Factory (ADF) job to periodically load the latest weather data into Azure Cosmos DB using the HTTP Source and Cosmos DB sink.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Connettere Power BI a Azure Cosmos DBConnect Power BI to Azure Cosmos DB

1. **Connettere l'account Cosmos** di Azure a Power BI: aprire Power BI Desktop e usare il connettore DB Cosmos di Azure per selezionare il database e il contenitore corretti.

   ![Connettore Power BI di Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Configurare l'aggiornamento incrementale:** seguire i passaggi descritti [nell'aggiornamento incrementale con Power BI](/power-bi/service-premium-incremental-refresh) per configurare l'aggiornamento incrementale per il set di dati. Aggiungere i parametri RangeStart e RangeEnd come illustrato nella schermata seguente:Add the **RangeStart** and **RangeEnd** parameters as shown in the following screenshot:

   ![Configurare i parametri di intervallo](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Poiché il set di dati ha una colonna Date in formato testo, i parametri **RangeStart** e **RangeEnd** devono essere trasformati per utilizzare il filtro seguente. Nel riquadro **Editor avanzato** modificare la query aggiungere il testo seguente per filtrare le righe in base ai parametri RangeStart e RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   A seconda della colonna e del tipo di dati presenti nel set di dati di origine, è possibile modificare di conseguenza i campi RangeStart e RangeEnd

   
   |Proprietà  |Tipo di dati  |Filtro  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) e [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0, 0)))       |
   |Data (ad esempio:- 2019-08-19)     |   string      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") e [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Data (ad esempio:- 2019-08-11 12:00:00)   |  string       |  [Document.date]> DateTime.ToText(RangeStart," aaaa-mm-gg HH:mm:ss") e [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-gg HH:mm:ss")       |


1. **Definire i criteri** di aggiornamento - Definire i criteri di aggiornamento passando alla scheda **Aggiornamento incrementale** nel menu di **scelta rapida** della tabella. Impostare i criteri di aggiornamento per l'aggiornamento **ogni giorno** e archiviare i dati dell'ultimo mese.

   ![Definire i criteri di aggiornamentoDefine refresh policy](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignorare l'avviso che indica che non è possibile confermare che *la query M non può essere sottoposta a piegatura.* Il connettore Cosmos DB di Azure esegue la piegatura delle query di filtro.

1. **Caricare i dati e generare i report:** utilizzando i dati caricati in precedenza, creare i grafici per creare report su temperatura e precipitazioni.

   ![Caricare i dati e generare report](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Pubblicare il report in Power BI Premium:** poiché l'aggiornamento incrementale è una funzionalità Solo Premium, la finestra di dialogo di pubblicazione consente solo la selezione di un'area di lavoro in base alla capacità Premium.Publish the report to Power BI Premium - Since incremental refresh is a Premium only feature, the publish dialog only allows ing a workspace on Premium capacity. Il primo aggiornamento può richiedere più tempo per l'importazione dei dati cronologici. Gli aggiornamenti dei dati successivi sono molto più rapidi perché usano l'aggiornamento incrementale.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Connettore di Power BI Azure Analysis - Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Inserire i dati meteo in Azure Cosmos DBIngest weather data into Azure Cosmos DB 

Configurare una pipeline di inserimento per caricare i [dati meteo](https://catalog.data.gov/dataset/local-weather-archive) in Azure Cosmos DB. È possibile configurare un processo di Azure Data Factory (ADF) per caricare periodicamente i dati meteo più recenti in Azure Cosmos DB usando l'origine HTTP e il sink cosmos DB.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Connettere Azure Analysis Services all'account Cosmos di AzureConnect Azure Analysis Services to Azure Cosmos account

1. **Creare un nuovo cluster** - di Azure Analysis Services[Creare un'istanza dei servizi di analisi di Azure](../analysis-services/analysis-services-create-server.md) nella stessa area dell'account Cosmos di Azure e del cluster Databricks.Create a new Azure Analysis Services cluster Create an instance of Azure Analysis services in the same region as the Azure Cosmos account and the Databricks cluster.

1. **Creare un nuovo progetto tabulare** -  di Analysis Services in Visual Studio Installare SQL Server Data Tools (SSDT) e creare un progetto tabulare di Analysis Services in Visual Studio.Create a new Analysis Services Tabular Project in Visual Studio[Install the SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) and create an Analysis Services Tabular project in Visual Studio.

   ![Creare un progetto di Azure Analysis ServicesCreate Azure Analysis Services project](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Scegliere l'istanza Area di lavoro **integrata** e impostare il livello di compatibilità su **SQL Server 2017 / Azure Analysis Services (1400)**

   ![Progettazione modelli tabulari di Azure Analysis ServicesAzure Analysis Services tabular model designer](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Aggiungere l'origine dati del database Cosmos** di Azure - Passare a **Models**> Data Sources New Data Source e aggiungere l'origine dati Azure Cosmos DB come illustrato nella schermata seguente:Add the Azure Cosmos DB data source - Navigate to Models**Data Sources** > New Data**Source** and add the Azure Cosmos DB data source as shown in the following screenshot:

   ![Aggiungi origine dati Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Connettersi a Database Cosmos di Azure fornendo **l'URI dell'account,** il **nome**del database e il nome del **contenitore.** È ora possibile visualizzare i dati dal contenitore Cosmos di Azure vengono importati in Power BI.

   ![Anteprima dei dati del database Cosmos di Azure](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Costruire il modello di Analysis Services** - aprire l'editor di query, eseguire le operazioni necessarie per ottimizzare il set di dati caricato:Construct the Analysis Services model - Open the query editor, perform the required operations to optimize the loaded data set:

   * Estrarre solo le colonne relative alle condizioni atmosferiche (temperatura e precipitazioni)

   * Estrarre le informazioni sul mese dalla tabella. Questi dati sono utili per creare partizioni come descritto nella sezione successiva.

   * Convertire le colonne di temperatura in numero

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

   Inoltre, modificare il tipo di dati delle colonne di temperatura in decimale per assicurarsi che questi valori possono essere tracciati in Power BI.

1. **Creare partizioni di analisi** di Azure: creare partizioni in Azure Analysis Services per dividere il set di dati in partizioni logiche che possono essere aggiornate in modo indipendente e a frequenze diverse. In questo esempio vengono create due partizioni che suddividerebbero il set di dati nei dati del mese più recente e in tutto il resto.

   ![Creare partizioni di Analysis ServicesCreate analysis services partitions](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Creare le due partizioni seguenti in Azure Analysis Services:Create the following two partitions in Azure Analysis Services:

   * **Ultimo mese** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Storico** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Distribuire il modello in Azure Analysis Server** - Fare clic con il pulsante destro del mouse sul progetto di Azure Analysis Services e scegliere **Distribuisci**. Aggiungere il nome del server nel riquadro delle proprietà del server di **distribuzione.**

   ![Distribuire il modello di Azure Analysis ServicesDeploy Azure Analysis Services model](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Configurare gli aggiornamenti e le unioni delle partizioni:** Azure Analysis Services consente l'elaborazione indipendente delle partizioni. Poiché si desidera che la partizione **Ultimo mese** venga costantemente aggiornata con i dati più recenti, impostare l'intervallo di aggiornamento su 5 minuti. Non è necessario aggiornare i dati nella partizione cronologica. Inoltre, è necessario scrivere codice per consolidare la partizione del mese più recente nella partizione cronologica e creare una nuova partizione del mese più recente.


## <a name="connect-power-bi-to-analysis-services"></a>Connettere Power BI ad Analysis Services

1. **Connettersi a Azure Analysis Server usando il connettore** del database di Azure Analysis Services - scegliere la **modalità attiva** e connettersi all'istanza di Azure Analysis Services, come illustrato nella schermata seguente:Connect to the Azure Analysis Server using the Azure Analysis Services database Connector - Choose the Live mode and connect to the Azure Analysis Services instance as shown in the following screenshot:

   ![Ottenere dati da Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Caricare i dati e generare report:** utilizzando i dati caricati in precedenza, creare grafici per creare report su temperatura e precipitazioni. Poiché si crea una connessione attiva, le query devono essere eseguite sui dati nel modello di Azure Analysis Services distribuito nel passaggio precedente. I grafici della temperatura verranno aggiornati entro cinque minuti dal caricamento dei nuovi dati in Azure Cosmos DB.

   ![Caricare i dati e generare report](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Power BI, vedere [Introduzione a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Connettere Qlik Sense ad Azure Cosmos DB e visualizzare i dati](visualize-qlik-sense.md)