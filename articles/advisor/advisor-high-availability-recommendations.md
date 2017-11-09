---
title: "Consigli di Azure Advisor sulla disponibilità elevata | Microsoft Docs"
description: "Usare Azure Advisor per aumentare la disponibilità delle distribuzioni di Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Consigli di Advisor sulla disponibilità elevata

Azure Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli da Advisor sulla disponibilità elevata nella scheda **Disponibilità elevata** del dashboard di Advisor.

![Pulsante Disponibilità elevata nel dashboard di Advisor](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantire la tolleranza di errore delle macchine virtuali

Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e consiglia di trasferirle in un set di disponibilità. Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungere almeno un'altra macchina virtuale. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

![Consiglio di Advisor: per la ridondanza delle macchine virtuali usare i set di disponibilità](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Garantire la tolleranza di errore del set di disponibilità 

Advisor identifica i set di disponibilità che contengono una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare una macchina virtuale o usarne una esistente e aggiungerla al set di disponibilità.  

![Consiglio di Advisor: aggiungere una o più macchine virtuali a questo set di disponibilità](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Garantire la tolleranza di errore del gateway applicazione
Per garantire la continuità aziendale delle applicazioni cruciali gestite dai gateway applicazione, Advisor identifica le istanze dei gateway applicazione non configurate per la tolleranza di errore e suggerisce le azioni correttive da intraprendere. Advisor identifica i gateway applicazione di medie o grandi dimensioni a istanza singola e consiglia di aggiungere almeno un'altra istanza. Identifica inoltre i gateway applicazione di piccole dimensioni a una o più istanze e consiglia di eseguire la migrazione a SKU di medie o grandi dimensioni. Advisor consiglia queste azioni per garantire che le istanze dei gateway applicazione siano configurate in modo da soddisfare gli attuali requisiti del contratto di servizio per tali risorse.

![Consiglio di Advisor: distribuire due o più istanze di gateway applicazione di medie o grandi dimensioni](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Migliorare le prestazioni e l'affidabilità dei dischi delle macchine virtuali

Advisor identifica le macchine virtuali con dischi Standard e consiglia di aggiornarle con dischi Premium.
 
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive. I dischi delle macchine virtuali che usano account di Archiviazione Premium memorizzano i dati in unità SSD. Per ottenere prestazioni ottimali per l'applicazione, è consigliabile eseguire la migrazione in Archiviazione Premium di tutti i dischi di macchine virtuali che richiedono un numero elevato di operazioni di I/O al secondo. 

Se i dischi non richiedono un numero elevato di operazioni di I/O al secondo, è possibile limitare i costi mantenendoli nel servizio di archiviazione Standard. L'archiviazione Standard memorizza i dati dei dischi delle macchine virtuali nelle unità disco rigido anziché nelle unità SSD. È possibile scegliere di eseguire la migrazione dei dischi delle macchine virtuali a dischi Premium. I dischi Premium sono supportati nella maggior parte degli SKU delle macchine virtuali. Tuttavia, in alcuni casi, per usare i dischi Premium potrebbe essere necessario aggiornare anche lo SKU della macchina virtuale.

![Consiglio di Advisor: migliorare l'affidabilità dei dischi delle macchine virtuali con l'aggiornamento ai dischi Premium](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteggere i dati delle macchine virtuali da eliminazioni accidentali
Advisor identifica le macchine virtuali in cui il backup non è abilitato e consiglia di abilitare il backup. L'impostazione del backup nelle macchine virtuali garantisce la disponibilità dei dati aziendali critici e la protezione contro le eliminazioni o i danni accidentali.

![Consiglio di Advisor: configurare il backup delle macchine virtuali per proteggere i dati cruciali](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro selezionare **Altri servizi**.

3. Nel riquadro del menu del servizio in **Monitoraggio e gestione** fare clic su **Azure Advisor**.  
 Viene visualizzato il dashboard di Advisor.

4. Nel dashboard di Advisor fare clic sulla scheda **Disponibilità elevata** e selezionare la sottoscrizione per cui si vuole ricevere consigli.

> [!NOTE]
> Per accedere ai consigli di Advisor, per prima cosa *registrare la sottoscrizione* con Advisor. Una sottoscrizione viene registrata quando il *proprietario della sottoscrizione* avvia il dashboard di Advisor e fa clic sul pulsante **Ottieni raccomandazioni**. Si tratta di un'*operazione una tantum*. Dopo aver registrato una sottoscrizione, è possibile accedere ai consigli di Advisor come *Proprietario*, *Collaboratore* o *Lettore* di una sottoscrizione, di un gruppo di risorse o di una risorsa specifica.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

