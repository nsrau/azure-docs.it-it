---
title: Pianificare e gestire i costi per Azure Cosmos DB
description: Informazioni su come pianificare e gestire i costi per Azure Cosmos DB usando l'analisi dei costi nel portale di Azure.Learn how to plan for and manage costs for Azure Cosmos DB by using cost analysis in Azure portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152585"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Pianificare e gestire i costi per Azure Cosmos DB

Questo articolo descrive come pianificare e gestire i costi per Azure Cosmos DB. In primo luogo, si usa il calcolatore della capacità di Azure Cosmos DB per pianificare i costi prima di aggiungere risorse. Successivamente, quando si aggiungono le risorse di Azure, è possibile esaminare i costi stimati. Dopo aver iniziato a usare le risorse di Azure Cosmos DB, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È inoltre possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si desidera agire.

Tenere presente che i costi per Azure Cosmos DB sono solo una parte dei costi mensili nella fattura di Azure.Understand that the costs for Azure Cosmos DB are only a portion of the monthly costs in your Azure bill. Se si usano altri servizi di Azure, vengono fatturati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Questo articolo illustra come pianificare e gestire i costi per Azure Cosmos DB. Dopo aver acquisito familiarità con la gestione dei costi per Azure Cosmos DB, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](../cost-management-billing/costs/understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i dati relativi ai costi, è necessario effettuare almeno l'accesso in lettura per l'account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Esaminare i costi stimati con il calcolatore di capacitàReview estimated costs with capacity calculator

Usare il calcolatore della capacità del database Cosmos di Azure per stimare i costi prima di creare le risorse in un account Cosmos di Azure.Use the [Azure Cosmos DB capacity calculator](https://cosmos.azure.com/capacitycalculator/) to estimate costs before you create the resources in an Azure Cosmos account. Il calcolatore di capacità viene usato per ottenere una stima della velocità effettiva e del costo del carico di lavoro necessari. La configurazione dei database e dei contenitori di Azure Cosmos con la giusta quantità di velocità effettiva di cui è stato eseguito il provisioning o le unità di [richiesta (RU/s)](request-units.md)per il carico di lavoro è essenziale per ottimizzare i costi e le prestazioni. È necessario immettere dettagli quali il tipo di API, il numero di aree, le dimensioni dell'elemento, le richieste di lettura/scrittura al secondo, i dati totali archiviati per ottenere una stima dei costi. Per altre informazioni sul calcolatore di capacità, vedere l'articolo [stima.](estimate-ru-with-capacity-planner.md)

La schermata seguente mostra la velocità effettiva e la stima dei costi utilizzando il calcolatore di capacità:The following screenshot shows the throughput and cost stimation by using the capacity calculator:

![Stima dei costi nel calcolatore della capacità di Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Esaminare i costi stimati dal portale di AzureReview estimated costs from the Azure portal

Quando si creano risorse del database Cosmos di Azure dal portale di Azure, è possibile visualizzare i costi stimati. Utilizzare la procedura seguente per esaminare la stima dei costi:

1. Accedere al portale di Azure e passare all'account Cosmos di Azure.Sign into the Azure portal and navigate to your Azure Cosmos account.
1. Passare a **Esplora dati**.
1. Creare un nuovo contenitore, ad esempio un contenitore grafico.
1. Immettere la velocità effettiva necessaria per il carico di lavoro, ad esempio 400 RU/s.Input the throughput required for your workload such as 400 RU/s. Dopo aver immesso il valore di velocità effettiva, è possibile visualizzare la stima dei prezzi come illustrato nella schermata seguente:After you input the throughput value, you can see the pricing estimate as shown in the following screenshot:

   ![Stima dei costi nel portale di Azure](./media/plan-manage-costs/cost-estimate-portal.png)

Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce di spendere oltre l'importo del credito. Quando si creano e si usano risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite vengono disabilitate per il resto del periodo di fatturazione. Non puoi modificare il limite di credito, ma puoi rimuoverlo. Per altre informazioni sui limiti di spesa, vedere Limite di spesa di Azure.For more information about spending limits, see [Azure spending limit.](../billing/billing-spending-limit.md)

## <a name="use-budgets-and-cost-alerts"></a>Usare budget e avvisi relativi ai costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare avvisi per informare automaticamente gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, pertanto sono utili come parte di una strategia di monitoraggio generale dei costi. Tuttavia, potrebbero avere funzionalità limitate per gestire i singoli costi del servizio di Azure come il costo di Azure Cosmos DB perché sono progettati per tenere traccia dei costi a un livello superiore.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse con Azure Cosmos DB, si incorre in costi. I costi unitari di utilizzo delle risorse variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o in base all'utilizzo dell'unità richiesta. Non appena inizia l'utilizzo di Azure Cosmos DB, i costi vengono sostenuti ed è possibile visualizzarli nel riquadro di analisi dei costi nel portale di Azure.As soon as usage of Azure Cosmos DB starts, costs are sucurd and you can see them in the [cost analysis](../cost-management/quick-acm-cost-analysis.md) pane in the Azure portal.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi di Azure Cosmos DB in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono per giorno, corrente, mese precedente e anno. È inoltre possibile visualizzare i costi rispetto ai budget e ai costi previsti. Il passaggio a visualizzazioni più lunghe nel tempo consente di identificare le tendenze di spesa e di vedere dove potrebbe essersi verificata una spesa eccessiva. Se hai creato budget, puoi anche vedere facilmente dove hanno superato. Per visualizzare i costi di Azure Cosmos DB nell'analisi dei costi:To view Azure Cosmos DB costs in cost analysis:

1. Accedere al portale di [Azure](https://portal.azure.com).

1. Aprire la finestra **Gestione costi - Fatturazione,** selezionare **Gestione costi** dal menu, quindi selezionare Analisi **dei costi.** È quindi possibile modificare l'ambito per una sottoscrizione specifica dall'elenco a discesa **Ambito.You** can then change the scope for a specific subscription from the Scope dropdown.

1. Per impostazione predefinita, il costo per tutti i servizi viene visualizzato nel primo grafico ad anello. Selezionare l'area nel grafico con l'etichetta "Azure Cosmos DB".

1. Per ridurre i costi per un singolo servizio, ad esempio Azure Cosmos DB, selezionare **Aggiungi filtro** e quindi selezionare **Nome servizio**. Scegliere **quindi Azure Cosmos DB** dall'elenco. Ecco un esempio che mostra i costi solo per Azure Cosmos DB:Here's an example showing costs for just Azure Cosmos DB:
 
   ![Monitorare i costi con il riquadro Analisi costi](./media/plan-manage-costs/cost-analysis-pane.png)

Nell'esempio precedente viene visualizzato il costo corrente per Azure Cosmos DB per il mese di novembre. I grafici contengono anche i costi di Azure Cosmos DB per posizione e per gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento dei prezzi in Azure Cosmos DB, vedere gli articoli seguenti:See the following articles to learn more on how pricing works in Azure Cosmos DB:

* [Modello di determinazione dei prezzi di Azure Cosmos DB](how-pricing-works.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)
* [Ottimizzare i costi di archiviazione in Azure Cosmos DB](optimize-cost-storage.md)