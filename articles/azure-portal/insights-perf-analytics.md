<properties 
	pageTitle="Monitoraggio delle prestazioni dell'applicazione web di Azure" 
	description="Tempo di caricamento e risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="awills"/>

# Monitoraggio delle prestazioni dell'applicazione web di Azure

Nel[portale Azure](http://portal.azure.com)è possibile impostare il monitoraggio per raccogliere le statistiche e informazioni dettagliate sulle dipendenze delle applicazioni nel[applicazioni web di Azure](../app-service-web/app-service-web-overview.md)o di[macchine virtuali](../virtual-machines/virtual-machines-about.md).

Azure supporta il Monitoraggio prestazioni applicazione (o *APM*, Application Performance Monitoring) sfruttando le *estensioni*. Le estensioni vengono installate nell'applicazione e raccolgono i dati e li riferiscono ai servizi di monitoraggio.

Application Insights e New Relic sono due delle estensioni disponibili di monitoraggio delle prestazioni. Per utilizzare New Relic, è necessario installare un agente in fase di esecuzione. Per utilizzare Application Insights, è necessario ricompilare il codice con un SDK; ed è inoltre possibile installare un'estensione che consente l'accesso ai dati aggiuntivi. SDK consente di scrivere il codice per monitorare l'utilizzo e prestazioni dell'applicazione in modo più dettagliato.

## Abilitare un'estensione

1. Fare clic su **Sfoglia** e selezionare l'app Web o la macchina virtuale da instrumentare.

2. Aggiungere Application Insights o l'estensione di New Relic. Se si sta strumentalizzando un'applicazione web:

![Impostazioni, Estensioni, Aggiungere, Application Insights](./media/insights-perf-analytics/05-extend.png)

Oppure, se si usa una macchina virtuale:

![Fare clic sul riquadro di analisi](./media/insights-perf-analytics/10-vm1.png)

### Per Application Insights: ricompilare con l'SDK

Application Insights funziona installando un SDK nell'applicazione.

In Visual Studio, aggiungere Application Insights SDK al progetto.

![Fare clic con il pulsante destro del mouse sul progetto Web e scegliere Aggiungi Application Insights](./media/insights-perf-analytics/03-add.png)

Quando viene chiesto di effettuare l'accesso, usare le credenziali dell'account Azure.

È possibile testare la telemetria eseguendo l'app nel computer di sviluppo oppure è possibile proseguire e ripubblicarla.

L’SDK fornisce un'API per [scrivere dati di telemetria personalizzati](../app-insights-api-custom-events-metrics.md) e tenere traccia dell'utilizzo.

## Esplorare i dati

Usare l'applicazione per un breve periodo di tempo per generare alcuni dati di telemetria.

1. Nel pannello relativo all'app Web o alla macchina virtuale verrà quindi visualizzata l'estensione installata.
2. Fare clic sulla riga che rappresenta l'applicazione per passare a tale provider: ![Selezione di Refresh](./media/insights-perf-analytics/06-overview.png)

È inoltre possibile usare **Sfoglia** per passare direttamente al componente Application Insights o all’account New Relic usato.

Dopo aver visualizzato il pannello, per Application Insights, ad esempio, è possibile eseguire le operazioni seguenti:- Aprire prestazioni:

![Nel pannello di panoramica di Application Insights, fare clic sul riquadro delle prestazioni](./media/insights-perf-analytics/07-dependency.png)

- Eseguire il drill-through per visualizzare le singole richieste:

![Nella griglia, fare clic su una dipendenza per visualizzare le richieste correlate.](./media/insights-perf-analytics/08-requests.png)

- Di seguito viene riportato un esempio che mostra la quantità di tempo trascorsa in una dipendenza SQL, inclusi il numero di chiamate SQL e le relative statistiche, ad esempio la durata media e la deviazione standard. 

![](./media/insights-perf-analytics/01-example.png)



## Passaggi successivi

* [Monitorare le metriche di integrità del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Application Insights per app JavaScript e pagine Web](../app-insights-web-track-usage.md) per ottenere l'analisi client sui browser che visitano una pagina Web.
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.
 

<!---HONumber=July15_HO3-->