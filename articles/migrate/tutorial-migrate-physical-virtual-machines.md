---
title: Eseguire la migrazione di computer fisici o macchine virtuali locali ad Azure con Migrazione server di Azure Migrate | Microsoft Docs
description: Questo articolo descrive come eseguire la migrazione di computer fisici o macchine virtuali locali ad Azure con Migrazione server di Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 728bf785edebcd17599b6a56edea1e26ed2d2fbc
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311784"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Eseguire la migrazione di server fisici o virtuali ad Azure 

Questo articolo illustra come eseguire la migrazione di server fisici o virtuali ad Azure. Lo strumento Migrazione server di Azure Migrate consente di eseguire la migrazione di server fisici e virtuali tramite replica basata su agente. Con questo strumento è possibile eseguire la migrazione di un'ampia gamma di computer ad Azure:

- Eseguire la migrazione di server fisici locali.
- Eseguire la migrazione di VM virtualizzate tramite piattaforme come Xen, KVM.
- Eseguire la migrazione di VM Hyper-V o VMware. Questa opzione è utile se per qualche motivo non è possibile usare il flusso standard offerto da Migrazione server di Azure Migrate per la migrazione di [Hyper-V](tutorial-migrate-hyper-v.md), [VMware senza agente](tutorial-migrate-vmware.md) o [VMware basata su agente](tutorial-migrate-vmware-agent.md).
- Eseguire la migrazione di VM in esecuzione in cloud privati.
- Eseguire la migrazione di VM in esecuzione in cloud pubblici, ad esempio Amazon Web Services (AWS) o Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dei carichi di lavoro e delle app locali, nonché delle istanze di VM del cloud. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.


In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Preparare Azure per la migrazione con lo strumento Migrazione server di Azure Migrate.
> * Verificare i requisiti dei computer di cui eseguire la migrazione e preparare un computer per l'appliance di replica di Azure Migrate usata per individuare ed eseguire la migrazione di computer ad Azure.
> * Aggiungere lo strumento Migrazione server di Azure Migrate nell'hub di Azure Migrate.
> * Configurare l'appliance di replica.
> * Installare il servizio Mobility nei computer di cui eseguire la migrazione.
> * Abilitare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa ad Azure.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli sulle procedure relative ad Azure Migrate.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Rivedere](migrate-architecture.md) l'architettura di migrazione.
2. Assicurarsi che al proprio account Azure sia assegnato il ruolo Collaboratore Macchina virtuale, in modo da avere le autorizzazioni per:

    - Creare una macchina virtuale nel gruppo di risorse selezionato.
    - Creare una macchina virtuale nella rete virtuale selezionata.
    - Scrivere in un disco gestito di Azure. 

3. [Configurare una rete di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quando si esegue la migrazione ad Azure, le macchine virtuali di Azure vengono create e aggiunte a una rete di Azure specificata durante la configurazione.


## <a name="prepare-azure"></a>Preparare Azure

Configurare le autorizzazioni di Azure prima di eseguire la migrazione con lo strumento Migrazione server di Azure Migrate.

- **Creare un progetto**: l'account Azure deve avere le autorizzazioni per creare un progetto di Azure Migrate. 
- **Registrare l'appliance di replica Azure Migrate**: l'appliance di replica crea e registra un'app Azure Active Directory nell'account Azure. Delegare le autorizzazioni per questa operazione.
- **Creare un insieme di credenziali delle chiavi**: per eseguire la migrazione delle macchine virtuali, Azure Migrate crea un insieme di credenziali delle chiavi nel gruppo di risorse, per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. Per creare l'insieme di credenziali, sono necessarie autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance di replica

Per la migrazione basata su agente, delegare allo strumento Migrazione server di Azure Migrate le autorizzazioni per creare e registrare un'app Azure AD nel proprio account. È possibile assegnare le autorizzazioni usando uno dei metodi seguenti:

- Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

Vale la pena notare che:

- Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
- Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance di replica. Dopo la configurazione dell'appliance di replica è possibile rimuoverle. 


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

    ![Autorizzazioni di Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni 

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="assign-permissions-to-create-key-vault"></a>Assegnare le autorizzazioni per creare l'insieme di credenziali delle chiavi

Per assegnare le autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate, eseguire questa procedura:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni. Sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).
3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse. 

## <a name="prepare-for-migration"></a>Preparare la migrazione

### <a name="check-machine-requirements-for-migration"></a>Verificare i requisiti del computer per la migrazione

Assicurarsi che i computer siano conformi ai requisiti per la migrazione ad Azure. 

> [!NOTE]
> La migrazione basata su agente con lo strumento Migrazione server di Azure Migrate si basa sulle funzionalità del servizio Azure Site Recovery. Alcuni requisiti potrebbero includere collegamenti alla documentazione di Site Recovery.

1. [Verificare](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) i requisiti dei server VMware.
2. [Verificare](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) i requisiti di supporto delle macchine virtuali per la migrazione.
3. Verificare le impostazioni delle macchine virtuali. Le macchine virtuali locali replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparare un computer per l'appliance di replica

Lo strumento Migrazione server di Azure Migrate usa un'appliance di replica per replicare i computer in Azure. L'appliance di replica esegue i componenti seguenti.

- **Server di configurazione**: Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- **Server di elaborazione** Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione cache in Azure. 

Prima di iniziare, è necessario preparare un computer Windows Server 2016 in cui ospitare l'appliance di replica. Il computer deve essere conforme a [questi requisiti](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Aggiungere lo strumento Migrazione server di Azure Migrate

Configurare un progetto di Azure Migrate e quindi aggiungervi lo strumento Migrazione server di Azure Migrate.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server** .
4. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server** .

    ![Individuare e valutare i server](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Aggiungi strumenti**.
6. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.
7. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare, quindi fare clic su **Avanti**.

    ![Creare un progetto di Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    È possibile creare un progetto di Azure Migrate in una delle aree geografiche seguenti.

    **Area geografica** | **Area**
    --- | ---
    Asia | Asia sud-orientale
    Europa | Europa settentrionale o Europa occidentale
    Stati Uniti | Stati Uniti orientali o Stati Uniti centro-occidentali

    L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. Per la migrazione effettiva è possibile selezionare qualsiasi area di destinazione.
8. In **Selezionare lo strumento di valutazione** selezionare **Ignora l'aggiunta di uno strumento di valutazione per adesso** > **Avanti**.
9. In **Selezionare lo strumento di migrazione** selezionare **Azure Migrate: Migrazione server** > **Avanti**.
10. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**
11. Dopo l'aggiunta, lo strumento viene visualizzato nel progetto di Azure Migrate > **Server** > **Strumenti di migrazione**.

## <a name="set-up-the-replication-appliance"></a>Configurare l'appliance di replica

La prima fase del processo di migrazione è la configurazione dell'appliance di replica. Scaricare il file del programma di installazione per l'appliance ed eseguirlo nel [computer preparato](#prepare-a-machine-for-the-replication-appliance). Dopo aver installato l'appliance, registrarla con Migrazione server di Azure Migrate.


### <a name="download-the-replication-appliance-installer"></a>Scaricare il programma di installazione dell'appliance di replica

1. Nel progetto di Azure Migrate selezionare **Server** e **Azure Migrate: Migrazione server** fare clic su **Individua**.

    ![Individuare le VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
4. In **Area di destinazione** selezionare l'area di Azure in cui eseguire la migrazione delle macchine virtuali.
5. Selezionare **Confermare che l'area di destinazione della migrazione è <nome area>** .
6. Fare clic su **Crea risorse**. Verrà creato un insieme di credenziali di Azure Site Recovery in background.
    - Se è già stata configurata la migrazione con Migrazione server di Azure Migrate, l'opzione della destinazione non può essere configurata, perché le risorse sono state configurate in precedenza.
    - Dopo aver fatto clic su questo pulsante non è più possibile cambiare l'area di destinazione di questo progetto.
    - Tutte le migrazioni successive avverranno in questa area.

7. In **Installare una nuova appliance di replica o aumentare le prestazioni della configurazione esistente?** selezionare **Installare un'appliance di replica**.
9. In **Scaricare e installare il software dell'appliance di replica** scaricare il programma di installazione dell'appliance e la chiave di registrazione. La chiave è necessaria per registrare l'appliance. È valida per cinque giorni dal momento in cui viene scaricata.

    ![Scaricare il provider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copiare il file di installazione dell'appliance e il file della chiave nel computer Windows Server 2016 creato per l'appliance.
11. Eseguire il file di installazione dell'appliance di replica, come descritto nella procedura seguente.
12. Una volta completata l'installazione e dopo il riavvio dell'appliance, in **Individua macchine virtuali** selezionare la nuova appliance in **Selezionare il server di configurazione** e fare clic su **Finalize registration** (Finalizza registrazione). Con la finalizzazione della registrazione vengono eseguite un paio di attività finali per preparare l'appliance di replica.

    ![Finalizzare la registrazione](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Una volta finalizzata la registrazione, possono essere necessari fino a 15 minuti prima che le macchine virtuali individuate vengano visualizzate in Migrazione server di Azure Migrate. Man mano che vengono individuate VM, il numero indicato in **Server individuati** aumenta.

![Server individuati](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installare il servizio Mobility

Nei computer di cui eseguire la migrazione, è necessario installare l'agente del servizio Mobility. I programmi di installazione degli agenti sono disponibili nell'appliance di replica. Individuare quello corretto e installare l'agente in ogni computer di cui eseguire la migrazione. Procedere come segue:

1. Accedere all'appliance di replica.
2. Passare a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Individuare il programma di installazione del sistema operativo del computer e la versione. Verificare i [sistemi operativi supportati](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Copiare il file del programma di installazione nel computer di cui eseguire la migrazione.
5. Assicurarsi di avere a disposizione la passphrase generata durante la distribuzione dell'appliance.
    - Archiviare il file in un file di testo temporaneo nel computer.
    - È possibile ottenere la passphrase nell'appliance di replica. Dalla riga di comando eseguire **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** per visualizzare la passphrase corrente.
    - Non rigenerare la passphrase. In caso contrario, la connettività si interrompe ed è necessario ripetere la registrazione dell'appliance di replica.


### <a name="install-on-windows"></a>Eseguire l'installazione in Windows

1. Estrarre il contenuto del file del programma di installazione in una cartella locale (ad esempio C:\Temp) nel computer, come indicato di seguito:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Eseguire il programma di installazione del servizio Mobility:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registrare l'agente con l'appliance di replica:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Eseguire l'installazione in Linux

1. Estrarre il contenuto del tarball del programma di installazione in una cartella locale del computer, ad esempio /tmp/MobSvcInstaller, come indicato di seguito:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Eseguire lo script del programma di installazione:
    ```
    sudo ./install -r MS -q
    ```
3. Registrare l'agente con l'appliance di replica:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replicare i computer

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server**, quindi **Replica**.

    ![Replicare le VM](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. In **Replica** > **Impostazioni origine** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata.
4. In **Server vCenter** specificare il nome del server vCenter che gestisce le VM, ovvero il server vSphere che le ospita.
5. In **Server di elaborazione** selezionare il nome dell'appliance di replica.
6. In **Credenziali guest** specificare l'account amministratore di macchine virtuali che verrà usato per l'installazione push del servizio Mobility. In questa esercitazione il servizio Mobility viene installato manualmente, quindi è possibile aggiungere qualsiasi account fittizio. Fare quindi clic su **Avanti: Macchine virtuali**.

    ![Replicare le VM](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. In **Macchine virtuali**, in **Importare le impostazioni di migrazione da una valutazione?** , lasciare l'impostazione predefinita **No, specificherò le impostazioni di migrazione manualmente**.
8. Selezionare ogni macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.

    ![Selezionare le VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione in cui eseguire la migrazione e quindi specificare il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione.
10. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
11. In **Vantaggio Azure Hybrid**:

    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

    ![Impostazioni di destinazione](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. In **Calcolo** esaminare il nome, le dimensioni, il tipo di disco del sistema operativo e il set di disponibilità delle VM. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Dimensioni macchina virtuale**: per impostazione predefinita, Migrazione server di Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**. 
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. 
    - **Set di disponibilità**: se la VM deve essere inclusa in un set di disponibilità di Azure dopo la migrazione, specificare il set. Il set deve trovarsi nel gruppo di risorse di destinazione specificato per la migrazione.

    ![Impostazioni calcolo](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

    ![Impostazioni dei dischi](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Le impostazioni non possono essere modificate dopo l'avvio della replica.



## <a name="track-and-monitor"></a>Tenere traccia e monitorare

- Quando si fa clic su **Replica** viene avviato un processo Avvia replica. 
- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.


È possibile tenere traccia dello stato del processo nelle notifiche del portale.

È possibile monitorare lo stato della replica facendo clic su **Replica dei server**  in **Azure Migrate: Migrazione server**.
![Monitorare la replica](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Eseguire una migrazione di test


All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sui computer locali, che rimangono operativi, e che la replica continui. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Per eseguire una migrazione di test, seguire questa procedura:


1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Migrazione server** fare clic su **Testare i server con migrazione completata**.

     ![Testare i server con migrazione completata](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Fare clic con il pulsante destro del mouse sulla VM da testare e scegliere **Migrazione di test**.

    ![Migrazione di test](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. In **Migrazione di test** selezionare la rete virtuale di Azure in cui verrà inserita la VM di Azure dopo la migrazione. È consigliabile usare una rete virtuale non di produzione.
4. Verrà avviato il processo **Migrazione di test**. Monitorare il processo nelle notifiche del portale.
5. Al termine della migrazione, visualizzare la VM di Azure di cui è stata eseguita la migrazione in **Macchine virtuali** nel portale di Azure. Il nome della macchina virtuale ha il suffisso **-Test**.
6. Al termine del test, fare clic con il pulsante destro del mouse sulla macchina virtuale di Azure in **Replica delle macchine virtuali** e scegliere **Pulisci migrazione di test**.

    ![Eseguire la pulizia della migrazione](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle macchine virtuali locali.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - È consigliabile distribuire [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/overview) per monitorare l'utilizzo delle risorse e le spese.


## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.
