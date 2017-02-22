---
title: "Consigli di Azure Advisor sulla disponibilità elevata | Microsoft Docs"
description: "Usare Azure Advisor per aumentare la disponibilità delle distribuzioni di Azure."
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 91b6fec93d4226af755be553e544a4ea7b1081ca

---

# <a name="advisor-high-availability-recommendations"></a>Consigli di Advisor sulla disponibilità elevata

Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli da Advisor sulla disponibilità elevata nella scheda **Disponibilità elevata** del dashboard di Advisor.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>Macchine virtuali senza un set di disponibilità

Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e consiglia di trasferirle in un set di disponibilità. Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungere almeno un'altra macchina virtuale dopo la creazione. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>Set di disponibilità con una singola macchina virtuale 

Advisor identifica i set di disponibilità contenenti una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia disponibile e soddisfi i requisiti del contratto di servizio per le macchine virtuali di Azure. È possibile scegliere di creare una macchina virtuale o usarne una esistente e aggiungerla al set di disponibilità.  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>Macchine virtuali con dischi Standard

Advisor identifica le macchine virtuali con dischi Standard e consiglia di aggiornarle con dischi Premium.  
Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. I dischi delle macchine virtuali che usano archiviazione Premium archiviano i dati in unità SSD. È consigliabile eseguire la migrazione di qualsiasi disco di macchine virtuali che richiede un numero elevato di IOPS ad Archiviazione Premium di Azure per ottenere prestazioni ottimali per l'applicazione. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo nel servizio di archiviazione Standard. L'archiviazione standard archivia i dati dei dischi delle macchine virtuali nelle unità disco rigido invece che nelle unità SSD. È possibile eseguire la migrazione dei dischi delle macchine virtuali a dischi Premium. I dischi Premium sono supportati nella maggior parte degli SKU delle macchine virtuali. Tuttavia, in alcuni casi, se si desidera usare i dischi Premium, potrebbe essere necessario aggiornare anche lo SKU della macchina virtuale.   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Come accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel riquadro di navigazione sinistro fare clic su **More services** (Altri servizi), nel riquadro con il menu dei servizi scorrere verso il basso fino a **Monitoring and Management** (Monitoraggio e gestione), quindi fare clic su **Azure Advisor**. Viene avviato il dashboard di Advisor. 
3. Nel dashboard di Advisor fare clic sulla scheda **Disponibilità elevata** e selezionare la sottoscrizione per cui si desidera ricevere consigli.

> [!NOTE]
> Advisor genera consigli per le sottoscrizioni in cui all'utente è stato assegnato il ruolo di **proprietario, collaboratore o lettore**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere le risorse seguenti:
-  [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
-  [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
-  [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
-  [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)



<!--HONumber=Nov16_HO3-->


