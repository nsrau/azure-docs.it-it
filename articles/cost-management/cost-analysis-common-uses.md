---
title: Common Cost Analysis USA in gestione costi di Azure
description: Questo articolo illustra come ottenere risultati per le attività comuni di analisi dei costi in gestione costi di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 4798b6908069af229c2bed4a2e5f38fa60e0d554
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849106"
---
# <a name="common-cost-analysis-uses"></a>Uso di analisi dei costi comuni

Gli utenti di gestione costi di Azure spesso vogliono rispondere alle domande poste da molti altri utenti. Questo articolo illustra come ottenere risultati per le attività comuni di analisi dei costi in gestione costi.

## <a name="view-cost-breakdown-by-azure-service"></a>Visualizza suddivisione dei costi per servizio di Azure

La visualizzazione dei costi da parte di un servizio di Azure può aiutare a comprendere meglio le parti dell'infrastruttura più convenienti. Ad esempio, i costi di calcolo della macchina virtuale potrebbero essere ridotti. Tuttavia, è possibile che si accumulino costi di rete significativi a causa della quantità di informazioni emesse dalle macchine virtuali. Comprendere i principali fattori di costo dei servizi di Azure è essenziale per poter modificare l'utilizzo del servizio, in base alle esigenze.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare **costo per servizio** e quindi raggruppare in base al **livello di servizio**.
1. Modificare la visualizzazione in **tabella**.

![Suddivisione dei costi per servizio di Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visualizzare la suddivisione dei costi per risorsa di Azure

I servizi sono compilati con risorse di Azure. La revisione dei costi in base alle risorse consente di identificare rapidamente i collaboratori di costo primario. Se un servizio dispone di risorse troppo onerose, provare a apportare modifiche per ridurre i costi.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare **costo per risorsa**.
1. Modificare la visualizzazione in **tabella**.

![Visualizzare la suddivisione dei costi per risorsa di Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visualizza suddivisione dei costi in base alle dimensioni selezionate

Le dimensioni consentono di organizzare i costi in base ai diversi valori dei metadati indicati negli addebiti. È ad esempio possibile raggruppare i costi in base alla località.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare il filtro **Raggruppa per** .  
    ![selezionare un elemento Group by](./media/cost-analysis-common-uses/group-by.png)
1. Facoltativamente, è possibile salvare la vista per un uso successivo.
1. Fare clic su un grafico a torta al di sotto del grafico per visualizzare dati più dettagliati.  
    ![Visualizza suddivisione dei costi in base alle dimensioni selezionate](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Visualizza i costi al giorno o al mese

Esaminare i costi giornalieri e mensili può essere utile per comprendere meglio se è presente un'ora della settimana o dell'anno in cui i costi sono maggiori. Se si dispone di più traffico dei clienti in un periodo festivo, questo comporta un aumento corrispondente dei costi di Azure? Venerdì è un giorno più costoso del lunedì?

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Impostare la **granularità** su **mensile** o **giornaliera**.

![Visualizza costi al giorno](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Visualizzare i costi per un tag specifico

Molti utenti di Azure applicano i tag alle risorse, ad esempio un centro di costo o un ambiente di sviluppo (produzione e test), per classificare meglio i costi. I tag vengono visualizzati come dimensione nell'analisi dei costi. È possibile usare la dimensione per ottenere informazioni dettagliate sulle categorizzazioni personalizzate per l'assegnazione di tag.

Il supporto per i tag si applica all'uso segnalato *dopo* l'applicazione del tag alla risorsa. I tag non vengono applicati retroattivamente per i rollup dei costi.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare **Group by** per il tag.

![Visualizzare i costi per un tag specifico](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Scaricare i dettagli di utilizzo

Il file di report dei dettagli sull'utilizzo in formato CSV fornisce una ripartizione di tutti gli addebiti accumulati per una fattura. È possibile utilizzare il report per confrontarlo e comprendere meglio la fattura. Ogni addebito addebitato sulla fattura corrisponde a costi interrotti nel report sull'utilizzo.

1. Nella portale di Azure passare alla scheda **utilizzo e addebiti** per un account o una sottoscrizione di fatturazione. Ad esempio: **Gestione costi + fatturazione** > **fatturazione** > **utilizzo + addebiti**.
1. Selezionare la voce da scaricare e quindi fare clic sul simbolo di download.  
    ![Scarica utilizzo e addebiti](./media/cost-analysis-common-uses/download1.png)
1.  Selezionare il file di utilizzo da scaricare.  
    ![Scegliere un file di utilizzo da scaricare](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visualizza ripartizione costi di EA mensile

La registrazione EA accumula i costi per l'intera organizzazione. Conoscere il modo in cui i costi accumulati e vengono fatturati nel tempo consente di coinvolgere le parti interessate appropriate per garantire che i costi siano gestiti in modo responsabile.

1. Nella portale di Azure passare a **Gestione costi + fatturazione** > **Panoramica**.
1. Fare clic su **suddivisione** per il mese corrente e visualizzare il Burn-down dell'impegno monetario.  
    Panoramica dei costi EA ![-Riepilogo della suddivisione](./media/cost-analysis-common-uses/breakdown1.png)
1.  Fare clic sulla scheda **utilizzo e addebiti** e visualizzare la suddivisione del mese precedente nell'intervallo di tempo scelto.  
    scheda utilizzo e addebiti ![](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visualizza il costo mensile per la registrazione per termine

Usare una visualizzazione grafica dei costi mensili della registrazione per comprendere le tendenze dei costi e gli importi fatturati per un determinato periodo.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare la registrazione e impostare il termine di registrazione.
1. Impostare la granularità su mensile, quindi impostare la visualizzazione su **colonna (in pila)** .

È possibile raggruppare e filtrare i dati per un'analisi più dettagliata.

![Costo di registrazione mensile per termine](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visualizza i costi accumulati per la registrazione EA

Consente di visualizzare i costi cumulativi netti nel tempo per comprendere le spese complessive per l'organizzazione per un determinato periodo.

1. Nella portale di Azure passare all'analisi dei costi per l'ambito. Ad esempio: **Gestione costi + fatturazione** > **Gestione costi** > l' **analisi dei costi**.
1. Selezionare la registrazione e quindi visualizzare i costi accumulati correnti.

![Costi accumulati per la registrazione](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Passaggi successivi
- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
- Leggere la [documentazione di gestione dei costi](index.yml).
