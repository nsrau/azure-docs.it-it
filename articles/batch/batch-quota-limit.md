---
title: Quote e limiti del servizio per Azure Batch | Microsoft Docs
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06445845ef8f93a06cb4b52e07108965cc3b7f8a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
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

### <a name="quotas-in-user-subscription-mode"></a>Quote in modalità di sottoscrizione utente

Se è stata usata una versione precedente dell'API Batch per creare un account Batch con modalità di allocazione pool impostata su **Sottoscrizione utente**, le quote vengono applicate in modo diverso. In questa modalità, che non è più consigliata, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. La quota di core di Azure Batch non è applicabile a un account creato in questa modalità. Vengono applicati invece le quote della sottoscrizione per i core di calcolo regionali e altre risorse. Per altre informazioni su tali quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Altri limiti
| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo |4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch |20 |
| Pacchetti dell'applicazione per applicazione |40 |
| Dimensioni del pacchetto dell'applicazione (ciascuno) |Circa 195 GB<sup>1</sup> |
| Dimensione massima dell'attività di avvio | 32768 caratteri<sup>2</sup> |
| Durata massima dell'attività | 7 giorni<sup>3</sup> |

<sup>1</sup> Limite di archiviazione di Azure per le dimensioni massime del BLOB in blocchi<br />
<sup>2</sup> Include i file di risorse e le variabili di ambiente<br />
<sup>3</sup> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 7 giorni. Le attività completate persistono per un tempo illimitato. I dati per le attività non completate entro la durata massima non sono accessibili.


## <a name="view-batch-quotas"></a>Visualizzare le quote Batch
Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
2. Selezionare **Quote** nel menu dell'account Batch.
3. Visualizzare le quote attualmente applicate all'account Batch
   
    ![Quote di account Batch][account_quotas]



## <a name="increase-a-quota"></a>Aumentare una quota
Per richiedere un aumento di quota per la sottoscrizione o l'account Batch usando il [portale di Azure][portal], seguire questa procedura. Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch.

### <a name="increase-a-batch-cores-quota"></a>Aumentare una quota di core Batch 

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo (**?**) nell'angolo superiore destro del portale.
2. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
3. In **Nozioni di base**:
   
    a. **Tipo di problema** > **Quota**
   
    b. Selezionare la propria sottoscrizione.
   
    c. **Tipo di quota** > **Batch**
   
    d. **Piano di supporto** > **Supporto per la quota - Incluso**
   
    Fare clic su **Avanti**.
4. In **Problema**:
   
    a. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale][support_sev].
   
    b. In **Dettagli**specificare ogni quota che si desidera modificare, il nome dell'account Batch e il nuovo limite.
   
    Fare clic su **Avanti**.
5. In **Informazioni contatto**:
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Fare clic su **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Si noti che il completamento della richiesta può richiedere fino a 2 giorni lavorativi.


## <a name="related-topics"></a>Argomenti correlati
* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)
* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
