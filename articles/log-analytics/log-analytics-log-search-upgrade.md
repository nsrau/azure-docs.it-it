---
title: Aggiornamento di Azure Log Analytics alla nuova ricerca log | Microsoft Docs
description: "Il nuovo linguaggio di query di Log Analytics è quasi pronto ed è possibile partecipare all'anteprima pubblica.  Questo articolo illustra i vantaggi del nuovo linguaggio e spiega come eseguire la conversione dell'area di lavoro."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 36a08cab2b1d409d2de382a07cfd7259a1c94e13
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Aggiornamento di Azure Log Analytics alla nuova ricerca log

Il nuovo linguaggio di query di Log Analytics è ora disponibile. Per sfruttarlo è necessario aggiornare l'area di lavoro.  È possibile aggiornare l'area di lavoro manualmente o attendere che venga aggiornata automaticamente durante il periodo di implementazione che inizia alla fine del mese di ottobre e termina alla fine dell'anno.  Questo articolo illustra i vantaggi del nuovo linguaggio e spiega come eseguire la conversione dell'area di lavoro.  

## <a name="why-the-new-language"></a>Perché il nuovo linguaggio?
Siamo consapevoli del fatto che qualsiasi transizione comporti disagi, ma non stiamo modificando il linguaggio di query solo per il gusto di farlo.  Questa modifica offre un valore significativo ai clienti di Log Analytics sotto molti aspetti.

- **Semplice e potente.** Il nuovo linguaggio è più facile da capire ed è analogo a SQL, con costrutti più simili al linguaggio naturale rispetto alla versione precedente.
- **Linguaggio di piping completo.**  Il nuovo linguaggio presenta funzionalità di piping più avanzate rispetto alla versione precedente.  È possibile eseguire il piping praticamente di qualsiasi output verso un altro comando, per creare query più complesse rispetto a quanto fosse possibile precedentemente.
- **Estrazione di campi in fase di ricerca.**  Il nuovo linguaggio supporta campi calcolati al runtime più avanzati rispetto alla versione precedente.  È possibile usare calcoli complessi per campi estesi e quindi usare i campi calcolati per altri comandi, inclusi join e aggregazioni.
- **Join avanzati.**  Il nuovo linguaggio fornisce join più avanzati rispetto alla versione precedente, inclusa la possibilità di eseguire join di tabelle su più campi, usare join interni ed esterni e creare un join su campi estesi.
- **Funzioni di data/ora.**  Il nuovo linguaggio presenta funzioni di data/ora più avanzate della versione precedente.
- **Funzioni di analisi avanzate.**  Il nuovo linguaggio presenta algoritmi avanzati per valutare i criteri dei set di dati e confrontare set di dati diversi.
- **Portale Advanced Analytics.**  Il portale Advanced Analytics offre funzionalità di analisi non disponibili nel portale Log Analytics, inclusa la modifica multirighe delle query, altre visualizzazioni e diagnostica avanzata.
- **Coerenza con altre applicazioni.**  Il nuovo linguaggio e il portale Advanced Analytics sono già utilizzati per l'analisi in Application Insights.  La sua implementazione per Log Analytics aggiunge coerenza tra i servizi di Azure.
- **Migliore integrazione con Power BI.** Le query nel nuovo linguaggio possono essere esportate in Power BI Desktop, che mette a disposizione complesse funzionalità di trasformazione di dati.
- **E altro ancora.** Per informazioni dettagliate ed esercitazioni sul nuovo linguaggio, vedere il sito sul [linguaggio di query di Azure Log Analytics](https://docs.loganalytics.io).


## <a name="when-can-i-upgrade"></a>Quando è possibile eseguire l'aggiornamento?
L'aggiornamento verrà implementato in tutte le aree di Azure, perciò potrebbe essere disponibile in alcune aree prima di altre.  Per sapere quando è disponibile l'aggiornamento dell'area di lavoro, fare attenzione alla comparsa del banner che invita a eseguire l'aggiornamento nella parte superiore dell'area di lavoro stessa.

![Aggiornamento 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Se l'area di lavoro viene aggiornata automaticamente, viene visualizzato un banner che segnala l'avvenuto aggiornamento e riporta un riepilogo degli eventuali problemi verificatisi.

 ![aggiornamento automatico](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Cosa accade dopo l'aggiornamento?
Durante la conversione, nell'area di lavoro vengono apportate le modifiche seguenti:

- Tutte le ricerche salvate, le regole di avviso e le viste create con Progettazione vista vengono convertite automaticamente al nuovo linguaggio.  Le ricerche incluse nelle soluzioni non vengono convertite automaticamente, ma sono convertite istantaneamente quando vengono aperte.  
- [Dashboard](log-analytics-dashboards.md) è deprecato in favore di [Progettazione vista](log-analytics-view-designer.md) e dei [dashboard di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards.md).  I riquadri aggiunti al Dashboard sono ancora disponibili, ma sono di sola lettura.
- L'[integrazione di Power BI](log-analytics-powerbi.md) viene sostituita da un nuovo processo.  Eventuali pianificazioni di Power BI create dall'utente vengono disabilitate ed è necessario sostituirle con il nuovo processo.
- Le risposte di [azioni avviso](log-analytics-alerts-actions.md) tramite webhook e runbook hanno un nuovo formato e può essere necessario aggiornare di conseguenza le regole di avviso.
- Per le domande più comuni sull'aggiornamento, vedere le [domande frequenti sulla ricerca log](log-analytics-log-search-faq.md).

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Come è possibile sapere se si sono verificati problemi di aggiornamento?
Dopo il completamento dell'aggiornamento, nelle impostazioni dell'area di lavoro è presente una sezione **Riepilogo aggiornamento**.  Controllare in questa sezione per informazioni sull'aggiornamento e per visualizzare eventuali problemi che si sono verificati.

 ![Riepilogo aggiornamento](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Come si esegue l'aggiornamento manuale?
È possibile aggiornare l'area di lavoro quando nella parte superiore del portale è visualizzato il banner.  

1.  Per avviare il processo di aggiornamento, fare clic sul banner con la dicitura **Altre informazioni e aggiorna**.

    ![Aggiornamento 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Leggere le informazioni aggiuntive sull'aggiornamento nella pagina delle informazioni sull'aggiornamento.

    ![Aggiornamento 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Fare clic su **Aggiorna** per avviare l'aggiornamento.

    ![Aggiornamento 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Una casella di notifica in alto a destra mostra lo stato.
    
    ![Aggiornamento 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  La procedura è terminata.  Passare alla pagina Registro log per esaminare l'area di lavoro aggiornata.

    ![Aggiornamento 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Se si verifica un problema che non consente il completamento dell'aggiornamento, è possibile passare al [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda o [creare una richiesta di assistenza](../azure-supportability/how-to-create-azure-support-request.md) dal portale di Azure.

## <a name="how-do-i-learn-the-new-language"></a>Come è possibile imparare il nuovo linguaggio?
Essendo usato da più servizi, abbiamo creato un [sito esterno in cui pubblicare la documentazione](https://docs.loganalytics.io/) per il nuovo linguaggio.  Sono incluse esercitazioni, esempi e una guida di riferimento completa che consente di imparare il necessario in breve tempo. È possibile eseguire un'esercitazione dettagliata sul nuovo linguaggio in [Getting Started with Queries](https://go.microsoft.com/fwlink/?linkid=856078) (Introduzione alle query). È anche possibile accedere alla guida di riferimento del linguaggio in [Log Analytics query language](https://go.microsoft.com/fwlink/?linkid=856079) (Linguaggio di query di Log Analytics).  

Se si vuole provare il nuovo linguaggio in un ambiente demo che include una serie di dati di esempio, vedere l'[ambiente playground](https://portal.loganalytics.io/demo#/discover/home).

Se si conosce già il linguaggio di query di Log Analytics precedente, è possibile usare il convertitore di linguaggio che viene aggiunto all'area di lavoro come parte dell'aggiornamento.  È sufficiente digitare la query precedente e quindi fare clic su **Converti** per visualizzare la versione convertita.  È possibile quindi fare clic sul pulsante di ricerca per eseguire la ricerca o copiare e incollare la query convertita per utilizzarla altrove, ad esempio come regola di avviso.  È anche possibile vedere il [foglio informativo](log-analytics-log-search-transition.md) con il confronto diretto con le query più comuni nel linguaggio legacy.

![Convertitore di linguaggio](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Passaggi successivi
- Completare un'[esercitazione sul nuovo linguaggio](https://go.microsoft.com/fwlink/?linkid=856078).
- Completare un'[esercitazione sull'uso del portale di ricerca log](log-analytics-log-search-log-search-portal.md) con il nuovo linguaggio di query.
- Vedere un'introduzione al nuovo [portale Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587).
