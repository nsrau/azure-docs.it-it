---
title: Esportare in SQL da Azure Application Insights | Documentazione Microsoft
description: Eseguire l'esportazione continua dei dati Application Insights in SQL tramite l'analisi di flusso.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
editor: mrbullwinkle
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: 8d008727d964df56d128265b632dafa4ab776f98
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Procedura dettagliata: Eseguire l'esportazione in SQL da Application Insights tramite l'analisi di flusso
Questo articolo illustra come spostare i dati di telemetria da [Azure Application Insights][start] in un database SQL di Azure usando l'[esportazione continua][export] e l'[analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/). 

L'esportazione continua sposta i dati di telemetria in Archiviazione di Azure in formato JSON. Gli oggetti JSON verranno analizzati con l'analisi di flusso di Azure e verranno create righe in una tabella di database.

Più in generale, l'esportazione continua consente di eseguire la propria analisi dei dati di telemetria che le app inviano ad Application Insights. È possibile adattare questo esempio di codice per eseguire altre operazioni con i dati di telemetria esportati, come l’aggregazione dei dati.

Si inizierà dal presupposto che si abbia già l'app che si vuole monitorare.

In questo esempio verranno usati i dati relativi alle visualizzazioni pagina, ma gli stessi criteri possono essere estesi facilmente ad altri tipi di dati, ad esempio eccezioni ed eventi personalizzati. 

## <a name="add-application-insights-to-your-application"></a>Aggiunta di Application Insights all'applicazione
Attività iniziali

1. [Installare Application Insights per le pagine Web](app-insights-javascript.md). 
   
    In questo esempio viene considerata l'elaborazione dei dati di visualizzazione della pagina dai browser client, ma è possibile anche impostare Application Insights per il lato server dell'app [Java](app-insights-java-get-started.md) o [ASP.NET](app-insights-asp-net.md) ed elaborare la richiesta, la dipendenza e altri dati di telemetria del server.
2. Pubblicare l'app e controllare i dati di telemetria visualizzati nella risorsa di Application Insights.

## <a name="create-storage-in-azure"></a>Creare l'archivio in Azure
L'esportazione continua invia sempre i dati a un account di Archiviazione di Azure, pertanto è prima necessario creare l'archivio.

1. Creare un account di archiviazione per la sottoscrizione nel [portale di Azure][portal].
   
    ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione Selezionare Classica, scegliere Crea. Specificare un nome di archiviazione.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Creare un contenitore
   
    ![Nel nuovo archivio selezionare Contenitori, fare clic sul riquadro Contenitori e quindi su Aggiungi](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. Copiare la chiave di accesso alle risorse di archiviazione.
   
    Sarà presto necessaria per configurare l'input per il servizio di analisi di flusso.
   
    ![Nella risorsa di archiviazione aprire Impostazioni, Chiavi ed eseguire una copia della chiave di accesso primaria](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Avviare l'esportazione continua nell'archiviazione di Azure
1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.
   
    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. Creare un'esportazione continua.
   
    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Verranno restituiti i dati di telemetria e sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md). 
   
    I dati verranno inoltre esportati nell'archivio. 
2. Esaminare i dati esportati, nel portale (scegliere **Esplora**, selezionare l'account di archiviazione, quindi **Contenitori**) o in Visual Studio. In Visual Studio, scegliere **Visualizza/Cloud Explorer**e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).
   
    ![In Visual Studio aprire Esplora server, Azure, Archiviazione](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione. 

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è usare l'analisi di flusso per spostare i dati in un database SQL. Sarà quindi più semplice eseguire molte query interessanti.

## <a name="create-an-azure-sql-database"></a>Creare un database SQL di Azure
Iniziando di nuovo dalla sottoscrizione nel [portale di Azure][portal], creare il database (e un nuovo server, se necessario) in cui si scriveranno i dati.

![Nuovo, Dati, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Assicurarsi che il server di database consenta di accedere ai servizi di Azure:

![Sfoglia, Server, il proprio server, Impostazioni, Firewall, Consenti l'accesso a Servizi di Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Creare una tabella nel database SQL di Azure
Connettersi al database creato nella sezione precedente con lo strumento di gestione preferito. In questa procedura dettagliata verranno usati gli [strumenti di gestione di SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

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

## <a name="create-an-azure-stream-analytics-instance"></a>Creare un'istanza di analisi di flusso di Azure
Dal [portale di Azure](https://portal.azure.com/), selezionare il servizio di Azure flusso Analitica e creare un nuovo processo di flusso Analitica:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

Quando viene creato il nuovo processo, selezionare **passare alla risorsa**.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Aggiungere un nuovo input

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

Impostarlo in modo da accettare l'input dal BLOB di esportazione continua:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

A questo punto è necessaria la chiave di accesso primaria dell'account di archiviazione, di cui si è preso nota in precedenza. Impostarla come chiave dell'account di archiviazione.

#### <a name="set-path-prefix-pattern"></a>Impostare lo schema prefisso percorso

**Assicurarsi di impostare il formato della data su AAAA-MM-GG (con i trattini).**

Lo schema prefisso percorso specifica il modo in cui l'analisi di flusso trova i file di input nell'archivio. È necessario configurarlo in modo che corrisponda alla modalità di archiviazione dei dati dell'esportazione continua. Impostarlo come segue:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Esempio:

* `webapplication27` è il nome della risorsa di Application Insights, **tutto minuscolo**. 
* `1234...` è la chiave di strumentazione della risorsa di Application Insights **con i trattini rimossi**. 
* `PageViews` è il tipo di dati da analizzare. I tipi disponibili dipendono dal filtro impostato nell'esportazione continua. Esaminare i dati esportati per vedere gli altri tipi disponibili e vedere il [modello di dati di esportazione](app-insights-export-data-model.md).
* `/{date}/{time}` è uno schema scritto letteralmente.

Per ottenere il nome e la chiave di strumentazione (iKey) della risorsa di Application Insights, aprire Essentials nella relativa pagina di panoramica o aprire le impostazioni.

> [!TIP]
> Utilizzare la funzione di esempio per verificare di aver impostato correttamente il percorso di input. In caso di errore: verificare che ci siano dati nell’archiviazione per l’intervallo di tempo esemplificativo che si seleziona. Modificare la definizione di input e controllare di impostare l'account di archiviazione, il prefisso del percorso e il formato di data corretto.
> 
> 
## <a name="set-query"></a>Impostare la query
Aprire la sezione delle query:

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
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Tenere presente che le prime proprietà sono specifiche dei dati relativi alle visualizzazioni pagina. Le esportazioni di altri tipi di dati di telemetria avranno proprietà diverse. Vedere il [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Configurare l'output nel database
Selezionare SQL come output.

![In Analisi di flusso selezionare Output](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

Specificare il database SQL.

![Inserire i dettagli del database](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

Chiudere la procedura guidata e attendere la notifica di configurazione dell'output.

## <a name="start-processing"></a>Avviare l'elaborazione
Avviare il processo dalla barra delle azioni:

![In Analisi di flusso fare clic su Avvia.](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

È possibile scegliere se avviare l'elaborazione dei dati a partire dai dati correnti o se includere i dati precedenti. La seconda opzione è utile se l'esportazione continua è già stata eseguita per un determinato periodo di tempo.

Dopo alcuni minuti, tornare agli strumenti di gestione di SQL Server e controllare il flusso dei dati. Usare ad esempio una query simile alla seguente:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Articoli correlati
* [Esportare in PowerBI usando l'analisi di flusso](app-insights-export-power-bi.md)
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)
* [Esportazione continua in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

