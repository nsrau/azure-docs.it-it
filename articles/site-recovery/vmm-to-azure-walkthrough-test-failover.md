---
title: Eseguire un failover di test per la replica Hyper-V (con System Center VMM) in Azure | Microsoft Docs
description: Riepiloga i passaggi necessari per eseguire un failover di test per le VM Hyper-V nei cloud VMM di cui viene eseguita la replica in Azure usando il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b562a23-7ba7-48ee-905d-c22b4b5d6466
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: 4688fc4bc74a9e0e04487cfbe965006070fd9a7b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-for-hyper-v-replication-with-vmm-to-azure"></a>Passaggio 11: Eseguire un failover di test per la replica Hyper-V (con VMM) in Azure

Dopo avere [abilitato la replica per VM Hyper-V](vmm-to-azure-walkthrough-enable-replication.md), usare le istruzioni disponibili in questo articolo per eseguire un failover di test dalle macchine virtuali Hyper-V locali gestite in cloud System Center Virtual Machine Manager (VMM) in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Prima di iniziare

Prima di eseguire un failover di test, è consigliabile verificare le proprietà delle VM e apportare eventuali modifiche necessarie. È possibile accedere alle proprietà delle VM in **Elementi replicati**. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.

## <a name="managed-disk-considerations"></a>Considerazioni su Managed Disks

[Managed Disks](../virtual-machines/windows/managed-disks-overview.md) semplifica la gestione dei dischi per le VM di Azure grazie alla gestione degli account di archiviazione associati ai dischi delle VM. 

- I dischi di Managed Disks vengono creati e collegati alla VM solo in caso di failover in Azure. Abilitando la protezione, i dati delle VM locali vengono replicati negli account di archiviazione.
- È possibile creare dischi di Managed Disks solo per le VM distribuite tramite il modello di distribuzione Resource Manager.
- Il failback da Azure a un ambiente Hyper-V locale non è al momento supportato per i computer con Managed Disks. Impostare **Usa il servizio Managed Disks** su **Sì** solo se si sta eseguendo esclusivamente una migrazione (failover in Azure senza failback)
- Quando questa impostazione è abilitata, possono essere selezionati solo i set di disponibilità in gruppi di risorse con l'impostazione **Usa il servizio Managed Disks** abilitata. Le VM con Managed Disks devono trovarsi nei set di disponibilità con l'opzione **Usa il servizio Managed Disks** impostata su **Sì**. Se l'impostazione non è abilitata per le VM, possono essere selezionati solo i set di disponibilità nei gruppi di risorse senza Managed Disks. [Altre informazioni](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).
- - Se l'account di archiviazione usato per la replica è stato crittografato con Crittografia del servizio di archiviazione, la creazione dei dischi di Managed Disks durante il failover non riuscirà. In questo caso, non abilitare l'uso del servizio Managed Disks oppure disabilitare la protezione per la VM e riabilitarla per usare un account di archiviazione senza la crittografia abilitata. [Altre informazioni](../virtual-machines/windows/managed-disks-overview.md#managed-disks-and-encryption).

 
## <a name="network-considerations"></a>Considerazioni sulla rete
    
- È possibile impostare l'indirizzo IP di destinazione da usare per la VM di Azure dopo il failover. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover ha esito negativo. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
- Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:
    - Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
    - Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
    - Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
- Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
- Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.


## <a name="view-and-manage-vm-settings"></a>Visualizzare e gestire le impostazioni delle VM

È consigliabile verificare le proprietà del computer di origine prima di eseguire un failover.

1. In **Elementi protetti** fare clic su **Elementi replicati** e quindi sulla VM.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-test-failover/test-failover1.png)
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla VM, stato integrità e ultimi punti di recupero disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-test-failover/test-failover2.png)
3. In **Calcolo e rete** è possibile:
    - Modificare il nome della VM di Azure. Il nome deve soddisfare i [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Specificare un [gruppo di risorse] successivo al failover.
    - Specificare le dimensioni di destinazione per la VM di Azure
    - Selezionare un [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md).
    - Specificare se usare [Managed Disks](#managed-disk-considerations). Selezionare **Sì** per collegare i dischi di Managed Disks al computer nella migrazione in Azure.
    - Visualizzare o modificare le impostazioni di rete, inclusi la rete/subnet in cui si troverà la VM di Azure dopo il failover e l'indirizzo IP che verrà assegnato a essa.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-test-failover/test-failover4.png)
4. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.


## <a name="run-a-test-failover"></a>Eseguire un failover di test

Eseguire quindi un failover di test per verificare che tutto funzioni come previsto.

- Per connettersi alle VM di Azure con RDP dopo il failover, eseguire la [preparazione alla connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Per un test completo è necessario copiare Active Directory e DNS nell'ambiente di test. [Altre informazioni](site-recovery-active-directory.md#test-failover-considerations).
 - Per informazioni complete sul failover di test, leggere [questo articolo](site-recovery-test-failover-to-azure.md).
 
 Eseguire quindi un failover:

1. Per eseguire il failover di una singola macchina, in **Elementi replicati** fare clic sulla VM e quindi sull'icona **+Failover di test**.
2. Per eseguire il failover di un piano di ripristino, in **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi su **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).
3. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
6. Se sono state preparate le connessioni dopo il failover, sarà possibile connettersi alla VM di Azure.
7. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test.



## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e su come eseguirli.
- [Informazioni sul failback](site-recovery-failback-from-azure-to-hyper-v.md), per il failback e la replica di macchine virtuali di Azure nel cloud VMM locale primario.

