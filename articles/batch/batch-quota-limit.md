---
title: Quote e limiti del servizio - Azure Batch | Microsoft Docs
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: de32ae16ea4d3c52b8017f35ae5af6009ab59205
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080926"
---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero previsto di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Quote di risorse

Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

Si noti che le quote non sono garantiti anche i valori. Le quote possono variare in base alle modifiche apportate dal servizio Batch o una richiesta dell'utente per modificare un valore di quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Quote di core in modalità di sottoscrizione utente

Se è stato creato un account Batch con modalità di allocazione pool impostata su **Sottoscrizione utente**, le quote vengono applicate in modo diverso. In questa modalità, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione al momento della creazione di un pool. Le quote di core di Azure Batch non sono applicabili a un account creato in questa modalità. Vengono applicati invece le quote della sottoscrizione per i core di calcolo regionali e altre risorse. Per altre informazioni su tali quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limiti delle dimensioni del pool

Limiti delle dimensioni del pool vengono impostate dal servizio Batch. A differenza [le quote di risorse](#resource-quotas), questi valori non possono essere modificati. Solo i pool con la comunicazione tra nodi e le immagini personalizzate hanno restrizioni diverse dalla quota standard.

| **Risorsa** | **Limite Massimo** |
| --- | --- |
| **Nodi di calcolo nel [pool in cui è abilitata la comunicazione tra i nodi](batch-mpi.md)**  ||
| Modalità di allocazione pool del servizio Batch | 100 |
| Modalità di allocazione pool di sottoscrizione batch | 80 |
| **Nodi di calcolo nel [pool creato con immagine di macchina virtuale personalizzata](batch-custom-images.md)** <sup>1</sup> ||
| Nodi dedicati | 2000 |
| Nodi a priorità bassa | 1000 |

<sup>1</sup> Per pool non abilitati alla comunicazione tra i nodi.

## <a name="other-limits"></a>Altri limiti

Impostare limiti aggiuntivi dal servizio Batch. A differenza [le quote di risorse](#resource-quotas), questi valori non possono essere modificati.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo | 4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch | 20 |
| Pacchetti dell'applicazione per applicazione | 40 |
| Pacchetti dell'applicazione per pool | 10 |
| Durata massima dell'attività | 180 giorni<sup>1</sup> |

<sup>1</sup> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 180 giorni. Le attività completate persistono per sette giorni. i dati per le attività non completate entro la durata massima non sono accessibili.

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch

Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
1. Selezionare **Quote** nel menu dell'account Batch.
1. Visualizzare le quote attualmente applicate all'account Batch

    ![Quote di account Batch][account_quotas]

## <a name="increase-a-quota"></a>Aumentare una quota

Per richiedere un aumento di quota per la sottoscrizione o l'account Batch usando il [portale di Azure][portal], seguire questa procedura. Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch. Per richiedere un aumento della quota, è necessario includere si vuole aumentare la quota per le macchine virtuali della serie. Quando viene applicato l'aumento della quota, viene applicato a tutte le serie di macchine virtuali.

### <a name="increase-cores-quota-in-batch"></a>Aumentare la quota di core batch 

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo ( **?** ) nell'angolo superiore destro del portale.
1. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
1. In **Nozioni di base**:
   
    a. **Tipo di problema** > **limiti di servizio e sottoscrizione (quote)**
   
    b. Selezionare la propria sottoscrizione.
   
    c. **Tipo di quota** > **Batch**
      
    Selezionare **Avanti**.
    
1. In **Dettagli**:
      
    a. Nelle **riportano informazioni dettagliate**, specificare il percorso, il tipo di quota e account Batch.
    
    ![Aumento della quota di batch][quota_increase]

    Tipi di quote includono:

    * **Per ogni account Batch**  
        Valori specifici per un singolo Batch dell'account, inclusi core dedicati e con priorità bassa e il numero di processi e pool.
        
    * **Per ogni area**  
        Valori che si applicano a tutti gli account Batch in un'area e include il numero di account Batch per area per sottoscrizione.

    Quota con priorità bassa è un singolo valore in tutte le serie VM. Se è necessario SKU vincolata, è necessario selezionare **core a bassa priorità** e includere le famiglie VM alla richiesta.

    b. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale][support_sev].

    Selezionare **Avanti**.

1. In **Informazioni contatto**:
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Selezionare **Create** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Le richieste di quota possono essere completate entro pochi minuti o fino a due giorni lavorativi.

## <a name="related-quotas-for-vm-pools"></a>Quote correlate per i pool di macchine virtuali

I pool di Batch nella configurazione di macchine virtuali distribuita in una rete virtuale di Azure allocano automaticamente risorse di rete di Azure aggiuntive. Le risorse seguenti sono necessarie per ogni 50 nodi del pool in una rete virtuale:

* Uno [gruppo di sicurezza di rete](../virtual-network/security-overview.md#network-security-groups)
* Uno [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Uno [bilanciamento del carico](../load-balancer/load-balancer-overview.md)

Queste risorse vengono allocate nella sottoscrizione che contiene la rete virtuale fornita durante la creazione del pool di Batch. Queste risorse sono limitate in base alle [quote delle risorse](../azure-subscription-service-limits.md) della sottoscrizione. Se sono previste distribuzioni di pool di grandi dimensioni in una rete virtuale, controllare le quote della sottoscrizione per queste risorse. Se necessario, richiedere un aumento selezionando **Guida e supporto** nel portale di Azure.


## <a name="related-topics"></a>Argomenti correlati
* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)
* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
