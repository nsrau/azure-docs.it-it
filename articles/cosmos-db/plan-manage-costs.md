---
title: Pianificare e gestire i costi per Azure Cosmos DB
description: Informazioni su come pianificare e gestire i costi per Azure Cosmos DB tramite l'analisi dei costi in portale di Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152585"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Pianificare e gestire i costi per Azure Cosmos DB

Questo articolo descrive come pianificare e gestire i costi per Azure Cosmos DB. Usare prima di tutto il calcolatore della capacità di Azure Cosmos DB per pianificare i costi prima di aggiungere le risorse. Successivamente, quando si aggiungono le risorse di Azure, è possibile esaminare i costi stimati. Dopo aver iniziato a usare le risorse di Azure Cosmos DB, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire.

Comprendere che i costi per Azure Cosmos DB sono solo una parte dei costi mensili nella fattura di Azure. Se si usano altri servizi di Azure, verranno addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Questo articolo illustra come pianificare e gestire i costi per Azure Cosmos DB. Dopo aver acquisito familiarità con la gestione dei costi per Azure Cosmos DB, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Esaminare i costi stimati con il calcolatore della capacità

Usare il [calcolatore della capacità di Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) per stimare i costi prima di creare le risorse in un account Azure Cosmos. Il calcolatore della capacità viene usato per ottenere una stima della velocità effettiva richiesta e del costo del carico di lavoro. Per ottimizzare i costi e le prestazioni, è essenziale configurare i database e i contenitori di Azure Cosmos con la quantità corretta di velocità effettiva con provisioning o [unità richiesta (UR/sec)](request-units.md)per il carico di lavoro. È necessario immettere i dettagli, ad esempio il tipo di API, il numero di aree, le dimensioni degli elementi, le richieste di lettura/scrittura al secondo, i dati totali archiviati per ottenere una stima dei costi. Per altre informazioni sul calcolatore della capacità, vedere l'articolo relativo alla [stima](estimate-ru-with-capacity-planner.md) .

Lo screenshot seguente mostra la velocità effettiva e la stima dei costi usando il calcolatore della capacità:

![Stima dei costi nel calcolatore di capacità Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Esaminare i costi stimati dal portale di Azure

Quando si creano Azure Cosmos DB risorse da portale di Azure, è possibile visualizzare i costi stimati. Per esaminare la stima dei costi, attenersi alla procedura seguente:

1. Accedere al portale di Azure e passare all'account Azure Cosmos.
1. Passare alla **Esplora dati**.
1. Creare un nuovo contenitore, ad esempio un contenitore Graph.
1. Immettere la velocità effettiva necessaria per il carico di lavoro, ad esempio 400 ur/sec. Dopo aver inserito il valore di velocità effettiva, è possibile visualizzare la stima dei prezzi come illustrato nello screenshot seguente:

   ![Stima dei costi in portale di Azure](./media/plan-manage-costs/cost-estimate-portal.png)

Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce la spesa rispetto all'importo del credito. Quando si creano e si usano le risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite sono disabilitate per il resto del periodo di fatturazione. Non è possibile modificare il limite di credito, ma è possibile rimuoverlo. Per altre informazioni sui limiti di spesa, vedere [limite di spesa di Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Usare budget e avvisi relativi ai costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare avvisi per informare automaticamente gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. Tuttavia, possono avere una funzionalità limitata per gestire i costi dei singoli servizi di Azure, come il costo di Azure Cosmos DB perché sono progettati per tenere traccia dei costi a un livello superiore.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse con Azure Cosmos DB, si incorrono i costi. I costi delle unità di utilizzo delle risorse variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o all'utilizzo delle unità richiesta. Non appena viene avviato l'utilizzo di Azure Cosmos DB, i costi sono sostenuti ed è possibile visualizzarli nel riquadro [analisi costi](../cost-management/quick-acm-cost-analysis.md) della portale di Azure.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi Azure Cosmos DB in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, l'attuale, il mese precedente e l'anno. È anche possibile visualizzare i costi rispetto ai budget e ai costi previsti. Passando a visualizzazioni più lunghe nel tempo è possibile identificare le tendenze di spesa e vedere dove potrebbero essersi verificate le eccedenze. Se sono stati creati budget, è anche possibile vedere dove sono stati superati. Per visualizzare Azure Cosmos DB costi nell'analisi dei costi:

1. Accedi al [portale di Azure](https://portal.azure.com).

1. Aprire la finestra **Gestione costi e fatturazione** , selezionare **Gestione costi** dal menu e quindi selezionare **analisi dei costi**. È quindi possibile modificare l'ambito per una sottoscrizione specifica dall'elenco a discesa **ambito** .

1. Per impostazione predefinita, i costi per tutti i servizi vengono visualizzati nel primo grafico ad anello. Selezionare l'area nel grafico con l'etichetta "Azure Cosmos DB".

1. Per limitare i costi per un singolo servizio, ad esempio Azure Cosmos DB, selezionare **Aggiungi filtro** e quindi selezionare **nome servizio**. Quindi, scegliere **Azure Cosmos DB** dall'elenco. Ecco un esempio che mostra i costi solo per Azure Cosmos DB:
 
   ![Monitorare i costi con il riquadro analisi costi](./media/plan-manage-costs/cost-analysis-pane.png)

Nell'esempio precedente viene visualizzato il costo corrente per Azure Cosmos DB per il mese di febbraio. I grafici contengono anche Azure Cosmos DB costi in base alla località e al gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul funzionamento dei prezzi in Azure Cosmos DB:

* [Modello di determinazione dei prezzi di Azure Cosmos DB](how-pricing-works.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)
* [Ottimizzare i costi di archiviazione in Azure Cosmos DB](optimize-cost-storage.md)