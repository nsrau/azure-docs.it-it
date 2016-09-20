<properties
	pageTitle="Monitorare le prestazioni dell'app Web di Azure | Microsoft Azure"
	description="Monitoraggio delle prestazioni applicative per le app Web di Azure. Tempo di caricamento e risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="awills"/>

# Monitoraggio delle prestazioni dell'applicazione web di Azure

Nel [portale di Azure](https://portal.azure.com) è possibile configurare il monitoraggio delle prestazioni applicative per le [app Web di Azure](../app-service-web/app-service-web-overview.md). [Visual Studio Application Insights](app-insights-overview.md) consente di instrumentare l'app per inviare dati di telemetria sulle proprie attività al servizio Application Insights, in cui verranno archiviati e analizzati. Sarà quindi possibile usare grafici delle metriche e strumenti di ricerca per diagnosticare i problemi, migliorare le prestazioni e valutare l'utilizzo.

## Fase di esecuzione o fase di compilazione

È possibile configurare il monitoraggio instrumentando l'app in uno dei due modi seguenti:

* **Fase di esecuzione**: è possibile selezionare un'estensione di monitoraggio delle prestazioni quando l'app Web è già attiva. Non è necessario ricompilarla o reinstallarla. Si ottiene un set di pacchetti standard che monitorano i tempi di risposta, le percentuali di riuscita, le eccezioni, le dipendenze e così via.

    **Application Insights** e **New Relic** sono due delle estensioni di monitoraggio delle prestazioni disponibili per la fase di esecuzione.
 
* **Fase di compilazione**: è possibile installare un pacchetto nell'app durante lo sviluppo. Questa opzione è più versatile. Oltre agli stessi pacchetti standard, è possibile scrivere codice per personalizzare la telemetria o per inviare dati di telemetria personalizzati. È possibile registrare attività specifiche o registrare eventi in base alla semantica del dominio dell'app.

    **Application Insights** offre pacchetti per la fase di compilazione.


## Compilare l'app con il pacchetto di Application Insights

Application Insights può fornire ulteriori dati di telemetria installando un SDK nell'applicazione.

In Visual Studio 2013 Update 2 o versione successiva aggiungere Application Insights SDK al progetto.

![Fare clic con il pulsante destro del mouse sul progetto Web e scegliere Aggiungi Application Insights](./media/app-insights-azure-web-apps/03-add.png)

Se viene chiesto di effettuare l'accesso, usare le credenziali dell'account Azure.

L'operazione ha due effetti:

1. Crea una risorsa di Application Insights in Azure, in cui vengono archiviati, analizzati e visualizzati i dati di telemetria.
2. Aggiunge il pacchetto NuGet di Application Insights al codice e lo configura per l'invio di dati di telemetria alla risorsa di Azure.

È possibile testare la telemetria eseguendo l'app nel computer di sviluppo (F5) oppure è possibile proseguire e ripubblicare l'app.

L’SDK fornisce un'API per [scrivere dati di telemetria personalizzati](../application-insights/app-insights-api-custom-events-metrics.md) e tenere traccia dell'utilizzo.

### ... oppure configurare una risorsa manualmente

Se l'SDK non è stato aggiunto in Visual Studio, è necessario configurare una risorsa di Application Insights in Azure, in cui i dati di telemetria vengono archiviati, analizzati e visualizzati.

![Fare clic su Aggiungi, Servizi per gli sviluppatori, Application Insights. Scegliere il tipo di app ASP.NET.](./media/app-insights-azure-web-apps/01-new.png)


## Abilitare un'estensione

1. Passare al pannello di controllo dell'app Web o della macchina virtuale da instrumentare.

2. Aggiungere Application Insights o l'estensione di New Relic.

    Se si sta strumentalizzando un'applicazione web:

![Impostazioni, Estensioni, Aggiungere, Application Insights](./media/app-insights-azure-web-apps/05-extend.png)

Oppure, se si usa una macchina virtuale:

![Fare clic sul riquadro di analisi](./media/app-insights-azure-web-apps/10-vm1.png)



## Esplorare i dati

1. Aprire la risorsa di Application Insights direttamente da Esplora oppure dallo strumento di monitoraggio delle prestazioni dell'app Web.

2. Fare clic su qualsiasi grafico per visualizzare altri dettagli:

    ![Nel pannello di panoramica di Application Insights, fare clic su un grafico](./media/app-insights-azure-web-apps/07-dependency.png)

    È possibile [personalizzare i pannelli delle metriche](../application-insights/app-insights-metrics-explorer.md).

3. Fare ancora clic per visualizzare i singoli eventi e le relative proprietà:

    ![Fare clic su un tipo di evento per aprire una ricerca filtrata su tale tipo](./media/app-insights-azure-web-apps/08-requests.png)

    Si noti il collegamento "…" per aprire tutte le proprietà.

    È possibile [personalizzare le ricerche](../application-insights/app-insights-diagnostic-search.md).

Per le ricerche più avanzate nei dati di telemetria, usare il [linguaggio di query di Analisi](../application-insights/app-insights-analytics-tour.md).


## Domande e risposte

Come si inviano i dati a un'altra risorsa di Application Insights?

* *Se Application Insights è stato aggiunto al codice in Visual Studio:* fare clic con il pulsante destro del mouse sul progetto, scegliere **Application Insights > Configura** e quindi scegliere la risorsa. Sarà possibile creare una nuova risorsa. Ricompilare e ridistribuire.
* *In caso contrario:* in Azure passare al pannello di controllo dell'app Web e aprire **Strumenti > Estensioni**. Eliminare l'estensione di Application Insights. Aprire quindi **Strumenti > Prestazioni**, "fare clic qui", scegliere Application Insights e quindi la risorsa desiderata. Per creare una nuova risorsa di Application Insights eseguire prima questa operazione.


## Passaggi successivi

* [Abilitare l'invio dei dati di diagnostica di Azure](app-insights-azure-diagnostics.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../azure-portal/insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](app-insights-web-track-usage.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* Configurare test Web come illustrato in [Monitorare la disponibilità e la velocità di risposta dei siti Web](app-insights-monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.

<!---HONumber=AcomDC_0907_2016-->