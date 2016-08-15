<properties
	pageTitle="Monitorare le prestazioni dell'app Web di Azure | Microsoft Azure"
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
	ms.date="07/28/2016"
	ms.author="awills"/>

# Monitoraggio delle prestazioni dell'applicazione web di Azure

Nel[portale Azure](https://portal.azure.com)è possibile impostare il monitoraggio per raccogliere le statistiche e informazioni dettagliate sulle dipendenze delle applicazioni nel[applicazioni web di Azure](../app-service-web/app-service-web-overview.md)o di[macchine virtuali](../virtual-machines/virtual-machines-linux-about.md).

Azure supporta Application Performance Monitoring (APM) sfruttando le estensioni. Le estensioni vengono installate nell'applicazione e raccolgono i dati e li riferiscono ai servizi di monitoraggio.

**Application Insights** e **New Relic** sono due delle estensioni disponibili di monitoraggio delle prestazioni. Per utilizzarli, è necessario installare un agente in fase di esecuzione. Con Application Insights, è inoltre disponibile l'opzione di compilare il codice con un SDK. SDK consente di scrivere il codice per monitorare l'utilizzo e prestazioni dell'applicazione in modo più dettagliato.

## Application Insights

### (Facoltativo) Ricompilare l'applicazione con l'SDK...

Application Insights può fornire ulteriori dati di telemetria installando un SDK nell'applicazione.

In Visual Studio 2013 Update 2 o versione successiva aggiungere Application Insights SDK al progetto.

![Fare clic con il pulsante destro del mouse sul progetto Web e scegliere Aggiungi Application Insights](./media/insights-perf-analytics/03-add.png)

Se viene chiesto di effettuare l'accesso, usare le credenziali dell'account Azure.

L'operazione ha due effetti:

1. Crea una risorsa di Application Insights in Azure, in cui vengono archiviati, analizzati e visualizzati i dati di telemetria.
2. Aggiunge il pacchetto NuGet di Application Insights al codice e lo configura per l'invio di dati di telemetria alla risorsa di Azure.

È possibile testare la telemetria eseguendo l'app nel computer di sviluppo (F5) oppure è possibile proseguire e ripubblicare l'app.

L’SDK fornisce un'API per [scrivere dati di telemetria personalizzati](../application-insights/app-insights-api-custom-events-metrics.md) e tenere traccia dell'utilizzo.

### ... oppure configurare una risorsa manualmente

Se l'SDK non è stato aggiunto in Visual Studio, è necessario configurare la risorsa di Application Insights in Azure, in cui i dati di telemetria vengono archiviati, analizzati e visualizzati.

![Fare clic su Aggiungi, Servizi per gli sviluppatori, Application Insights. Scegliere il tipo di app ASP.NET.](./media/insights-perf-analytics/01-new.png)


## Abilitare un'estensione

1. Passare al pannello di controllo dell'app Web o della macchina virtuale da instrumentare.

2. Aggiungere Application Insights o l'estensione di New Relic.

    Se si sta strumentalizzando un'applicazione web:

![Impostazioni, Estensioni, Aggiungere, Application Insights](./media/insights-perf-analytics/05-extend.png)

Oppure, se si usa una macchina virtuale:

![Fare clic sul riquadro di analisi](./media/insights-perf-analytics/10-vm1.png)



## Esplorare i dati

1. Aprire la risorsa di Application Insights direttamente da Esplora oppure dallo strumento di monitoraggio delle prestazioni dell'app Web.

2. Fare clic su qualsiasi grafico per visualizzare altri dettagli:

    ![Nel pannello di panoramica di Application Insights, fare clic su un grafico](./media/insights-perf-analytics/07-dependency.png)

    È possibile [personalizzare il riquadro delle metriche](../application-insights/app-insights-metrics-explorer.md).

3. Fare ancora clic per visualizzare i singoli eventi e le relative proprietà:

    ![Fare clic su un tipo di evento per aprire una ricerca filtrata su tale tipo](./media/insights-perf-analytics/08-requests.png)

    Si noti il collegamento "…" per aprire tutte le proprietà.

    È possibile [personalizzare le ricerche](../application-insights/app-insights-diagnostic-search.md).

Per le ricerche più avanzate nei dati di telemetria, usare il [linguaggio di query di Analisi](../application-insights/app-insights-analytics-tour.md).


## Domande e risposte

Come si inviano i dati a un'altra risorsa di Application Insights?

* *Se Application Insights è stato aggiunto al codice in Visual Studio:* fare clic con il pulsante destro del mouse sul progetto, scegliere **Application Insights > Configura** e quindi scegliere la risorsa. Sarà possibile creare una nuova risorsa. Ricompilare e ridistribuire.
* *In caso contrario:* In Azure passare al pannello di controllo dell'app Web e aprire **> Estensioni**. Eliminare l'estensione di Application Insights. Aprire quindi **Strumenti > Prestazioni**, 'fare clic qui', scegliere Application Insights e quindi la risorsa. Per creare una nuova risorsa di Application Insights eseguire prima questa operazione.


## Passaggi successivi

* [Monitorare le metriche di integrità del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](../application-insights/app-insights-web-track-usage.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Configurare test Web di disponibilità](../application-insights/app-insights-monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.

<!---HONumber=AcomDC_0803_2016-->