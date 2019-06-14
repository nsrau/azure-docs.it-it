---
title: Ridurre i costi del servizio con Azure Advisor | Microsoft Docs
description: Usare Azure Advisor per ottimizzare il costo delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254714"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Ridurre i costi del servizio con Azure Advisor

Advisor aiuta a ottimizzare e ridurre la spesa complessiva legata ad Azure identificando le risorse inattive e sottoutilizzate. È possibile ottenere consigli sui costi nella scheda **Costo** del dashboard di Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Ottimizzare la spesa correlata alle macchine virtuali ridimensionando o arrestando le istanze sottoutilizzate 

Sebbene in alcuni scenari applicativi possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro mediante la gestione delle dimensioni e del numero delle macchine virtuali. Advisor monitora l'utilizzo della macchina virtuale per 7 giorni e quindi identifica le macchine virtuali di utilizzo ridotto. Virtuale macchine sono considerate utilizzo ridotto se l'uso della CPU è 5% o meno e relativo utilizzo di rete è inferiore al 2% o se il carico di lavoro corrente può essere soddisfatto dalle dimensioni della macchina virtuale inferiori.

Advisor mostra il costo stimato qualora si continui a eseguire la macchina virtuale, consentendo pertanto di scegliere se arrestarla o ridimensionarla.

Per un'individuazione più mirata delle macchine virtuali sottoutilizzate, è possibile perfezionare la regola di utilizzo medio della CPU in base alla sottoscrizione.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Ridurre i costi eliminando i circuiti ExpressRoute il cui provisioning è stato annullato

Advisor identifica i circuiti ExpressRoute che sono rimasti nello stato provider *Senza provisioning* per più di un mese e consiglia di eliminare il circuito se non si intende eseguirne il provisioning con il provider di connettività.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Ridurre i costi eliminando o riconfigurando i gateway di rete virtuale inattivi

Advisor identifica le attività di controllo di rete virtuale che sono rimaste inattive per oltre 90 giorni. Poiché questi gateway vengono fatturati su base oraria, è opportuno considerare la riconfigurazione o eliminarli se non si prevede di usarli più. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Acquistare istanze di macchina virtuale riservate per risparmiare denaro rispetto ai costi dei piani con pagamento in base al consumo

Advisor esamina l'utilizzo della macchina virtuale negli ultimi 30 giorni e determina se è possibile risparmiare denaro acquistando una prenotazione di Azure. Advisor mostra le aree e le dimensioni con le massime potenzialità di risparmio e indica il risparmio stimato ottenuto con l'acquisto di prenotazioni. Con le prenotazioni di Azure è possibile sostenere anticipatamente i costi di base delle macchine virtuali. Saranno applicati automaticamente sconti per MV nuove o esistenti che hanno la stessa dimensione e area delle prenotazioni. [Altre informazioni sulle istanze di macchina virtuale riservate di Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor manda inoltre un avviso di istanze riservate che è necessario che scadranno entro 30 giorni. Consiglia di acquistare istanze riservate per evitare di pagare prezzi con pagamento a consumo.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminare gli indirizzi IP pubblici non associati per risparmiare denaro

Advisor identifica gli indirizzi IP pubblici che non sono attualmente associati alle risorse di Azure, ad esempio macchine virtuali o servizi di bilanciamento del carico. Questi IP pubblico indirizzi sono dotate di un addebito nominale. Se non si prevede di usarli, eliminarli può produrre risparmi.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Eliminare le pipeline di Data Factory di Azure che hanno esito negativo

Azure Advisor rileverà le pipeline di Azure Data Factory che ripetutamente esito negativo e consiglia di risolvere i problemi o se non sono più necessari, eliminare le pipeline di errori. Ti verranno fatturate per queste pipeline anche se tuttavia non collaborano è mentre hanno esito negativo. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Usare gli snapshot Standard per i dischi gestiti
Per salvare il 60% dei costi, si consiglia di archiviare gli snapshot di archiviazione Standard, indipendentemente dal tipo di archiviazione del disco padre. Questa è l'opzione predefinita per gli snapshot di Managed Disks. Azure Advisor identifica gli snapshot vengono archiviati archiviazione Premium e consiglia la migrazione di snapshot da Premium nell'archiviazione Standard. [Altre informazioni sui prezzi di Managed Disks](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Come accedere ai consigli sui costi in Azure Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Costo**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [esercitazione introduttiva](advisor-get-started.md)
* [Advisor Performance recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor High Availability recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sulla sicurezza)
