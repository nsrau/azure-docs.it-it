---
title: "Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure | Microsoft Docs"
description: "Questo documento illustra come usare la funzionalità di identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure
Questo documento illustra come usare il Centro sicurezza di Azure per monitorare l'identità e l'attività di accesso degli utenti.

## <a name="why-monitor-identity-and-access"></a>Perché monitorare identità e accesso
L'identità deve essere il piano di controllo per l'organizzazione e alla protezione dell'identità deve attribuita la massima priorità. Mentre in passato le organizzazioni erano protette da perimetri che costituivano uno dei limiti di difesa principali, oggi con la quantità sempre più ampia di dati e app trasferiti nel cloud l'identità diventa il nuovo perimetro.

Il monitoraggio delle attività relative all'identità consentirà di intraprendere azioni proattive prima che un evento imprevisto si verifichi o azioni reattive per interrompere un tentativo di attacco. Il dashboard Identità e accesso fornisce una panoramica dello stato dell'identità, con il numero di tentativi di accesso non riusciti, l'account utente usato durante i tentativi, gli account bloccati, gli account con password modificata o reimpostata e il numero attuale di account connessi.

## <a name="how-to-monitor-identity-and-access-activities"></a>Come monitorare l'identità e le attività di accesso
Seguire questa procedura per visualizzare le attività correnti correlate a identità e accesso. È necessario accedere al dashboard **Identità e accesso**:

1.  Aprire il dashboard del **Centro sicurezza**.
2.  Nel riquadro a sinistra fare clic su **Identità e accesso** in **Prevenzione**. Se sono presenti più aree di lavoro viene visualizzato il selettore delle aree di lavoro.

    ![Selezione dell'area di lavoro](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se l'ultima colonna visualizza **UPGRADE PLAN** (AGGIORNA PIANO), l'area di lavoro usa la sottoscrizione gratuita ed è necessario eseguire l'aggiornamento al piano Standard per usare questa funzionalità. Se la colonna visualizza REQUIRES UPDATE (AGGIORNAMENTO NECESSARIO), per usare questa funzionalità è necessario aggiornare [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). Per altre informazioni sul piano tariffario, vedere i prezzi del Centro sicurezza di Azure. 
    > 
3. Se è presente più di un'area di lavoro da analizzare, è possibile assegnare una priorità all'analisi in base alla colonna **ACCESSI NON RIUSCITI**, che indica il numero corrente di tentativi di accesso non riusciti nell'area di lavoro. Selezionare l'area di lavoro che si intende usare per visualizzare il dashboard **Identità e accesso**.

    ![Identità e accesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Le informazioni disponibili in questo dashboard consentono di identificare immediatamente una potenziale attività sospetta. Questo dashboard è suddiviso in tre aree principali:
    * **Comportamento identità**: riepiloga le attività correlate all'identità che vengono eseguite nell'area di lavoro.
    * **Accessi non riusciti**: consente di identificare rapidamente la causa principale del tentativo di accesso non riuscito e visualizza un elenco dei dieci account con il maggior numero di tentativi di accesso non riusciti.
    * **Accessi nel tempo**: consente di identificare rapidamente il numero di accessi nel tempo e visualizza un elenco di account computer con il maggior numero di tentativi di accesso.
    
Indipendentemente dal riquadro selezionato, il dashboard visualizzato si basa sulla [query di ricerca nei log](https://docs.microsoft.com/azure/security-center/security-center-search), l'unica differenza è il tipo di query e il risultato. È comunque possibile selezionare un elemento, ad esempio un computer, fare clic su di esso e vedere i dati correlati. 

## <a name="see-also"></a>Vedere anche
In questo documento si è appreso come monitorare identità e accesso nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.


