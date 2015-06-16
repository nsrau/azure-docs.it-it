<properties 
	pageTitle="Rilevare e diagnosticare gli arresti anomali nelle app Windows Store e Phone con Application Insights" 
	description="Analizzare i problemi di prestazioni nell'app per dispositivo Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

# Rilevare e diagnosticare gli arresti anomali nelle app Windows Store e Phone con Application Insights

*Application Insights è disponibile in anteprima.*

Se gli utenti incontrano arresti anomali nell'app, si desidera avere rapidamente informazioni e dettagli su cosa è successo. Con Application Insights è possibile monitorare la frequenza con cui si verificano arresti anomali, ricevere avvisi quando si verificano e analizzare i report di singoli incidenti imprevisti.

"Arresto anomalo" significa che l'applicazione viene terminata a causa di un'eccezione non rilevata. Se l'applicazione rileva un'eccezione, è possibile creare un report con l'API TrackException(), ma continuare l'esecuzione. In tal caso, non verrà registrata come arresto anomalo.


## Monitorare la frequenza di arresti anomali

Se questa operazione non è stata ancora eseguita, aggiungere [Application Insights al progetto dell'app][windows] e ripubblicarlo.

Gli arresti anomali vengono visualizzati nel pannello della panoramica dell'applicazione nel [portale di Application Insights][portal].

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

È possibile modificare l'intervallo di tempo visualizzato nel grafico.


## Impostare un avviso per rilevare arresti anomali

![Dal grafico arresti anomali fare clic su regole di avviso e quindi Aggiungi avviso](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## Diagnosticare gli arresti anomali

Per scoprire se si verificano arresti anomali di alcune versioni dell'app più di altre, fare clic nel grafico di arresti anomali e quindi segmentare in base alla versione dell'applicazione:

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


Per individuare le eccezioni che causano arresti anomali, aprire la ricerca diagnostica. È possibile rimuovere altri tipi di telemetria per concentrarsi sulle eccezioni:

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[Ulteriori informazioni sull'applicazione di filtri nella ricerca diagnostica][diagnostic]
 

Fare clic su qualsiasi eccezione per visualizzarne i dettagli, tra cui le proprietà associate e l'analisi dello stack.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

Vedere le altre eccezioni e eventi che si sono verificati simili a quell'eccezione:


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## Inserire gli eventi e log di traccia

Per facilitare la diagnosi dei problemi, è possibile [inserire chiamate di traccia e eseguire una ricerca nei log in Application Insights][diagnostic].

## <a name="debug"></a>Confronto tra la modalità Debug e Release

#### Debug

Se si compila in modalità di debug, gli eventi vengono inviati non appena vengono generati. Se si perde la connessione a Internet e quindi si chiude l'app prima di recuperare la connettività, la telemetria offline viene rimossa.

#### Release

Se si esegue la compilazione nella configurazione di rilascio, gli eventi vengono archiviati nel dispositivo e inviati alla ripresa dell'applicazione. I dati vengono inviati anche al primo uso dell'applicazione. Se dopo l'avvio la connettività Internet non è disponibile, la telemetria precedente, nonché quella per il ciclo di vita corrente, viene archiviata e inviata alla ripresa successiva.

## <a name="next"></a>Passaggi successivi

[Rilevare, valutare e diagnosticare problemi con Application Insights][detect]

[Acquisire i log di diagnostica][trace]

[Risoluzione dei problemi](app-insights-windows-troubleshoot.md)




<!--Link references-->

[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54--> 