---
title: Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 4/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9e8f450825b7b4ad0402b8976d68bc23c18ce855
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357871"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali

Questo articolo descrive come abilitare la replica delle macchine virtuali VMware locali per il ripristino di emergenza in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

Questa è la terza esercitazione di una serie che mostra come configurare il ripristino di emergenza in Azure per macchine virtuali VMware locali. Nell'esercitazione precedente [è stato preparato l'ambiente VMware locale](vmware-azure-tutorial-prepare-on-premises.md) per il ripristino di emergenza in Azure.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare le impostazioni di replica di origine e un server di configurazione di Site Recovery locale.
> * Configurare le impostazioni della destinazione della replica.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale VMware.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere l'articolo nella sezione delle procedure del sommario di Site Recovery.

## <a name="before-you-start"></a>Prima di iniziare

Completare le esercitazioni precedenti:
1. Assicurarsi di [configurare Azure](tutorial-prepare-azure.md) per il ripristino di emergenza di VMware locale in Azure.
2. Seguire [questa procedura](vmware-azure-tutorial-prepare-on-premises.md) per preparare la distribuzione VMware locale per il ripristino di emergenza in Azure.
3. Questa esercitazione illustra come eseguire la replica di una singola macchina virtuale. Se si intende distribuire più macchine virtuali VMware, è consigliabile usare lo strumento [Deployment Planner](https://aka.ms/asr-deployment-planner). [Altre informazioni](site-recovery-deployment-planner.md) su questo strumento.
4. Per questa esercitazione è possibile scegliere tra diverse opzioni per eseguire le operazioni:
    - Questa esercitazione usa un modello OVA per creare la macchina virtuale VMware per il server di configurazione. Se per qualche motivo questa opzione non è accettabile, seguire [questa procedura](physical-manage-configuration-server.md) per configurare il server di configurazione manualmente.
    - In questa esercitazione Site Recovery scarica e installa automaticamente MySQL nel server di configurazione. Se si preferisce, è possibile configurarlo manualmente. [Altre informazioni](vmware-azure-deploy-configuration-server.md#configure-settings)




## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare il nome dell'insieme di credenziali. In questo scenario viene usato **ContosoVMVault**.
2. In **Attività iniziali** selezionare su Site Recovery. Selezionare quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **I computer sono virtuali** selezionare **Sì con VMware vSphere Hypervisor**. Selezionare **OK**.



## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Nell'ambiente di origine è necessario un singolo computer locale a disponibilità elevata per ospitare i componenti locali di Site Recovery:

- **Server di configurazione**: Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- **Server di elaborazione** Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione cache in Azure. Il server di elaborazione installa anche l'agente del servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.
- **Server master di destinazione**: Il server di destinazione master gestisce i dati di replica durante il failback da Azure.


Tutti questi componenti vengono installati insieme nei singoli computer locali noti come *server di configurazione*. Per impostazione predefinita, per il ripristino di emergenza di VMware il server di configurazione viene configurato come macchina virtuale VMware a disponibilità elevata. A questo scopo, scaricare un modello OVA (Open Virtualization Application) preparato e importarlo in VMware per creare la macchina virtuale. 

- L'ultima versione del server di configurazione è disponibile nel portale. È anche possibile scaricarla direttamente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).
- Se per qualche motivo non è possibile usare un modello OVA per configurare una macchina virtuale, seguire [questa procedura](physical-manage-configuration-server.md) per configurare il server di configurazione manualmente.
- La licenza fornita con il modello OVF è una licenza di valutazione valida per 180 giorni. La versione di Windows in esecuzione nella macchina virtuale deve essere attivata con la licenza necessaria. 


### <a name="download-the-vm-template"></a>Scaricare il modello di VM

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** selezionare **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
4. Scaricare il modello OVF per il server di configurazione.



## <a name="import-the-template-in-vmware"></a>Importare il modello in VMware


1. Accedere al server VMware vCenter o all'host vSphere ESXi con il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) **per avviare la relativa procedura**guidata. 

     ![Modello OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale).
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Ready to complete** (Pronto per completare), per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Power on after deployment** > **Finish** (Accendi al termine della distribuzione, Fine).

   > [!TIP]
   > Se si vuole aggiungere un'altra scheda di interfaccia di rete, deselezionare **Power on after deployment** > **Finish** (Accendi al termine della distribuzione, Fine). Per impostazione predefinita, il modello contiene una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.

## <a name="add-an-additional-adapter"></a>Aggiungere un'altra scheda

Se si vuole aggiungere un'altra scheda di interfaccia di rete al server di configurazione, eseguire questa operazione prima di registrare il server nell'insieme di credenziali. L'aggiunta di altre schede non è supportata dopo la registrazione.

1. Nell'inventario del client vSphere fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Edit Settings** (Modifica impostazioni).
2. In **Hardware** selezionare **Add** (Aggiungi) > **Ethernet Adapter** (Scheda Ethernet). Quindi selezionare **Avanti**.
3. Selezionare un tipo di scheda e una rete. 
4. Per connettere la scheda di interfaccia di rete virtuale all'accensione della macchina virtuale, selezionare **Connect at power on** (Connetti all'accensione). Selezionare **Next** > **Finish** (Avanti > Fine). Selezionare **OK**.


## <a name="register-the-configuration-server"></a>Registrare il server di configurazione 

Dopo la configurazione del server di configurazione, registrarlo nell'insieme di credenziali.

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione di Azure Site Recovery entro pochi secondi.
5. Immettere un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi selezionare **Avanti**.
6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.
7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente entro pochi secondi.


### <a name="configure-settings-and-add-the-vmware-server"></a>Configurare le impostazioni e aggiungere il server VMware

Completare la configurazione e la registrazione del server di configurazione. 


1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività**. Dagli elenchi a discesa selezionare prima l'interfaccia di rete usata dal server di elaborazione predefinito per il rilevamento e l'installazione push del servizio Mobility nelle macchine di origine e quindi selezionare la scheda di rete usata dal server di configurazione per la connettività con Azure. Selezionare quindi **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
2. In **Seleziona l'insieme di credenziali di Servizi di ripristino** selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti.
3. In **Installa software di terze parti** accettare il contratto di licenza. Selezionare **Scarica e installa** per installare Server MySQL. Se MySQL è stato incluso nel percorso, questo passaggio viene ignorato.
4. Selezionare **Installa VMware PowerCLI**. Prima di eseguire questa operazione, assicurarsi che tutte le finestre del browser siano chiuse. Selezionare quindi **Continua**.
5. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
6. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
7. Immettere le credenziali utente che verranno usate dal server di configurazione per connettersi al server VMware. Assicurarsi che il nome utente e la password siano corretti e che l'utente faccia parte del gruppo Amministratori della macchina virtuale da proteggere. Site Recovery usa queste credenziali per individuare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** e quindi **Continua**.
8. In **Configura le credenziali della macchina virtuale** immettere il nome utente e la password che verranno usati per installare automaticamente il servizio Mobility nelle macchine virtuali quando la replica è abilitata.
    - Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare.
    - Per Linux, specificare i dettagli dell'account radice.
9. Selezionare **Finalizza configurazione** per completare la registrazione.
10. Al termine della registrazione, verificare nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. Fare quindi clic su **OK** per configurare le impostazioni della destinazione.


Dopo la registrazione del server di configurazione, Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le macchine virtuali.

> [!NOTE]
> Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, selezionare **Server di configurazione** > ***nome del server*** > **Aggiorna server**.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Selezionare **Preparare l'infrastruttura** > **Destinazione**. Selezionare la sottoscrizione di Azure da usare. Si usa un modello di Resource Manager.
2. Site Recovery verifica la disponibilità di una o più reti virtuali. Questi elementi devono essere disponibili quando si configurano i componenti di Azure nella [prima esercitazione](tutorial-prepare-azure.md) in questa serie di esercitazioni.

   ![Scheda Destinazione](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Aprire il [portale di Azure](https://portal.azure.com) e selezionare **Tutte le risorse**.
2. Selezionare l'insieme di credenziali di Servizi di ripristino (**ContosoVMVault** in questa esercitazione).
3. Per creare i criteri di replica, selezionare **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
4. In **Creare i criteri di replica** immettere il nome dei criteri. In questo scenario viene usato **VMwareRepPolicy**.
5. In **Soglia RPO** usare il valore predefinito di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
6. In **Conservazione del punto di ripristino** specificare per quanto tempo viene conservato ogni punto di ripristino. Per questa esercitazione, selezionare 72 ore. Le macchine virtuali replicate possono essere ripristinate in qualsiasi punto all'interno dell'intervallo di conservazione.
7. In **Frequenza snapshot coerenti con l'app** specificare la frequenza di snapshot coerenti con l'applicazione. Viene usato il valore predefinito di 60 minuti. Fare clic su **OK** per creare i criteri.

   ![Creare criteri di replica](./media/vmware-azure-tutorial/replication-policy.png)

- I criteri vengono automaticamente associati al server di configurazione.
- Per impostazione predefinita, vengono creati automaticamente i criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica per le macchine virtuali nel modo seguente:

1. Selezionare **Eseguire la replica dell'applicazione** > **Origine**.
1. In **Origine** selezionare **Locale** e selezionare il server di configurazione in **Percorso di origine**.
1. In **Tipo di computer** selezionare **Macchine virtuali**.
1. In **vCenter/vSphere Hypervisor** selezionare l'host vSphere o il server vCenter che gestisce l'host.
1. Selezionare il server di elaborazione, installato per impostazione predefinita nella macchina virtuale del server di configurazione. Selezionare **OK**.
1. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le macchine virtuali di cui viene effettuato il failover. Viene usato il modello di distribuzione di Resource Manager. 
1. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure create dopo il failover.
1. Selezionare **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutte le macchine virtuali in cui viene abilitata la replica. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
1. In **Macchine virtuali** > **Seleziona macchine virtuali** selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Selezionare **OK**. Se non è possibile visualizzare/selezionare alcuna macchina virtuale specifica, vedere [altre informazioni](https://aka.ms/doc-plugin-VM-not-showing) per la risoluzione del problema.
1. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
1. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti.
1. Selezionare **Abilita replica**. Quando viene abilitata la replica per una macchina virtuale, Site Recovery installa il servizio Mobility.
1. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.
1. Le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.
1. Per eseguire il monitoraggio delle macchine virtuali aggiunte, è possibile controllare l'ora dell'ultima individuazione di macchine virtuali in **Server di configurazione** > **Ora ultimo contatto**. Per aggiungere macchine virtuali senza attendere l'individuazione pianificata, evidenziare il server di configurazione senza selezionarlo e quindi selezionare **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi
Dopo l'abilitazione della replica eseguire un ripristino per assicurarsi che tutto funzioni come previsto.
> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](site-recovery-test-failover-to-azure.md)
