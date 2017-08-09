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
ms.date: 07/31/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 5101d0356460160aed8559a314e8287ff5845121
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log

> [!NOTE]
> L'aggiornamento del nuovo linguaggio di query di Log Analytics è attualmente facoltativo, perciò si ha tutto il tempo di [passare al nuovo linguaggio](https://docs.loganalytics.io/learn/tutorials/getting_started_with_queries.html).  

Il nuovo linguaggio di query di Log Analytics è arrivato ed è necessario aggiornare l'area di lavoro per sfruttarlo.  Questo articolo illustra i vantaggi del nuovo linguaggio e spiega come eseguire la conversione dell'area di lavoro.  Se si sceglie di non eseguire l'aggiornamento adesso, l'area di lavoro continuerà a funzionare come prima, ma sarà convertita automaticamente in un secondo momento.  Si riceverà la notifica della data prestabilita con molto anticipo.

Questo articolo riporta informazioni dettagliate sul nuovo linguaggio e il processo di aggiornamento.

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
- **E altro ancora.** Per informazioni dettagliate ed esercitazioni sul nuovo linguaggio, vedere il sito sul [linguaggio di query di Azure Log Analytics](https://docs.loganalytics.io/index.html).


## <a name="when-can-i-upgrade"></a>Quando è possibile eseguire l'aggiornamento?
L'aggiornamento verrà implementato in tutte le aree di Azure, perciò potrebbe essere disponibile in alcune aree prima di altre.  Per sapere quando è disponibile l'aggiornamento della propria area di lavoro, osservare quando compare il banner viola nella parte superiore dell'area di lavoro stessa che invita a eseguire l'aggiornamento. 

![Aggiornamento 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>Cosa succede quando si esegue l'aggiornamento?
Quando si converte l'area di lavoro, eventuali ricerche salvate, regole di avviso e viste create con Progettazione viste vengono convertite automaticamente al nuovo linguaggio.  Le ricerche incluse nelle soluzioni non vengono convertite automaticamente, ma sono convertite istantaneamente quando vengono aperte.  Il processo è completamente trasparente all'utente.

## <a name="can-i-go-back-after-i-upgrade"></a>Dopo aver eseguito l'aggiornamento è possibile tornare indietro?
Quando si esegue l'aggiornamento, viene creata una copia di backup completa dell'area di lavoro che include uno snapshot di tutte le ricerche, le regole di avviso e le viste.  Ciò consente di ripristinare l'area di lavoro precedente in un secondo momento.

Per ripristinare l'area di lavoro precedente, passare a **Impostazioni** nell'area di lavoro e poi **Riepilogo aggiornamento**.  È quindi possibile selezionare l'opzione **Restore legacy workspace** (Ripristina area di lavoro legacy).  

![Ripristino dell'area di lavoro precedente](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>Come si esegue l'aggiornamento?
È possibile aggiornare l'area di lavoro quando si vede il banner viola nella parte superiore del portale.  Si noti che è possibile eseguire l'aggiornamento solo dal portale di OMS, anche se l'area di lavoro convertita sarà disponibile anche nel portale di Azure dopo il completamento dell'aggiornamento.

1.  Avviare il processo di aggiornamento facendo clic nel banner viola indicante **Altre informazioni e aggiorna**.<br>![Aggiornamento 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  Leggere le informazioni aggiuntive sull'aggiornamento nella pagina delle informazioni sull'aggiornamento.<br>![Aggiornamento 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  Fare clic su **Aggiorna** per avviare l'aggiornamento.<br>![Aggiornamento 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Una casella di notifica in alto a destra mostra lo stato.<br>![Aggiornamento 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  La procedura è terminata.  Passare alla pagina Registro log per esaminare l'area di lavoro aggiornata.<br>![Aggiornamento 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

Se si verifica un problema che non consente il completamento dell'aggiornamento, è possibile passare al [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda o [creare una richiesta di assistenza](../azure-supportability/how-to-create-azure-support-request.md) dal portale di Azure.

## <a name="how-do-i-learn-the-new-language"></a>Come è possibile imparare il nuovo linguaggio?
Essendo usato da più servizi, abbiamo creato un [sito esterno in cui pubblicare la documentazione](https://docs.loganalytics.io/) per il nuovo linguaggio.  Sono incluse esercitazioni, esempi e una guida di riferimento completa che consente di imparare il necessario in breve tempo. È possibile completare un'esercitazione sul nuovo linguaggio nella sezione di [introduzione alle query](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html) e accedere alla guida di riferimento al linguaggio nella sezione sul [linguaggio di query di Log Analytics](https://docs.loganalytics.io/queryLanguage/query_language.html).  

Se si conosce già il linguaggio di query di Log Analytics precedente, è possibile usare il convertitore di linguaggio che viene aggiunto all'area di lavoro come parte dell'aggiornamento.

È sufficiente digitare la query precedente e quindi fare clic su **Converti** per visualizzare la versione convertita.  È possibile quindi fare clic sul pulsante di ricerca per eseguire la ricerca o copiare e incollare la query convertita per utilizzarla altrove, ad esempio come regola di avviso.
  
![Convertitore di linguaggio](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Passaggi successivi
- Completare un'[esercitazione sul nuovo linguaggio](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html).
- Completare un'[esercitazione sull'uso del portale di ricerca log](log-analytics-log-search-log-search-portal.md) con il nuovo linguaggio di query.
- Vedere un'introduzione al nuovo [portale Advanced Analytics](https://docs.loganalytics.io/learn/tutorial_getting_started_with_analytics_portal.html).
