---
title: Analisi della conservazione degli utenti per applicazioni Web con Azure Application Insights | Microsoft Docs
description: Quanti utenti tornano all'app?
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: 7f7ca19ab171278bbd82f68e3822bc650b25373d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analisi della conservazione degli utenti per applicazioni Web con Application Insights

La funzionalità di conservazione in [Azure Application Insights](app-insights-overview.md) consente di analizzare il numero di utenti che tornano all'app e la frequenza con cui si eseguono attività specifiche o si raggiungono determinati obiettivi. Ad esempio, se si esegue un sito di giochi, è possibile confrontare il numero di utenti che ritornano sul sito dopo aver perso una partita con il numero di utenti che ritornano dopo averla vinta. Queste informazioni consentono di migliorare sia l'esperienza per l'utente che la strategia aziendale.

## <a name="get-started"></a>Attività iniziali

Se nello strumento Conservazione nel portale di Application Insights non vengono ancora visualizzati i dati, leggere le [informazioni su come iniziare a usare gli strumenti di utilizzo](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Strumento Conservazione

![Strumento Conservazione](./media/app-insights-usage-retention/retention.png)

1. La barra degli strumenti consente agli utenti di creare nuovi report di conservazione, aprire i report di conservazione esistenti, salvare il report di conservazione corrente o salvarlo con un altro nome, ripristinare le modifiche apportate al report salvati, aggiornare i dati del report, condividere un report tramite posta elettronica o un collegamento diretto e accedere alla pagina della documentazione. 
2. Per impostazione predefinita, il report di conservazione mostra tutti gli utenti che non hanno fatto alcuna operazione, poi sono tornati e non hanno fatto altro per un periodo. È possibile selezionare una diversa combinazione di eventi per restringere l'ambito ad attività specifiche degli utenti.
3. Aggiungere uno o più filtri alle proprietà. Ad esempio, è possibile concentrarsi sugli utenti di un determinato paese o area. Fare clic su **Aggiorna** sopo aver impostato i filtri. 
4. Il grafico generale della conservazione mostra un riepilogo degli utenti conservati per il periodo di tempo selezionato. 
5. Nella griglia viene visualizzato il numero di utenti conservati secondo il generatore di query al numero 2. Ogni riga rappresenta una coorte di utenti che hanno eseguito qualsiasi evento nel periodo di tempo indicato. Ogni cella nella riga mostra il numero di utenti della coorte che sono ritornati almeno una volta nel periodo successivo. Alcuni utenti potrebbero ritornare in periodi diversi. 
6. Le schede dei dettagli mostrano i primi 5 eventi di avvio e i primi 5 eventi restituiti per consentire agli utenti una migliore comprensione del report di conservazione. 

![Passaggio del mouse sullo strumento Conservazione](./media/app-insights-usage-retention/hover.png)

Gli utenti possono passare il mouse sulle celle dello strumento Conservazione per accedere al pulsante di analisi e alle descrizioni dei comandi che spiegano il significato di ogni cella. Usando il pulsante di analisi, gli utenti accedono allo strumento di analisi con una query pre-popolata per la generazione di utenti dalla cella. 

## <a name="use-business-events-to-track-retention"></a>Usare gli eventi aziendali per tenere traccia della conservazione

Per ottenere un'analisi di conservazione più utile, misurare gli eventi che rappresentano attività aziendali significativi. 

Ad esempio, molti utenti potrebbero aprire una pagina nell'app senza giocare al gioco visualizzato. Il rilevamento delle sole visualizzazioni di pagina offrirebbe pertanto una stima imprecisa del numero di persone che torna per giocare in seguito al primo accesso. Per ottenere un quadro chiaro dei giocatori che ritornano, l'app deve inviare un evento personalizzato quando un utente gioca realmente.  

È buona norma codificare gli eventi personalizzati che rappresentano la azioni chiave aziendali e usarle per l'analisi di conservazione. Per acquisire il risultato di gioco, è necessario scrivere una riga di codice che invii un evento personalizzato ad Application Insights. Se la si scrive nella pagina Web o in Node.js, questa ha un aspetto simile al seguente:

```JavaScript
    appinsights.trackEvent("won game");
```

O nel codice server di ASP.NET:

```C#
   telemetry.TrackEvent("won game");
```

[Altre informazioni sulla scrittura di eventi personalizzati](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Passaggi successivi
- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    - [Utenti, sessioni ed eventi](app-insights-usage-segmentation.md)
    - [Grafici a imbuto](usage-funnels.md)
    - [Flussi degli utenti](app-insights-usage-flows.md)
    - [Cartelle di lavoro](app-insights-usage-workbooks.md)
    - [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)


