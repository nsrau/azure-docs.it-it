---
title: Valutazione baseline Web nella baseline della soluzione Sicurezza e controllo di Operations Management Suite | Microsoft Docs
description: "Questo documento illustra come usare la valutazione baseline Web nella soluzione Sicurezza e controllo di OMS per eseguire una valutazione baseline di tutti i server Web monitorati per finalità di conformità e sicurezza."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Valutazione baseline Web nella soluzione Sicurezza e controllo di Operations Management Suite
Questo documento illustra come usare le funzionalità della valutazione baseline Web in Sicurezza e controllo di OMS per accedere allo stato di protezione delle risorse monitorate.

## <a name="what-is-web-baseline-assessment"></a>Informazioni sulla valutazione baseline Web
OMS Security offre attualmente la possibilità di eseguire una valutazione baseline della sicurezza dei sistemi operativi. Analizza le impostazioni del sistema operativo dei server ogni 24 ore e permette di visualizzare le impostazioni potenzialmente vulnerabili. Per altre informazioni sull'argomento, vedere [Valutazione baseline nella soluzione Sicurezza e controllo di Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline).

L'obiettivo della valutazione baseline Web è trovare le impostazioni del server Web potenzialmente vulnerabili. Le tre origini principali per le configurazioni baseline Web sono le configurazioni di .NET, ASP.NET e IIS.  Come avviene per la valutazione baseline del sistema operativo, OMS Security analizza i server Web ogni 24 ore e permette di visualizzarne lo stato di sicurezza.  In Internet Information Services (IIS) le configurazioni sono personalizzabili e questo consente di eseguire l'override di diversi livelli di applicazioni e siti. L'analisi controlla le impostazioni per ogni livello di applicazione e sito, oltre che per il livello radice predefinito. Questo permette di identificare non solo le impostazioni potenzialmente vulnerabili e di apportare rapidamente le correzioni necessarie, ma anche le raccomandazioni per tali impostazioni.

>[!NOTE] 
>È possibile scaricare gli identificatori di configurazioni comuni e le regole di base usate da Sicurezza di OMS in questa [pagina](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Valutazione baseline della sicurezza Web

Per questa versione di anteprima la funzionalità è accessibile tramite l'opzione di ricerca di OMS e il dashboard Sicurezza e controllo di OMS. Per eseguire la query appropriata, seguire questa procedura:

1. Nel dashboard principale di **Microsoft Operations Management Suite** fare clic sul riquadro **Sicurezza e controllo**.
2. Nel dashboard **Sicurezza e controllo** è possibile visualizzare la prospettiva della baseline Web accanto a quella della baseline del sistema operativo.
   
    ![Valutazione baseline della sicurezza Web di Sicurezza e controllo di OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. Il riquadro sinistro visualizza il numero di server Web rispetto alla baseline, la percentuale media di regole passate in tutti i server esaminati e l'elenco dei server valutati.
4. Il riquadro destro elenca le regole univoche non riuscite per *gravità* e *tipo di regola*. Facendo clic su una delle regole del riquadro destro, verranno visualizzati i dettagli della regola. Un esempio è riportato nell'immagine seguente. La regola valutata viene elencata in *Impostazione della regola*. Il campo *AzId* è un identificatore univoco di ogni regola, usato da Microsoft per tenere traccia delle regola di base. Gli utenti possono anche visualizzare il *risultato previsto* (valore consigliato da Microsoft) e altri dettagli sull'impatto della regola sulla sicurezza.
    
    ![Query](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. È possibile creare query personalizzate per esaminare i risultati. 

La prima query che è possibile usare è **Web Baseline Assessment Summary** (Riepilogo valutazione baseline Web). Nel campo **Iniziare la ricerca qui** digitare questa query: Type*=SecurityBaselineSummary BaselineType=Web*. Il seguente è un esempio di output:

![Risultato della query](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>In questa query ogni record indica il riepilogo della valutazione in un singolo server.

Dopo aver aperto **Ricerca log**, è possibile digitare varie query per ottenere altre informazioni sulla valutazione baseline Web. Oltre alla precedente, in questa versione di anteprima è possibile usare anche le query seguenti:

**Web Baseline Rule Assessment** (Valutazione regola baseline Web): ogni record rappresenta una singola valutazione della regola baseline Web in un server singolo. Include tutti i dati di una regola non riuscita e i valori di *SitePath*, in cui la regola è stata valutata, *ExpectedResult* e *ActualResult*.

Query: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Risultato della query 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Show all results for a specific server** (Mostra tutti i risultati per un server specifico): questa query mostra come visualizzare i risultati di un server specifico: Query: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Risultato della query 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

È possibile usare record e query per creare dashboard, report o avvisi personalizzati. Ecco un controllo interfaccia utente di esempio che è possibile aggiungere al dashboard. Per informazioni su come visualizzare i dati mediante Visualizza finestra di progettazione in OMS, vedere [questo post di blog](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). La figura seguente mostra un esempio dell'aspetto del riquadro dopo la personalizzazione.

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Vedere anche
In questo documento sono state fornite informazioni sulla valutazione baseline Web di Sicurezza e controllo di OMS. Per altre informazioni sulle funzionalità di OMS per la sicurezza, vedere gli articoli seguenti:

* [Panoramica di Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-responding-alerts.md)
* [Monitoraggio delle risorse nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-monitoring-resources.md)


