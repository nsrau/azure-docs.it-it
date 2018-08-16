---
title: Quote e limiti del servizio per Azure Batch | Microsoft Docs
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3f4907d99b25df31ac7f081282cebe700f55b62
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423743"
---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero previsto di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

> [!NOTE]
> Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.
> 
> 

## <a name="resource-quotas"></a>Quote di risorse
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Quote di core in modalità di sottoscrizione utente

Se è stato creato un account Batch con modalità di allocazione pool impostata su **Sottoscrizione utente**, le quote vengono applicate in modo diverso. In questa modalità, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione al momento della creazione di un pool. Le quote di core di Azure Batch non sono applicabili a un account creato in questa modalità. Vengono applicati invece le quote della sottoscrizione per i core di calcolo regionali e altre risorse. Per altre informazioni su tali quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Altri limiti

| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo |4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch |20 |
| Pacchetti dell'applicazione per applicazione |40 |
| Durata massima dell'attività | 7 giorni<sup>1</sup> |
| Nodi di calcolo nel pool in cui è abilitata la comunicazione tra i nodi | 100 |

<sup>1</sup> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 7 giorni. Le attività completate persistono per un tempo illimitato. I dati per le attività non completate entro la durata massima non sono accessibili.

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch

Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
1. Selezionare **Quote** nel menu dell'account Batch.
1. Visualizzare le quote attualmente applicate all'account Batch
   
    ![Quote di account Batch][account_quotas]



## <a name="increase-a-quota"></a>Aumentare una quota

Per richiedere un aumento di quota per la sottoscrizione o l'account Batch usando il [portale di Azure][portal], seguire questa procedura. Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch.

### <a name="increase-a-batch-cores-quota"></a>Aumentare una quota di core Batch 

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo (**?**) nell'angolo superiore destro del portale.
1. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
1. In **Nozioni di base**:
   
    a. **Tipo di problema** > **Quota**
   
    b. Selezionare la propria sottoscrizione.
   
    c. **Tipo di quota** > **Batch**
   
    d. **Piano di supporto** > **Supporto per la quota - Incluso**
   
    Fare clic su **Avanti**.
1. In **Problema**:
   
    a. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale][support_sev].
   
    b. In **Dettagli**specificare ogni quota che si desidera modificare, il nome dell'account Batch e il nuovo limite.
   
    Fare clic su **Avanti**.
1. In **Informazioni contatto**:
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Fare clic su **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Si noti che il completamento della richiesta può richiedere fino a 2 giorni lavorativi.

## <a name="related-quotas-for-vm-pools"></a>Quote correlate per i pool di macchine virtuali

I pool di Batch nella configurazione di macchine virtuali distribuita in una rete virtuale di Azure allocano automaticamente risorse di rete di Azure aggiuntive. Le risorse seguenti sono necessarie per ogni 50 nodi del pool in una rete virtuale:

* 1 [gruppo di sicurezza di rete](../virtual-network/security-overview.md#network-security-groups)
* 1 [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 1 [bilanciamento del carico](../load-balancer/load-balancer-overview.md)

Queste risorse vengono allocate nella sottoscrizione che contiene la rete virtuale fornita durante la creazione del pool di Batch. Queste risorse sono limitate in base alle [quote delle risorse](../azure-subscription-service-limits.md) della sottoscrizione. Se sono previste distribuzioni di pool di grandi dimensioni in una rete virtuale, controllare le quote della sottoscrizione per queste risorse. Se necessario, richiedere un aumento selezionando **Guida e supporto** nel portale di Azure.


## <a name="related-topics"></a>Argomenti correlati
* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)
* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
