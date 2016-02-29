<properties 
	pageTitle="Diagnostica proattiva quasi in tempo reale in Application Insights" 
	description="La diagnostica proattiva quasi in tempo reale informa automaticamente l'utente se il tempo di risposta del server presenta un comportamento insolito. Non è necessaria alcuna configurazione." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# Diagnostica proattiva quasi in tempo reale

*Questa funzionalità è in versione di valutazione preliminare.*

*Inviare commenti e suggerimenti a:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

[Visual Studio Application Insights](app-insights-overview.md) avvisa automaticamente l'utente in tempo quasi reale se la frequenza delle richieste non riuscite dell'app Web aumenta in modo significativo. Per poter valutare e diagnosticare il problema, la notifica include un'analisi delle caratteristiche delle richieste non riuscite e i dati di telemetria correlati. Sono disponibili anche collegamenti al portale di Application Insights per un'ulteriore diagnosi. La funzionalità non necessita di alcuna installazione o configurazione, perché usa algoritmi di Machine Learning per stimare la normale percentuale di errori di base. Per poter funzionare, è necessario un determinato volume di traffico minimo.

Ecco un avviso di esempio:

![Esempio di avviso intelligente che l'analisi del cluster riguardo all'errore](./media/app-insights-nrt-proactive-diagnostics/010.png)

Per ottenere avvisi di errore, è necessario configurare [Application Insights per ASP.NET](app-insights-asp-net.md) o [per l'app Web Java](app-insights-java-get-started.md).

## Funzionamento

La funzionalità di diagnostica proattiva quasi in tempo reale monitora i dati di telemetria ricevuti dall'app e, in particolare, la frequenza delle richieste non riuscite. Questa metrica indica in genere il numero di richieste HTTP che hanno restituito un codice di risposta pari o superiore a 400, a meno che non sia stato scritto codice personalizzato per applicare un [filtro](app-insights-api-filtering-sampling.md#filtering) o generare chiamate [TrackRequest](app-insights-api-custom-events-metrics.md#track-request) personalizzate.

In base al comportamento precedente di questa metrica, il servizio prevede l'intervallo di valori attesi. Se il valore effettivo è significativamente più elevato, genera un avviso.

Quando viene generato un avviso, il servizio esegue un'analisi a cluster su più dimensioni delle richieste, per provare a identificare un modello dei valori che caratterizzano gli errori. Nell'esempio precedente l'analisi ha rilevato che la maggior parte degli errori riguarda un nome della richiesta specifico, rilevando tuttavia che il numero di errori è indipendente dell'istanza dell'host o del server.

L'analizzatore cerca quindi le eccezioni e gli errori di dipendenza associati alle richieste identificati nel cluster, insieme a un esempio degli eventuali log di traccia associati a queste richieste.

I risultati dell'analisi vengono inviati all'utente con un messaggio di posta elettronica, a meno che sia stato configurato di non eseguire questa operazione.

Come per gli [avvisi impostati manualmente](app-insights-alerts.md), è possibile esaminare lo stato dell'avviso e configurarlo nel pannello Avvisi della risorsa di Application Insights. A differenza di altri avvisi, non è però necessario impostare o configurare un avviso di errore adattivo. Se necessario, è possibile disabilitarlo o modificare gli indirizzi di posta elettronica di destinazione.

## Se si riceve un avviso

Un avviso indica che è stata rilevata un'anomalia nella frequenza delle richieste non riuscite. È probabile che si sia verificato un problema con l'app o il relativo ambiente. È probabile che la nuova versione appena caricata non funzioni correttamente o che una dipendenza, ad esempio un database o una risorsa esterna, non funzioni correttamente.

Dalla percentuale delle richieste e dal numero di utenti interessati è possibile decidere il livello di priorità del problema.

In molti casi, si potrà diagnosticare il problema rapidamente dal nome della richiesta, dalle eccezioni, dalle dipendenze e da altri dati forniti.

Se tuttavia è necessario approfondire l'analisi, i collegamenti in ogni sezione consentiranno di passare direttamente a una [pagina di ricerca](app-insights-diagnostic-search.md) filtrata in base alle richieste, all'eccezione, alle dipendenze o alla traccia rilevante. In alternativa, è possibile aprire il [portale di Azure](https://portal.azure.com), passare alla risorsa di Application Insights per l'ap e aprire il pannello Errori.

## Esaminare gli avvisi recenti

Per esaminare gli avvisi nel portale, aprire **Impostazioni, quindi gli eventi operativi**.

![Riepilogo degli avvisi](./media/app-insights-nrt-proactive-diagnostics/040.png)

Fare clic su un avviso per visualizzarne i dettagli completi.


## Configurare gli avvisi 

> **La configurazione dell'esperienza utente non è ancora disponibile.**
> 
> Inviare invece un messaggio di posta elettronica a [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).
>
> Ecco come funziona:

Aprire la pagina degli avvisi. L'avviso di errore adattivo è disponibile insieme agli eventuali avvisi impostati manualmente e si può vedere se attualmente è in uno stato di avviso.

![Nella pagina Panoramica fare clic sul riquadro Avvisi. In alternativa, in qualsiasi pagina di Metrica fare clic su pulsante Avvisi.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Fare clic sull'avviso per configurarlo.

![Configurazione](./media/app-insights-nrt-proactive-diagnostics/030.png)

Si noti che è possibile disabilitare l'avviso di errore adattivo, ma non eliminarlo o crearne un altro.


## Qual è la differenza

La diagnostica proattiva quasi in tempo reale integra altre funzionalità simili ma distinte di Application Insights.

* [Gli avvisi relativi alla metrica](app-insights-alerts.md) vengono impostati dall'utente e possono monitorare un'ampia gamma di metriche, ad esempio utilizzo della CPU, frequenza delle richieste, tempi di caricamento delle pagine e così via. È possibile usarli per avvisare l'utente, ad esempio, se è necessario aggiungere altre risorse. Al contrario, la diagnostica proattiva quasi in tempo reale copre una gamma ristretta di metriche critiche, attualmente solo la frequenza delle richieste non riuscite, progettate per inviare notifiche quasi in tempo reale dopo l'aumento significativo della frequenza delle richieste non riuscite dell'app Web rispetto al comportamento normale della stessa app Web.
* Il [rilevamento proattivo](app-insights-proactive-detection.md) usa anche l'intelligenza artificiale per individuare modelli insoliti nella metrica, senza richiedere alcuna configurazione. A differenza della diagnostica proattiva quasi in tempo reale, lo scopo del rilevamento proattivo consiste nel trovare i segmenti dei molteplici modi di utilizzo che potrebbero essere gestiti in modo non corretto, ad esempio da pagine specifiche in un tipo specifico di browser. L'analisi viene eseguita ogni giorno e se viene trovato un risultato, è probabilmente molto meno urgente di un avviso. Al contrario, viene eseguita l'analisi per la diagnostica proattiva quasi in tempo reale viene eseguita continuamente sui dati di telemetria in ingresso e l'utente riceverà una notifica entro pochi minuti se la frequenza degli errori del server sono maggiori del previsto.


## Inviare commenti e suggerimenti

*Questa funzionalità è in versione di valutazione preliminare. L'invio di commenti e suggerimenti sarà molto apprezzato. Inviare commenti e suggerimenti a:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *o fare clic sul collegamento corrispondente nel messaggio di avviso.*

<!---HONumber=AcomDC_0218_2016-->