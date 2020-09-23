---
title: Gestire le risorse e le quote
titleSuffix: Azure Machine Learning
description: Informazioni sulle quote nelle risorse per Azure Machine Learning e su come richiedere più quote.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: c86397b20a95f045ac5edfeb2cfa4833982df990
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897417"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gestire e aumentare le quote per le risorse con Azure Machine Learning


In questo articolo vengono illustrati i limiti preconfigurati per le risorse di Azure per la sottoscrizione di [Azure Machine Learning](overview-what-is-azure-ml.md) e le quote che è possibile gestire. Questi limiti vengono applicati per evitare lo sforamento del budget a causa di illeciti e per rispettare i vincoli di capacità di Azure. 

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate ad Azure Machine Learning. Questi limiti vanno dal limite applicato al numero delle [aree di lavoro](concept-workspace.md) ai limiti relativi al calcolo effettivo sottostante usato per il training o l'inferenza/assegnazione del punteggio dei modelli. 

Tenere presenti questi limiti quando si progettano e si ridimensionano le risorse di Azure Machine Learning per i carichi di lavoro di produzione. Ad esempio, se il cluster non raggiunge il numero previsto di nodi, è possibile che sia stato raggiunto un limite di core dell'ambiente di calcolo di Azure Machine Learning per la sottoscrizione. Per aumentare il limite o la quota oltre il valore del limite predefinito, è possibile aprire una richiesta di assistenza clienti online senza alcun addebito. Non è possibile aumentare i limiti oltre il valore massimo indicato nelle tabelle seguenti a causa dei vincoli di capacità di Azure. Se non è presente una colonna Limite massimo, la risorsa specificata non è associata a limiti modificabili.


Insieme alla gestione delle quote, è anche possibile scoprire come [pianificare & gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerazioni speciali

+ Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. È anche possibile [aumentare le quote](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

+ La quota viene condivisa fra tutti i servizi nelle sottoscrizioni, incluso Azure Machine Learning. L'unica eccezione è data dall'ambiente di calcolo di Azure Machine Learning che ha una quota separata rispetto alla quota dei core. Nel valutare le esigenze di capacità, assicurarsi di calcolare l'utilizzo della quota in tutti i servizi.

+ I limiti predefiniti variano in base al tipo di categoria dell'offerta, ad esempio Versione di valutazione gratuita o Pagamento in base al consumo, e alle serie delle macchine virtuali, ad esempio Dv2, F, G e così via.

## <a name="default-resource-quotas"></a>Quote predefinite per le risorse

Ecco un riepilogo dei limiti di quota per i vari tipi di risorsa all'interno della sottoscrizione di Azure.

> [!IMPORTANT]
> I limiti sono soggetti a modifiche. I limiti più aggiornati sono sempre disponibili nel [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) sulle quote a livello di servizio per tutti i servizi di Azure.

### <a name="virtual-machines"></a>Macchine virtuali
Per ogni sottoscrizione di Azure è previsto un limite al numero di macchine virtuali nei servizi o in autonomia. I core delle macchine virtuali devono rispettare un limite totale per l'area e un limite di serie per dimensione (Dv2, F e così via) per l'area, entrambi applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione sarà autorizzata a distribuire 30 VM A1 o 30 VM D1 o una combinazione delle due che non superi un totale di 30 core, ad esempio 10 VM A1 e 20 VM D1.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
Nell'[ambiente di calcolo di Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) è previsto un limite di quota predefinito per il numero di core e per il numero di risorse di calcolo consentite per ogni area in una sottoscrizione. Questa quota è separata dalla quota di core della VM precedente e i limiti di core non vengono condivisi tra i due tipi di risorse poiché AmlCompute è un servizio gestito che distribuisce le risorse in un modello ospitato.

Risorse disponibili:
+ I core dedicati per area hanno un limite predefinito di 24-300, a seconda del tipo di offerta di sottoscrizione con valori predefiniti maggiori per i tipi di offerta EA e CSP.  Il numero di core dedicati per ogni sottoscrizione può essere aumentato ed è diverso per ogni famiglia di macchine virtuali. Alcune famiglie di macchine virtuali specializzate come le serie NCv2, NCv3 o ND iniziano con un valore predefinito di zero core. Contattare il supporto di Azure inviando una richiesta di quota per ottenere informazioni sulle opzioni di aumento.

+ I core a bassa priorità per area hanno un limite predefinito di 100-3000 a seconda del tipo di offerta di sottoscrizione con impostazioni predefinite maggiori per i tipi di offerta EA e CSP. Il numero di core a bassa priorità per ogni sottoscrizione può essere aumentato ed è un valore singolo per le famiglie di macchine virtuali. Contattare il supporto di Azure per ottenere informazioni sulle opzioni di aumento.

+ I cluster per ogni area hanno un limite predefinito di 200. Questi sono condivisi tra un cluster di training e un'istanza di calcolo, che viene considerata come un cluster a nodo singolo per finalità di quota. Contattare il supporto di Azure se si vuole richiedere un aumento oltre questo limite.

+ Sono presenti altri limiti rigidi che non possono essere superati una volta raggiunti.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| Aree di lavoro massime per gruppo di risorse | 800 |
| Numero massimo di nodi in una singola risorsa dell'ambiente di calcolo di Azure Machine Learning (AmlCompute) | 100 nodi |
| Processi MPI GPU massimi per nodo | 1-4 |
| Numero massimo di ruoli di lavoro GPU per nodo | 1-4 |
| Durata massima del processo | 21 giorni<sup>1</sup> |
| Durata massima del processo in un nodo a priorità bassa | 7 giorni<sup>2</sup> |
| Parametri massimi dei server per nodo | 1 |

<sup>1</sup> La durata massima equivale al tempo che trascorre tra l'inizio e il termine dell'esecuzione di un processo. Le esecuzioni completate persistono per un tempo illimitato. I dati relativi alle esecuzioni non completate entro la durata massima non sono accessibili.
<sup>2</sup> I processi in un nodo a priorità bassa possono essere interrotti in presenza di un vincolo di capacità. Si consiglia di implementare il checkpoint nel processo.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning Pipelines
Per le [pipeline di Azure Machine Learning](concept-ml-pipelines.md) esiste un limite di quota per il numero di passaggi in una pipeline e per il numero di esecuzioni basate su pianificazione di pipeline pubblicate per area in una sottoscrizione.
- Il numero massimo di passaggi consentiti in una pipeline è 30.000
- Il numero massimo della somma delle esecuzioni basate su pianificazione e dei pull di BLOB per le pianificazioni attivate tramite blog di pipeline pubblicate per ogni sottoscrizione al mese è 100.000

### <a name="container-instances"></a>Istanze di Container

È anche previsto un limite sul numero di istanze di contenitore che è possibile attivare in un determinato periodo di tempo (su base oraria con ambito) o nell'intera sottoscrizione.
Per informazioni sui limiti, vedere [limiti delle istanze del contenitore](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Archiviazione
È previsto un limite sul numero di account di archiviazione per ogni area, nonché in una sottoscrizione specifica. Il limite predefinito è 250 e include sia l'account di archiviazione Standard sia l'account di archiviazione Premium. Se sono necessari più di 250 account di archiviazione in una determinata area, inviare una richiesta tramite il [supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione per una determinata area.


## <a name="workspace-level-quota"></a>Quota a livello di area di lavoro

Per gestire meglio le allocazioni delle risorse per l'ambiente di calcolo di destinazione di Azure Machine Learning (Amlcompute) tra [aree di lavoro](concept-workspace.md) diverse, è stata introdotta una funzionalità che consente di distribuire le quote a livello di sottoscrizione (per famiglia di VM) e di configurarle a livello di area di lavoro. Il comportamento predefinito prevede che tutte le aree di lavoro abbiano una quota corrispondente alla quota a livello di sottoscrizione per tutte le famiglie di macchine virtuali. Tuttavia, man mano che il numero di aree di lavoro aumenta e i carichi di lavoro con priorità variabile iniziano a condividere le stesse risorse, gli utenti richiedono un metodo per condividere meglio la capacità ed evitare problemi di contesa delle risorse. Azure Machine Learning offre una soluzione con l'offerta di calcolo gestito consentendo agli utenti di impostare una quota massima per una particolare famiglia di macchine virtuali in ogni area di lavoro. Ciò equivale a distribuire la capacità tra le aree di lavoro e gli utenti possono scegliere anche l'allocazione in eccesso per ottenere il massimo utilizzo. 

Per impostare le quote a livello di area di lavoro, passare a qualsiasi area di lavoro nella sottoscrizione e fare clic su **Usages + quotas** (Utilizzi + quote) nel riquadro sinistro. Selezionare quindi la scheda **Configure quotas** (Configura quote) per visualizzare le quote, espandere qualsiasi famiglia di macchine virtuali e impostare un limite di quota per qualsiasi area di lavoro elencata nella famiglia di macchine virtuali. Tenere presente che non è possibile impostare un valore negativo o un valore superiore a quello della quota a livello di sottoscrizione. Inoltre, come si può osservare, per impostazione predefinita a tutte le aree di lavoro viene assegnata l'intera quota di sottoscrizione per consentire un utilizzo completo della quota allocata.

[![Quota a livello di area di lavoro di Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> È necessario avere le autorizzazioni a livello di sottoscrizione per impostare la quota a livello di area di lavoro. Questa impostazione viene applicata in modo che i proprietari delle singole aree di lavoro non modifichino o aumentino le quote e inizino a usare le risorse riservate a un'altra area di lavoro. Per questa ragione, è consigliabile che un amministratore della sottoscrizione esegua l'allocazione e la distribuzione delle quote tra le aree di lavoro.



## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso

Azure Machine Learning quota di calcolo nella sottoscrizione viene gestita separatamente da altre quote di risorse di Azure. Per visualizzare questa quota, è necessario eseguire il drill-down nei servizi Machine Learning.  

1. Nel riquadro di sinistra selezionare il **servizio Machine Learning** e quindi selezionare un'area di lavoro dall'elenco visualizzato.

2. Nel pannello successivo, nella sezione **Supporto e risoluzione dei problemi**, selezionare **Utilizzo e quote** per visualizzare i limiti di quota correnti e l'utilizzo.

3. Selezionare una sottoscrizione per visualizzare i limiti di quota. Ricordare di filtrare i dati in base all'area a cui si è interessati.

4. È ora possibile passare da una vista a livello di sottoscrizione e una vista a livello di area di lavoro:
    + **Vista sottoscrizioni:** questa vista consente di visualizzare l'utilizzo della quota di core per famiglia di macchine virtuali, espanderlo per area di lavoro ed espanderlo ulteriormente in base ai nomi dei cluster effettivi. Questa vista è ottimale per ottenere rapidamente informazioni dettagliate sull'utilizzo di core per una particolare famiglia di macchine virtuali in modo da visualizzare la suddivisione per aree di lavoro e per cluster sottostanti per ognuna delle aree di lavoro. La convenzione generale in questa vista è (utilizzo/quota) dove l'utilizzo è il numero corrente di core aumentati e la quota è il numero massimo logico di core a cui la risorsa può essere ridimensionata. Per ogni **area di lavoro**, la quota è la quota a livello di area di lavoro (come illustrato sopra) che indica il numero massimo di core del ridimensionamento per una particolare famiglia di macchine virtuali. Analogamente, per un **cluster**, la quota corrisponde ai core corrispondenti al numero massimo di nodi a cui può essere ridimensionato il cluster in base a quanto definito dalla proprietà max_nodes.
    
    + **Vista area di lavoro:** questa vista consente di visualizzare l'utilizzo della quota di core per area di lavoro, espanderlo per famiglia di macchine virtuali ed espanderlo ulteriormente in base ai nomi dei cluster effettivi. Questa vista è ottimale per ottenere rapidamente informazioni dettagliate sull'utilizzo di core per una area di lavoro in modo da visualizzare la suddivisione per famiglie di macchine virtuali e per cluster sottostanti per ognuna delle famiglie.

La visualizzazione delle quote per le altre risorse di Azure, ad esempio Macchine virtuali, Archiviazione e Rete, è facile tramite il portale di Azure.

1. Nel riquadro a sinistra selezionare **Tutti i servizi** e quindi **Sottoscrizioni** nella categoria Generale.

2. Nell'elenco delle sottoscrizioni selezionare la sottoscrizione per cui si vuole individuare la quota.

3. Selezionare **utilizzo e quote** per visualizzare i limiti e l'utilizzo correnti della quota. Utilizzare i filtri per selezionare il provider e le posizioni. 

## <a name="request-quota-increases"></a>Richiedere aumenti di quota

Per aumentare il limite o la quota oltre il valore del limite predefinito, è possibile [aprire una richiesta di assistenza clienti online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) senza alcun addebito.

I limiti non possono essere aumentati oltre il valore del limite massimo indicato nelle tabelle. Se non è indicato alcun limite massimo, la risorsa non è associata a limiti modificabili. [Vedere le istruzioni dettagliate per aumentare la quota](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Quando si richiede un aumento di quota, è necessario selezionare il servizio per cui si richiede l'aumento, che può essere Machine Learning, Istanze di Container o Archiviazione. Con l'ambiente di calcolo di Azure Machine Learning è anche possibile fare clic sul pulsante **Richiedi quota** quando è visualizzata la quota seguendo i passaggi precedenti.

> [!NOTE]
> Le [sottoscrizioni per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non hanno i requisiti necessari per un aumento dei limiti o delle quote. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per ulteriori informazioni, vedere [Aggiornare la versione di valutazione gratuita alla sottoscrizione con pagamento in base al consumo](../billing/billing-upgrade-azure-subscription.md) e [Domande frequenti sulla sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).

## <a name="next-steps"></a>Passaggi successivi

+ [Pianificare e gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md)
