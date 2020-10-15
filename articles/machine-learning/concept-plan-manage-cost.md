---
title: Pianificare e gestire i costi
titleSuffix: Azure Machine Learning
description: Pianificare e gestire i costi per Azure Machine Learning con l'analisi dei costi in portale di Azure. Quando si compilano modelli di apprendimento automatico, è possibile ottenere ulteriori suggerimenti per ridurre i costi.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 031ec44cec473d9f2b2f05669aa3fc18084985f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704729"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Pianificare e gestire i costi per Azure Machine Learning

Questo articolo descrive come pianificare e gestire i costi per Azure Machine Learning. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi prima di aggiungere le risorse. Successivamente, quando si aggiungono le risorse di Azure, esaminare i costi stimati. Infine, è possibile usare i suggerimenti per il risparmio dei costi durante il training del modello con i cluster di calcolo gestiti Azure Machine Learning.

Dopo aver iniziato a usare le risorse di Azure Machine Learning, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. Esaminare anche i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire.

Comprendere che i costi per Azure Machine Learning sono solo una parte dei costi mensili nella fattura di Azure. Se si usano altri servizi di Azure, verranno addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Questo articolo illustra come pianificare e gestire i costi per Azure Machine Learning. Dopo aver acquisito familiarità con la gestione dei costi per Azure Machine Learning, applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

Quando si esegue il training dei modelli di Machine Learning, usare i cluster di calcolo gestiti Azure Machine Learning per sfruttare i suggerimenti più convenienti:

* Configurare i cluster di training per la scalabilità automatica
* Impostare le quote per la sottoscrizione e le aree di lavoro
* Impostare i criteri di terminazione per l'esecuzione del training
* Usare macchine virtuali con priorità bassa (VM)
* Usare un'istanza di macchina virtuale riservata di Azure

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure. 

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Stimare i costi

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di creare le risorse in un account Azure Machine Learning. A sinistra selezionare **ai + Machine Learning**, quindi selezionare **Azure Machine Learning** per iniziare.  

Lo screenshot seguente mostra la stima dei costi tramite il calcolatore:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Stima dei costi in Azure Calculator":::

Quando si aggiungono nuove risorse all'area di lavoro, tornare al calcolatore e aggiungere la stessa risorsa qui per aggiornare le stime dei costi.

Per ulteriori informazioni, vedere [Azure Machine Learning prezzi](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Ottenere gli avvisi relativi ai costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare [avvisi](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. Tuttavia, i budget e gli avvisi possono avere funzionalità limitate per gestire i singoli costi dei servizi di Azure, perché sono progettati per tenere traccia dei costi a un livello superiore.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse con Azure Machine Learning, si incorrono i costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o in base all'utilizzo delle unità richiesta. Non appena viene avviato l'utilizzo di Azure Machine Learning, i costi sono sostenuti. Per visualizzare questi costi, nel riquadro [analisi costi](../cost-management/quick-acm-cost-analysis.md) del portale di Azure.

È possibile visualizzare i costi in grafici e tabelle per intervalli di tempo diversi. È anche possibile visualizzare i costi rispetto ai budget e ai costi previsti. Passando a visualizzazioni più lunghe nel tempo è possibile identificare le tendenze di spesa e vedere dove si è verificata la sovraspesa. Se sono stati creati budget, vedere dove sono stati superati.  

Non verrà visualizzata un'area di servizio separata per Machine Learning.  Verranno invece visualizzate le varie risorse aggiunte alle aree di lavoro Machine Learning.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Usare Azure Machine Learning cluster di calcolo (AmlCompute)

Con i dati che cambiano continuamente, è necessario un training del modello veloce e semplificato e una ripetizione del training per mantenere modelli accurati. Tuttavia, il training continuo comporta un costo, soprattutto per i modelli di apprendimento avanzato sulle GPU. 

Azure Machine Learning utenti possono usare il cluster di calcolo Azure Machine Learning gestito, denominato anche AmlCompute. AmlCompute supporta un'ampia gamma di opzioni GPU e CPU. Il AmlCompute è ospitato internamente per conto della sottoscrizione da Azure Machine Learning. Offre la stessa sicurezza, conformità e governance di livello aziendale in Azure IaaS cloud scale.

Poiché questi pool di calcolo si trovano all'interno dell'infrastruttura IaaS di Azure, è possibile distribuire, ridimensionare e gestire la formazione con gli stessi requisiti di sicurezza e conformità del resto dell'infrastruttura.  Queste distribuzioni si verificano nella sottoscrizione e rispettano le regole di governance. Altre informazioni sulle [Azure Machine Learning di calcolo](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Configurare i cluster di training per la scalabilità automatica

I cluster con scalabilità automatica in base ai requisiti del carico di lavoro consentono di ridurre i costi, in modo da usare solo gli elementi necessari.

I cluster AmlCompute sono progettati per la scalabilità dinamica in base al carico di lavoro. Il cluster può essere scalato fino al numero massimo di nodi configurati. Al termine di ogni esecuzione, il cluster rilascia i nodi e viene ridimensionato al numero minimo di nodi configurato.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

È anche possibile configurare la quantità di tempo di inattività del nodo prima della riduzione. Per impostazione predefinita, il tempo di inattività prima del ridimensionamento è impostato su 120 secondi.

+ Se si esegue una sperimentazione meno iterativa, ridurre questo tempo per risparmiare sui costi.
+ Se si esegue una sperimentazione di sviluppo/test altamente iterativa, potrebbe essere necessario aumentare il tempo in modo che non si paghi per la scalabilità costante verso l'alto e verso il basso dopo ogni modifica apportata allo script o all'ambiente di training.

I cluster AmlCompute possono essere configurati per i requisiti di carico di lavoro modificabili in portale di Azure, usando la [classe AMLCOMPUTE SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true), l'interfaccia della riga di comando di [AmlCompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute), con le [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Impostare le quote sulle risorse

AmlCompute viene fornita con una [configurazione di quota (o limite)](how-to-manage-quotas.md#azure-machine-learning-compute). Questa quota è per famiglia di VM (ad esempio, serie dv2, serie NCv3) e varia in base all'area per ogni sottoscrizione. Le sottoscrizioni iniziano con impostazioni predefinite minime, ma è possibile usare questa impostazione per controllare la quantità di risorse Amlcompute disponibili per la creazione di una sottoscrizione. 

Configurare anche la [quota a livello di area di lavoro per famiglia di VM](how-to-manage-quotas.md#workspace-level-quota), per ogni area di lavoro in una sottoscrizione. In questo modo è possibile avere un controllo più granulare sui costi che ogni area di lavoro potrebbe potenzialmente sostenere e limitare alcune famiglie di macchine virtuali. 

Per impostare le quote a livello di area di lavoro, iniziare dall' [portale di Azure](https://portal.azure.com).  Selezionare un'area di lavoro nella sottoscrizione e selezionare **utilizzi + quote** nel riquadro sinistro. Selezionare quindi la scheda **Configura quote** per visualizzare le quote. Per impostare la quota, è necessario disporre dei privilegi nell'ambito della sottoscrizione, poiché si tratta di un'impostazione che interessa più aree di lavoro.

## <a name="set-run-autotermination-policies"></a>Imposta criteri esecuzione autoterminazione 

In alcuni casi, è necessario configurare le esecuzioni di training per limitarne la durata o terminarle in anticipo. Ad esempio, quando si usa l'ottimizzazione predefinita dell'iperparametro Azure Machine Learning o l'apprendimento automatico automatico.

Ecco alcune opzioni:
* Definire un parametro denominato `max_run_duration_seconds` in RunConfiguration per controllare la durata massima di un'esecuzione che può estendersi nel calcolo scelto (calcolo locale o remoto del cloud).
* Per l' [ottimizzazione iperparametri](how-to-tune-hyperparameters.md#early-termination), definire un criterio di terminazione anticipato da un criterio bandito, un criterio di arresto medio o un criterio di selezione troncamento. Per controllare ulteriormente le sweep di iperparametri, usare parametri come `max_total_runs` o `max_duration_minutes` .
* Per [Machine Learning automatizzato](how-to-configure-auto-train.md#exit), impostare criteri di terminazione simili usando il  `enable_early_stopping` flag. Usare anche proprietà quali `iteration_timeout_minutes` e `experiment_timeout_minutes` per controllare la durata massima di un'esecuzione o per l'intero esperimento.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Usare macchine virtuali con priorità bassa

Azure ti permette di usare capacità inutilizzate in eccesso come Low-Priority macchine virtuali tra set di scalabilità di macchine virtuali, batch e il servizio Machine Learning. Queste allocazioni sono pre-emptible, ma hanno un prezzo ridotto rispetto alle VM dedicate. In generale, è consigliabile usare Low-Priority VM per i carichi di lavoro batch. È consigliabile usarli anche per recuperare le interruzioni tramite i reinvii (per l'inferenza del batch) o i riavvii (per un training di apprendimento approfondito con Checkpoint).

Low-Priority VM hanno una singola quota separata dal valore della quota dedicata, ovvero dalla famiglia di macchine virtuali. [Altre informazioni sulle quote AmlCompute](how-to-manage-quotas.md).

 Le macchine virtuali Low-Priority non funzionano per le istanze di calcolo, perché devono supportare esperienze interattive dei notebook.

## <a name="use-reserved-instances"></a>Usare le istanze riservate

Un altro modo per risparmiare denaro sulle risorse di calcolo è l'istanza di macchina virtuale riservata di Azure. Con questa offerta, si esegue il commit di un anno o di tre anni. Questi sconti variano fino al 72% dei prezzi con pagamento in base al consumo e vengono applicati direttamente alla fattura mensile di Azure.

Azure Machine Learning calcolo supporta le istanze riservate intrinsecamente. Se si acquista un'istanza riservata di un anno o di tre anni, lo sconto verrà applicato automaticamente al calcolo gestito da Azure Machine Learning.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
* [Gestire e aumentare le quote di risorse](how-to-manage-quotas.md)
* [Gestione dei costi con l'analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Creare Azure Machine Learning calcolo con [SDK](how-to-create-attach-compute-cluster.md) o in [Studio](how-to-create-attach-compute-studio.md#amlcompute).
