---
title: Gestire le quote & delle risorse
titleSuffix: Azure Machine Learning
description: Informazioni sulle quote sulle risorse per Azure Machine Learning e su come richiedere altre quote.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399148"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gestire e richiedere quote per risorse di Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo fornisce informazioni dettagliate sui limiti preconfigurati per le risorse di Azure per la sottoscrizione. Sono inoltre incluse istruzioni su come richiedere miglioramenti delle quote per ogni tipo di risorsa. Questi limiti vengono applicati per evitare lo sforamento del budget a causa di illeciti e per rispettare i vincoli di capacità di Azure.

Come con altri servizi di Azure, esistono limiti su alcune risorse associate ad Azure Machine Learning.As with other Azure services, there are limits on certain resources associated with Azure Machine Learning. Questi limiti vanno da un limite al numero di aree di lavoro ai limiti del calcolo sottostante effettivo che viene utilizzato per il training del modello o l'inferenza/punteggio. 

Quando si progettano e si ridimensionano le risorse di Azure Machine Learning per i carichi di lavoro di produzione, considerare questi limiti. Ad esempio, se il cluster non raggiunge il numero di nodi di destinazione, è possibile che sia stato raggiunto un limite di core di Azure Machine Learning Compute per la sottoscrizione. Per aumentare il limite o la quota oltre il valore del limite predefinito, è possibile aprire una richiesta di assistenza clienti online senza alcun addebito. Non è possibile aumentare i limiti oltre il valore massimo indicato nelle tabelle seguenti a causa dei vincoli di capacità di Azure. Se non è presente una colonna Limite massimo, la risorsa specificata non è associata a limiti modificabili.

## <a name="special-considerations"></a>Considerazioni speciali

+ Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

+ Your quota is shared across all the services in your subscriptions including Azure Machine Learning. L'unica eccezione è data dall'ambiente di calcolo di Azure Machine Learning che ha una quota separata rispetto alla quota dei core. Nel valutare le esigenze di capacità, assicurarsi di calcolare l'utilizzo della quota in tutti i servizi.

+ I limiti predefiniti variano in base al tipo di categoria dell'offerta, ad esempio prova gratuita, con pagamento in base al costo e serie di macchine virtuali, ad esempio Dv2, F, G e così via.

## <a name="default-resource-quotas"></a>Quote predefinite per le risorse

Ecco un riepilogo dei limiti di quota per i vari tipi di risorsa all'interno della sottoscrizione di Azure.

> [!IMPORTANT]
> I limiti sono soggetti a modifiche. I limiti più aggiornati sono sempre disponibili nel [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) sulle quote a livello di servizio per tutti i servizi di Azure.

### <a name="virtual-machines"></a>Macchine virtuali
Per ogni sottoscrizione di Azure è previsto un limite al numero di macchine virtuali che è possibile avere tra i servizi o autonomi. Questo limite è a livello di area sia per i core totali sia per ogni famiglia.

I core di macchina virtuale hanno un limite totale regionale e un limite regionale per dimensione (Dv2, F e così via), entrambi applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione sarà autorizzata a distribuire 30 VM A1 o 30 VM D1 o una combinazione delle due che non superi un totale di 30 core, ad esempio 10 VM A1 e 20 VM D1.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Per un elenco più dettagliato e aggiornato dei limiti di quota, vedere l'articolo sulle quote a livello di Azure [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
Nell'ambiente di calcolo di Azure Machine Learning è previsto un limite di quota predefinito per il numero di core e per il numero di risorse di calcolo consentite per ogni area in una sottoscrizione. Questa quota è separata dalla quota core della macchina virtuale precedente e i limiti di core non sono condivisi tra i due tipi di risorse poiché AmlCompute è un servizio gestito che distribuisce le risorse in un modello ospitato per conto di.

Risorse disponibili:
+ I core dedicati per area hanno un limite predefinito di 24 - 300 a seconda del tipo di offerta di abbonamento con valori predefiniti più elevati per i tipi di offerta EA e CSP.  Il numero di core dedicati per sottoscrizione può essere aumentato ed è diverso per ogni famiglia di macchine virtuali. Alcune famiglie di VM specializzate come NCv2, NCv3 o la serie ND iniziano con un valore predefinito di zero core. Contattare il supporto di Azure generando una richiesta di quota per discutere l'aumento delle opzioni.

+ I core con priorità bassa per area hanno un limite predefinito di 100 - 3000 a seconda del tipo di offerta di abbonamento con valori predefiniti più elevati per i tipi di offerta EA e CSP. Il numero di core con priorità bassa per sottoscrizione può essere aumentato ed è un singolo valore tra le famiglie di macchine virtuali. Contattare il supporto di Azure per ottenere informazioni sulle opzioni di aumento.

+ I cluster per area hanno un limite predefinito di 200.Clusters per region have a default limit of 200. Questi sono condivisi tra un cluster di training e un'istanza di calcolo (che viene considerato come un cluster a nodo singolo per scopi di quota). Contattare il supporto di Azure se si vuole richiedere un aumento oltre questo limite.

+ Ci sono altri limiti rigorosi che non possono essere superati una volta raggiunto.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| Aree di lavoro massime per gruppo di risorse | 800 |
| Numero massimo di nodi in una singola risorsa dell'ambiente di calcolo di Azure Machine Learning (AmlCompute) | 100 nodi |
| Processi MPI GPU massimi per nodo | 1-4 |
| Numero massimo di ruoli di lavoro GPU per nodo | 1-4 |
| Durata massima del processo | 90 giorni<sup>1</sup> |
| Durata massima del processo in un nodo con priorità bassaMaximum job lifetime on a Low-Priority Node | 7 giorni<sup>2</sup> |
| Parametri massimi dei server per nodo | 1 |

<sup>1</sup> La durata massima equivale al tempo che trascorre tra l'inizio e il termine dell'esecuzione di un processo. Le esecuzioni completate persistono per un tempo illimitato. I dati relativi alle esecuzioni non completate entro la durata massima non sono accessibili.
<sup>2</sup> I processi in un nodo con priorità bassa possono essere annullati ogni volta che è presente un vincolo di capacità. Ti consigliamo di implementare il checkpoint nel tuo lavoro.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning Pipelines
Per le pipeline di Azure Machine Learning è presente un limite di quota per il numero di passaggi in una pipeline e sul numero di esecuzioni basate sulla pianificazione delle pipeline pubblicate per ogni area in una sottoscrizione.
- Il numero massimo di passaggi consentiti in una pipeline è 30.000
- Il numero massimo di esecuzioni basate sulla pianificazione e pull BLOB per le pianificazioni attivate dal blog di pipeline pubblicate per sottoscrizione al mese è 100.000

> [!NOTE]
> Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Istanze di Container

È anche previsto un limite sul numero di istanze di contenitore che è possibile attivare in un determinato periodo di tempo (su base oraria con ambito) o nell'intera sottoscrizione.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Per un elenco più dettagliato e aggiornato dei limiti di quota, vedere l'articolo sulle quote a livello di Azure [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Archiviazione
È previsto un limite sul numero di account di archiviazione per ogni area, nonché in una sottoscrizione specifica. Il limite predefinito è 250 e include sia gli account di archiviazione Standard che quelli Premium.The default limit is 250 and includes both Standard And Premium Storage accounts. Se sono necessari più di 250 account di archiviazione in una determinata area, effettuare una richiesta tramite il supporto di [Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione per una determinata area.


## <a name="workspace-level-quota"></a>Quota livello area di lavoro

Per gestire meglio le allocazioni di risorse per Amlcompute tra diverse aree di lavoro, è stata introdotta una funzionalità che consente di distribuire le quote a livello di sottoscrizione (per famiglia di macchine virtuali) e di configurarle a livello di area di lavoro. Il comportamento predefinito è che tutte le aree di lavoro hanno la stessa quota della quota del livello di sottoscrizione per qualsiasi famiglia di macchine virtuali. Tuttavia, con l'aumento del numero di aree di lavoro e i carichi di lavoro con priorità variabile iniziano a condividere le stesse risorse, gli utenti desiderano un modo per condividere meglio la capacità ed evitare problemi di contesa delle risorse. Azure Machine Learning offre una soluzione con l'offerta di elaborazione gestita consentendo agli utenti di impostare una quota massima per una determinata famiglia di macchine virtuali in ogni area di lavoro. Ciò è analogo alla distribuzione della capacità tra le aree di lavoro e gli utenti possono scegliere di sovra-allocare anche per aumentare il massimo utilizzo. 

Per impostare le quote a livello di area di lavoro, passare a qualsiasi area di lavoro nella sottoscrizione e fare clic su **Utilizzo e quote** nel riquadro sinistro. Selezionare quindi la scheda **Configura quote** per visualizzare le quote, espandere qualsiasi famiglia di macchine virtuali e impostare un limite di quota in qualsiasi area di lavoro elencata in tale famiglia di macchine virtuali. Tenere presente che non è possibile impostare un valore negativo o un valore superiore alla quota del livello di sottoscrizione. Inoltre, come si osserverebbe, per impostazione predefinita a tutte le aree di lavoro viene assegnata l'intera quota di sottoscrizione per consentire l'utilizzo completo della quota allocata.

[![Quota a livello di area di lavoro di Azure Machine LearningAzure Machine Learning workspace level quota](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Si tratta solo di una funzionalità dell'edizione Enterprise. Se nella sottoscrizione è presente sia un'area di lavoro Basic che un'area di lavoro Enterprise Edition, è possibile utilizzarla solo per impostare quote nelle aree di lavoro dell'organizzazione. Le aree di lavoro di base continueranno a avere la quota del livello di sottoscrizione che è il comportamento predefinito.
>
> Sono necessarie autorizzazioni a livello di sottoscrizione per impostare la quota a livello di area di lavoro. Viene applicata in modo che i singoli proprietari dell'area di lavoro non modifichino o aumentino le quote e inizino a invadere le risorse messe da parte per un'altra area di lavoro. Pertanto, un amministratore della sottoscrizione è più adatto per allocare e distribuire queste quote tra aree di lavoro.



## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso

La visualizzazione delle quote per le diverse risorse, come Macchine virtuali, Archiviazione e Rete, è facile tramite il portale di Azure.

1. Nel riquadro a sinistra selezionare **Tutti i servizi** e quindi **Sottoscrizioni** nella categoria Generale.

1. Nell'elenco delle sottoscrizioni selezionare la sottoscrizione per cui si vuole individuare la quota.

   **Tenere conto dell'avvertenza** che riguarda la visualizzazione della quota dell'ambiente di calcolo di Azure Machine Learning. Come indicato in precedenza, questa quota è separata dalla quota di calcolo nella sottoscrizione.

1. Nel riquadro sinistro selezionare **Servizio Machine Learning** e quindi selezionare un'area di lavoro dall'elenco visualizzato

1. Nel pannello successivo, nella sezione **Supporto e risoluzione dei problemi**, selezionare **Utilizzo e quote** per visualizzare i limiti di quota correnti e l'utilizzo.

1. Selezionare una sottoscrizione per visualizzare i limiti di quota. Ricordare di filtrare i dati in base all'area a cui si è interessati.

1. È ora possibile passare da una visualizzazione a livello di sottoscrizione a una visualizzazione a livello di area di lavoro:You can now toggle between a subscription level view and a workspace level view:
    + **Visualizzazione Sottoscrizione:** In questo modo è possibile visualizzare l'utilizzo della quota principale in base alla famiglia di macchine virtuali, espandendola in base all'area di lavoro e espandendola ulteriormente in base ai nomi effettivi del cluster. Questa visualizzazione è ottimale per accedere rapidamente ai dettagli sull'utilizzo del core per una determinata famiglia di macchine virtuali per visualizzare la disgregazione da parte delle aree di lavoro e ulteriormente dai cluster sottostanti per ognuna di queste aree di lavoro. La convenzione generale in questa visualizzazione è (utilizzo/quota), in cui l'utilizzo è il numero corrente di core con scalabilità verticale e la quota è il numero massimo logico di core a cui la risorsa può essere scalata. Per ogni **area di lavoro,** la quota sarà la quota del livello dell'area di lavoro (come spiegato in precedenza) che indica il numero massimo di core a cui è possibile eseguire la scalabilità per una determinata famiglia di macchine virtuali. Per un **cluster** in modo analogo, la quota è in realtà i core corrispondenti al numero massimo di nodi che il cluster può scalare a definito dalla proprietà max_nodes.

    + **Visualizzazione area di lavoro:** In questo modo è possibile visualizzare l'utilizzo della quota di base in base all'area di lavoro, espandendola in base alla famiglia di macchine virtuali e espandendola ulteriormente in base ai nomi effettivi del cluster. Questa visualizzazione è ottimale per accedere rapidamente ai dettagli dell'utilizzo del core per una determinata area di lavoro per visualizzare la disgregazione da parte delle famiglie di macchine virtuali e ulteriormente dai cluster sottostanti per ognuna di queste famiglie.

## <a name="request-quota-increases"></a>Richiedere aumenti di quota

Se si desidera aumentare il limite o la quota al di sopra del limite predefinito, aprire una richiesta di [assistenza clienti online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) senza alcun costo aggiuntivo.

I limiti non possono essere aumentati oltre il valore del limite massimo indicato nelle tabelle. Se non è indicato alcun limite massimo, la risorsa non è associata a limiti modificabili. [Questo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) articolo descrive il processo di aumento della quota in modo più dettagliato.

Quando si richiede un aumento di quota, è necessario selezionare il servizio per cui si richiede l'aumento, che può essere Machine Learning, Istanze di Container o Archiviazione. Oltre a Calcolo di Azure Machine Learning, è possibile fare clic sul pulsante **Richiedi quota** durante la visualizzazione della quota seguendo i passaggi precedenti.

> [!NOTE]
> Le [sottoscrizioni per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non hanno i requisiti necessari per un aumento dei limiti o delle quote. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per ulteriori informazioni, vedere [Aggiornare la versione di valutazione gratuita alla sottoscrizione con pagamento in base al consumo](../billing/billing-upgrade-azure-subscription.md) e [Domande frequenti sulla sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).
