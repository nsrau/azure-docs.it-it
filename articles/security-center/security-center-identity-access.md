---
title: "Monitorare identità e accesso nel Centro sicurezza di Azure | Microsoft Docs"
description: "Informazioni su come usare le funzionalità per identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità."
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Monitorare identità e accesso nel Centro sicurezza di Azure
Questo articolo illustra come usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso degli utenti.

## <a name="why-monitor-identity-and-access"></a>Perché monitorare identità e accesso
L'identità deve essere il piano di controllo per l'organizzazione e alla protezione dell'identità deve essere attribuita la massima priorità. In passato, le organizzazioni erano circondate da perimetri che costituivano uno dei limiti di difesa principali. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Il monitoraggio delle attività associate alle identità consente di intraprendere azioni proattive prima che si verifichi un evento imprevisto o azioni reattive per bloccare un tentativo di attacco. Il dashboard Identità e accesso offre una panoramica dello stato delle identità, che include:

* Numero di tentativi di accesso non riusciti. 
* Account utente usati durante tali tentativi.
* Account che sono stati bloccati.
* Account con password modificate o reimpostate. 
* Numero corrente di account che hanno eseguito l'accesso.

## <a name="monitor-identity-and-access-activities"></a>Monitorare identità e attività di accesso
Per visualizzare le attività correnti correlate a identità e accesso, è necessario accedere al dashboard **Identità e accesso**.

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro sinistro selezionare **Identità e accesso** in **Prevenzione**. Se sono presenti più aree di lavoro viene visualizzato il selettore delle aree di lavoro.

    ![Selezione dell'area di lavoro](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se nella colonna a destra viene visualizzato **PIANO DI AGGIORNAMENTO**, questa area di lavoro usa una sottoscrizione gratuita. Per usare questa funzionalità, eseguire l'aggiornamento alla sottoscrizione Standard. Se nella colonna a destra viene visualizzato **RICHIEDE L'AGGIORNAMENTO**, aggiornare [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per usare questa funzionalità. Per altre informazioni sul piano tariffario, vedere i prezzi del Centro sicurezza. 
    > 
3. Se sono presenti più aree di lavoro da analizzare, classificare l'analisi in ordine di priorità in base alla colonna **ACCESSI NON RIUSCITI**, in cui viene visualizzato il numero corrente di tentativi di accesso non riusciti nell'area di lavoro. Selezionare l'area di lavoro che si intende usare per visualizzare il dashboard **Identità e accesso**.

    ![Identità e accesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Le informazioni disponibili in questo dashboard consentono di identificare immediatamente una potenziale attività sospetta. Il dashboard è suddiviso in tre aree principali.

    a. **Comportamento identità**: offre un riepilogo delle attività correlate alle identità che vengono eseguite nell'area di lavoro.

    b. **Accessi non riusciti**: consente di identificare rapidamente la causa principale dei tentativi di accesso non riusciti e visualizza un elenco dei 10 account con il maggior numero di tentativi non riusciti.

    c. **Accessi nel tempo**: consente di identificare rapidamente il numero di accessi nel tempo e visualizza un elenco degli account computer con il maggior numero di tentativi di accesso.
    
Indipendentemente dal riquadro selezionato, il dashboard visualizzato è basato sulla query di ricerca log. L'unica differenza è costituita dal tipo di query e dal risultato. È comunque possibile selezionare un elemento, ad esempio un computer, e visualizzare i dati correlati. 

## <a name="see-also"></a>Vedere anche 
In questo articolo si è appreso come monitorare identità e accesso nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

