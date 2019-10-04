---
title: Quote e limiti del servizio - Azure Batch | Microsoft Docs
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 8b089a1b32ee70479072522372c060713108957c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350104"
---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero previsto di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Quote di risorse

Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

Si noti inoltre che le quote non sono valori garantiti. Le quote possono variare in base alle modifiche apportate al servizio batch o a una richiesta dell'utente per modificare un valore di quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Quote di core in modalità di sottoscrizione utente

Se è stato creato un account Batch con modalità di allocazione pool impostata su **Sottoscrizione utente**, le quote vengono applicate in modo diverso. In questa modalità, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione al momento della creazione di un pool. Le quote di core di Azure Batch non sono applicabili a un account creato in questa modalità. Vengono applicati invece le quote della sottoscrizione per i core di calcolo regionali e altre risorse. Per altre informazioni su tali quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limiti delle dimensioni del pool

I limiti delle dimensioni del pool vengono impostati dal servizio batch. A differenza [delle quote di risorse](#resource-quotas), questi valori non possono essere modificati. Solo i pool con comunicazione tra nodi e immagini personalizzate presentano limitazioni diverse dalla quota standard.

| **Risorsa** | **Limite Massimo** |
| --- | --- |
| **Nodi di calcolo nel [pool in cui è abilitata la comunicazione tra i nodi](batch-mpi.md)**  ||
| Modalità di allocazione pool del servizio Batch | 100 |
| Modalità di allocazione pool di sottoscrizione batch | 80 |
| **Nodi di calcolo nel [pool creato con una risorsa immagine gestita](batch-custom-images.md)** <sup>1</sup> ||
| Nodi dedicati | 2000 |
| Nodi a priorità bassa | 1000 |

<sup>1</sup> Per pool non abilitati alla comunicazione tra i nodi.

## <a name="other-limits"></a>Altri limiti

Limiti aggiuntivi impostati dal servizio batch. A differenza [delle quote di risorse](#resource-quotas), questi valori non possono essere modificati.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo | 4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch | 20 |
| Pacchetti dell'applicazione per applicazione | 40 |
| Pacchetti di applicazioni per pool | 10 |
| Durata massima dell'attività | 180 giorni<sup>1</sup> |
| [Montaggi](virtual-file-mount.md) per nodo di calcolo | 10 |

<sup>1</sup> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 180 giorni. Le attività completate vengono mantenute per sette giorni; i dati per le attività non completate entro la durata massima non sono accessibili.

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch

Visualizzare le quote dell'account batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
1. Selezionare **Quote** nel menu dell'account Batch.
1. Visualizzare le quote attualmente applicate all'account Batch

    ![Quote di account Batch][account_quotas]

## <a name="increase-a-quota"></a>Aumentare una quota

Seguire questa procedura per richiedere un aumento della quota per l'account batch o per la sottoscrizione usando il [portale di Azure][portal]. Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch. Per richiedere un aumento della quota, è necessario includere la serie di VM per cui si vuole aumentare la quota. Quando viene applicato l'aumento della quota, questo viene applicato a tutte le serie di macchine virtuali.

### <a name="increase-cores-quota-in-batch"></a>Aumentare la quota di core in batch 

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo ( **?** ) nell'angolo superiore destro del portale.
1. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
1. In **Nozioni di base**:
   
    a.  > **Limiti di servizio e sottoscrizione del tipo di problema (quote)**
   
    b. Selezionare la propria sottoscrizione.
   
    c. **Tipo di quota** > **Batch**
      
    Selezionare **Avanti**.
    
1. In **Dettagli**:
      
    a. In specificare i **Dettagli**specificare il percorso, il tipo di quota e l'account batch.
    
    ![Aumento quota batch][quota_increase]

    I tipi di quota includono:

    * **Per account batch**  
        Valori specifici di un singolo account batch, inclusi i core dedicati e con priorità bassa, nonché il numero di processi e pool.
        
    * **Per area**  
        Valori che si applicano a tutti gli account batch in un'area e che includono il numero di account batch per area per sottoscrizione.

    La quota con priorità bassa è un singolo valore in tutte le serie di macchine virtuali. Se sono necessari SKU vincolati, è necessario selezionare **core a bassa priorità** e includere le famiglie di macchine virtuali da richiedere.

    b. Selezionare una **gravità** a seconda dell' [effetto aziendale][support_sev].

    Selezionare **Avanti**.

1. In **Informazioni contatto**:
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Selezionare **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Le richieste di quota possono essere completate entro pochi minuti o fino a due giorni lavorativi.

## <a name="related-quotas-for-vm-pools"></a>Quote correlate per i pool di macchine virtuali

I pool di Batch nella configurazione di macchine virtuali distribuita in una rete virtuale di Azure allocano automaticamente risorse di rete di Azure aggiuntive. Le risorse seguenti sono necessarie per ogni 50 nodi del pool in una rete virtuale:

* Un [gruppo di sicurezza di rete](../virtual-network/security-overview.md#network-security-groups)
* Un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Un servizio di [bilanciamento del carico](../load-balancer/load-balancer-overview.md)

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
