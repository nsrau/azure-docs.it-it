<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights: monitoraggio dell'integrità e dell'utilizzo di un'app

*Application Insights è disponibile in anteprima.*

Application Insights consente di monitorare un'applicazione live in base a:

-   **Disponibilità**: esecuzione di test automatici degli URL ogni pochi minuti da tutto il mondo.
-   **Prestazioni**: rilevamento e diagnostica dei problemi e delle eccezioni relativi alle prestazioni.
-   **Utilizzo**: possibilità di sapere cosa fanno gli utenti con l'app, in modo da poterla migliorare.

La configurazione è molto semplice e i risultati saranno visibili in pochi minuti. Al momento le app Web ASP.NET sono supportate (sui propri server o su Azure).

È necessario un account [Microsoft Azure](http://azure.com) (periodo di prova gratuito disponibile).

Esistono due modi per iniziare a utilizzare Application Insights:

-   (Consigliato) [Aggiungere Application Insights al proprio progetto in Visual Studio](#add) per monitorare le prestazioni e l'utilizzo dell'applicazione.
-   [Installare un agente sul server senza ridistribuzione][redfield]: consente di monitorare un sito Web live senza ridistribuirlo e senza dover modificare il codice sorgente. In questo modo è possibile monitorare le prestazioni e le eccezioni. È possibile aggiungere il monitoraggio dell'utilizzo in un secondo momento.

> [WACOM.NOTE] Esiste una [versione precedente di Application Insights](http://msdn.microsoft.com/it-it/library/dn481095.aspx) in Visual Studio Online che supporta una più vasta gamma di tipi di applicazioni. La stiamo ricostruendo da zero come parte di Microsoft Azure e nel presente argomento viene trattata la nuova versione.

## <a name="add"></a>Aggiunta di Application Insights al progetto

È necessario disporre di [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o versione successiva).

### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio 2013, assicurarsi che Application Insights sia selezionato.

![Creazione di un progetto ASP.NET](./media/appinsights/appinsights-01-vsnewp1.png)

Se si tratta del primo utilizzo, verrà richiesto di accedere o di iscriversi all'anteprima di Microsoft Azure. Questo account è diverso dall'account Visual Studio Online.

Utilizzare **Configura impostazioni** se si desidera che il nome della risorsa Azure sia diverso da quello del progetto o se si desidera visualizzare la risorsa sotto lo stesso gruppo di un progetto diverso.

*Se non è disponibile alcuna opzione di Application Insights, verificare che si stia utilizzando Visual Studio 2013 Update 3, che gli Strumenti Application Insights siano abilitati in Estensioni e aggiornamenti e che si stia creando un progetto Web, Windows Store o Windows Phone.*

### ... o se è un progetto esistente

Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere Aggiungi Application Insights.

![Scegliere Aggiungi Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Se si desidera configurare l'analisi di utilizzo Web, è necessario eseguire un altro passaggio, ma prima osserviamo alcuni dati...*

### <a name="run"></a>2. Per eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo diverse pagine.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati ricevuti.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Per visualizzare i dati di monitoraggio

Aprire Application Insights dal progetto.

![Fare clic con il pulsante destro del mouse sul progetto e aprire il portale di Azure](./media/appinsights/appinsights-04-openPortal.png)

Cercare i dati nei riquadri **Integrità applicazione**. All'inizio si vedranno solo uno o due punti. Ad esempio:

![Fare clic per visualizzare altri dati](./media/appinsights/appinsights-41firstHealth.png)

Fare clic su qualsiasi riquadro per visualizzare altri dettagli. È possibile modificare i dati che verranno visualizzati nei report. [Ulteriori informazioni sulla configurazione dei report di Integrità applicazione.][perf]

### <a name="webclient"></a>4. Per configurare l'analisi di utilizzo Web

Se si è aggiunto Application Insights a un progetto Web *esistente*, nei riquadri di analisi dell'utilizzo non verrà visualizzato nulla. È necessario ora eseguire un ulteriore passaggio.

In Application Insights, scegliere Avvio rapido, Instrument your website.

![Nel progetto in Application Insights, fare clic su Avvio rapido, Instrument your website e copiare il codice](./media/appinsights/appinsights-06webcode.png)

Copiare il codice JavaScript nelle pagine Web che si desidera monitorare, subito prima del tag \</head\> di chiusura. In un progetto ASP.NET, un modo efficace di monitorare tutte le pagine consiste nell'inserire il codice nella pagina principale, di solito denominata `_SiteLayout` o `Views\Shared\_Layout`. Si noti che il codice contiene la chiave Application Insights dell'applicazione.

Eseguire nuovamente l'applicazione per visualizzare i dati in **Analisi uso**.

![Fare clic per visualizzare altri dati](./media/appinsights/appinsights-05-usageTiles.png)

[Fare clic sui grafici per visualizzare altri dettagli.][perf]

### <a name="deploy"></a>5. Per distribuire l'applicazione

Distribuire l'applicazione e osservare l'accumulo dei dati.

Una volta creata l'applicazione live, [configurare i test Web][availability] per assicurarsi che resti live.

![Monitoraggio di un'applicazione di esempio in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Modifica del nome dell'applicazione nel portale

È possibile cambiare la risorsa alla quale il progetto invia la telemetria.

La "risorsa" è la falda di Application Insights denominata in cui vengono visualizzati i risultati. È possibile inserire più di una risorsa in un gruppo, se ad esempio di dispone di diversi progetti che fanno parte di un'unica applicazione aziendale.

In Esplora soluzioni, fare clic con il pulsante destro del mouse su ApplicationInsights.config e scegliere **Aggiorna Application Insights**. Verrà aperta una procedura guidata in cui è possibile scegliere una risorsa esistente diversa oppure crearne una nuova.

In seguito, tornare al portale ed eliminare la risorsa precedente.

## <a name="video"></a>Video

### Introduzione

> [AZURE.VIDEO application-insights-introduction]

### Guida introduttiva

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Passaggi successivi

[Monitoraggio dell'utilizzo dell'app Web][usage]

[Monitoraggio delle prestazioni delle applicazioni Web][perf]

[Acquisizione e ricerca nei registri di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]

## Ulteriori informazioni

-   [Application Insights - Guida introduttiva][start]
-   [Monitoraggio di un server Web live][redfield]
-   [Monitoraggio delle prestazioni delle applicazioni Web][perf]
-   [Ricerca nei registri di diagnostica][diagnostic]
-   [Monitoraggio della disponibilità con test Web][availability]
-   [Monitoraggio dell'utilizzo][usage]
-   [Domande e risposte e risoluzione dei problemi][qna]

<!--Link references-->

[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/

