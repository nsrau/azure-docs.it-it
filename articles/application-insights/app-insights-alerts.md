---
title: Impostare gli avvisi in Application Insights di Azure | Microsoft Docs
description: "Ricevere notifiche su tempi di risposta più lenti, eccezioni e altre prestazioni o modifiche nell&quot;uso delle app Web."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 04965375fc94fc1aa8b1c48deb743bb1d0cf1c26
ms.contentlocale: it-it
ms.lasthandoff: 03/21/2017


---
# <a name="set-alerts-in-application-insights"></a>Impostare gli avvisi in Application Insights
[Application Insights di Azure][start] può informare di eventuali cambiamenti nelle prestazioni e nelle metriche di utilizzo dell'app Web in uso. 

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Esistono tre tipologie di avvisi:

* Gli **avvisi delle metriche** indicano quando una qualsiasi metrica supera un valore di soglia per un determinato periodo, ad esempio i tempi di risposta, il numero di eccezioni, l'uso della CPU o il numero di visualizzazioni della pagina. 
* I [**test Web**][availability] indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability].
* Gli avvisi della [**diagnostica proattiva**](app-insights-proactive-diagnostics.md) vengono configurati automaticamente per informare su schemi di prestazioni insoliti.

L'articolo è incentrato sugli avvisi delle metriche.

## <a name="set-a-metric-alert"></a>Impostare un avviso metrica
Aprire il pannello Regole di avviso e usare il pulsante Aggiungi. 

![Nel pannello Regole di avviso scegliere Aggiungi avviso. Impostare l'app come la risorsa da misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/app-insights-alerts/01-set-metric.png)

* Impostare la risorsa prima delle altre proprietà. **Scegliere la risorsa "(components)"** per impostare avvisi sulle metriche relative a prestazioni e utilizzo.
* Il nome assegnato all'avviso deve essere univoco all'interno del gruppo di risorse (non solo dell'applicazione).
* Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.
* Se si seleziona la casella "Proprietari di posta elettronica...", verranno inviati avvisi tramite posta elettronica a chiunque abbia accesso a questo gruppo di risorse. Per espandere il set di utenti, aggiungerli al [gruppo di risorse o a una sottoscrizione](app-insights-resources-roles-access-control.md) (non la risorsa).
* Se si specifica "Indirizzi di posta elettronica aggiuntivi", gli avvisi verranno inviati agli utenti o gruppi corrispondenti (indipendentemente dalla selezione della casella relativa ai "proprietari di posta elettronica..."). 
* Impostare un [indirizzo di webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) se è stata impostata un'app Web che risponde agli avvisi. L'app verrà richiamata sia quando l'avviso viene attivato sia quando viene risolto. Si noti però che attualmente i parametri di query non vengono passati come proprietà webhook.
* È possibile disabilitare o abilitare l'avviso usando i pulsanti nella parte superiore del pannello.

*Il pulsante Aggiungi avviso non è visibile.* 

* Si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Dare un'occhiata al pannello Controllo di accesso. [Informazioni sul controllo di accesso][roles].

> [!NOTE]
> Nel pannello degli avvisi si noterà che è già presente un avviso configurato: [Diagnostica proattiva](app-insights-proactive-failure-diagnostics.md). Si tratta di un avviso automatico che controlla una particolare metrica, la frequenza di errori delle richieste. A meno che non si decida di disabilitare l'avviso proattivo, non è necessario impostare un proprio avviso relativo alla frequenza di errori delle richieste. 
> 
> 

## <a name="see-your-alerts"></a>Visualizzare gli avvisi
Si riceve un messaggio di posta elettronica quando lo stato dell'avviso passa da inattivo ad attivo e viceversa. 

Lo stato corrente di ogni avviso viene visualizzato nel pannello delle regole di avviso.

Nell'elenco a discesa Avvisi è disponibile un riepilogo delle attività recenti:

![Menu a discesa degli avvisi](./media/app-insights-alerts/010-alert-drop.png)

La cronologia delle modifiche di stato si trova nel Log attività:

![Nel pannello Panoramica fare clic su Impostazioni, Log di controllo](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Funzionamento degli avvisi
* Un avviso può avere tre stati: "Mai attivato", "Attivato" e "Risolto". Lo stato Attivato indica che la condizione specificata aveva valore true al momento dell'ultima valutazione.
* Quando lo stato di un avviso viene modificato, viene generata una notifica. Se la condizione dell'avviso era già impostata su true durante la creazione dell'avviso, è possibile che non si riceva alcuna notifica finché la condizione non viene impostata su false.
* Ogni notifica genera un messaggio di posta elettronica se è stata selezionata la casella dei messaggi di posta elettronica o sono stati specificati indirizzi di posta elettronica. È anche possibile esaminare l'elenco di riepilogo a discesa Notifiche.
* Un avviso viene valutato ogni volta che arriva una metrica, ma non altrimenti.
* La valutazione aggrega la metrica per il periodo precedente e quindi la confronta con la soglia per determinare il nuovo stato.
* Il periodo scelto specifica l'intervallo per il quale vengono aggregate le metriche. Non influisce sulla frequenza con cui viene valutato l'avviso: ciò dipende dalla frequenza di arrivo delle metriche.
* Se per un periodo di tempo non arrivano dati per una determinata metrica, il divario comporta diversi effetti sulla valutazione dell'avviso e sui grafici in Esplora metriche. Se in Esplora metriche non viene visualizzato alcun dato per un periodo più lungo dell'intervallo di campionamento del grafico, il grafico mostra un valore pari a 0. Un avviso basato sulla metrica stessa non viene valutato nuovamente e lo stato dell'avviso rimane invariato. 
  
    Quando infine arrivano i dati, il grafico viene riportato su un valore diverso da zero. L'avviso viene valutato in base ai dati disponibili per il periodo specificato. Se il nuovo punto dati è l'unico disponibile nel periodo, la funzione di aggregazione si basa solo su quel punto dati.
* Un avviso può spesso passare velocemente dallo stato di avviso a quello integro e viceversa, anche se si imposta un periodo prolungato. Questa situazione può verificarsi se il valore della metrica si aggira intorno alla soglia. Non esiste alcuna isteresi nella soglia: la transizione allo stato di avviso si verifica in corrispondenza dello stesso valore della transizione allo stato integro.

## <a name="what-are-good-alerts-to-set"></a>Quali sono gli avvisi corretti da impostare?
Dipende dall'applicazione. Per iniziare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre l'app è in esecuzione per acquisire informazioni sul comportamento normale. È così possibile trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi per ricevere le notifiche quando le metriche superano la zona normale. 

Gli avvisi più diffusi includono:

* Le [metriche del browser][client], soprattutto i **tempi di caricamento delle pagine** del browser, sono ottimali per le applicazioni Web. Se la pagina presenta molti script, è opportuno prestare attenzione alle **eccezioni del browser**. Per ottenere queste metriche e questi avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il **tempo di risposta del server** per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a questa metrica per vedere se varia in modo sproporzionato in caso di frequenza elevata delle richieste: questa situazione può indicare l'esaurimento delle risorse da parte dell'app. 
* **Eccezioni del server** : per visualizzarle, è necessario effettuare alcuni passaggi di [configurazione aggiuntivi](app-insights-asp-net-exceptions.md).

Non dimenticare che la [diagnostica proattiva sulla frequenza errori](app-insights-proactive-failure-diagnostics.md) monitora automaticamente la frequenza con cui l'applicazione risponde alle richieste con codici di errore. 

## <a name="automation"></a>Automazione
* [Usare PowerShell per automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)
* [Usare webhook per automatizzare la risposta agli avvisi](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Vedere anche
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md)
* [Automatizzare la configurazione degli avvisi](app-insights-powershell-alerts.md)
* [Diagnostica proattiva](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


