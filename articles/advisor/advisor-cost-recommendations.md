---
title: Ridurre i costi del servizio con Azure Advisor | Microsoft Docs
description: Usare Azure Advisor per ottimizzare il costo delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: 9f074c8077f41392fa9d45022a6a7a537964afa9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74180592"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Ridurre i costi del servizio con Azure Advisor

Advisor aiuta a ottimizzare e ridurre la spesa complessiva legata ad Azure identificando le risorse inattive e sottoutilizzate. È possibile ottenere consigli sui costi dalla scheda **costo** del dashboard di Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Ottimizzare la spesa correlata alle macchine virtuali ridimensionando o arrestando le istanze sottoutilizzate 

Sebbene in alcuni scenari applicativi possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro mediante la gestione delle dimensioni e del numero delle macchine virtuali. I modelli di valutazione avanzata di Advisor considerano le macchine virtuali per l'arresto quando P95th del valore massimo del valore massimo di utilizzo della CPU è inferiore al 3% e l'utilizzo della rete è inferiore al 2% per un periodo di 7 giorni. Le macchine virtuali vengono considerate a destra quando è possibile adattare il carico corrente in uno SKU più piccolo (all'interno della stessa famiglia di SKU) o in un numero minore di istanze, in modo che il carico corrente non superi il 80% di utilizzo quando i carichi di lavoro non rientrano nell'utente e non oltre il 40% quando il carico di lavoro viene utilizzato dall'utente. In questo caso, il tipo di carico di lavoro viene determinato analizzando le caratteristiche di utilizzo della CPU del carico di lavoro.

Le azioni consigliate sono arrestate o ridimensionate, specifiche della risorsa consigliata per. Advisor Mostra il risparmio sui costi stimato per le azioni consigliate, ovvero ridimensionare o arrestare. Inoltre, per l'azione di ridimensionamento consigliata, Advisor fornisce informazioni sugli SKU correnti e di destinazione. 

Se si desidera essere più aggressivi nell'identificare le macchine virtuali sottoutilizzate, è possibile modificare la regola di utilizzo della CPU in base alla sottoscrizione.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Ridurre i costi eliminando i circuiti ExpressRoute il cui provisioning è stato annullato

Advisor identifica i circuiti ExpressRoute che sono rimasti nello stato provider *Senza provisioning* per più di un mese e consiglia di eliminare il circuito se non si intende eseguirne il provisioning con il provider di connettività.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Ridurre i costi eliminando o riconfigurando i gateway di rete virtuale inattivi

Advisor identifica i gateway di rete virtuale che sono rimasti inattivi per più di 90 giorni. Poiché questi gateway vengono fatturati su base oraria, è opportuno considerare la riconfigurazione o eliminarli se non si prevede di usarli più. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Acquistare istanze di macchina virtuale riservate per risparmiare denaro rispetto ai costi dei piani con pagamento in base al consumo

Advisor esamina l'utilizzo della macchina virtuale negli ultimi 30 giorni e determina se è possibile risparmiare denaro acquistando una prenotazione di Azure. Advisor mostra le aree e le dimensioni con le massime potenzialità di risparmio e indica il risparmio stimato ottenuto con l'acquisto di prenotazioni. Con le prenotazioni di Azure è possibile sostenere anticipatamente i costi di base delle macchine virtuali. Saranno applicati automaticamente sconti per MV nuove o esistenti che hanno la stessa dimensione e area delle prenotazioni. [Altre informazioni sulle istanze di macchina virtuale riservate di Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor invierà inoltre una notifica all'utente delle istanze riservate che scadranno entro i 30 giorni successivi. Ti consigliamo di acquistare nuove istanze riservate per evitare di pagare i prezzi con pagamento in base al consumo.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminare gli indirizzi IP pubblici non associati per risparmiare denaro

Advisor identifica gli indirizzi IP pubblici che non sono attualmente associati alle risorse di Azure, ad esempio i bilanciamenti del carico o le macchine virtuali. Questi indirizzi IP pubblici sono con un addebito nominale. Se non si prevede di usarli, l'eliminazione di tali elementi può comportare un risparmio sui costi.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Elimina Azure Data Factory pipeline che non riescono

Azure Advisor rileverà Azure Data Factory pipeline che hanno ripetutamente esito negativo e si consiglia di risolvere i problemi o eliminare le pipeline con esito negativo se non sono più necessarie. Verranno addebitati i costi per queste pipeline anche se non vengono utilizzate in caso di errore. 

## <a name="use-standard-snapshots-for-managed-disks"></a>USA snapshot standard per Managed Disks
Per risparmiare il 60% dei costi, è consigliabile archiviare gli snapshot nell'archiviazione standard, indipendentemente dal tipo di archiviazione del disco padre. Questa è l'opzione predefinita per gli snapshot Managed Disks. Azure Advisor identificherà gli snapshot archiviati con archiviazione Premium e consiglia di eseguire la migrazione dello snapshot dall'archiviazione Premium a quella standard. [Scopri di più sui prezzi dei dischi gestiti](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Come accedere ai consigli sui costi in Azure Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Costo**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Attività iniziali](advisor-get-started.md)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
