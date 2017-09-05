---
title: "Domande frequenti sulla nuova funzionalità di ricerca log di Log Analytics | Microsoft Docs"
description: "Questo articolo riporta le domande più frequenti riguardanti l'aggiornamento di Log Analytics al nuovo linguaggio di query."
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
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Domande frequenti sulla nuova funzionalità di ricerca log di Log Analytics

Questo articolo riporta le domande più frequenti riguardanti l'aggiornamento di [Log Analytics al nuovo linguaggio di query](log-analytics-log-search-upgrade.md).



## <a name="upgrade-process"></a>Processo di aggiornamento

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Se sono disponibili varie aree di lavoro è possibile aggiornarle tutte contemporaneamente?  
R: No.  L'aggiornamento viene applicato a una singola area di lavoro alla volta. Non è attualmente disponibile alcun modo per aggiornare contemporaneamente molte aree di lavoro. Si noti che l'operazione interessa anche gli altri utenti dell'area di lavoro aggiornata.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>I dati di log esistenti raccolti in un'area di lavoro esistente verranno modificati in seguito all'aggiornamento?  
No. I dati di log disponibili nelle ricerche dell'area di lavoro non sono interessati dall'aggiornamento. Le ricerche, gli avvisi e le visualizzazioni salvati verranno convertiti automaticamente nel nuovo linguaggio di ricerca.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>Cosa accade se non si aggiorna l'area di lavoro?  
La funzionalità di ricerca log legacy verrà deprecata nei prossimi mesi. Le aree di lavoro non aggiornate verranno in seguito aggiornate automaticamente.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>L'area di lavoro è stata aggiornata, anche se non si è scelto di effettuare l'aggiornamento. Perché?  
È possibile sia stata aggiornata da un altro amministratore dell'area di lavoro. Si noti che tutte le aree di lavoro verranno aggiornate automaticamente quando il nuovo linguaggio sarà disponibile a livello generale.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>Se si esegue l'aggiornamento per errore, è possibile annullare l'operazione e ripristinare tutto?  
Non c'è problema.  Prima dell'aggiornamento dell'area di lavoro viene creato uno snapshot ed è quindi possibile ripristinarlo. Tenere presente, tuttavia, che andranno perduti gli avvisi, le ricerche o le visualizzazioni salvati dopo l'aggiornamento.  Per ripristinare l'ambiente dell'area di lavoro, seguire la procedura in [Dopo aver eseguito l'aggiornamento è possibile tornare indietro?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade).



## <a name="log-searches"></a>Ricerche log

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Le ricerche salvate all'esterno dell'area di lavoro aggiornata possono essere convertite automaticamente al nuovo linguaggio di ricerca?
È possibile usare lo strumento di conversione del linguaggio nella pagina della ricerca log per convertire ogni ricerca.  Non è disponibile alcun metodo per convertire automaticamente più ricerche senza aggiornare l'area di lavoro.


## <a name="alerts"></a>Avvisi

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Nel tempo sono state create molte regole di avviso. È necessario crearle nuovamente nel nuovo linguaggio dopo l'aggiornamento?  
No, le regole di avviso vengono convertite automaticamente nel nuovo linguaggio di ricerca durante l'aggiornamento.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>L'aggiornamento implica modifiche per l'integrazione con Power BI?
Sì.  Dopo aver aggiornato l'area di lavoro, il processo per esportare i dati di Log Analytics in Power BI non funzionerà più.  Le pianificazioni esistenti create prima dell'aggiornamento verranno disabilitate.  Dopo l'aggiornamento, Azure Log Analytics usa la stessa piattaforma di Application Insights e il processo per esportare le query di Log Analytics in Power BI è uguale al [processo usato per esportare le query di Application Insights in Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

## <a name="dashboards"></a>Dashboard

### <a name="can-i-still-use-dashboards"></a>È ancora possibile usare i dashboard?
È possibile continuare a usare i dashboard creati prima dell'aggiornamento dell'area di lavoro, ma non è possibile modificarli o crearne di nuovi.  È possibile continuare a creare e modificare le viste con [Progettazione viste](log-analytics-view-designer.md). 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni, sull'[aggiornamento dell'area di lavoro al nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md).

