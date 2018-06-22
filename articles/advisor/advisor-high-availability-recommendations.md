---
title: Consigli di Azure Advisor sulla disponibilità elevata | Microsoft Docs
description: Usare Azure Advisor per aumentare la disponibilità delle distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 23764b476f01c30b1755c507a0cfa5ead27be91e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736552"
---
# <a name="advisor-high-availability-recommendations"></a>Consigli di Advisor sulla disponibilità elevata

Azure Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli da Advisor sulla disponibilità elevata nella scheda **Disponibilità elevata** del dashboard di Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantire la tolleranza di errore delle macchine virtuali

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e consiglia di trasferirle in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungere almeno un'altra macchina virtuale. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantire la tolleranza di errore del set di disponibilità 

Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica i set di disponibilità che contengono una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare una macchina virtuale o di aggiungerne una esistente al set di disponibilità.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantire la tolleranza di errore del gateway applicazione
Per garantire la continuità aziendale delle applicazioni cruciali gestite dai gateway applicazione, Advisor identifica le istanze dei gateway applicazione non configurate per la tolleranza di errore e suggerisce le azioni correttive da intraprendere. Advisor identifica i gateway applicazione di medie o grandi dimensioni a istanza singola e consiglia di aggiungere almeno un'altra istanza. Identifica inoltre i gateway applicazione di piccole dimensioni a una o più istanze e consiglia di eseguire la migrazione a SKU di medie o grandi dimensioni. Advisor consiglia queste azioni per garantire che le istanze dei gateway applicazione siano configurate in modo da soddisfare gli attuali requisiti del contratto di servizio per tali risorse.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali

Advisor identifica le macchine virtuali con dischi Standard e consiglia di aggiornarle con dischi Premium.
 
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di Archiviazione Premium memorizzano i dati in unità SSD. Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di macchine virtuali che richiedono un numero elevato di operazioni di I/O al secondo. 

Se i dischi non richiedono un numero elevato di operazioni di I/O al secondo, è possibile limitare i costi mantenendoli nel servizio di archiviazione Standard. L'archiviazione Standard memorizza i dati dei dischi delle macchine virtuali nelle unità disco rigido anziché nelle unità SSD. È possibile scegliere di eseguire la migrazione dei dischi delle macchine virtuali a dischi Premium. I dischi Premium sono supportati nella maggior parte degli SKU delle macchine virtuali. Tuttavia, in alcuni casi, per usare i dischi Premium potrebbe essere necessario aggiornare anche lo SKU della macchina virtuale.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteggere i dati delle macchine virtuali da eliminazioni accidentali
L'impostazione del backup nelle macchine virtuali garantisce la disponibilità dei dati aziendali critici e la protezione contro le eliminazioni o i danni accidentali.  Advisor identifica le macchine virtuali in cui il backup non è abilitato e consiglia di abilitare il backup. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario
Quando si esegue un carico di lavoro aziendale critico, è importante avere accesso al supporto tecnico se necessario. Advisor identifica potenziali sottoscrizioni aziendali critiche che non hanno il supporto tecnico incluso nel piano di supporto e consiglia di eseguire l'aggiornamento a un'opzione che includa il supporto tecnico.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Come accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor fare clic sulla scheda **Disponibilità elevata**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

