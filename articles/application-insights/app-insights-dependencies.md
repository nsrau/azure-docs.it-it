<properties 
	pageTitle="Diagnosticare i problemi delle dipendenze in Application Insights" 
	description="Trovare gli errori i e rallentamenti delle prestazioni causati dalle dipendenze" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# Diagnosticare i problemi delle dipendenze in Application Insights


Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. In Application Insights di Visual Studio, è possibile vedere facilmente per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce.

## Dove è possibile usarlo

Il monitoraggio predefinito delle dipendenze è attualmente disponibile per:

* App Web e servizi di ASP.NET in esecuzione su un server IIS oppure su Azure

Per altri tipi, ad esempio app Web Java o applicazioni per dispositivi, è possibile scrivere il proprio codice di monitoraggio usando l'API TrackDependency.

Il monitoraggio predefinito delle dipendenze attualmente segnala chiamate ai seguenti tipi di dipendenze:

* Database SQL
* Servizi wcf e Web di ASP.NET
* Chiamate HTTP locali o remote
* Azure DocumentDB, tabelle, archivio BLOB e coda

Anche in questo caso, è possibile scrivere le proprie chiamate SDK per monitorare altre dipendenze.

## Configurazione del monitoraggio delle dipendenze

Per ottenere il monitoraggio delle dipendenze, è necessario:

* Usare [Status Monitor](app-insights-monitor-performance-live-website-now.md) sul server IIS per abilitare il monitoraggio
* Aggiungere l'[estensione Application Insights](../insights-perf-analytics.md) all'app Web o alla VM di Azure

(per una VM di Azure, è possibile installare l'estensione dal pannello di controllo di Azure oppure installare Status Monitor proprio come si farebbe su qualsiasi computer).

È possibile eseguire i passaggi precedenti per un'app Web già distribuita. Per ottenere il monitoraggio delle dipendenze standard non è necessario aggiungere Application Insights al progetto di origine.

## Diagnosi dei problemi relativi alle prestazioni delle dipendenze

Per valutare le prestazioni delle richieste al server:

![Nella pagina Panoramica dell'applicazione in Application Insights fare clic sul riquadro Prestazioni](./media/app-insights-dependencies/01-performance.png)

Scorrere fino a esaminare la griglia di richieste:

![Elenco di richieste con conteggi e medie](./media/app-insights-dependencies/02-reqs.png)

Quella superiore impiega molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:


![Elenco di occorrenze di richiesta](./media/app-insights-dependencies/03-instances.png)

Fare clic su qualsiasi istanza con esecuzione prolungata per esaminarla ulteriormente.

> [AZURE.NOTE]Scorrere verso il basso per scegliere un'istanza. Una latenza nella pipeline potrebbe indicare che i dati per le istanze superiore sono incompleti.

Scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:


![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/app-insights-dependencies/05-detail.png)

Il dettaglio include informazioni sufficienti a diagnosticare il problema.



## Errori

Se sono presenti richieste non riuscite, fare clic sul grafico.

![Fare clic sul grafico delle richieste non riuscite](./media/app-insights-dependencies/06-fail.png)

Fare clic in un tipo di richiesta e un'istanza di richiesta per trovare una chiamata non riuscita a una dipendenza remota.


![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->