---
title: Quote e limiti del servizio per Azure Batch | Documentazione Microsoft
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
ms.date: 03/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 4d2ba8d3a5efad1be3395aae732874e7a770f64b
ms.lasthandoff: 04/06/2017


---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero di destinazione di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

> [!NOTE]
> Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.
> 
> 

## <a name="resource-quotas"></a>Quote di risorse
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Altri limiti
| **Risorsa** | **Limite massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo |4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch |20 |
| Pacchetti dell'applicazione per applicazione |40 |
| Dimensioni del pacchetto dell'applicazione (ciascuno) |Circa 195 GB<sup>1</sup> |

<sup>1</sup> Limite di archiviazione di Azure per le dimensioni massime del BLOB in blocchi

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch
Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.
2. Selezionare **Proprietà** nel pannello del menu Account Batch
3. Il pannello **Proprietà** mostra le quote attualmente applicate all'account Batch
   
    ![Quote di account Batch][account_quotas]

## <a name="increase-a-quota"></a>Aumentare una quota
Per richiedere un aumento di quota usando il [portale di Azure][portal], seguire questa procedura.

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

## <a name="related-topics"></a>Argomenti correlati
* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)
* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

