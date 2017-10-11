---
title: Eseguire un failover di test per la replica del server fisico in Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per eseguire un failover di test per i server fisici di cui viene eseguita la replica in Azure usando il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>Passaggio 11: Eseguire un failover di test dei server fisici in Azure

Questo articolo illustra come eseguire un failover di test da server fisici locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

Prima di eseguire un failover di test, è consigliabile verificare le proprietà dei server e apportare eventuali modifiche necessarie. È possibile accedere alle proprietà delle VM in **Elementi replicati**. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.

## <a name="managed-disk-considerations"></a>Considerazioni su Managed Disks

[Managed Disks](../virtual-machines/windows/managed-disks-overview.md) semplifica la gestione dei dischi per le VM di Azure grazie alla gestione degli account di archiviazione associati ai dischi delle VM. 

- Abilitando la protezione per un server, i dati della VM vengono replicati in un account di archiviazione. I dischi di Managed Disks vengono creati e collegati alla VM solo in caso di failover.
- È possibile creare dischi di Managed Disks solo per le VM di Azure distribuite usando il modello di Resource Manager.  
- Quando questa impostazione è abilitata, possono essere selezionati solo i set di disponibilità in gruppi di risorse con l'impostazione **Usa il servizio Managed Disks** abilitata. Le VM con Managed Disks devono trovarsi nei set di disponibilità con l'opzione **Usa il servizio Managed Disks** impostata su **Sì**. Se l'impostazione non è abilitata per le VM, possono essere selezionati solo i set di disponibilità nei gruppi di risorse senza Managed Disks.
- [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) su Managed Disks e set di disponibilità.
- Se l'account di archiviazione usato per la replica è stato crittografato con Crittografia del servizio di archiviazione, la creazione dei dischi di Managed Disks durante il failover non riuscirà. In questo caso, non abilitare l'uso del servizio Managed Disks oppure disabilitare la protezione per la VM e riabilitarla per usare un account di archiviazione senza la crittografia abilitata. [Altre informazioni](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>Considerazioni sulla rete

È possibile impostare l'indirizzo IP di destinazione per una VM di Azure creata dopo il failover.

- Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP.
- Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce.
- Lo stesso indirizzo IP di destinazione può essere usato per il failover di test, se è disponibile nella rete di failover di test.
- Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione:

     - Se il numero di schede di rete nel computer di origine è minore o uguale al numero di schede consentito per le dimensioni del computer di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
     - Se il numero di schede della macchina virtuale di origine supera il numero consentito per le dimensioni della destinazione, verranno usate le dimensioni massime della destinazione.
     - Ad esempio, se una macchina di origine ha due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
   - Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
   - Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.
 - [Altre informazioni](vmware-walkthrough-network.md) sugli indirizzi IP.



## <a name="view-and-modify-vm-settings"></a>Visualizzare e modificare le impostazioni delle VM

È consigliabile verificare le proprietà del server di origine prima di eseguire un failover.

1. In **Elementi protetti** fare clic su **Elementi replicati** e quindi sul computer.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sul computer, stato integrità e ultimi punti di recupero disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile:
    - Modificare il nome della VM di Azure. Il nome deve soddisfare i [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Specificare un [gruppo di risorse] successivo al failover.
    - Specificare le dimensioni di destinazione per la VM di Azure
    - Selezionare un [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md).
    - Specificare se usare [Managed Disks](#managed-disk-considerations). Selezionare **Sì** per collegare i dischi di Managed Disks al computer nella migrazione in Azure.
    - Visualizzare o modificare le impostazioni di rete, inclusi la rete/subnet in cui si troverà la VM di Azure dopo il failover e l'indirizzo IP che verrà assegnato a essa.
4. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Dopo aver completato la configurazione, eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

- Per connettersi alle VM di Azure con RDP dopo il failover, eseguire la [preparazione alla connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Per un test completo è necessario copiare Active Directory e DNS nell'ambiente di test. [Altre informazioni](site-recovery-active-directory.md#test-failover-considerations).
 - Per informazioni complete sul failover di test, leggere [questo articolo](site-recovery-test-failover-to-azure.md).
- Ottenere una rapida panoramica video prima di iniziare:

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

Eseguire quindi un failover:

1. Per eseguire il failover di un singolo computer, in **Impostazioni** > **Elementi replicati** fare clic sul computer > icona **+Failover di test**.

    ![Failover di test](./media/physical-walkthrough-test-failover/test-failover.png)

2. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).  

3. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.

4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sul computer per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Impostazioni** > **Processi** > **Processi di Site Recovery**.

5. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.

6. Se sono state preparate le connessioni dopo il failover, sarà possibile connettersi alla VM di Azure.

### <a name="delete-test-failover-vms"></a>Eliminare le VM del failover di test

1. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel computer o nel piano di ripristino.
2. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.
3. L'azione di pulizia elimina le VM di Azure create durante il failover di test.

## <a name="summary"></a>Riepilogo

Se il failover di test è stato completato correttamente, viene eseguita la replica dei server fisici che riescono a eseguire il failover in Azure. È ora possibile eseguire i failover in base ai requisiti aziendali. 

Tenere presente che attualmente non è possibile eseguire il failback da Azure in un server fisico. È necessario eseguire il failback in una VM VMware, quindi è necessaria un'infrastruttura VMware locale per eseguire il failback. [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback delle VM di Azure in VMware.


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire i failover](site-recovery-failover.md) in base alle esigenze.
