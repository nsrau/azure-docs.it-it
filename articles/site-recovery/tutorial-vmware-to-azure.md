---
title: Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8acc8deff8b635c97e8722d65a728aebf0e49bb3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali

Questa esercitazione illustra come configurare il ripristino di emergenza in Azure per VM VMware locali che eseguono Windows. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Specificare l'origine e la destinazione della replica.
> * Configurare l'ambiente di origine, inclusi i componenti locali di Site Recovery, e l'ambiente di destinazione della replica.
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md)

Prima di iniziare, è utile [esaminare l'architettura](concepts-vmware-to-azure-architecture.md) per uno scenario di ripristino di emergenza.


## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **Insiemi di credenziali dei servizi di ripristino** fare clic sul nome dell'insieme di credenziali, **ContosoVMVault**.
2. In **Attività iniziali** fare clic su Site Recovery. Fare quindi clic su **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. In "In quale destinazione si vuole eseguire la replica dei computer" selezionare **In Azure**.
5. In **I computer sono virtuali** selezionare **Sì con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine, è necessario un singolo computer locale a disponibilità elevata per l'hosting dei componenti locali di Site Recovery. I componenti includono il server di configurazione, il server di elaborazione e il server di destinazione master.

- Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility nelle VM da replicare ed esegue l'individuazione automatica delle VM VMware locali.
- Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

Per configurare il server di configurazione come una VM VMware a disponibilità elevata, si scarica un modello OVF preparato e si importa il modello in VMware per creare la VM. Dopo aver configurato il server di configurazione, registrarlo nell'insieme di credenziali. Dopo la registrazione, Site Recovery individua le VM VMware locali.

### <a name="download-the-vm-template"></a>Scaricare il modello di VM

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** fare clic su **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVF (Open Virtualization Format) per il server di configurazione.

  > [!TIP]
  L'ultima versione del modello del server di configurazione può essere scaricata direttamente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

1. Accedere al server VMware vCenter o all'host vSphere ESXi usando il client VMWare vSphere.
2. Dal menu **File** scegliere **Deploy OVF Template** (Distribuisci modello OVF) per avviare la relativa procedura guidata.  

     ![Modello OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. In **Select source** (Seleziona origine) specificare il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) fare clic su **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale).
4. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
5. In **Ready to complete** (Completamento):
  - Per configurare la VM con le impostazioni predefinite, selezionare **Power on after deployment** (Accendi al termine della distribuzione) > **Finish** (Fine).
  - Se si vuole aggiungere un'altra interfaccia di rete, deselezionare **Power on after deployment** (Accendi al termine della distribuzione) e quindi selezionare **Finish** (Fine). Per impostazione predefinita, il modello del server di configurazione viene distribuito con una singola scheda di interfaccia di rete, ma è possibile aggiungerne altre dopo la distribuzione.

  
## <a name="add-an-additional-adapter"></a>Aggiungere un'altra scheda

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla VM e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** fare clic su **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi fare clic su **Next**.
3. Selezionare un tipo di scheda e una rete. 
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della VM, selezionare **Connect at power on** (Connetti all'accensione). Fare clic su **Next** (Avanti) > **Finish** (Fine) e quindi su **OK**.


## <a name="register-the-configuration-server"></a>Registrare il server di configurazione 

1. Accendere la VM dalla console del client VMWare vSphere.
2. La VM si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e specificare una password amministratore.
3. Al termine dell'installazione, accedere alla VM come amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione di Azure Site Recovery.
5. Specificare un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi fare clic su **Next**.
6. Lo strumento verifica che la VM possa connettersi ad Azure. Dopo aver stabilito la connessione, fare clic su **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.
7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Eseguire di nuovo l'accesso al computer. Verrà avviata automaticamente la procedura guidata per la gestione del server di configurazione.

### <a name="configure-settings-and-connect-to-vmware"></a>Configurare le impostazioni e connettersi a VMware

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività** e quindi la scheda di interfaccia di rete che riceverà il traffico di replica. Fare quindi clic su **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
2. In **Seleziona l'insieme di credenziali di Servizi di ripristino** selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti.
3. In **Installa software di terze parti** accettare il contratto di licenza e fare clic su **Scarica e installa** per installare MySQL Server.
4. Fare clic su **Installa VMware PowerLCI**. Prima di eseguire questa operazione, assicurarsi che tutte le finestre del browser siano chiuse. Fare quindi clic su **Continua**.
5. In **Convalida la configurazione dell'appliance** verranno verificati i prerequisiti prima di continuare.
6. In **Configure vCenter Server/vSphere ESXi server** (Configura server vCenter/vSphere ESXi) specificare il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le VM da replicare. Specificare la porta su cui il server è in ascolto e un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
7. Specificare le credenziali che verranno usate dal server di configurazione per connettersi al server VMware. Site Recovery usa queste credenziali per individuare automaticamente le VM VMware disponibili per la replica. Fare clic su **Aggiungi** e quindi su **Continua**.
8. In **Configura le credenziali della macchina virtuale** specificare il nome utente e la password che verranno usati per installare automaticamente il servizio Mobility nei computer quando verrà abilitata la replica. Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare. Per Linux, specificare i dettagli dell'account radice.
9. Fare clic su **Finalizza configurazione** per completare la registrazione. 
10. Al termine della registrazione, nel portale di Azure verificare che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. Fare quindi clic su **OK** per configurare le impostazioni della destinazione.


Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le VM.

> [!NOTE]
> Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, fare clic su **Server di configurazione** > ***nome del server*** > **Aggiorna server**.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Tutte le risorse**.
2. Fare clic sull'insieme di credenziali di Servizi di ripristino denominato **ContosoVMVault**.
3. Per creare criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
4. In **Creare i criteri di replica** specificare il nome dei criteri **VMwareRepPolicy**.
5. In **Soglia RPO** usare il valore predefinito di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
6. In **Conservazione del punto di ripristino** usare il valore predefinito di 24 ore per la durata del periodo di conservazione per ogni punto di ripristino. Per questa esercitazione, selezionare 72 ore. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
7. In **Frequenza snapshot coerenti con l'app** usare il valore predefinito di 60 minuti per la frequenza con cui vengono creati snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

   ![Criterio](./media/tutorial-vmware-to-azure/replication-policy.png)

I criteri vengono automaticamente associati al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

## <a name="enable-replication"></a>Abilitare la replica

Quando viene abilitata la replica per una macchina virtuale, Site Recovery installa il servizio Mobility. Le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

Per abilitare la replica, procedere come descritto di seguito:

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host.
5. Selezionare il server di elaborazione (server di configurazione). Quindi fare clic su **OK**.
6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.
7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati.
8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover.
9. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
10. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
11. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
12. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verificare che siano stati selezionati i criteri di replica corretti.
13. Fare clic su **Abilita la replica**.

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

Per monitorare le macchine virtuali aggiunte è possibile controllare l'ora dell'ultima individuazione di macchine virtuali in **Server di configurazione**
> **Ora ultimo contatto**. Per aggiungere VM senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](site-recovery-test-failover-to-azure.md)
