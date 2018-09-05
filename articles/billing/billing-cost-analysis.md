---
title: Esplorare i costi di Azure con l'analisi dei costi | Microsoft Docs
description: Questo articolo consente di usare l'analisi dei costi per esplorare e analizzare i costi aziendali di Azure.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db06956e01ed38ce1f8ee0ce92526be16733fd73
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818125"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Esplorare e analizzare i costi con l'analisi dei costi

Per poter controllare al meglio e ottimizzare i costi di Azure, è necessario conoscere l'origine dei costi all'interno dell'organizzazione. È anche utile sapere quanto costano i servizi e quali ambienti e sistemi supportano. È fondamentale avere visibilità sul dettaglio dei costi per conoscere in modo approfondito i modelli di spesa aziendali, che possono essere usati per applicare meccanismi di controllo dei costi, ad esempio i budget.

In questo articolo si usa l'analisi dei costi per esplorare e analizzare i costi aziendali. Si visualizzano i costi aggregati per l'organizzazione per capire dove i costi sono maggiori e identificare le tendenze di spesa. Si visualizzano i costi accumulati nel corso del tempo per stimare le tendenze dei costi mensili, trimestrali o addirittura annuali rispetto a un budget. Un budget consente di rispettare i vincoli finanziari oppure di visualizzare i costi giornalieri o mensili per isolare le irregolarità di spesa. È anche possibile scaricare i dati del report corrente per un'ulteriore analisi o per usarli in un sistema esterno.

## <a name="requirements"></a>Requisiti

L'analisi dei costi è disponibile per tutti i clienti con contratto Enterprise. È necessario avere l'accesso in lettura ad almeno uno degli ambiti seguenti per visualizzare i dati dei costi.

- Account di fatturazione con contratto Enterprise di Azure (registrazione)
- Sottoscrizione con contratto Enterprise di Azure
- Gruppo di risorse della sottoscrizione con contratto Enterprise di Azure

## <a name="review-costs-in-cost-analysis"></a>Esaminare i costi nell'analisi dei costi

Per esaminare i costi con l'analisi dei costi, aprire il portale di Azure, quindi passare a **Gestione dei costi e fatturazione** &gt; **Account di fatturazione** &gt; selezionare l'account di fatturazione con contratto Enterprise &gt; in Gestione costi selezionare **Analisi dei costi**.

La visualizzazione dell'analisi dei costi iniziale include le aree seguenti:

**Totale**: visualizza i costi totali per il mese corrente.

**Budget**: visualizza il limite di spesa per l'ambito selezionato, se disponibile.

**Costo accumulato**: visualizza le spese giornaliere accumulate totali, a partire dall'inizio del mese. Se è stato [creato un budget](billing-cost-management-budget-scenario.md#create-the-azure-budget) per l'account di fatturazione o la sottoscrizione, è possibile visualizzare rapidamente la tendenza di spesa in relazione al budget. Passare il puntatore del mouse su una data per visualizzare il costo accumulato fino a quel giorno.

**Grafici pivot (ad anello)**: forniscono pivot dinamici. Suddividono il costo totale in base a un set comune di proprietà standard. I grafici mostrano i costi accumulati per il mese corrente, dal più alto al più basso. È possibile cambiare i grafici pivot in qualsiasi momento selezionando un pivot diverso. Per impostazione predefinita, i costi sono suddivisi per servizio (categoria del contatore), località (area) e ambito figlio (ad esempio, gli account di registrazione sotto gli account di fatturazione, i gruppi di risorse sotto le sottoscrizioni e le risorse sotto i gruppi di risorse).

![Visualizzazione iniziale dell'analisi dei costi](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Personalizzazione delle visualizzazioni dei costi

La visualizzazione predefinita fornisce risposte rapide alle domande più comuni, ad esempio:

- Quanto è stato speso?
- Il budget verrà rispettato?

Esistono tuttavia molti i casi in cui è necessaria un'analisi più approfondita. La personalizzazione inizia nella parte superiore della pagina, con la selezione della data.

Per impostazione predefinita, l'analisi dei costi mostra i dati per il mese corrente. Usare il selettore data per passare rapidamente all'ultimo mese, al mese corrente, al trimestre di calendario, all'anno di calendario corrente o a un intervallo di date personalizzato di propria scelta. Selezionare l'ultimo mese è il modo più rapido per analizzare la fattura di Azure più recente e riconciliare facilmente le spese. Le opzioni relative a trimestre e anno corrente consentono di tenere traccia dei costi rispetto ai budget a lungo termine. In alternativa, è possibile selezionare un intervallo di date più lungo o con granularità più fine, ad esempio un singolo giorno, gli ultimi sette giorni o un altro periodo fino a un anno prima del mese corrente.

![Selettore date](./media/billing-cost-analysis/date-selector.png)

Per impostazione predefinita, l'analisi dei costi mostra anche i costi **accumulati**. I costi accumulati includono tutti i costi per ogni singolo giorno oltre a quelli dei giorni precedenti, con una visualizzazione in continua crescita dei costi giornalieri accumulati. Questa visualizzazione è ottimizzata per mostrare la tendenza rispetto a un budget per l'intervallo di tempo selezionato.

È disponibile anche una visualizzazione **giornaliera**. Mostra i costi per ogni singolo giorno e non una tendenza di crescita. La visualizzazione giornaliera è ottimizzata per mostrare eventuali irregolarità, ad esempio impennate o flessioni dei costi da un giorno all'altro. Quando si seleziona un budget, la visualizzazione giornaliera mostra anche una stima del budget giornaliero. Se i costi giornalieri sono costantemente al di sopra del budget giornaliero stimato, è probabile che il budget mensile verrà superato. Il budget stimato giornaliero è semplicemente un mezzo per poter visualizzare il budget a un livello più basilare. Quando si verificano fluttuazioni nei costi giornalieri, il confronto tra il budget giornaliero stimato e il budget mensile è meno preciso.

![Visualizzazione giornaliera](./media/billing-cost-analysis/daily-view.png)

È possibile usare l'opzione **Raggruppa per** per selezionare una categoria di gruppi e cambiare i dati visualizzati nel grafico ad area del totale nella parte superiore. Il raggruppamento consente di visualizzare rapidamente come la spesa viene organizzata per tipo di risorsa. Ecco una visualizzazione dei costi dei servizi di Azure nell'ultimo mese.

![Visualizzazione cumulativa giornaliera raggruppata](./media/billing-cost-analysis/grouped-daily-accum-view.png)

I grafici pivot sono nella visualizzazione Totale nella parte superiore. Usarli per aprire le visualizzazioni di categorie di raggruppamenti e filtri diverse. Quando si seleziona una categoria di gruppi, il set completo di dati per la visualizzazione del totale è nella parte inferiore della visualizzazione. Di seguito è riportato un esempio per i gruppi di risorse.

![Dati completi per la visualizzazione corrente](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Scaricare i dati dell'analisi dei costi

Quando si **scaricano** le informazioni dall'analisi dei costi, viene generato un file CSV per tutti i dati attualmente visualizzati nel portale di Azure. Eventuali filtri o raggruppamenti applicati vengono inclusi nel file. Nel file CSV vengono inclusi anche alcuni dati sottostanti per il grafico Totale della parte superiore, che non sono visualizzati attivamente nel portale.

## <a name="next-steps"></a>Passaggi successivi

Vedere altri [documenti sulla fatturazione e la gestione dei costi di Azure](billing-cost-management-budget-scenario.md).
