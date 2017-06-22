---
title: Analisi della conservazione degli utenti per applicazioni Web con Azure Application Insights | Microsoft Docs
description: Quanti utenti tornano all&quot;app?
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
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analisi della conservazione degli utenti per applicazioni Web con Application Insights

Il pannello Conservazione in [Azure Application Insights](app-insights-overview.md) consente analizzare il numero di utenti che tornano all'app e la frequenza con cui si eseguono attività specifiche o a si raggiungono determinati obiettivi. Ad esempio, se si esegue un sito di giochi, è possibile confrontare il numero di utenti che ritornano sul sito dopo aver perso una partita con il numero di utenti che ritornano dopo averla vinta. Queste informazioni consentono di migliorare sia l'esperienza per l'utente che la strategia aziendale.

## <a name="get-started"></a>Attività iniziali

Se nel pannello Conservazione nel portale di Application Insights non vengono ancora visualizzati i dati, leggere le [informazioni su come iniziare a usare gli strumenti d'uso](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Strumento di conservazione

![Strumento Conservazione](./media/app-insights-usage-retention/retention.png)

a. La pagina Panoramica è sempre presente. Per di più, è possibile salvare i report a cui è stato assegnato un nome con diverse impostazioni dei parametri. Salvarli tra i report condivisi per fare in modo che siano visibili ad altri utenti che hanno accesso alla risorsa.

b. Per impostazione predefinita, il grafico conta tutti gli utenti che hanno usato gli eventi personalizzati o le visualizzazioni di pagine ricevute dall'app. Selezionarne uno o un sottoinsieme per concentrarsi su un'attività utente in particolare. 

c. Aggiungere uno o più filtri alle proprietà. Ad esempio, è possibile concentrarsi sugli utenti di un determinato paese o regione. Fare clic su **Aggiorna** sopo aver impostato i filtri.

d. **Ripristina impostazioni predefinite** cancella sempre i filtro personalizzati e i filtri degli eventi.

e. Il grafico di riepilogo mostra i totali per il periodo di tempo selezionato.

f. La griglia mostra il numero di utenti che hanno ripetuto le azioni selezionate in un determinato periodo. Ogni riga rappresenta una coorte di utenti che ha eseguito una delle azioni selezionate nel tempo periodo indicato. Ogni cella nella riga mostra il numero di utenti della coorte che sono ritornati almeno una volta nel periodo successivo. Alcuni utenti potrebbero ritornare in periodi diversi.

Tutti gli utenti che hanno usato l'app nell'intervallo di tempo del grafico vengono rappresentati esattamente in una riga del grafico. Ogni utente viene conteggiato nel periodo in cui ha eseguito per la prima volta l'azione selezionata nell'intervallo di tempo del grafico. Pertanto la prima riga tende ad avere un numero maggiore.


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

* [Panoramica sull'uso](app-insights-usage-overview.md)
* [Utenti e sessioni](app-insights-usage-segmentation.md)
* [Codifica di eventi personalizzati](app-insights-api-custom-events-metrics.md)


