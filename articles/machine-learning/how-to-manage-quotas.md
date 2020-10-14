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
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a81af14992c8557c245ab3a1073f031a6c505084
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019393"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gestire e aumentare le quote per le risorse con Azure Machine Learning

Azure usa limiti e quote per impedire il superamento delle esecuzioni del budget a causa di frodi e per rispettare i vincoli di capacità di Azure. Considerare questi limiti durante la scalabilità per i carichi di lavoro di produzione. In questo articolo vengono fornite informazioni su:

> [!div class="checklist"]
> + Limiti predefiniti per le risorse di Azure correlate ai [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Visualizzare le quote e i limiti.
> + Creare quote a livello di area di lavoro.
> + Aumento della quota della richiesta.
> + Endpoint privati e quote DNS.

Insieme alla gestione delle quote, è anche possibile scoprire come [pianificare & gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerazioni speciali

+ Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, [contattare il supporto tecnico di Azure per aumentare la quota](#request-quota-increases).

+ La quota è condivisa tra tutti i servizi nelle sottoscrizioni, incluso Azure Machine Learning. Calcola l'utilizzo in tutti i servizi durante la valutazione della capacità.
    + Azure Machine Learning calcolo è un'eccezione e ha una quota separata dalla quota di calcolo di base. 

+ I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita, pagamento in base al consumo e serie di macchine virtuali (VM), ad esempio dv2, F, G e così via.

## <a name="default-resource-quotas"></a>Quote predefinite per le risorse

In questa sezione vengono illustrati i limiti di quota predefiniti e massimi per le risorse seguenti:

+ Macchine virtuali
+ Ambiente di calcolo di Azure Machine Learning
+ Pipeline di Azure Machine Learning
+ Istanze di contenitore
+ Archiviazione

> [!IMPORTANT]
> I limiti sono soggetti a modifiche. I limiti più aggiornati sono sempre disponibili nel [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) sulle quote a livello di servizio per tutti i servizi di Azure.

### <a name="virtual-machines"></a>Macchine virtuali
Ogni sottoscrizione di Azure ha un limite per il numero di macchine virtuali in tutti i servizi. I core delle macchine virtuali hanno un limite totale a livello di area e un limite a livello di area per ogni serie di dimensioni (dv2, F e così via). Entrambi i limiti vengono applicati separatamente.

Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione sarà autorizzata a distribuire 30 VM a1 o 30 VM D1 o una combinazione dei due che non superi un totale di 30 core.

Non è possibile aumentare i limiti per le macchine virtuali sopra il valore indicato nella tabella seguente.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
[Azure Machine Learning calcolo](concept-compute-target.md#azure-machine-learning-compute-managed) ha un limite di quota predefinito sia per il numero di core che per il numero di risorse di calcolo univoche consentito per area in una sottoscrizione. Questa quota è separata dalla quota di core della VM della sezione precedente.

[Richiedere un aumento della quota](#request-quota-increases) per aumentare i limiti di questa sezione fino al **limite massimo** indicato nella tabella.

Risorse disponibili:
+ I **Core dedicati per area** hanno un limite predefinito di 24-300 a seconda del tipo di offerta di sottoscrizione.  Il numero di core dedicati per ogni sottoscrizione può essere aumentato per ogni famiglia di macchine virtuali. Le famiglie di macchine virtuali specializzate come NCv2, NCv3 o ND iniziano con un valore predefinito di zero core.

+ I **core a bassa priorità per area** hanno un limite predefinito di 100-3000 a seconda del tipo di offerta di sottoscrizione. Il numero di core a bassa priorità per ogni sottoscrizione può essere aumentato ed è un valore singolo per le famiglie di macchine virtuali.

+ I **cluster per area** hanno un limite predefinito di 200. Questi sono condivisi tra un cluster di training e un'istanza di calcolo, che viene considerata come un cluster a nodo singolo per finalità di quota.

La tabella seguente illustra i limiti aggiuntivi che non possono essere superati.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| Aree di lavoro per gruppo di risorse | 800 |
| Nodi in una singola risorsa di calcolo di Azure Machine Learning (AmlCompute) | 100 nodi |
| Processi MPI GPU per nodo | 1-4 |
| Thread di lavoro GPU per nodo | 1-4 |
| Durata dei processi | 21 giorni<sup>1</sup> |
| Durata del processo in un nodo Low-Priority | 7 giorni<sup>2</sup> |
| Server di parametri per nodo | 1 |

<sup>1</sup> durata massima indica la durata compresa tra l'inizio e il completamento di un'esecuzione. Esecuzioni completate vengono mantenute per un periodo illimitato. I dati per le esecuzioni non completate entro la durata massima non sono accessibili.
<sup>2</sup> I processi in un nodo a priorità bassa possono essere interrotti in presenza di un vincolo di capacità. Si consiglia di implementare i punti di controllo nel processo.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning Pipelines
[Azure Machine Learning pipeline](concept-ml-pipelines.md) presentano i limiti seguenti.

| **Risorsa** | **Limite** |
| --- | --- |
| Passaggi in una pipeline | 30.000 |
| Aree di lavoro per gruppo di risorse | 800 |

### <a name="container-instances"></a>Istanze di Container

Per altre informazioni, vedere [limiti delle istanze del contenitore](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Archiviazione
Gli account di archiviazione di Azure hanno un limite di 250 account di archiviazione per ogni area, per sottoscrizione. Sono inclusi gli account di archiviazione standard e Premium.

Per aumentare il limite, effettuare una richiesta tramite il [supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Il team di archiviazione di Azure esaminerà il caso e potrà approvare fino a 250 account di archiviazione per un'area.


## <a name="workspace-level-quota"></a>Quota a livello di area di lavoro

Usare le quote a livello di area di lavoro per gestire Azure Machine Learning l'allocazione delle destinazioni di calcolo tra più [aree di lavoro](concept-workspace.md) nella stessa sottoscrizione.

Per impostazione predefinita, tutte le aree di lavoro condividono la stessa quota della quota a livello di sottoscrizione per le famiglie di macchine virtuali. Tuttavia, è possibile impostare una quota massima per le singole famiglie di macchine virtuali nelle aree di lavoro di una sottoscrizione. Ciò consente di condividere la capacità ed evitare problemi di conflitto di risorse:

1. Passare a qualsiasi area di lavoro nella sottoscrizione.
1. Nel riquadro sinistro selezionare **utilizzi + quote**.
1. Selezionare la scheda **Configura quote** per visualizzare le quote.
1. Espandere una famiglia di macchine virtuali.
1. Impostare un limite di quota per qualsiasi area di lavoro elencata in tale famiglia di macchine virtuali.

Non è possibile impostare un valore negativo o un valore superiore a quello della quota a livello di sottoscrizione.

[![Quota a livello di area di lavoro di Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> È necessario avere le autorizzazioni a livello di sottoscrizione per impostare la quota a livello di area di lavoro.

## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso

Per visualizzare la quota per le varie risorse di Azure, ad esempio macchine virtuali, archiviazione, rete, usare la portale di Azure:

1. Nel riquadro a sinistra selezionare **Tutti i servizi** e quindi **Sottoscrizioni** nella categoria Generale.

2. Nell'elenco delle sottoscrizioni selezionare la sottoscrizione per cui si vuole individuare la quota.

3. Selezionare **utilizzo e quote** per visualizzare i limiti e l'utilizzo correnti della quota. Utilizzare i filtri per selezionare il provider e le posizioni. 

La quota di calcolo Azure Machine Learning nella sottoscrizione viene gestita separatamente dalle altre quote di Azure. 

1. Passare all'area di lavoro **Azure Machine Learning** nel portale di Azure.

2. Nel riquadro sinistro, nella **sezione supporto e risoluzione dei problemi** selezionare **utilizzo + quote** per visualizzare i limiti e l'utilizzo correnti della quota.

3. Selezionare una sottoscrizione per visualizzare i limiti di quota. Ricordare di filtrare i dati in base all'area a cui si è interessati.

4. È possibile passare da una visualizzazione a livello di sottoscrizione a una vista a livello di area di lavoro e viceversa.

## <a name="request-quota-increases"></a>Richiedere aumenti di quota

Per aumentare il limite o la quota superiore al limite predefinito, [aprire una richiesta di assistenza clienti online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) senza alcun addebito.

I limiti non possono essere aumentati oltre il valore limite massimo indicato nelle tabelle precedenti. Se non esiste alcun limite massimo, non è possibile modificare il limite per la risorsa.

Quando si richiede un aumento della quota, selezionare il servizio su cui si richiede di aumentare la quota. Ad esempio Azure Machine Learning, istanze di contenitore, archiviazione e così via. Per Azure Machine Learning calcolo, è possibile selezionare il pulsante **Richiedi quota** durante la visualizzazione della quota attenendosi alla procedura descritta sopra.

> [!NOTE]
> Le [sottoscrizioni per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non hanno i requisiti necessari per un aumento dei limiti o delle quote. Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per ulteriori informazioni, vedere [Aggiornare la versione di valutazione gratuita alla sottoscrizione con pagamento in base al consumo](../billing/billing-upgrade-azure-subscription.md) e [Domande frequenti sulla sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumenta la quota di endpoint privati e DNS privati

Esistono limitazioni al numero di endpoint privati e di zone DNS private che è possibile creare in una sottoscrizione.

Sebbene Azure Machine Learning crei risorse nella sottoscrizione (Customer), esistono alcuni scenari in cui le risorse vengono create in una sottoscrizione di proprietà di Microsoft.

 Negli scenari seguenti potrebbe essere necessario richiedere una quota di quote nella sottoscrizione di proprietà di Microsoft:

* __Area di lavoro con collegamento privato abilitato con una chiave gestita dal cliente (CMK)__
* __Container Registry di Azure per l'area di lavoro dietro la rete virtuale__
* __Collegare un cluster del servizio Azure Kubernetes abilitato al collegamento privato all'area di lavoro__.

Per richiedere una concessione per questi scenari, attenersi alla procedura seguente:

1. [Creare una richiesta di supporto di Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) e selezionare le opzioni seguenti nella sezione __nozioni di base__ :

    | Campo | Selezione |
    | ----- | ----- |
    | Tipo di problema | Prerequisiti tecnici |
    | Servizio | Servizi personali. Selezionare __Machine Learning__ nell'elenco a discesa. |
    | Tipo di problema | Configurazione dell'area di lavoro, SDK e CLI |
    | Sottotipo di problema | Problemi di provisioning o gestione dell'area di lavoro |

2. Nella sezione __Dettagli__ usare il campo __Description (Descrizione__ ) per specificare l'area di Azure che si vuole usare e lo scenario che si prevede di usare. Se è necessario richiedere aumenti di quota per più sottoscrizioni, elencare anche gli ID sottoscrizione in questo campo.

3. Selezionare __Crea__ per creare la richiesta.

## <a name="next-steps"></a>Passaggi successivi

+ [Pianificare e gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md)
