<properties 
	pageTitle="Application Insights: rilevamento proattivo delle anomalie" 
	description="Application Insights esegue un'analisi approfondita dei dati di telemetria dell'app e segnalare potenziali problemi." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>

#  Application Insights: rilevamento proattivo delle anomalie

*Application Insights è disponibile in anteprima.*


Application Insights esegue un'analisi approfondita dei dati di telemetria dell'app e può segnalare potenziali problemi di prestazioni. Probabile si è arrivati a leggere questo articolo proprio perché si è ricevuto un avviso di anomalia tramite posta elettronica.

## Informazioni sugli avvisi di anomalie

* *Perché ho ricevuto questo avviso?*
 * Application Insights analizza periodicamente i dati usando regole per il riconoscimento di schemi e ricerca anomalie che possono indicare problemi di prestazioni nell'applicazione.
* *La notifica indica la sicura presenza di un problema?*
 * No. Invita semplicemente a controllare con attenzione un determinato componente. 
* *Cosa devo fare?*
 * [Esaminare i dati presentati](#responding-to-an-alert) e valutare se possono effettivamente indicare un problema. Se non indicano problemi, si può continuare senza intervenire.
* *Il servizio implica l'accesso manuale ai dati da parte di Microsoft?*
 * No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i suoi dati rimangono [privati](app-insights-data-retention-privacy.md).


## Informazioni sul processo di rilevamento

* *Quali tipi di anomalie vengono rilevati?*
 * Vengono analizzati quegli schemi che sarebbe troppo lungo verificare manualmente, ad esempio prestazioni insufficienti per una combinazione specifica di località, ora del giorno e piattaforma.
* *Si possono creare regole personalizzate di rilevamento delle anomalie?*
 * Non per questo tipo di analisi approfondita. Si possono tuttavia [impostare avvisi](app-insights-alerts.md) per essere informati quando una certa metrica supera una soglia.
* *Con quale frequenza viene eseguita l'analisi?*
 * L'analisi non viene eseguita su una risorsa dell'app che non restituisce un numero elevato di dati di telemetria. È quindi improbabile che vengano ricevuti avvisi relativi alle sessioni di debug.


## Come rispondere a un avviso

Aprire il report delle anomalie dal messaggio di posta elettronica o dall'elenco delle anomalie.

![](./media/app-insights-anomaly/02.png)

L'avviso include:

* La descrizione
* Informazioni sull'impatto, ad esempio il numero di utenti interessati o la frequenza con cui si verifica il problema.

Fare clic su un grafico per aprire un pannello con ulteriori dettagli.

Modificare l'intervallo di tempo e i filtri per esplorare i dati di telemetria.




## Messaggi di posta elettronica di notifica

* *È necessario sottoscrivere questo servizio per ricevere le notifiche?*
 * No. L'apposito bot analizza periodicamente i dati di tutti gli utenti di Application Insights e invia le notifiche se vengono rilevati problemi.
* *È possibile annullare la sottoscrizione oppure ottenere le notifiche inviate ai colleghi?*
 * Fare clic sul collegamento nell'avviso o nel messaggio di posta elettronica. Aprire le impostazioni delle anomalie. ![](./media/app-insights-anomaly/01.png) Attualmente vengono inviate a coloro che hanno [accesso in scrittura alla risorsa Application Insights](app-insights-resources-roles-access-control.md).
* *Non voglio ricevere un numero elevato di messaggi.*
 * Ogni giorno vengono ricevuti al massimo tre messaggi senza alcuna ripetizione dello stesso messaggio.
* *Se non eseguo alcuna operazione, riceverò un promemoria?*
 * No, il messaggio relativo a un singolo problema viene ricevuto una sola volta.
* *Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*
 * Nel pannello di panoramica dell'app in Application Insights fare clic sul riquadro **Anomalie**. 






 

<!---HONumber=August15_HO6-->