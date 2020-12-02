---
title: Pianificare la gestione dei costi per Azure ExpressRoute
description: Informazioni su come pianificare e gestire i costi per Azure ExpressRoute usando l'analisi dei costi nel portale di Azure.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 38b6aa35c1b12e3fdaed4a60cd6e241a05d42efe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501509"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Pianificare e gestire i costi per Azure ExpressRoute

Questo articolo descrive come pianificare e gestire i costi per ExpressRoute. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi di ExpressRoute prima di aggiungere le risorse per il servizio per stimare i costi. Quando si aggiungono le risorse di Azure, esaminare i costi stimati. 

Dopo aver iniziato a usare le risorse di ExpressRoute, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. 

Tenere presente che i costi per ExpressRoute sono solo una parte dei costi mensili nella fattura di Azure. Anche se in questo articolo viene illustrato come pianificare e gestire i costi per ExpressRoute, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi in gestione costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. 

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Confronto tra locale e standard e Premium

ExpressRoute dispone di tre SKU del circuito diversi: [*locale*](./expressroute-faqs.md#expressroute-local), *standard* e [*Premium*](./expressroute-faqs.md#expressroute-premium). Il modo in cui viene addebitato l'utilizzo di ExpressRoute varia tra questi tre tipi di SKU. Con lo SKU locale viene addebitato automaticamente un piano dati illimitato. Con lo SKU standard e Premium è possibile scegliere tra un piano dati a consumo o senza limiti. Tutti i dati in ingresso sono gratuiti tranne quando si usa il componente aggiuntivo Copertura globale. È importante comprendere quali tipi di SKU e piano dati funzionano meglio per il carico di lavoro per ottimizzare i costi e il budget.

## <a name="estimate-costs"></a>Stimare i costi

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di creare un circuito ExpressRoute. 

1. A sinistra selezionare **rete** e quindi selezionare **Azure ExpressRoute** per iniziare. 

1. Selezionare la *zona* appropriata a seconda della posizione del peering. Vedere [provider di connettività ExpressRoute](./expressroute-locations-providers.md#partners) per selezionare la *zona* appropriata nell'elenco a discesa. 

1. Selezionare quindi lo *SKU*, la *velocità del circuito* e il *piano dati* per cui si vuole eseguire una stima. 

1. Nel *trasferimento di dati in uscita aggiuntivo* immettere una stima in GB della quantità di dati in uscita che è possibile usare nel corso di un mese. 

1. Infine, è possibile aggiungere il *componente aggiuntivo copertura globale* alla stima.

Lo screenshot seguente mostra la stima dei costi tramite il calcolatore:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="ExpressRoute stima dei costi in Azure Calculator":::

Per altre informazioni, vedere [prezzi di Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Costo stimato del gateway ExpressRoute

Se si usa un gateway ExpressRoute per collegare una rete virtuale al circuito ExpressRoute, attenersi alla procedura seguente per stimare i costi per l'utilizzo del gateway.

1. A sinistra selezionare **rete** e quindi selezionare **gateway VPN** per iniziare. 

1. Selezionare l' *area* per il gateway e quindi modificare il *tipo* in **gateway ExpressRoute**.

1. Selezionare il *tipo di gateway* dall'elenco a discesa.

1. Immettere le *ore del gateway*. (720 ore = 30 giorni)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Informazioni sul modello di fatturazione completo per ExpressRoute

ExpressRoute viene eseguito nell'infrastruttura di Azure che accumula i costi insieme a ExpressRoute quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire questo costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Costi che in genere si accumulano con ExpressRoute

Quando si crea un circuito ExpressRoute, è possibile scegliere di creare un gateway ExpressRoute per collegare la rete virtuale al circuito. Ai gateway viene addebitata una tariffa oraria più il costo di un circuito ExpressRoute. Vedere [prezzi di ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) e selezionare Gateway ExpressRoute per visualizzare le tariffe per SKU del gateway diversi.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Potrebbero maturare costi dopo l'eliminazione delle risorse

Se si dispone di un gateway ExpressRoute dopo l'eliminazione del circuito ExpressRoute, il costo verrà ancora addebitato fino a quando non viene eliminato.

### <a name="using-monetary-credit"></a>Uso del credito monetario

È possibile pagare gli addebiti per ExpressRoute con il credito dell'impegno monetario EA. Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse di Azure con ExpressRoute, si incorrono i costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o all'utilizzo di unità (byte, megabyte e così via). Non appena viene avviato l'uso di ExpressRoute, i costi sono sostenuti ed è possibile visualizzare i costi nell' [analisi](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)dei costi.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi del circuito ExpressRoute in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. Puoi anche visualizzare i costi rispetto ai budget e ai costi previsti. Il passare a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Viene visualizzato il punto in cui potrebbe essersi verificata la sovraspesa. Se sono stati creati budget, è anche possibile vedere dove sono stati superati.

Per visualizzare i costi di ExpressRoute nell'analisi dei costi:

1. Accedere al portale di Azure.

1. Passare a **sottoscrizioni**, selezionare una sottoscrizione dall'elenco e quindi selezionare  **analisi dei costi** nel menu. Selezionare **ambito** per passare a un ambito diverso nell'analisi dei costi. Per impostazione predefinita, i costi per i servizi vengono visualizzati nel primo grafico ad anello.

    I costi mensili effettivi vengono visualizzati quando inizialmente si apre l'analisi dei costi. Ecco un esempio che Mostra tutti i costi di utilizzo mensili.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Esempio che mostra i costi accumulati per una sottoscrizione":::
    

1.  Per limitare i costi per un singolo servizio, ad esempio Expressroute, selezionare **Aggiungi filtro** e quindi selezionare **nome servizio**. Quindi, selezionare **ExpressRoute**.

    Ecco un esempio che mostra i costi solo per ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Esempio che mostra i costi accumulati per ExpressRoute":::

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per le aree di Azure (località) e i costi ExpressRoute per gruppo di risorse. Da qui è possibile esplorare i costi autonomamente.

## <a name="create-budgets-and-alerts"></a>Creare budget e avvisi

È possibile creare [budget](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di gruppo e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessari o altri utenti per eseguire ulteriori analisi dei dati per i costi. Ad esempio, un team Finance può analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su come funziona il prezzo con Azure ExpressRoute. Vedere [Panoramica dei prezzi di Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
