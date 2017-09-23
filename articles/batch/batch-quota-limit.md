---
title: Quote e limiti del servizio per Azure Batch | Microsoft Docs
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero di destinazione di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch o una quota di core di macchine virtuali regionali per la sottoscrizione.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

> [!NOTE]
> Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.
> 
> 

## <a name="resource-quotas"></a>Quote di risorse
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>Quote in modalità di sottoscrizione utente

Per un account Batch con modalità di allocazione di pool impostata su **sottoscrizione utente**, le macchine virtuali Batch e altre risorse, ad esempio gli account di archiviazione, vengono create direttamente nella sottoscrizione al momento della creazione di un pool. La quota di core di Azure Batch non è applicabile a un account creato in questa modalità. Vengono applicati invece le quote della sottoscrizione per i core di calcolo regionali e altre risorse. Per altre informazioni su tali quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

Quando si pianifica l'uso delle risorse per un account creato in modalità di sottoscrizione utente, tenere presente che le risorse di Batch seguenti (oltre ai core di calcolo) sono obbligatorie per ogni 40 macchine virtuali Linux o 20 macchine virtuali Windows:

| Risorsa | Quota | Provider |
| --- | ---| --- |
| Un account di archiviazione | Account di archiviazione | Microsoft.Storage |
| Un indirizzo IP pubblico | Indirizzi IP pubblici | Microsoft.Network | 
| Una rete virtuale | Reti virtuali | Microsoft.Network | 
| Un gruppo di sicurezza di rete | Gruppi di sicurezza di rete | Microsoft.Network | 
| Un set di scalabilità di macchine virtuali | Set di scalabilità di macchine virtuali | Microsoft.Compute | 
| Un bilanciamento del carico | Servizi di bilanciamento del carico | Microsoft.Network | 

La quota di core a livello di area o per ogni famiglia di macchine virtuali deve essere impostata in base alle dimensioni di macchina virtuale necessarie per il pool o i pool di Batch:

| Quota | Provider |
| --- | ---- |
| Totale core regionali | Microsoft.Compute |
| … Core a livello di famiglia | Microsoft.Compute |



## <a name="other-limits"></a>Altri limiti
| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo |4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch |20 |
| Pacchetti dell'applicazione per applicazione |40 |
| Dimensioni del pacchetto dell'applicazione (ciascuno) |Circa 195 GB<sup>1</sup> |
| Dimensione massima dell'attività di avvio | 32768 caratteri<sup>2</sup> |

<sup>1</sup> Limite di archiviazione di Azure per le dimensioni massime del BLOB in blocchi<br />
<sup>2</sup> include i file di risorse e le variabili di ambiente

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch
Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
2. Selezionare **Proprietà** nel pannello del menu dell'account Batch.
3. Il pannello **Proprietà** mostra le quote attualmente applicate all'account Batch
   
    ![Quote di account Batch][account_quotas]

Per un account Batch creato in modalità di sottoscrizione utente, visualizzare le quote delle sottoscrizioni correlate nel portale di Azure.

1. Selezionare **Sottoscrizioni** e quindi scegliere la sottoscrizione in uso per l'account Batch.

2. Nel pannello **Sottoscrizione** selezionare **Utilizzo e quote**.



## <a name="increase-a-quota"></a>Aumentare una quota
Per richiedere un aumento di quota per la sottoscrizione o l'account Batch usando il [portale di Azure][portal], seguire questa procedura. Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch.

### <a name="increase-a-batch-cores-quota"></a>Aumentare una quota di core Batch 

Se l'account Batch è stato creato in modalità **servizio Batch**, seguire questa procedura per richiedere l'aumento di una quota di core Batch:

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo (**?**) nell'angolo superiore destro del portale.
2. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
3. Nel pannello **Informazioni di base** :
   
    a. **Tipo di problema** > **Quota**
   
    b. Selezionare la propria sottoscrizione.
   
    c. **Tipo di quota** > **Batch**
   
    d. **Piano di supporto** > **Supporto per la quota - Incluso**
   
    Fare clic su **Avanti**.
4. Nel pannello **Problema** :
   
    a. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale][support_sev].
   
    b. In **Dettagli**specificare ogni quota che si desidera modificare, il nome dell'account Batch e il nuovo limite.
   
    Fare clic su **Avanti**.
5. Nel pannello **Informazioni di contatto** :
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Fare clic su **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Si noti che il completamento della richiesta può richiedere fino a 2 giorni lavorativi.

### <a name="increase-a-subscription-cores-quota"></a>Aumentare la quota di core della sottoscrizione

Se l'account Batch è stato creato in modalità di **sottoscrizione utente** ed è necessario disporre di core regionali o a livello di famiglia, richiedere un aumento di quote per la sottoscrizione. Per istruzioni, vedere [Richieste di aumento della quota di core per Resource Manager](../azure-supportability/resource-manager-core-quotas-request.md).



## <a name="related-topics"></a>Argomenti correlati
* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)
* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

