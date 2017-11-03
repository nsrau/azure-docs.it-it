---
title: Esportare da Azure Application Insights usando l'analisi di flusso | Documentazione Microsoft
description: "L'analisi di flusso può trasformare, filtrare e instradare continuativamente i dati esportati da Application Insights."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 978af1a57a5fc3d9c95d517288a074c636874984
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Usare l'analisi di flusso per elaborare dati esportati da Application Insights
L'[analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) è lo strumento ideale per elaborare dati [esportati da Application Insights](app-insights-export-telemetry.md). L'analisi di flusso può eseguire il pull di dati da un'ampia gamma di origini. Può trasformare e filtrare i dati e quindi instradarli a molti sink diversi.

In questo esempio verrà creato un adattatore che recupera dati da Application Insights, rinomina ed elabora alcuni dei campi e invia tramite pipe i dati in Power BI.

> [!WARNING]
> Esistono [modi consigliati migliori e più semplici per visualizzare dati di Application Insights in Power BI](app-insights-export-power-bi.md). Il percorso descritto qui è solo un esempio per mostrare come elaborare dati esportati.
> 
> 

![Diagramma a blocchi per l'esportazione tramite SA in PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Creare l'archivio in Azure
L'esportazione continua invia sempre i dati a un account di Archiviazione di Azure, pertanto è prima necessario creare l'archivio.

1. Creare un account di archiviazione "classico" per la sottoscrizione nel [portale di Azure](https://portal.azure.com).
   
   ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione](./media/app-insights-export-stream-analytics/030.png)
2. Creare un contenitore
   
    ![Nel nuovo archivio selezionare Contenitori, fare clic sul riquadro Contenitori e quindi su Aggiungi](./media/app-insights-export-stream-analytics/040.png)
3. Copiare la chiave di accesso alle risorse di archiviazione.
   
    Sarà presto necessaria per configurare l'input per il servizio di analisi di flusso.
   
    ![Nella risorsa di archiviazione aprire Impostazioni, Chiavi ed eseguire una copia della chiave di accesso primaria](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Avviare l'esportazione continua nell'archiviazione di Azure
[Esportazione continua](app-insights-export-telemetry.md) sposta i dati da Application Insights nell'archiviazione di Azure.

1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.
   
    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-export-stream-analytics/050.png)
2. Creare un'esportazione continua.
   
    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-export-stream-analytics/060.png)

    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-export-stream-analytics/070.png)

    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-export-stream-analytics/080.png)

1. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Verranno restituiti i dati di telemetria e sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md). 
   
    I dati verranno inoltre esportati nell'archivio. 
2. Esaminare i dati esportati. In Visual Studio, scegliere **Visualizza/Cloud Explorer**e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione. 

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è usare l'analisi di flusso per spostare i dati in un Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Creare un'istanza di analisi di flusso di Azure
Nel [portale di Azure classico](https://manage.windowsazure.com/)selezionare il servizio di analisi di flusso di Azure e creare un nuovo processo di analisi di flusso:

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

Quando viene creato il nuovo processo, espanderne i dettagli:

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>Impostare il percorso BLOB
Impostarlo in modo da accettare l'input dal BLOB di esportazione continua:

![](./media/app-insights-export-stream-analytics/120.png)

A questo punto è necessaria la chiave di accesso primaria dell'account di archiviazione, di cui si è preso nota in precedenza. Impostarla come chiave dell'account di archiviazione.

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>Impostare lo schema prefisso percorso
![](./media/app-insights-export-stream-analytics/140.png)

**Assicurarsi di impostare il formato della data su AAAA-MM-GG (con i trattini).**

Lo schema prefisso percorso specifica dove l'analisi di flusso trova i file di input nell'archivio. È necessario configurarlo in modo che corrisponda alla modalità di archiviazione dei dati dell'esportazione continua. Impostarlo come segue:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Esempio:

* `webapplication27` è il nome della risorsa di Application Insights, **tutto minuscolo**.
* `1234...` è la chiave di strumentazione della risorsa di Application Insights, **senza trattini**. 
* `PageViews` è il tipo di dati da analizzare. I tipi disponibili dipendono dal filtro impostato nell'esportazione continua. Esaminare i dati esportati per vedere gli altri tipi disponibili e vedere il [modello di dati di esportazione](app-insights-export-data-model.md).
* `/{date}/{time}` è uno schema scritto letteralmente.

> [!NOTE]
> Controllare lo spazio di archiviazione per assicurarsi di ottenere il percorso corretto.
> 
> 

### <a name="finish-initial-setup"></a>Completare l'installazione iniziale
Verificare il formato di serializzazione:

![Confermare e chiudere la procedura guidata](./media/app-insights-export-stream-analytics/150.png)

Chiudere la procedura guidata e attendere il completamento dell'installazione.

> [!TIP]
> Utilizzare il comando di esempio per scaricare alcuni dati. Utilizzare come esempio di test per eseguire il debug della query.
> 
> 

## <a name="set-the-output"></a>Visualizzare l'output
Selezionare il processo e impostare l'output.

![Selezionare il nuovo canale, fare clic su Output, su Aggiungi e quindi su Power BI](./media/app-insights-export-stream-analytics/160.png)

Fornire l’ **account aziendale o dell’istituto di istruzione** per autorizzare l'analisi di flusso per l’accesso alla risorsa di Power BI. Creare quindi un nome per l'output e per il set di dati Power BI e la tabella di destinazione.

![Inventare tre nomi](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>Impostare la query
La query gestisce la conversione dall'input all'output.

![Selezionare il processo e fare clic su Query. Incollare l'esempio.](./media/app-insights-export-stream-analytics/180.png)

Utilizzare la funzione Test per verificare di ottenere l'output corretto. Assegnare i dati di esempio presenti nella pagina di input. 

### <a name="query-to-display-counts-of-events"></a>Query per visualizzare i conteggi degli eventi
Incollare questa query:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input è l'alias assegnato all'input del flusso
* pbi-output è l'alias dell'output definito
* Utilizziamo [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) perché il nome dell'evento si trova in una matrice JSON annidata. L'istruzione SELECT seleziona quindi il nome dell'evento insieme al conteggio del numero di istanze che presentano tale nome nel periodo di tempo indicato. La clausola [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) raggruppa gli elementi in periodi di tempo di 1 minuto.

### <a name="query-to-display-metric-values"></a>Query per visualizzare i valori delle metriche
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Questa query entra nella telemetria delle metriche per ottenere l'ora dell'evento e il valore della metrica. I valori delle metriche sono all'interno di una matrice, pertanto si utilizza il modello OUTER APPLY GetElements per estrarre le righe. "myMetric" è il nome della metrica in questo caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Query per includere i valori delle proprietà delle dimensioni
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Questa query include i valori delle proprietà delle dimensioni senza dipendere da una dimensione specifica in un indice fissato nella matrice di dimensioni.

## <a name="run-the-job"></a>Eseguire il processo
Per la data di inizio del processo, è possibile selezionare una data nel passato. 

![Selezionare il processo e fare clic su Query. Incollare l'esempio.](./media/app-insights-export-stream-analytics/190.png)

Attendere fino al termine dell'esecuzione del processo.

## <a name="see-results-in-power-bi"></a>Visualizzare i risultati in Power BI
> [!WARNING]
> Esistono [modi consigliati migliori e più semplici per visualizzare dati di Application Insights in Power BI](app-insights-export-power-bi.md). Il percorso descritto qui è solo un esempio per mostrare come elaborare dati esportati.
> 
> 

Aprire Power BI con l’account aziendale o dell’istituto di istruzione e selezionare il set di dati e la tabella definiti come output del processo di Analisi di flusso.

![In Power BI selezionare il set di dati e i campi.](./media/app-insights-export-stream-analytics/200.png)

È ora possibile usare questo set di dati nei report e nei dashboard in [Power BI](https://powerbi.microsoft.com).

![In Power BI selezionare il set di dati e i campi.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Dati non visualizzati
* Verificare di aver [impostato il formato di data](#set-path-prefix-pattern) correttamente su AAAA-MM-GG (con i trattini).

## <a name="video"></a>Video
Noam Ben Zeev mostra come elaborare dati esportati usando l'analisi di flusso.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Esportazione continua](app-insights-export-telemetry.md)
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

