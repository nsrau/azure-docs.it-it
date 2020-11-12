---
title: Gestire le risorse e le quote
titleSuffix: Azure Machine Learning
description: Informazioni sulle quote e sui limiti delle risorse per Azure Machine Learning e su come richiedere aumenti di quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: d82cbafbbdeb379c8eb97494ca8d3243f356b7a1
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542117"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Gestire e aumentare le quote per le risorse con Azure Machine Learning

Azure usa limiti e quote per evitare sovraccarichi del budget dovuti a frodi e per rispettare i vincoli di capacità di Azure. Considerare questi limiti durante la scalabilità per i carichi di lavoro di produzione. In questo articolo vengono fornite informazioni su:

> [!div class="checklist"]
> + Limiti predefiniti per le risorse di Azure correlate ai [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Creazione di quote a livello di area di lavoro.
> + Visualizzazione delle quote e dei limiti.
> + Aumento della quota richiesta.
> + Endpoint privati e quote DNS.

Insieme alla gestione delle quote, è possibile ottenere informazioni su come [pianificare e gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerazioni speciali

+ Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, [contattare il supporto tecnico di Azure per aumentare la quota](#request-quota-increases).

+ Una quota viene condivisa tra tutti i servizi nelle sottoscrizioni, incluso Azure Machine Learning. Calcola l'utilizzo in tutti i servizi quando stai valutando la capacità.
 
  Azure Machine Learning calcolo è un'eccezione. Ha una quota separata dalla quota di calcolo di base. 

+ I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita, pagamento in base al consumo e serie di macchine virtuali (VM), ad esempio dv2, F e G.

## <a name="default-resource-quotas"></a>Quote predefinite per le risorse

In questa sezione vengono illustrati i limiti di quota predefiniti e massimi per le risorse seguenti:

+ Macchine virtuali
+ Ambiente di calcolo di Azure Machine Learning
+ Pipeline di Azure Machine Learning
+ Istanze di Azure Container
+ Archiviazione di Azure

> [!IMPORTANT]
> I limiti sono soggetti a modifiche. Per informazioni aggiornate, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md) per tutte le risorse di Azure.

### <a name="virtual-machines"></a>Macchine virtuali
Ogni sottoscrizione di Azure ha un limite per il numero di macchine virtuali in tutti i servizi. I core delle macchine virtuali hanno un limite totale regionale e un limite a livello di area per ogni serie di dimensioni. Entrambi i limiti vengono applicati separatamente.

Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione sarà autorizzata a distribuire 30 VM a1 o 30 VM D1 o una combinazione dei due che non superi un totale di 30 core.

Non è possibile aumentare i limiti per le macchine virtuali sopra i valori indicati nella tabella seguente.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning
[Azure Machine Learning calcolo](concept-compute-target.md#azure-machine-learning-compute-managed) ha un limite di quota predefinito per il numero di core e il numero di risorse di calcolo univoche consentite per area in una sottoscrizione. Questa quota è separata dalla quota di core della VM della sezione precedente.

[Richiedere un aumento della quota](#request-quota-increases) per aumentare i limiti di questa sezione fino al limite massimo indicato nella tabella.

Risorse disponibili:
+ I **Core dedicati per area** hanno un limite predefinito di 24 a 300, a seconda del tipo di offerta di sottoscrizione. È possibile aumentare il numero di core dedicati per ogni sottoscrizione per ogni famiglia di macchine virtuali. Le famiglie di macchine virtuali specializzate come NCv2, NCv3 o ND iniziano con un valore predefinito di zero core.

+ I **core a bassa priorità per area** hanno un limite predefinito di 100 a 3.000, a seconda del tipo di offerta di sottoscrizione. Il numero di core a bassa priorità per ogni sottoscrizione può essere aumentato ed è un valore singolo per le famiglie di macchine virtuali.

+ I **cluster per area** hanno un limite predefinito di 200. Questi sono condivisi tra un cluster di training e un'istanza di calcolo. Un'istanza di calcolo è considerata un cluster a nodo singolo per finalità di quota.

La tabella seguente Mostra limiti aggiuntivi che non possono essere superati.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| Aree di lavoro per gruppo di risorse | 800 |
| Nodi in una singola risorsa di calcolo di Azure Machine Learning (AmlCompute) | 100 nodi |
| Processi MPI GPU per nodo | 1-4 |
| Thread di lavoro GPU per nodo | 1-4 |
| Durata dei processi | 21 giorni<sup>1</sup> |
| Durata del processo in un nodo con priorità bassa | 7 giorni<sup>2</sup> |
| Server di parametri per nodo | 1 |

<sup>1</sup> durata massima è la durata compresa tra l'inizio di un'esecuzione e il suo completamento. Esecuzioni completate vengono mantenute per un periodo illimitato. I dati per le esecuzioni non completate entro la durata massima non sono accessibili.
<sup>2</sup> i processi in un nodo con priorità bassa possono essere interrotti ogni volta che è presente un vincolo di capacità. Si consiglia di implementare i checkpoint nel processo.

### <a name="azure-machine-learning-pipelines"></a>Pipeline di Azure Machine Learning
[Azure Machine Learning pipeline](concept-ml-pipelines.md) presentano i limiti seguenti.

| **Risorsa** | **Limite** |
| --- | --- |
| Passaggi in una pipeline | 30.000 |
| Aree di lavoro per gruppo di risorse | 800 |

### <a name="container-instances"></a>Istanze di contenitore

Per altre informazioni, vedere [limiti delle istanze del contenitore](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Archiviazione
Archiviazione di Azure ha un limite di 250 account di archiviazione per area, per sottoscrizione. Questo limite include gli account di archiviazione standard e Premium.

Per aumentare il limite, effettuare una richiesta tramite il [supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Il team di archiviazione di Azure esaminerà il caso e potrà approvare fino a 250 account di archiviazione per un'area.


## <a name="workspace-level-quotas"></a>Quote a livello di area di lavoro

Usare le quote a livello di area di lavoro per gestire Azure Machine Learning l'allocazione delle destinazioni di calcolo tra più [aree di lavoro](concept-workspace.md) nella stessa sottoscrizione.

Per impostazione predefinita, tutte le aree di lavoro condividono la stessa quota della quota a livello di sottoscrizione per le famiglie di macchine virtuali. Tuttavia, è possibile impostare una quota massima per le singole famiglie di macchine virtuali nelle aree di lavoro di una sottoscrizione. Ciò consente di condividere la capacità ed evitare problemi di conflitto di risorse.

1. Passare a qualsiasi area di lavoro nella sottoscrizione.
1. Nel riquadro sinistro selezionare **utilizzi + quote**.
1. Selezionare la scheda **Configura quote** per visualizzare le quote.
1. Espandere una famiglia di macchine virtuali.
1. Impostare un limite di quota per qualsiasi area di lavoro elencata in tale famiglia di macchine virtuali.

Non è possibile impostare un valore negativo o un valore superiore a quello della quota a livello di sottoscrizione.

[![Screenshot che mostra una quota a livello di area di lavoro Azure Machine Learning.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Per impostare una quota a livello di area di lavoro, è necessario disporre delle autorizzazioni a livello di sottoscrizione.

## <a name="view-your-usage-and-quotas"></a>Visualizzare le quote e l'uso

Per visualizzare la quota per le varie risorse di Azure, ad esempio macchine virtuali, archiviazione o rete, usare la portale di Azure:

1. Nel riquadro sinistro selezionare tutti i **Servizi** e quindi selezionare **sottoscrizioni** nella categoria **generale** .

2. Dall'elenco delle sottoscrizioni selezionare la sottoscrizione di cui si sta cercando la quota.

3. Selezionare **utilizzo e quote** per visualizzare i limiti e l'utilizzo correnti della quota. Utilizzare i filtri per selezionare il provider e le posizioni. 

È possibile gestire la quota di calcolo Azure Machine Learning sulla sottoscrizione separatamente dalle altre quote di Azure: 

1. Passare all'area di lavoro **Azure Machine Learning** nel portale di Azure.

2. Nel riquadro sinistro, nella sezione **supporto e risoluzione dei problemi** , selezionare **utilizzo e quote** per visualizzare i limiti e l'utilizzo correnti della quota.

3. Selezionare una sottoscrizione per visualizzare i limiti di quota. Filtrare per l'area a cui si è interessati.

4. È possibile passare tra una visualizzazione a livello di sottoscrizione e una visualizzazione a livello di area di lavoro.

## <a name="request-quota-increases"></a>Richiedere aumenti di quota

Per aumentare il limite o la quota superiore al limite predefinito, [aprire una richiesta di assistenza clienti online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) senza alcun addebito.

Non è possibile aumentare i limiti sopra i valori massimi mostrati nelle tabelle precedenti. Se non esiste alcun limite massimo, non è possibile modificare il limite per la risorsa.

Quando si richiede un aumento della quota, selezionare il servizio che si sta tenendo in considerazione. Ad esempio, selezionare Azure Machine Learning, istanze di contenitore o archiviazione. Per Azure Machine Learning calcolo, è possibile selezionare il pulsante **Richiedi quota** durante la visualizzazione della quota nei passaggi precedenti.

> [!NOTE]
> Le [sottoscrizioni della versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non sono idonee per il limite o la quota. Se si dispone di una sottoscrizione di valutazione gratuita, è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/) al consumo. Per altre informazioni, vedere [aggiornare la versione di valutazione gratuita di Azure alle](../cost-management-billing/manage/upgrade-azure-subscription.md) [domande frequenti sull'account gratuito di Azure](https://azure.microsoft.com/free/free-account-faq)con pagamento in base al consumo.

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumenti della quota di endpoint e DNS privati

Sono previsti limiti per il numero di endpoint privati e le zone DNS private che è possibile creare in una sottoscrizione.

Azure Machine Learning crea risorse nella sottoscrizione (Customer), ma alcuni scenari creano risorse in una sottoscrizione di proprietà di Microsoft.

 Negli scenari seguenti potrebbe essere necessario richiedere una quota di quote nella sottoscrizione di proprietà di Microsoft:

* Area di lavoro abilitata per il collegamento privato di Azure con una chiave gestita dal cliente (CMK)
* Registro Azure Container per l'area di lavoro dietro alla rete virtuale
* Collegamento di un cluster del servizio Azure Kubernetes abilitato per il collegamento privato all'area di lavoro

Per richiedere una concessione per questi scenari, attenersi alla procedura seguente:

1. [Creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) e selezionare le opzioni seguenti nella sezione __nozioni di base__ :

    | Campo | Selezione |
    | ----- | ----- |
    | Tipo di problema | **Tecnico** |
    | Servizio | **Servizi personali**. Quindi selezionare __Machine Learning__ nell'elenco a discesa. |
    | Tipo di problema | **Configurazione e sicurezza dell'area di lavoro** |
    | Sottotipo di problema | **Richiesta di limite dell'endpoint privato e della zona di DNS privato** |

2. Nella sezione dei __Dettagli__ usare il campo __Description (Descrizione__ ) per specificare l'area di Azure e lo scenario che si prevede di usare. Se è necessario richiedere aumenti di quota per più sottoscrizioni, elencare gli ID sottoscrizione in questo campo.

3. Selezionare __Crea__ per creare la richiesta.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Screenshot di un endpoint privato e di una richiesta di aumento della quota DNS privata.":::

## <a name="next-steps"></a>Passaggi successivi

+ [Pianificare e gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md)
