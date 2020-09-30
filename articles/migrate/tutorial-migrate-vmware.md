---
title: Eseguire la migrazione di macchine virtuali VMware senza agente con Migrazione server di Azure Migrate
description: Informazioni su come eseguire una migrazione senza agente di macchine virtuali VMware con Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 37181246a20044f16414735e2247fa90fc36433b
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530523"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Eseguire la migrazione di VM VMware ad Azure (senza agente)

Questo articolo illustra come eseguire la migrazione di macchine virtuali VMware locali in Azure usando lo strumento [Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) con il metodo di migrazione senza agente. Per eseguire la migrazione di macchine virtuali VMware è anche possibile usare il metodo di migrazione basata su agente. [Confrontare](server-migrate-overview.md#compare-migration-methods) i metodi.

Questa esercitazione è la terza di una serie che illustra come valutare le macchine virtuali VMware ed eseguirne la migrazione ad Azure. 

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere lo strumento Azure Migrate: Migrazione del server.
> * Individuare le VM di cui eseguire la migrazione.
> * Avviare la replica delle VM.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa delle VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Completare la prima esercitazione](tutorial-prepare-vmware.md) per preparare Azure e VMware per la migrazione.
2. È consigliabile, anche se non necessario, completare la seconda esercitazione per [valutare le macchine virtuali VMware](tutorial-assess-vmware.md) prima di eseguirne la migrazione ad Azure. 


## <a name="add-the-azure-migrate-server-migration-tool"></a>Aggiungere lo strumento Migrazione server di Azure Migrate

Se non è stato ancora configurato un progetto Azure Migrate, [farlo](how-to-add-tool-first-time.md) prima di aggiungere lo strumento. Se è già stato configurato un progetto, seguire queste istruzioni per aggiungere lo strumento:

1. Nel progetto di Azure Migrate fare clic su **Panoramica**. 
2. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server**.

     ![Valutare ed eseguire la migrazione dei server](./media/tutorial-migrate-vmware/assess-migrate.png)

3. In **Strumenti di migrazione** selezionare **Fare clic qui per aggiungere uno strumento di migrazione quando si è pronti per la migrazione**.

    ![Selezionare uno strumento](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Nell'elenco di strumenti selezionare **Azure Migrate: Migrazione server** > **Aggiungi strumento**

    ![Strumento Migrazione server](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate

Lo strumento Azure Migrate: Migrazione del server esegue un'appliance leggera per VM VMware che viene usata per l'individuazione, la valutazione e la migrazione senza agente di macchine virtuali VMware. Se è stata seguita l'[esercitazione sulla valutazione](tutorial-assess-vmware.md), l'appliance è già configurata. In caso contrario, configurarla adesso usando uno di questi metodi:

- **Modello OVA**: [Configurare](how-to-set-up-appliance-vmware.md) l'appliance in una macchina virtuale VMware usando un modello OVA scaricato.
- **Script**: [configurare](deploy-appliance-script.md) l'appliance in una macchina virtuale VMware o in un computer fisico usando uno script di installazione di PowerShell. Questo metodo deve essere usato se non è possibile configurare una macchina virtuale con un modello OVA o se si usa Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="replicate-vms"></a>Replicare le VM

Dopo aver configurato l'appliance e completato l'individuazione, è possibile iniziare la replica delle macchine virtuali VMware in Azure. 

- È possibile eseguire fino a 300 repliche contemporaneamente.
- Nel portale è possibile selezionare fino a 10 macchine virtuali contemporaneamente per la migrazione. Per eseguire la migrazione di più VM, aggiungerle a gruppi in batch di 10.

Per abilitare la replica, procedere come descritto di seguito:

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server**, quindi **Replica**.

    ![Replicare le VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. In **Replica** > **Impostazioni origine** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata e quindi **OK**. 

    ![Impostazioni origine](./media/tutorial-migrate-vmware/source-settings.png)

4. In **Macchine virtuali** selezionare le VM da replicare. Per applicare le dimensioni e il tipo di disco delle VM di una valutazione, se ne è stata eseguita una, in **Importare le impostazioni di migrazione da una valutazione di Azure Migrate?** selezionare **Sì** e quindi selezionare il gruppo di VM e il nome della valutazione. Se non si usano le impostazioni di valutazione, selezionare **No**.
   
    ![Selezionare la valutazione](./media/tutorial-migrate-vmware/select-assessment.png)

5. In **Macchine virtuali** selezionare le VM di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.

    ![Selezionare le VM](./media/tutorial-migrate-vmware/select-vms.png)

6. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione. Specificare il gruppo di risorse in cui si troveranno le macchine virtuali di Azure dopo la migrazione.
7. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
8. In **Opzioni di disponibilità** selezionare:
    -  Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Se si seleziona questa opzione, sarà necessario specificare la zona di disponibilità da usare per ogni macchina selezionata nella scheda Calcolo. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità
    -  Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità.
    - L'opzione La ridondanza dell'infrastruttura non è richiesta se non è necessaria una di queste configurazioni di disponibilità per le macchine migrate.

9. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

    ![Impostazioni di destinazione](./media/tutorial-migrate-vmware/target-settings.png)

10. In **Calcolo** controllare il nome della macchina virtuale, le dimensioni, il tipo di disco del sistema operativo e la configurazione della disponibilità, se selezionata nel passaggio precedente. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale mostra le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Zona di disponibilità**: specificare la zona di disponibilità da usare.
    - **Set di disponibilità**: specificare il set di disponibilità da usare.

> [!NOTE]
>Se si vuole selezionare un'opzione di disponibilità diversa per un set di macchine virtuali, andare al passaggio 1 e ripetere i passaggi selezionando opzioni di disponibilità diverse dopo aver avviato la replica per un set di macchine virtuali.


 ![Impostazioni di calcolo per le VM](./media/tutorial-migrate-vmware/compute-settings.png)

11. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
   
    ![Screenshot che mostra la scheda Dischi della finestra di dialogo Replica.](./media/tutorial-migrate-vmware/disks.png)

12. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Dopo l'avvio della replica non è più possibile modificare le impostazioni.

### <a name="provisioning-for-the-first-time"></a>Primo provisioning

Per la prima VM replicata nel progetto, lo strumento Migrazione del server effettua automaticamente il provisioning delle risorse nello stesso gruppo di risorse del progetto.

- **Bus di servizio**: Migrazione del server usa il bus di servizio per inviare i messaggi di orchestrazione della replica all'appliance.
- **Account di archiviazione del gateway**: lo strumento Migrazione server usa l'account di archiviazione del gateway per archiviare le informazioni sullo stato delle VM da replicare.
- **Account di archiviazione di log**: l'appliance di Azure Migrate carica i log di replica per le VM in un account di archiviazione di log. Azure Migrate applica le informazioni di replica ai dischi gestiti di replica.
- **Insieme di credenziali delle chiavi**: l'appliance di Azure Migrate usa l'insieme di credenziali delle chiavi per gestire le stringhe di connessione per il bus di servizio e le chiavi di accesso per gli account di archiviazione usati nella replica.

## <a name="track-and-monitor"></a>Tenere traccia e monitorare

1. Tenere traccia dello stato del processo nelle notifiche del portale.
2. Monitorare lo stato della replica facendo clic su **Replica dei server** in **Azure Migrate: Migrazione server**.

     ![Monitorare la replica](./media/tutorial-migrate-vmware/replicating-servers.png)

La replica avviene nel modo indicato di seguito:
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

## <a name="run-a-test-migration"></a>Eseguire una migrazione di test


All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sui computer locali, che rimangono operativi, e che la replica continui. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Per eseguire una migrazione di test, seguire questa procedura:


1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Migrazione server** fare clic su **Testare i server con migrazione completata**.

     ![Testare i server con migrazione completata](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Fare clic con il pulsante destro del mouse sulla VM da testare e scegliere **Migrazione di test**.

    ![Migrazione di test](./media/tutorial-migrate-vmware/test-migrate.png)

3. In **Migrazione di test** selezionare la rete virtuale di Azure in cui verrà inserita la VM di Azure dopo la migrazione. È consigliabile usare una rete virtuale non di produzione.
4. Verrà avviato il processo **Migrazione di test**. Monitorare il processo nelle notifiche del portale.
5. Al termine della migrazione, visualizzare la VM di Azure di cui è stata eseguita la migrazione in **Macchine virtuali** nel portale di Azure. Il nome della macchina virtuale ha il suffisso **-Test**.
6. Al termine del test, fare clic con il pulsante destro del mouse sulla macchina virtuale di Azure in **Replica delle macchine virtuali** e scegliere **Pulisci migrazione di test**.

    ![Eseguire la pulizia della migrazione](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle macchine virtuali locali.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-vmware/replicate-servers.png)

2. In **Replica delle macchine virtuali** fare clic con il pulsante destro del mouse sulla VM e scegliere **Esegui la migrazione**.
3. In **Esegui la migrazione** > **Spegnere le macchine virtuali ed eseguire una migrazione pianificata senza perdita di dati** selezionare **Sì** > **OK**.
    - Per impostazione predefinita, Azure Migrate arresta la VM locale ed esegue una replica su richiesta per sincronizzare le eventuali modifiche apportate alla macchina virtuale dopo l'ultima replica. Questa operazione assicura che non vi sia alcuna perdita di dati.
    - Se non si vuole arrestare la VM, selezionare **No**
4. Verrà avviato un processo di migrazione per la VM. Tenere traccia del processo nelle notifiche di Azure.
5. Al termine del processo, è possibile visualizzare e gestire la VM dalla pagina **Macchine virtuali**.

## <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Arresta replica**. La replica della macchina virtuale locale verrà arrestata e verrà eseguita la pulizia delle informazioni sullo stato della replica della VM.
2. Installare l'agente [Windows](../virtual-machines/extensions/agent-windows.md) o [Linux](../virtual-machines/extensions/agent-linux.md) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
3. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
4. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
5. Trasferire il traffico all'istanza della VM di Azure di cui è stata eseguita la migrazione.
6. Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
7. Rimuovere le macchine virtuali locali dai processi di backup locali.
8. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure. 

## <a name="post-migration-best-practices"></a>Procedure consigliate dopo la migrazione

- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
    - Distribuire [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
-  È consigliabile distribuire [Gestione costi di Azure](../cost-management-billing/cloudyn/overview.md) per monitorare l'utilizzo delle risorse e le spese.


## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.
