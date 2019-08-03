---
title: Eseguire la migrazione di VM VMware locali ad Azure con lo strumento di migrazione server di Azure Migrate senza agente | Microsoft Docs
description: Descrive come eseguire una migrazione senza agente di VM VMware locali ad Azure usando Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7fba9cbbaa15359e7e4dd95e66645dd5f4022431
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640777"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Eseguire la migrazione di VM VMware ad Azure (senza agente)

Questo articolo illustra come eseguire una migrazione senza agente di VM VMware locali ad Azure con lo strumento di migrazione server di Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione dei carichi di lavoro e delle app locali, nonché delle istanze di VM AWS/GCP, in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

Questa esercitazione è la terza di una serie che illustra come valutare le VM VMware ed eseguirne la migrazione ad Azure usando gli strumenti di valutazione e migrazione server di Azure Migrate. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare le VM per la migrazione.
> * Aggiungere lo strumento di migrazione server di Azure Migrate.
> * Individuare le VM di cui eseguire la migrazione.
> * Avviare la replica delle VM.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa delle VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="migration-methods"></a>Metodi di migrazione

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione con replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione con un agente per la replica. Installare un agente nella VM per la replica.

Per decidere se usare la migrazione senza agente o basata su agente, vedere questi articoli:

- [Informazioni](server-migrate-overview.md) sul funzionamento della migrazione senza agente e [confronto dei metodi di migrazione](server-migrate-overview.md#compare-migration-methods).
- [Articolo](tutorial-migrate-vmware-agent.md) sul metodo basato su agente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Conoscere](migrate-architecture.md) l'architettura di migrazione di VMware.
2. [Completare la prima esercitazione](tutorial-prepare-vmware.md) di questa serie per configurare Azure e VMware per la migrazione. In particolare, per questa esercitazione è necessario:
    - [Preparare Azure](tutorial-prepare-vmware.md#prepare-azure) per la migrazione.
    - [Preparare l'ambiente locale](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) per la migrazione.
    
3. Prima di eseguire la migrazione di VM VMware ad Azure, è consigliabile provare a valutarle con lo strumento di valutazione server di Azure Migrate. Per configurare la valutazione, [completare la seconda esercitazione](tutorial-assess-vmware.md) della serie. Se non si vogliono valutare le VM, si può ignorare questa esercitazione. Nonostante sia consigliabile provare una valutazione, non è obbligatorio eseguirne una prima di provare una migrazione.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Aggiungere lo strumento di migrazione server di Azure Migrate

Se non è stata effettuata la seconda esercitazione per valutare le VM VMware, è necessario [seguire queste istruzioni](how-to-add-tool-first-time.md) per configurare un progetto di Azure Migrate e selezionare lo strumento di migrazione server di Azure Migrate. 

Se è stata seguita la seconda esercitazione ed è già disponibile un progetto di Azure Migrate configurato, aggiungere lo strumento di migrazione server di Azure Migrate come segue:

1. Nel progetto di Azure Migrate fare clic su **Panoramica**. 
2. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server**.

     ![Valutare ed eseguire la migrazione dei server](./media/tutorial-migrate-vmware/assess-migrate.png)

3. In **Strumenti di migrazione** selezionare **Fare clic qui per aggiungere uno strumento di migrazione quando si è pronti per la migrazione**.

    ![Selezionare uno strumento](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Nell'elenco di strumenti selezionare **Azure Migrate: Migrazione server** > **Aggiungi strumento**

    ![Strumento Migrazione server](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate

Lo strumento di migrazione server di Azure Migrate esegue un'appliance leggera per VM VMware. L'appliance esegue l'individuazione delle VM e ne invia i metadati e i dati sulle prestazioni allo strumento di migrazione server di Azure Migrate. La stessa appliance viene usata anche dallo strumento di valutazione server di Azure Migrate.

Se è stata seguita la seconda esercitazione per valutare le VM VMware, l'appliance è già stata configurata durante tale esercitazione. In caso contrario, è ora necessario configurare l'appliance. A tale scopo, eseguire l'operazione seguente: 

- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento di valutazione server di Azure Migrate. 
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

Per configurare l'appliance, seguire le istruzioni riportate in [questo articolo](how-to-set-up-appliance-vmware.md).


## <a name="prepare-vms-for-migration"></a>Preparare le VM per la migrazione

Affinché sia possibile eseguirne la migrazione ad Azure, Azure Migrate richiede alcune modifiche alle VM.

- Per alcuni sistemi operativi, Azure Migrate apporta automaticamente queste modifiche. [Altre informazioni](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Se la VM di cui si esegue la migrazione non ha uno di questi sistemi operativi, seguire le istruzioni per prepararla.
- È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
- Le modifiche di configurazione apportate alle VM locali vengono replicate in Azure dopo l'abilitazione della replica per la VM. Affinché le modifiche vengano replicate, verificare che il punto di ripristino usato per la migrazione sia successivo al momento in cui sono state apportate le modifiche di configurazione in locale.


### <a name="prepare-windows-server-vms"></a>Preparare le VM Windows Server

**Azione** | **Dettagli** | **Istruzioni**
--- | --- | ---
Assicurarsi che i volumi di Windows nella VM di Azure usino le stesse assegnazioni di lettere di unità della VM locale | Configurare il criterio SAN Porta in linea tutti i dischi. | 1. Accedere alla VM con un account amministratore e aprire una finestra di comando.<br/> 2. Digitare **diskpart** per eseguire l'utilità Diskpart.<br/> 3. Digitare **SAN POLICY=OnlineAll**<br/> 4. Digitare Exit per uscire da Diskpart e chiudere il prompt dei comandi.
Abilitare la console seriale di accesso di Azure per la VM di Azure | Questa operazione è utile per la risoluzione dei problemi. Non è necessario riavviare la VM. La VM di Azure verrà avviata usando l'immagine del disco e tale avvio equivale a un riavvio per la nuova VM. | Seguire [queste istruzioni](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) per abilitare la console.
Installare i servizi di integrazione guest Hyper-V | In caso di migrazione di macchine virtuali che eseguono Windows Server 2003, installare i servizi di integrazione guest Hyper-V nel sistema operativo delle VM. | [Altre informazioni](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)
Desktop remoto | Abilitare Desktop remoto nella VM e controllare che Windows Firewall non blocchi l'accesso tramite Desktop remoto nei profili di rete. | [Altre informazioni](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)

### <a name="prepare-linux-vms"></a>Preparare le VM Linux

**Azione** | **Dettagli** 
--- | --- | ---
Installare Linux Integration Services per Hyper-V | Nella maggior parte delle nuove versioni delle distribuzioni di Linux è presente per impostazione predefinita.
Ricompilare l'immagine init di Linux in modo che includa i driver Hyper-V necessari | Questa operazione, necessaria solo in alcune distribuzioni, garantisce che la VM venga avviata in Azure.
Abilitare la registrazione della console seriale di Azure | Questa operazione è utile per la risoluzione dei problemi. Non è necessario riavviare la VM. La VM di Azure verrà avviata usando l'immagine del disco e tale avvio equivale a un riavvio per la nuova VM.<br/> Seguire [queste istruzioni](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) per abilitare la registrazione.
Aggiornare il file di mappa dei dispositivi | Aggiornare il file di mappa dei dispositivi contenente le associazioni tra nome del dispositivo e volume, per usare identificatori di dispositivo permanenti.
Aggiornare le voci fstab | Aggiornare le voci in modo da usare identificatori di volume permanenti.
Rimuovere la regola di udev | Rimuovere qualsiasi regola di udev che riservi i nomi di interfaccia in base all'indirizzo MAC e così via.
Aggiornare le interfacce di rete | Aggiornare le interfacce di rete in modo che ricevano l'indirizzo IP in base a DHCP.
Abilitare ssh | Assicurarsi che ssh sia abilitato e che il servizio sshd sia impostato per l'avvio automatico al riavvio.<br/> Assicurarsi che le richieste di connessione ssh in ingresso non siano bloccate da regole gestibili tramite script o del firewall del sistema operativo.

Per informazioni sulla procedura per eseguire una VM Linux in Azure e istruzioni per alcune delle distribuzioni di Linux più diffuse, vedere [questo articolo](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).  


## <a name="replicate-vms"></a>Replicare le VM

Al termine dell'individuazione, è possibile avviare la replica delle VM VMware in Azure.

1. Nel progetto di Azure Migrate selezionare **Server** e **Azure Migrate: Migrazione server**, quindi **Replica**.

    ![Replicare le VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. In **Replica** > **Impostazioni origine**  > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata e quindi **OK**. 

    ![Impostazioni origine](./media/tutorial-migrate-vmware/source-settings.png)

    - Questo passaggio presuppone che sia già stata configurata un'appliance completando l'esercitazione.
    - Se non si è configurata un'appliance, seguire le istruzioni riportate in [questo articolo](how-to-set-up-appliance-vmware.md).

4. In **Macchine virtuali** selezionare le VM da replicare.
    - Se è stata eseguita una valutazione delle VM, è possibile applicare i consigli dei risultati della valutazione in merito al tipo di disco (Premium/Standard) e alle dimensioni delle VM. A questo scopo, in **Importare le impostazioni di migrazione da una valutazione di Azure Migrate?** selezionare l'opzione **Sì**.
    - Se non è stata eseguita una valutazione o non si vogliono usare le impostazioni della valutazione, selezionare l'opzione **No**.
    - Se si è scelto di usare la valutazione, selezionare il gruppo di VM e il nome della valutazione.

    ![Selezionare la valutazione](./media/tutorial-migrate-vmware/select-assessment.png)

5. In **Macchine virtuali** cercare le VM in base alle esigenze e selezionare ogni macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.

    ![Selezionare le VM](./media/tutorial-migrate-vmware/select-vms.png)

6. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione in cui eseguire la migrazione e quindi specificare il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
7. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

    ![Impostazioni di destinazione](./media/tutorial-migrate-vmware/target-settings.png)

8. In **Calcolo** esaminare il nome, le dimensioni, il tipo di disco del sistema operativo e il set di disponibilità delle VM. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale conterrà le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Set di disponibilità**: se la VM deve essere inclusa in un set di disponibilità di Azure dopo la migrazione, specificare il set. Il set deve trovarsi nel gruppo di risorse di destinazione specificato per la migrazione.

    ![Impostazioni di calcolo per le VM](./media/tutorial-migrate-vmware/compute-settings.png)

9. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

    ![Dischi](./media/tutorial-migrate-vmware/disks.png)

10. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Le impostazioni non possono essere modificate dopo l'avvio della replica.

### <a name="provisioning-for-the-first-time"></a>Primo provisioning

Per la prima VM replicata nel progetto di Azure Migrate, lo strumento Migrazione server di Azure Migrate effettua automaticamente il provisioning delle risorse nello stesso gruppo di risorse del progetto.

- **Bus di servizio**: lo strumento Migrazione server di Azure Migrate usa il bus di servizio per inviare messaggi di orchestrazione della replica all'appliance.
- **Account di archiviazione del gateway**: lo strumento Migrazione server usa l'account di archiviazione del gateway per archiviare le informazioni sullo stato delle VM da replicare.
- **Account di archiviazione di log**: l'appliance di Azure Migrate carica i log di replica per le VM in un account di archiviazione di log. Azure Migrate applica le informazioni di replica ai dischi gestiti di replica.
- **Insieme di credenziali delle chiavi**: l'appliance di Azure Migrate usa l'insieme di credenziali delle chiavi per gestire le stringhe di connessione per il bus di servizio e le chiavi di accesso per gli account di archiviazione usati nella replica. Le autorizzazioni necessarie all'insieme di credenziali delle chiavi per accedere all'account di archiviazione dovrebbero essere state configurate durante la preparazione. [Esaminare le autorizzazioni](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Tenere traccia e monitorare


- Quando si fa clic su **Replica** viene avviato un processo Avvia replica. 
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

È possibile tenere traccia dello stato del processo nelle notifiche del portale.

È possibile monitorare lo stato della replica facendo clic su **Replica dei server**  in **Azure Migrate: Migrazione server**.
![Monitorare la replica](./media/tutorial-migrate-vmware/replicating-servers.png)




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

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla VM e scegliere **Arresta migrazione**. La replica della macchina virtuale locale verrà arrestata e verrà eseguita la pulizia delle informazioni sullo stato della replica della VM.
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
