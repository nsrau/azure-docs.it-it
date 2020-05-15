---
title: Eseguire la migrazione di macchine virtuali Hyper-V ad Azure con Migrazione server di Azure Migrate
description: Informazioni su come eseguire la migrazione di macchine virtuali Hyper-V locali ad Azure con Migrazione server di Azure Migrate
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 87f746108599928d3e1b4a022abc1b3a3779ef29
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853545"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Eseguire la migrazione di VM Hyper-V ad Azure 

Questo articolo illustra come eseguire una migrazione senza agente di VM Hyper-V locali ad Azure con Azure Migrate: Server Migration.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

Questa esercitazione è la terza di una serie che illustra come valutare le macchine virtuali Hyper-V ed eseguirne la migrazione ad Azure usando gli strumenti Valutazione server e Migrazione server di Azure Migrate. In questa esercitazione verranno illustrate le procedure per:


> [!div class="checklist"]
> * Preparare Azure e l'ambiente Hyper-V locale
> * Configurare l'ambiente di origine.
> * Configurare l'ambiente di destinazione.
> * Abilitare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa ad Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Rivedere](hyper-v-migration-architecture.md) l'architettura di migrazione di Hyper-V.
2. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) requisiti dell'host Hyper-V e gli URL di Azure a cui devono accedere gli host Hyper-V.
3. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) i requisiti delle macchine virtuali Hyper-V di cui eseguire la migrazione. Le macchine virtuali Hyper-V devono essere conformi ai [requisiti delle macchine virtuali di Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).
2. È consigliabile completare le esercitazioni precedenti di questa serie. La [prima esercitazione](tutorial-prepare-hyper-v.md) di questa serie mostra configurare Azure e Hyper-V per la migrazione. La seconda esercitazione illustra come [valutare le macchine virtuali Hyper-V](tutorial-assess-hyper-v.md) prima della migrazione, usando lo strumento Valutazione server di Azure Migrate. 
    > [!NOTE]
    > Benché sia consigliabile provare una valutazione, non è necessario farlo prima di eseguire la migrazione delle macchine virtuali.
    > Per la migrazione di macchine virtuali Hyper-V, lo strumento Migrazione server di Azure Migrate esegue agenti software (provider Microsoft Azure Site Recovery e agente di Servizi di ripristino di Microsoft Azure) negli host Hyper-V o nei nodi del cluster, per orchestrare e replicare i dati in Azure Migrate. Per la migrazione di Hyper-V non viene usata l'[appliance di Azure Migrate](migrate-appliance.md).

3. Assicurarsi che al proprio account Azure sia assegnato il ruolo Collaboratore Macchina virtuale, in modo da avere le autorizzazioni per:

    - Creare una macchina virtuale nel gruppo di risorse selezionato.
    - Creare una macchina virtuale nella rete virtuale selezionata.
    - Scrivere in un disco gestito di Azure.
4. [Configurare una rete di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando si esegue la migrazione ad Azure, le macchine virtuali di Azure create vengono aggiunte a una rete di Azure specificata durante la configurazione.

## <a name="add-the-azure-migrateserver-migration-tool"></a>Aggiungere lo strumento Migrazione server di Azure Migrate

Aggiungere lo strumento Migrazione server di Azure Migrate.

- Se è stata seguita la seconda esercitazione per [valutare le macchine virtuali VMware](tutorial-assess-hyper-v.md), è già stato configurato un progetto di Azure Migrate e si può procedere e aggiungere lo strumento ora.
- Se non è stata seguita la seconda esercitazione, [seguire queste istruzioni](how-to-add-tool-first-time.md) per configurare un progetto di Azure Migrate. Lo strumento Migrazione server di Azure Migrate verrà aggiunto durante la creazione del progetto.

Se è già stato configurato un progetto, seguire queste istruzioni per aggiungere lo strumento:

1. Nel progetto di Azure Migrate fare clic su **Panoramica**. 
2. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server**.
3. In **Strumenti di migrazione** selezionare **Fare clic qui per aggiungere uno strumento di migrazione quando si è pronti per la migrazione**.

    ![Selezionare uno strumento](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Nell'elenco di strumenti selezionare **Azure Migrate: Migrazione server** > **Aggiungi strumento**

    ![Strumento Migrazione server](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>Preparare gli host Hyper-V


1. Nel progetto di Azure Migrate selezionare **Server**, quindi in **Azure Migrate: Migrazione server** fare clic su **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **Area di destinazione** selezionare l'area di Azure in cui eseguire la migrazione delle macchine virtuali.
6. Selezionare **Confermare che l'area di destinazione della migrazione è <nome area>** .
7. Fare clic su **Crea risorse**. Verrà creato un insieme di credenziali di Azure Site Recovery in background.
    - Se è già stata configurata la migrazione con Migrazione server di Azure Migrate, questa opzione non verrà visualizzata, perché le risorse sono state configurate in precedenza.
    - Dopo aver fatto clic su questo pulsante non è più possibile cambiare l'area di destinazione di questo progetto.
    - Tutte le migrazioni successive avverranno in questa area.
    
8. In **Preparare i server host Hyper-V** scaricare il provider di replica Hyper-V e il file della chiave di registrazione.
    - La chiave di registrazione è necessaria per registrare l'host Hyper-V con Migrazione server di Azure Migrate.
    - La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Scaricare il provider e la chiave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copiare il file del programma di installazione del provider e il file della chiave di registrazione in ogni host Hyper-V (o nodo del cluster) che esegue le VM da replicare.
5. Eseguire il file del programma di installazione del provider in ogni host, come descritto nella procedura seguente.
6. Dopo aver installato il provider negli host, in **Individua macchine virtuali** fare clic su **Finalize registration** (Finalizza registrazione).

    ![Finalizzare la registrazione](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Una volta finalizzata la registrazione, possono essere necessari fino a 15 minuti prima che le VM individuate vengano visualizzate in Migrazione server di Azure Migrate. Man mano che vengono individuate VM, il numero indicato in **Server individuati** aumenta.

![Server individuati](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Replicare le VM Hyper-V

Al termine dell'individuazione, è possibile avviare la replica delle VM Hyper-V in Azure.

> [!NOTE]
> È possibile replicare fino a 10 VM contemporaneamente. Se è necessario replicarne più, replicarle simultaneamente in batch di 10.

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server**, quindi **Replica**.
2. In **Replica** > **Impostazioni origine**  > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**. Fare quindi clic su **Avanti: Macchine virtuali**.
3. In **Macchine virtuali** selezionare le VM da replicare.
    - Se è stata eseguita una valutazione delle VM, è possibile applicare i consigli dei risultati della valutazione in merito al tipo di disco (Premium/Standard) e alle dimensioni delle VM. A questo scopo, in **Importare le impostazioni di migrazione da una valutazione di Azure Migrate?** selezionare l'opzione **Sì**.
    - Se non è stata eseguita una valutazione o non si vogliono usare le impostazioni della valutazione, selezionare l'opzione **No**.
    - Se si è scelto di usare la valutazione, selezionare il gruppo di VM e il nome della valutazione.

        ![Selezionare la valutazione](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. In **Macchine virtuali** cercare le VM necessarie e selezionare quelle di cui si vuole eseguire la migrazione. Quindi fare clic su **Avanti: Impostazioni di destinazione**.

    ![Selezionare le VM](./media/tutorial-migrate-hyper-v/select-vms.png)

5. In **Impostazioni di destinazione** selezionare l'area di destinazione della migrazione, la sottoscrizione e il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione.
7. In **Account di archiviazione di replica** selezionare l'account di archiviazione di Azure in cui verranno archiviati i dati replicati in Azure.
8. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
9. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

    ![Impostazioni di destinazione](./media/tutorial-migrate-hyper-v/target-settings.png)

10. In **Calcolo** controllare il nome, le dimensioni, il tipo di disco del sistema operativo e il set di disponibilità delle VM. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale conterrà le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Set di disponibilità**: se la VM deve essere inclusa in un set di disponibilità di Azure dopo la migrazione, specificare il set. Il set deve trovarsi nel gruppo di risorse di destinazione specificato per la migrazione.

    ![Impostazioni di calcolo per le VM](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

    ![Dischi](./media/tutorial-migrate-hyper-v/disks.png)

10. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Le impostazioni non possono essere modificate dopo l'avvio della replica.

## <a name="provisioning-for-the-first-time"></a>Primo provisioning

Se si tratta della prima VM che si sta replicando nel progetto Azure Migrate, lo strumento Azure Migrate: Migrazione del server effettua automaticamente il provisioning di queste risorse nello stesso gruppo di risorse del progetto.

- **Bus di servizio**: Azure Migrate: Migrazione del server usa il bus di servizio per inviare i messaggi di orchestrazione della replica all'appliance.
- **Account di archiviazione del gateway**: Azure Migrate: lo strumento Migrazione server usa l'account di archiviazione del gateway per archiviare le informazioni sullo stato delle VM da replicare.
- **Account di archiviazione di log**: l'appliance di Azure Migrate carica i log di replica per le VM in un account di archiviazione di log. Azure Migrate applica le informazioni di replica ai dischi gestiti dalla replica.
- **Insieme di credenziali delle chiavi**: l'appliance di Azure Migrate usa l'insieme di credenziali delle chiavi per gestire le stringhe di connessione per il bus di servizio e le chiavi di accesso per gli account di archiviazione usati nella replica. Le autorizzazioni necessarie all'insieme di credenziali delle chiavi per accedere all'account di archiviazione dovrebbero essere state configurate durante la [preparazione di Azure](tutorial-prepare-hyper-v.md#prepare-azure) per la valutazione e la migrazione delle macchine virtuali Hyper-V. 


## <a name="track-and-monitor"></a>Tenere traccia e monitorare


- Quando si fa clic su **Replica** viene avviato un processo Avvia replica. 
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente in Azure.

È possibile tenere traccia dello stato del processo nelle notifiche del portale.

È possibile monitorare lo stato della replica facendo clic su **Replica dei server** in **Azure Migrate: Migrazione server**.
![Monitorare la replica](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Eseguire una migrazione di test


All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sui computer locali, che rimangono operativi, e che la replica continui. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale di Azure non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Per eseguire una migrazione di test, seguire questa procedura:


1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Migrazione server** fare clic su **Testare i server con migrazione completata**.

     ![Testare i server con migrazione completata](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Fare clic con il pulsante destro del mouse sulla VM da testare e scegliere **Migrazione di test**.

    ![Migrazione di test](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. In **Migrazione di test** selezionare la rete virtuale di Azure in cui verrà inserita la VM di Azure dopo la migrazione. È consigliabile usare una rete virtuale non di produzione.
4. Verrà avviato il processo **Migrazione di test**. Monitorare il processo nelle notifiche del portale.
5. Al termine della migrazione, visualizzare la VM di Azure di cui è stata eseguita la migrazione in **Macchine virtuali** nel portale di Azure. Il nome della macchina virtuale ha il suffisso **-Test**.
6. Al termine del test, fare clic con il pulsante destro del mouse sulla macchina virtuale di Azure in **Replica delle macchine virtuali** e scegliere **Pulisci migrazione di test**.

    ![Eseguire la pulizia della migrazione](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle macchine virtuali locali.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. In **Replica delle macchine virtuali** fare clic con il pulsante destro del mouse sulla VM e scegliere **Esegui la migrazione**.
3. In **Esegui la migrazione** > **Spegnere le macchine virtuali ed eseguire una migrazione pianificata senza perdita di dati** selezionare **Sì** > **OK**.
    - Per impostazione predefinita, Azure Migrate arresta la VM locale ed esegue una replica su richiesta per sincronizzare le eventuali modifiche apportate alla macchina virtuale dopo l'ultima replica. Questa operazione assicura che non vi sia alcuna perdita di dati.
    - Se non si vuole arrestare la VM, selezionare **No**
4. Verrà avviato un processo di migrazione per la VM. Tenere traccia del processo nelle notifiche di Azure.
5. Al termine del processo, è possibile visualizzare e gestire la VM dalla pagina **Macchine virtuali**.

## <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla VM e scegliere **Arresta migrazione**. Vengono eseguite le operazioni seguenti:
    - Arresta la replica per il computer locale.
    - Rimuove il computer dal numero di **Server in fase di replica** in Azure Migrate: Server Migration.
    - Esegue la pulizia delle informazioni sullo stato di replica per la macchina virtuale.
2. Installare l'agente [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
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
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuire [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
-  È consigliabile distribuire [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/overview) per monitorare l'utilizzo delle risorse e le spese.


## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.
