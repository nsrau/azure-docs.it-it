---
title: Esportare in Power BI da Application Insights
description: Articoli
services: application-insights
documentationcenter: ''
author: noamben
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/05/2016
ms.author: awills

---
# Feed di Power BI da Application Insights
[Power BI](http://www.powerbi.com/) è una suite di strumenti di analisi business che consente di analizzare i dati e condividere informazioni dettagliate. Dashboard completi sono disponibili in tutti i dispositivi. È possibile combinare dati di diverse origini, inclusi i dati di [Visual Studio Application Insights](app-insights-overview.md).

Per iniziare, vedere [Visualizzare i dati di Application Insights in Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/).

Il dashboard iniziale può essere personalizzato unendo i grafici di Application Insights con i grafici di altre origini. È disponibile una raccolta di visualizzazioni nella quale è possibile ottenere più grafici e ogni grafico include parametri che possono essere impostati.

![](./media/app-insights-export-power-bi/010.png)

Dopo l'importazione iniziale, il dashboard e i report continuano a essere aggiornati ogni giorno. È possibile controllare la pianificazione dell'aggiornamento nel set di dati.

**Campionamento.** Se l'applicazione invia una grande quantità di dati ed è in uso Application Insights SDK per ASP.NET 2.0.0 Beta3 o versioni successive, la funzionalità del campionamento adattivo può operare e inviare solo una percentuale dei dati di telemetria. La stessa considerazione vale se il campionamento è stato impostato manualmente nell'SDK o durante l'inserimento. [Altre informazioni sul campionamento.](app-insights-sampling.md)

## Metodi alternativi per la visualizzazione dei dati di Application Insights
* Quando non è necessario visualizzare dati non Azure, può essere consigliabile usare i [dashboard di Azure contenenti i grafici di Application Insights](app-insights-dashboards.md). Ad esempio, se si desidera impostare un dashboard dei grafici di Application Insights che esegue il monitoraggio di diversi componenti di un sistema, anche con alcuni monitoraggi dei servizi di Azure, un dashboard di Azure rappresenta la soluzione ideale. Per impostazione predefinita, l'aggiornamento viene eseguito con una maggiore frequenza. 
* [Esportazione continua](app-insights-export-telemetry.md) copia i dati in ingresso nell'archiviazione di Azure da cui possono essere spostati ed elaborati nel modo desiderato.
* [Dati di analisi](app-insights-analytics.md) consente di eseguire query complesse sui dati non elaborati mantenuti in Application Insights.

## Creare il proprio adattatore Power BI usando Analisi di flusso
Il pacchetto di contenuto di Power BI per Application Insights consente di visualizzare un utile subset dei dati di telemetria dell'app probabilmente sufficiente per le proprie esigenze. Tuttavia, se si vuole ottenere una maggior quantità di dati di telemetria o calcolare alcuni dati in base a dati di telemetria non elaborati, è possibile creare un adattatore mediante il servizio Analisi di flusso di Azure.

In questo schema, i dati vengono esportati da Application Insights all'archiviazione di Azure. [Analisi di flusso](https://azure.microsoft.com/services/stream-analytics/) effettua il pool dei dati, rinomina ed elabora alcuni campi e invia una pipe dei dati in Power BI. Analisi di flusso è un servizio in grado di filtrare, aggregare ed eseguire calcoli su un flusso continuo di dati.

![Diagramma a blocchi per l'esportazione tramite SA in PBI](./media/app-insights-export-power-bi/020.png)

> [!TIP]
> **Non è necessario seguire la procedura della parte rimanente di questo articolo** usando Analisi di flusso per visualizzare i dati di Application Insights in Power BI. Esiste un modo molto più semplice. È possibile [usare l'adattatore gratuito](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/). Seguire le istruzioni della parte rimanente di questo articolo solo se l'adattatore non consente di ottenere tutti i dati desiderati oppure se si vuole definire aggregazioni o funzioni personalizzate da eseguire nei dati.
> 
> 

### Creare l'archivio in Azure
L'esportazione continua invia sempre i dati a un account di Archiviazione di Azure, pertanto è prima necessario creare l'archivio.

1. È stato provato [Power BI Power Pack per Application Insights](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)? Se è sufficiente per le proprie esigenze, non è necessario procedere con la parte rimanente di questo articolo.
2. Creare un account di archiviazione "classico" per la sottoscrizione nel [portale di Azure](https://portal.azure.com).
   
   ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione](./media/app-insights-export-power-bi/030.png)
3. Creare un contenitore
   
    ![Nel nuovo archivio selezionare Contenitori, fare clic sul riquadro Contenitori e quindi su Aggiungi](./media/app-insights-export-power-bi/040.png)
4. Copiare la chiave di accesso alle risorse di archiviazione.
   
    Sarà presto necessaria per configurare l'input per il servizio di analisi di flusso.
   
    ![Nella risorsa di archiviazione aprire Impostazioni, Chiavi ed eseguire una copia della chiave di accesso primaria](./media/app-insights-export-power-bi/045.png)

### Avviare l'esportazione continua nell'archiviazione di Azure
[Esportazione continua](app-insights-export-telemetry.md) sposta i dati da Application Insights nell'archiviazione di Azure.

1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.
   
    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-export-power-bi/050.png)
2. Creare un'esportazione continua.
   
    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-export-power-bi/060.png)

    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-export-power-bi/070.png)

    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-export-power-bi/080.png)

1. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Quando verranno restituiti i dati di telemetria, sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md).
   
    I dati verranno inoltre esportati nell'archivio.
2. Esaminare i dati esportati. In Visual Studio, scegliere **Visualizza/Cloud Explorer** e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).
   
    ![](./media/app-insights-export-power-bi/04-data.png)
   
    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione.

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è usare l'analisi di flusso per spostare i dati in un Power BI.

### Creare un'istanza di analisi di flusso di Azure
Nel [portale di Azure classico](https://manage.windowsazure.com/) selezionare il servizio di analisi di flusso di Azure e creare un nuovo processo di analisi di flusso:

![](./media/app-insights-export-power-bi/090.png)

![](./media/app-insights-export-power-bi/100.png)

Quando viene creato il nuovo processo, espanderne i dettagli:

![](./media/app-insights-export-power-bi/110.png)

#### Impostare il percorso BLOB
Impostarlo in modo da accettare l'input dal BLOB di esportazione continua:

![](./media/app-insights-export-power-bi/120.png)

A questo punto è necessaria la chiave di accesso primaria dell'account di archiviazione, di cui si è preso nota in precedenza. Impostarla come chiave dell'account di archiviazione.

![](./media/app-insights-export-power-bi/130.png)

#### Impostare lo schema prefisso percorso
![](./media/app-insights-export-power-bi/140.png)

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

#### Completare l'installazione iniziale
Verificare il formato di serializzazione:

![Confermare e chiudere la procedura guidata](./media/app-insights-export-power-bi/150.png)

Chiudere la procedura guidata e attendere il completamento dell'installazione.

> [!TIP]
> Utilizzare il comando di esempio per scaricare alcuni dati. Utilizzare come esempio di test per eseguire il debug della query.
> 
> 

### Visualizzare l'output
Selezionare il processo e impostare l'output.

![Selezionare il nuovo canale, fare clic su Output, su Aggiungi e quindi su Power BI](./media/app-insights-export-power-bi/160.png)

Fornire l’**account aziendale o dell’istituto di istruzione** per autorizzare l'analisi di flusso per l’accesso alla risorsa di Power BI. Creare quindi un nome per l'output e per il set di dati Power BI e la tabella di destinazione.

![Inventare tre nomi](./media/app-insights-export-power-bi/170.png)

### Impostare la query
La query gestisce la conversione dall'input all'output.

![Selezionare il processo e fare clic su Query. Incollare l'esempio.](./media/app-insights-export-power-bi/180.png)

Utilizzare la funzione Test per verificare di ottenere l'output corretto. Assegnare i dati di esempio presenti nella pagina di input.

#### Query per visualizzare i conteggi degli eventi
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

#### Query per visualizzare i valori delle metriche
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

#### Query per includere i valori delle proprietà delle dimensioni
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

### Eseguire il processo
Per la data di inizio del processo, è possibile selezionare una data nel passato.

![Selezionare il processo e fare clic su Query. Incollare l'esempio.](./media/app-insights-export-power-bi/190.png)

Attendere fino al termine dell'esecuzione del processo.

### Visualizzare i risultati in Power BI
Aprire Power BI con l’account aziendale o dell’istituto di istruzione e selezionare il set di dati e la tabella definiti come output del processo di Analisi di flusso.

![In Power BI selezionare il set di dati e i campi.](./media/app-insights-export-power-bi/200.png)

È ora possibile usare questo set di dati nei report e nei dashboard in [Power BI](https://powerbi.microsoft.com).

![In Power BI selezionare il set di dati e i campi.](./media/app-insights-export-power-bi/210.png)

### Dati non visualizzati
* Verificare di aver [impostato il formato di data](#set-path-prefix-pattern) correttamente su AAAA-MM-GG (con i trattini).

### Video
Noam Ben Zeev spiega come esportare i dati in Power BI.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## Risorse correlate
* [Esportazione continua](app-insights-export-telemetry.md)
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Altri esempi e procedure dettagliate](app-insights-code-samples.md)

<!---HONumber=AcomDC_0420_2016-->