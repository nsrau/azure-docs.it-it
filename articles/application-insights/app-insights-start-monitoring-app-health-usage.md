<properties 
	pageTitle="Aggiungere Application Insights SDK al progetto Web" 
	description="Analizzare l'uso, la disponibilità e le prestazioni dell'applicazione locale o Web di Microsoft Azure con Application Insights." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>

# Application Insights: monitoraggio dell'integrità e dell'utilizzo di un'app

*Application Insights è disponibile in anteprima.*

Application Insights consente di monitorare un'applicazione live in base a:

* **Disponibilità**: esecuzione di test automatici degli URL ogni pochi minuti da tutto il mondo.
* **Prestazioni**  : rilevamento e diagnostica dei problemi e delle eccezioni relativi alle prestazioni.
* **Utilizzo**: possibilità di sapere cosa fanno gli utenti con l'app, in modo da poterla migliorare.

In alternativa, leggere [Application Insights - Guida introduttiva][start].

## <a name="add"></a>Aggiungere Application Insights al progetto

Saranno necessari [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o versione successiva)e un account in [Microsoft Azure](http://azure.com).

### Se è un nuovo progetto...

Quando si crea un nuovo progetto in Visual Studio 2013, assicurarsi che Application Insights sia selezionato. 


![Create an ASP.NET project](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

Se si tratta del primo uso, verrà richiesto di accedere o di iscriversi all'anteprima di Microsoft Azure. Questo account è diverso da quello di Visual Studio Online.

Usare **Configura impostazioni** se si desidera che il nome della risorsa Azure sia diverso da quello del progetto o se si desidera visualizzare la risorsa nello stesso gruppo di un progetto diverso. 

*Se non è disponibile alcuna opzione di Application Insights, verificare che si stia usando Visual Studio 2013 Update 3, che gli Strumenti Application Insights siano abilitati in Estensioni e aggiornamenti e che si stia creando un progetto Web, Windows Store o Windows Phone.*

### o se è un progetto esistente

Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere Aggiungi Application Insights.

![Choose Add Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

*Se si desidera configurare l'analisi di utilizzo Web, è necessario eseguire un altro passaggio, ma prima osserviamo alcuni dati...*


### <a name="run"></a>2. Eseguire il progetto

Eseguire l'applicazione con F5 e provarla aprendo diverse pagine.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati ricevuti.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Visualizzare i dati monitorati

Aprire Application Insights dal progetto.

![Right-click your project and open the Azure portal](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)


Cercare i dati nei riquadri **Integrità applicazione**. All'inizio si vedranno solo uno o due punti. ad esempio:

![Click through to more data](./media/app-insights-start-monitoring-app-health-usage/appinsights-41firstHealth.png)

Fare clic su un riquadro per visualizzare altri dettagli. È possibile modificare i dati che verranno visualizzati nei report. [Altre informazioni sulla configurazione dei report di Integrità applicazione.][perf]


### <a name="deploy"></a>4. Distribuire l'applicazione

Distribuire l'applicazione e osservare l'accumulo dei dati.



Una volta creata l'applicazione live, [configurare i test Web][availability] per assicurarsi che resti live. 

![Example application monitor in Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-00-appblade.png)

### Modifica del nome dell'applicazione nel portale

È possibile cambiare la risorsa alla quale il progetto invia la telemetria. 

La "risorsa" è il pannello di Application Insights denominato in cui vengono visualizzati i risultati. È possibile inserire più di una risorsa in un gruppo, se ad esempio di dispone di diversi progetti che fanno parte di un'unica applicazione aziendale. 

In Esplora soluzioni fare clic con il pulsante destro del mouse su ApplicationInsights.config e scegliere **Aggiorna Application Insights**. Verrà aperta una procedura guidata in cui è possibile scegliere una risorsa esistente diversa oppure crearne una nuova.

In seguito, tornare al portale ed eliminare la risorsa precedente.

## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Passaggi successivi

[Monitoraggio dell'utilizzo dell'app Web][usage]

[Monitoraggio delle prestazioni delle applicazioni Web][perf]

[Acquisizione e ricerca nei registri di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 

<!--HONumber=46--> 
 