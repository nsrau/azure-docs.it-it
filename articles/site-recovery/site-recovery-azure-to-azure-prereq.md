---
title: Prerequisiti per la replica in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo riepiloga i prerequisiti per la replica di macchine virtuali e computer fisici in Azure tramite il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Prerequisiti per la replica di macchine virtuali di Azure in un'altra area tramite Azure Site Recovery

> [!div class="op_single_selector"]
> * [Eseguire la replica da Azure ad Azure](site-recovery-azure-to-azure-prereq.md)
> * [Eseguire la replica da locale ad Azure](site-recovery-prereq.md)

Il servizio Azure Site Recovery contribuisce al miglioramento della strategia di continuità aziendale e ripristino di emergenza (BCDR) tramite l'orchestrazione degli elementi seguenti:
* Replica di macchine virtuali di Azure in un'altra area di Azure.
* Replica delle macchine virtuali e dei server fisici locali in Azure o in un data center secondario. 

In caso di interruzioni nella località primaria, è possibile eseguire il failover a una località secondaria per mantenere disponibili app e carichi di lavoro. Sarà possibile tornare alla località primaria quando sarà di nuovo operativa. Per altre informazioni su Site Recovery, vedere [Che cos'è Site Recovery?](site-recovery-overview.md).

Questo articolo riepiloga i prerequisiti necessari per avviare la replica di Site Recovery da locale ad Azure.

È possibile inserire commenti nella parte inferiore di questo articolo oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisiti di Azure

**Requisito** | **Dettagli**
--- | ---
**Account di Azure** | Account [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servizio Site Recovery** | Per altre informazioni sui prezzi di Site Recovery, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). È consigliabile creare un insieme di credenziali di Servizi di ripristino nell'area di Azure di destinazione da usare come percorso di ripristino di emergenza. Ad esempio, se le VM di origine sono in esecuzione negli Stati Uniti orientali e si vuole eseguire la replica nell'area Stati Uniti centrali, è consigliabile creare l'insieme di credenziali negli Stati Uniti centrali.|
**Capacità di Azure** | Per l'area di Azure di destinazione da usare come percorso di ripristino di emergenza, è necessario avere una sottoscrizione con capacità sufficiente per le macchine virtuali, gli account di archiviazione e i componenti di rete. È possibile contattare il supporto tecnico per aumentare la capacità.
**Indicazioni per l'archiviazione** | Assicurarsi di seguire le [linee guida per l'archiviazione](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) per le macchine virtuali di Azure di origine per evitare problemi di prestazioni. Se si seguono le impostazioni predefinite, Site Recovery crea gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni specifiche, assicurarsi di rispettare gli [obiettivi di scalabilità per i dischi delle macchine virtuali](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Indicazioni per la rete** | È necessario aggiungere all'elenco elementi consentiti la connettività in uscita dalla VM di Azure per URL o intervalli di indirizzi IP specifici. Per altre informazioni, vedere l'articolo [Networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md) (Indicazioni sulla rete per la replica di macchine virtuali di Azure).
**Macchina virtuale di Azure** | Assicurarsi che nella VM Windows o Linux siano presenti tutti i certificati radice più recenti. Se i certificati radice più recenti non sono presenti, non è possibile registrare la VM in Site Recovery a causa di vincoli di sicurezza.

>[!NOTE]
>Per altre informazioni sul supporto per configurazioni specifiche, leggere la [matrice di supporto](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni, vedere [networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md) (Indicazioni sulla rete per la replica di macchine virtuali di Azure).
- Iniziare a proteggere i carichi di lavoro [eseguendo la replica di macchine virtuali di Azure](site-recovery-azure-to-azure.md).

