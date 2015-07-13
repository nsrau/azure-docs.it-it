<properties 
	pageTitle="Diagnosi dei problemi relativi alle dipendenze in Application Insights" 
	description="Trovare gli errori e i rallentamenti delle prestazioni causati dalle dipendenze" 
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
 
# Diagnosi dei problemi relativi alle dipendenze in Application Insights


Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP oppure un database o un file system. In Application Insights di Visual Studio è possibile vedere facilmente per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce.

## Dove è possibile usarlo

Il monitoraggio predefinito delle dipendenze è attualmente disponibile per:

* Servizi e app Web di ASP.NET in esecuzione in un server IIS oppure in Azure

Per altri tipi, ad esempio app per dispositivi o app Web Java, è possibile scrivere il proprio monitor usando l'API TrackDependency.

Il monitor predefinito delle dipendenze attualmente segnala le chiamate ai tipi di dipendenze seguenti:

* Database SQL
* Servizi WCF e Web di ASP.NET
* Chiamate HTTP locali o remote
* Azure DocumentDB, tabelle, archivio BLOB e coda

Anche in questo caso, è possibile scrivere le proprie chiamate SDK per monitorare altre dipendenze.

## Configurazione del monitoraggio delle dipendenze

Per ottenere il monitoraggio delle dipendenze, è necessario:

* Usare [Status Monitor](app-insights-monitor-performance-live-website-now.md) nel server IIS per abilitare il monitoraggio.
* Aggiungere l'[estensione Application Insights](../insights-perf-analytics.md) all'app Web o alla macchina virtuale di Azure.

Per una macchina virtuale di Azure, è possibile installare l'estensione dal pannello di controllo di Azure oppure installare Status Monitor proprio come si farebbe in qualsiasi computer.

È possibile eseguire i passaggi precedenti per un'app Web già distribuita. Per ottenere il monitoraggio delle dipendenze standard non è necessario aggiungere Application Insights al progetto di origine.

## Diagnosi dei problemi relativi alle prestazioni delle dipendenze

Per valutare le prestazioni delle richieste al server:

![Nella pagina Panoramica dell'applicazione in Application Insights fare clic sul riquadro Prestazioni](./media/app-insights-dependencies/01-performance.png)

Scorrere verso il basso per esaminare la griglia delle richieste:

![Elenco di richieste con medie e conteggi](./media/app-insights-dependencies/02-reqs.png)

Quella elencata per prima richiede molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare i singoli eventi relativi alla richiesta:


![Elenco di occorrenze della richiesta](./media/app-insights-dependencies/03-instances.png)

Fare clic su qualsiasi istanza con esecuzione prolungata per esaminarla ulteriormente.

> [AZURE.NOTE]Scorrere verso il basso per scegliere un'istanza. Una latenza nella pipeline potrebbe indicare che i dati per le prime istanze sono incompleti.

Scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-dependencies/04-dependencies.png)

Sembra che la maggior parte del tempo dedicato a questa richiesta sia stata impiegata in una chiamata a un servizio locale.

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