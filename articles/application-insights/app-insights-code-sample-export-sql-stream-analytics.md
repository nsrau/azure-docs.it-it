<properties 
	pageTitle="Procedura dettagliata: esportare i dati di telemetria nel database SQL da Application Insights"
	description="Scrivere il codice per la propria analisi dei dati di telemetria in Application Insights usando la funzionalità di esportazione continua."
	services="application-insights"
	documentationCenter=""
	authors="noamben"
	manager="douge"/>

<tags 
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="awills"/>
 
# Procedura dettagliata: Eseguire l'esportazione in SQL da Application Insights tramite l'analisi di flusso

Questo articolo illustra come spostare i dati di telemetria da [Application Insights di Visual Studio][start] in un database SQL di Azure usando l'[esportazione continua][export] e l'[analisi di flusso di Azure](http://azure.microsoft.com/services/stream-analytics/).

L'esportazione continua sposta i dati di telemetria in Archiviazione di Azure in formato JSON. Gli oggetti JSON verranno analizzati con l'analisi di flusso di Azure e verranno create righe in una tabella di database.

Più in generale, l'esportazione continua consente di eseguire la propria analisi dei dati di telemetria che le app inviano ad Application Insights. È possibile adattare questo esempio di codice per eseguire altre operazioni con i dati di telemetria esportati, ad esempio l'aggregazione dei dati e la relativa pubblicazione nelle applicazioni di visualizzazione PowerBI.

Si partirà dal presupposto che si disponga già dell'app che si vuole monitorare.


In questo esempio verranno usati i dati relativi alle visualizzazioni pagina, ma gli stessi criteri possono essere estesi facilmente ad altri tipi di dati, ad esempio eccezioni ed eventi personalizzati.


## Aggiungere Application Insights SDK

Per monitorare l'applicazione, [aggiungere un Application Insights SDK][start] all'applicazione. Esistono diversi SDK e strumenti di supporto per diverse piattaforme, linguaggi e IDE. È possibile monitorare le pagine Web, i server Web ASP.NET o Java e i dispositivi mobili di diversi tipi. Tutti gli SDK inviano dati di telemetria al [portale di Application Insights][portal], dove è possibile usare potenti strumenti di analisi e diagnostica ed esportare i dati nell'archivio.

Attività iniziali

1. Ottenere un [account in Microsoft Azure](http://azure.microsoft.com/pricing/).
2. Nel [portale di Azure][portal] aggiungere una nuova risorsa di Application Insights per la propria app:

    ![Scegliere Nuovo, quindi Servizi per gli sviluppatori, Application Insights e scegliere il tipo di applicazione](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    Il tipo di app e la sottoscrizione potrebbero essere diversi.
3. Aprire Avvio rapido per scoprire come configurare l'SDK per il proprio tipo di app.

    ![Scegliere Avvio rapido e seguire le istruzioni](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Se il tipo di app non è elencato, dare uno sguardo alla pagina [Introduzione][start].

4. In questo esempio viene eseguito il monitoraggio di un'app Web, quindi è possibile usare gli strumenti di Azure in Visual Studio per installare l'SDK. A questo punto, specificare il nome della risorsa di Application Insights:

    ![In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse e scegliere Aggiungi Application Insights. In Invia telemetria a scegliere di creare una nuova risorsa o usarne una esistente.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Pubblicare l'app e controllare i dati di telemetria visualizzati nella risorsa di Application Insights.


## Creare l'archivio in Azure

L'esportazione continua invia sempre i dati a un account di Archiviazione di Azure, pertanto è prima necessario creare l'archivio.

1. Creare un account di archiviazione per la sottoscrizione nel [portale di Azure][portal].

    ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Creare un contenitore

    ![Nel nuovo archivio selezionare Contenitori e quindi Aggiungi](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Copiare la chiave di accesso alle risorse di archiviazione.

    Sarà presto necessaria per configurare l'input per il servizio di analisi di flusso.

    ![Nella risorsa di archiviazione aprire Impostazioni, Chiavi ed eseguire una copia della chiave di accesso primaria](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Avviare l'esportazione continua nell'archiviazione di Azure

1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.

    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Creare un'esportazione continua.

    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


3. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Quando verranno restituiti i dati di telemetria, sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md).

    I dati verranno inoltre esportati nell'archivio.

4. Esaminare i dati esportati. In Visual Studio, scegliere **Visualizza/Cloud Explorer** e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).

    ![In Visual Studio aprire Esplora server, Azure, Archiviazione](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione.

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è usare l'analisi di flusso per spostare i dati in un database SQL. Sarà quindi più semplice eseguire molte query interessanti.

## Creare un database SQL di Azure

Dalla sottoscrizione nel [portale di Azure][portal] creare il database (e un nuovo server, a meno che non sia già disponibile) in cui verranno scritti i dati.

![Nuovo, Dati, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Assicurarsi che il server di database consenta di accedere ai servizi di Azure:


![Sfoglia, Server, il proprio server, Impostazioni, Firewall, Consenti l'accesso a Servizi di Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Creare una tabella nel database SQL di Azure

Connettersi al database creato nella sezione precedente con lo strumento di gestione preferito. In questa procedura dettagliata verranno usati gli [strumenti di gestione di SQL Server ](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Creare una nuova query ed eseguire il codice T-SQL seguente:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

In questo esempio vengono usati i dati delle visualizzazioni pagina. Per visualizzare gli altri dati disponibili, esaminare l'output JSON e vedere il [modello di dati di esportazione](app-insights-export-data-model.md).

## Creare un'istanza di analisi di flusso di Azure

Nel [portale di Azure classico](https://manage.windowsazure.com/) selezionare il servizio di analisi di flusso di Azure e creare un nuovo processo di analisi di flusso:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Quando viene creato il nuovo processo, espanderne i dettagli:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Impostare il percorso BLOB

Impostarlo in modo da accettare l'input dal BLOB di esportazione continua:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

A questo punto è necessaria la chiave di accesso primaria dell'account di archiviazione, di cui si è preso nota in precedenza. Impostarla come chiave dell'account di archiviazione.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Impostare lo schema prefisso percorso 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Assicurarsi di impostare il formato della data su AAAA-MM-GG (con i trattini).

Lo schema prefisso percorso specifica il modo in cui l'analisi di flusso trova i file di input nell'archivio. È necessario configurarlo in modo che corrisponda alla modalità di archiviazione dei dati dell'esportazione continua. Impostarlo come segue:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Esempio:

* `webapplication27` è il nome della risorsa di Application Insights, **tutto minuscolo**. 
* `1234...` è la chiave di strumentazione della risorsa di Application Insights **con i trattini rimossi**. 
* `PageViews` è il tipo di dati da analizzare. I tipi disponibili dipendono dal filtro impostato nell'esportazione continua. Esaminare i dati esportati per vedere gli altri tipi disponibili e vedere il [modello di dati di esportazione](app-insights-export-data-model.md).
* `/{date}/{time}` è uno schema scritto letteralmente.

Per ottenere il nome e la chiave di strumentazione (iKey) della risorsa di Application Insights, aprire Essentials nella relativa pagina di panoramica o aprire le impostazioni.

#### Completare l'installazione iniziale

Verificare il formato di serializzazione:

![Confermare e chiudere la procedura guidata](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Chiudere la procedura guidata e attendere il completamento dell'installazione.

## Impostare la query

Aprire la sezione delle query:

![In Analisi di flusso selezionare Query](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Sostituire la query predefinita con:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Tenere presente che le prime proprietà sono specifiche dei dati relativi alle visualizzazioni pagina. Le esportazioni di altri tipi di dati di telemetria avranno proprietà diverse. Vedere il [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà](app-insights-export-data-model.md).

## Configurare l'output nel database

Selezionare SQL come output.

![In Analisi di flusso selezionare Output](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Specificare il database SQL.


![Inserire i dettagli del database](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Chiudere la procedura guidata e attendere la notifica di configurazione dell'output.

## Avviare l'elaborazione

Avviare il processo dalla barra delle azioni:

![In Analisi di flusso fare clic su Avvia](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

È possibile scegliere se avviare l'elaborazione dei dati a partire dai dati correnti o se includere i dati precedenti. La seconda opzione è utile se l'esportazione continua è già stata eseguita per un determinato periodo di tempo.


![In Analisi di flusso fare clic su Avvia](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Dopo alcuni minuti, tornare agli strumenti di gestione di SQL Server e controllare il flusso dei dati. Usare ad esempio una query simile alla seguente:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Articoli correlati

* [Esportare in SQL usando un ruolo di lavoro](app-insights-code-sample-export-telemetry-sql-database.md)
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)
* [Esportazione continua in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=September15_HO1-->