---
title: Prima di iniziare la replica delle macchine virtuali di Azure in un'altra area | Microsoft Docs
description: Riepiloga i passaggi da effettuare prima di replicare le macchine virtuali di Azure tra le aree di Azure con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---

# <a name="step-2-before-you-start"></a>Passaggio 2: Prima di iniziare

Dopo aver esaminato l'[architettura](azure-to-azure-walkthrough-architecture.md) per la replica delle macchine virtuali di Azure tra le aree di Azure con [Azure Site Recovery](site-recovery-overview.md), consultare questo articolo per verificare i prerequisiti.

- Al termine della lettura, l'utente avrà una migliore comprensione di cosa è necessario per far funzionare la distribuzione e avrà terminato i passaggi dei prerequisiti.
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> La replica delle macchine virtuali di Azure è attualmente in anteprima.



## <a name="support-recommendations"></a>Consigli per il supporto

Vedere la tabella riportata di seguito. Ottenere un elenco completo dei requisiti di supporto dalla [matrice di supporto](site-recovery-support-matrix-azure-to-azure.md).

**Componente** | **Requisito**
--- | ---
**Insieme di credenziali dei servizi di ripristino** | È consigliabile creare un insieme di credenziali di servizi di ripristino nell'area di Azure di destinazione da usare come percorso di ripristino di emergenza. Ad esempio, se si desidera replicare le macchine virtuali di origine dagli Stati Uniti orientali agli Stati Uniti centrali, creare l'insieme di credenziali negli Stati Uniti centrali.
**Sottoscrizione di Azure** | È necessario che la sottoscrizione di Azure sia abilitata per la creazione di macchine virtuali nella località di destinazione da usare come area per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.
**Capacità dell'area di destinazione** | Nell'area di destinazione di Azure, la sottoscrizione deve avere una capacità sufficiente per le macchine virtuali, gli account di archiviazione e i componenti di rete.
**Archiviazione** | Usare le [informazioni aggiuntive sull'archiviazione](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) per le macchine virtuali di Azure di origine onde evitare problemi con le prestazioni.<br/><br/> Gli account di archiviazione devono trovarsi nella stessa area dell'insieme di credenziali.<br/><br/> In India centrale e India meridionale non è possibile eseguire la replica in account Premium.<br/><br/> Se si distribuisce la replica con le impostazioni predefinite, Site Recovery crea gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano le impostazioni, seguire gli [obiettivi di scalabilità per i dischi della macchina virtuale](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Rete** | È necessario consentire la connettività in uscita dalle macchine virtuali di Azure per determinati intervalli di URL/IP.<br/><br/> Gli account di rete devono trovarsi nella stessa area dell'insieme di credenziali.
**Macchina virtuale di Azure** | Assicurarsi che tutti i certificati radice più recenti siano nella macchina virtuale Windows/Linux di Azure. In caso contrario, non sarà possibile registrare la macchina virtuale in Site Recovery a causa dei vincoli di sicurezza.
**Account utente Azure** | L'account utente di Azure deve avere determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una macchina virtuale di Azure.


## <a name="set-permissions-on-the-account"></a>Impostare le autorizzazioni per l'account

1. Informazioni sulle [autorizzazioni](site-recovery-role-based-linked-access-control.md) necessarie per la replica.
2. Seguire queste [istruzioni](../active-directory/role-based-access-control-configure.md#add-access) per aggiungere le autorizzazioni.


## <a name="verify-target-resources"></a>Verificare le risorse di destinazione

1. Abilitando la sottoscrizione di Azure è possibile creare macchine virtuali nell'area di destinazione da usare per il ripristino di emergenza che si desidera usare come area di ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.
2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per abilitare le macchine virtuali con le dimensioni che corrispondono alle macchine virtuali di origine. Per impostazione predefinita, quando si configura la replica, Site Recovery sceglie le stesse dimensioni per la macchina virtuale di destinazione, oppure le dimensioni più vicine possibile. Ulteriori informazioni sulla [risoluzione dei problemi](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097) relativi alle risorse di destinazione.

## <a name="verify-azure-vm-certificates"></a>Verificare i certificati della macchina virtuale di Azure

1. Controllare che nella macchina virtuale Windows o Linux siano presenti tutti i certificati radice più recenti da replicare. Se i certificati radice più recenti non sono presenti, non è possibile registrare la VM in Site Recovery a causa di vincoli di sicurezza.
2. Per le macchine virtuali Windows, eseguire le operazioni seguenti:

    - Installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer.
    - Se si lavora in un ambiente non connesso, seguire il processo di aggiornamento standard dei processi/certificati di Windows Update dell'organizzazione per ottenere i certificati radice più recenti e i CRL aggiornati sulle macchine virtuali.
3. Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati aggiornato nella macchina virtuale. Ulteriori informazioni sulla [risoluzione dei problemi](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066) relativi alle radici attendibili.


## <a name="next-steps"></a>Passaggi successivi

Passare al [Passaggio 3: pianificare la rete](azure-to-azure-walkthrough-network.md) per configurare la connettività in uscita.

