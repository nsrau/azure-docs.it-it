---
title: Utilizzi dell'analisi dei costi comune in Gestione costi di Azure
description: Questo articolo illustra come ottenere risultati per le attività di analisi dei costi comune in Gestione costi di Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 2102e3af26c8f5c4ed85543bc19360a923d070f3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199977"
---
# <a name="common-cost-analysis-uses"></a>Utilizzi dell'analisi dei costi comune

Gli utenti di Gestione costi di Azure spesso desiderano rispondere alle domande poste da molti altri utenti. Questo articolo illustra come ottenere risultati per le attività di analisi dei costi comune in Gestione costi.

## <a name="view-cost-breakdown-by-azure-service"></a>Visualizzare la suddivisione dei costi per servizio di Azure

La visualizzazione dei costi da parte di un servizio di Azure può aiutare a comprendere meglio le parti più costose dell'infrastruttura. Ad esempio, i costi di calcolo della macchina virtuale potrebbero essere ridotti. Tuttavia, è possibile che si accumulino costi di rete significativi a causa della quantità di informazioni emesse dalle macchine virtuali. Comprendere i principali fattori di costo dei servizi di Azure è essenziale per poter modificare l'utilizzo del servizio, in base alle esigenze.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Costo per servizio** e quindi raggruppare per **Livello di servizio**.
1. Modificare la visualizzazione in **Tabella**.

![Ripartizione dei costi per servizio di Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visualizzare la riipartizione dei costi per servizio di Azure

I servizi sono compilati con le risorse di Azure. La revisione dei costi in base alle risorse consente di identificare rapidamente i collaboratori del costo primario. Se un servizio dispone di risorse troppo onerose, provare ad apportare modifiche per ridurre i costi.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Costo per risorsa**.
1. Modificare la visualizzazione in **Tabella**.

![Visualizzare la ripartizione dei costi per servizio di Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visualizzare la suddivisione dei costi in base alle dimensioni selezionate

Le dimensioni consentono di organizzare i costi in base ai diversi valori dei metadati indicati negli addebiti. È ad esempio possibile raggruppare i costi in base alla località.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare il filtro **Raggruppa per**.  
    ![Consente di selezionare un elemento da Raggruppa per](./media/cost-analysis-common-uses/group-by.png)
1. Facoltativamente, è possibile salvare la vista per un uso successivo.
1. Fare clic su un grafico a torta sotto al grafico per visualizzare dati più dettagliati.  
    ![Visualizzare la ripartizione dei costi in base alle dimensioni selezionate](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Visualizzare i costi al giorno o al mese

Esaminare i costi giornalieri e mensili può essere utile per comprendere meglio se è presente un'ora della settimana o dell'anno in cui i costi sono maggiori. Se si registra più traffico di clienti in un periodo festivo, questo comporta un aumento corrispondente dei costi di Azure? Venerdì è un giorno più costoso del lunedì?

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Impostare la **Granularità** su **Mensile** o **Giornaliera**.

![Visualizzare i costi al giorno](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Visualizzare i costi per un tag specifico

Molti utenti di Azure applicano i tag alle risorse, ad esempio un centro di costo o un ambiente di sviluppo (produzione e test), per classificare meglio gli addebiti. I tag vengono visualizzati come dimensione nell'analisi dei costi. È possibile usare la dimensione per ottenere informazioni dettagliate sulle categorizzazioni personalizzate per l'assegnazione di tag.

Il supporto per i tag si applica all'uso segnalato *dopo* che il tag è stato applicato alla risorsa. I tag non vengono applicati retroattivamente per l'accumulo dei costi.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Raggruppa per** per il tag.

![Visualizzare i costi per un tag specifico](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Scaricare i dettagli di utilizzo

Il file di report dei dettagli sull'utilizzo in formato CSV fornisce una ripartizione di tutti gli addebiti accumulati per una fattura. È possibile utilizzare il report per confrontarlo e comprendere meglio la fattura. Ogni addebito inserito in fattura corrisponde alla ripartizione degli addebiti nel report sull'utilizzo.

1. Nel portale di Azure passare alla scheda **Utilizzo e addebiti** per un account o una sottoscrizione di fatturazione. Ad esempio: **Gestione dei costi e fatturazione** > **Fatturazione** > **Utilizzo e addebiti**.
1. Selezionare la voce da scaricare e fare clic sul simbolo di download.  
    ![Scaricare l'utilizzo e gli addebiti](./media/cost-analysis-common-uses/download1.png)
1.  Selezionare il file di utilizzo da scaricare.  
    ![Scegliere un file di utilizzo da scaricare](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visualizzare la ripartizione dei costi di EA mensile

La registrazione EA accumula i costi per l'intera organizzazione. Conoscere il modo in cui i costi vengono accumulati e fatturati nel tempo consente di coinvolgere le parti interessate appropriate per garantire che i costi siano gestiti in modo responsabile.

1. Nel portale di Azure passare a **Gestione dei costi e fatturazione** > **Panoramica**.
1. Fare clic su **Ripartizione** per il mese corrente e visualizzare il burn-down dell'impegno monetario.  
    ![Panoramica dei costi EA - riepilogo della ripartizione](./media/cost-analysis-common-uses/breakdown1.png)
1.  Fare clic sulla scheda **Utilizzo e addebiti** e visualizzare la ripartizione del mese precedente nell'intervallo di tempo scelto.  
    ![Scheda Utilizzo e addebiti](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visualizzare il costo mensile per l'iscrizione per termine

Usare una vista grafica dei costi mensili della registrazione per comprendere le tendenze dei costi e gli importi fatturati per un determinato periodo.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare la registrazione e impostare il termine di registrazione.
1. Impostare la granularità su mensile, quindi impostare la vista su **Colonna    (in pila)** .

È possibile raggruppare e filtrare i dati per un'analisi più dettagliata.

![Costo di registrazione mensile per termine](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visualizzare i costi accumulati per la registrazione EA

Consente di visualizzare i costi cumulativi netti nel tempo per comprendere le spese complessive per l'organizzazione per un determinato periodo.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare la registrazione, quindi visualizzare i costi accumulati correnti.

![Costi accumulati per la registrazione](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Passaggi successivi
- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
- Leggere la [Documentazione di Gestione dei costi di Azure](../index.yml).
