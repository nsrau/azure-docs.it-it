<properties
   pageTitle="Monitor application performance"
   description="Chart load and response time, dependency information and set alerts on performance." 
   services="azure-portal" documentationCenter="na" authors="alancameronwills" manager="keboyd"/> 

<tags
   ms.service="azure-portal"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="04/28/2015"
   ms.author="awills"/>

# Monitorare le prestazioni dell'applicazione 

Nel [portale di Azure](http://portal.azure.com) è possibile configurare il monitoraggio per raccogliere le statistiche e i dettagli sulle dipendenze dell'applicazione nelle app Web o nelle macchine virtuali. 

Azure supporta il Monitoraggio prestazioni applicazione (o *APM*, Application Performance Monitoring) sfruttando le *estensioni*. Le estensioni vengono installate nell'applicazione e raccolgono dati e li riferiscono ai servizi di monitoraggio. 

## Abilitare un'estensione 

1. Fare clic su **Sfoglia** e selezionare l'app Web o la macchina virtuale da instrumentare. 
2. Fare clic sul riquadro **Monitoraggio applicazioni** in **Monitoraggio**. 
3. Scegliere il provider di estensioni da usare, ad esempio **Application Insights** o **New Relic**. 

![APM app Web](./media/insights-perf-analytics/05-extend.png) 

Se invece si usa una macchina virtuale: 

![Macchina virtuale](./media/insights-perf-analytics/10-vm1.png) 

## Esaminare i dati Usare l'applicazione per un breve periodo di tempo in modo da generare alcuni dati di telemetria. 

1. Nel pannello relativo all'app Web o alla macchina virtuale verrà quindi visualizzata l'estensione installata
2. Fare clic sulla riga che rappresenta l'applicazione per passare al provider specifico: 
![Fare clic su Aggiorna](./media/insights-perf-analytics/06-overview.png) 

È anche possibile usare **Sfoglia** per passare direttamente al componente Application Insights o all'account New Relic usato. Dopo avere visualizzato il pannello, per Application Insights, ad esempio, è possibile eseguire le operazioni seguenti: - Aprire Prestazioni: 

![Nel pannello di panoramica per Application Insights, fare clic sul riquadro Prestazioni](./media/insights-perf-analytics/07-dependency.png) 
- Eseguire il drill-through per visualizzare le singole richieste: 

![Nella griglia fare clic su una dipendenza per visualizzare le richieste correlate.](./media/insights-perf-analytics/08-requests.png) 
- L'esempio seguente mostra la quantità di tempo trascorso in una dipendenza SQL, inclusi il numero di chiamate SQL e statistiche quali la durata media e la deviazione standard. 

![](./media/insights-perf-analytics/01-example.png) 

### Requisiti aggiuntivi per Application Insights 
New Relic può essere installato automaticamente senza strumentazione aggiuntiva, ma Application Insights ha un requisito aggiuntivo. 

![Nella finestra di dialogo Nuovo progetto selezionare Aggiungi Application Insights](./media/insights-perf-analytics/03-add.png) 

Quando viene richiesto l'accesso, usare le credenziali dell'account Azure. 

## Passaggi successivi 
* [Monitorare le metriche dell'integrità del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo. 
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate a frequenza elevata sul servizio. 
* [Ricevere notifiche relative agli avvisi](insights-receive-alert-notifications.md) quando si verificano eventi operativi o se le metriche superano una soglia specifica. 
* Usare [Application Insights per app e pagine Web JavaScript](../app-insights-web-track-usage.md) per ottenere analisi client sui browser che visitano una pagina Web. 
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](../app-insights-monitor-web-app-availability.md) con Application Insights in modo da rilevare se la pagina non è disponibile.


<!--HONumber=54--> 