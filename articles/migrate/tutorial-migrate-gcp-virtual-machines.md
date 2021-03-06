---
title: Individuare, verificare ed eseguire la migrazione di istanze di VM GCP (Google Cloud Platform) ad Azure
description: Questo articolo illustra come eseguire la migrazione di VM GCP ad Azure con Azure Migrate.
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 0093bb453131eb87172021a976b8019a23e445f1
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302593"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Individuare, valutare ed eseguire la migrazione di VM GCP (Google Cloud Platform) ad Azure

Questa esercitazione illustra come individuare, valutare ed eseguire la migrazione di macchine virtuali (VM) GCP (Google Cloud Platform) a VM di Azure con Azure Migrate: Valutazione server o Azure Migrate: Migrazione del server.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Verificare i prerequisiti per la migrazione.
> * Preparare le risorse di Azure con Azure Migrate: Migrazione del server. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare le istanze di VM GCP per la migrazione.
> * Aggiungere lo strumento Azure Migrate: Migrazione del server nell'hub di Azure Migrate.
> * Configurare l'appliance di replica e distribuire il server di configurazione.
> * Installare il servizio di mobilità nelle VM GCP di cui eseguire la migrazione.
> * Abilitare la replica per le VM.
> * Rilevare e monitorare lo stato della replica. 
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa ad Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="discover-and-assess"></a>Individuazione e valutazione

Prima di procedere con la migrazione ad Azure, è consigliabile eseguire un'individuazione delle macchine virtuali e una valutazione della migrazione. Questa valutazione consente di dimensionare correttamente le VM GCP per la migrazione ad Azure, oltre che di stimare i potenziali costi dell'esecuzione in Azure.

Configurare una valutazione nel modo seguente:

1. Seguire l'[esercitazione](./tutorial-discover-gcp.md) per configurare Azure e preparare le VM GCP per una valutazione. Tenere presente quanto segue:

    - Azure Migrate usa l'autenticazione della password durante l'individuazione delle istanze di VM GCP. Le istanze di GCP non supportano l'autenticazione della password per impostazione predefinita. Prima di procedere all'individuazione, è necessario abilitare l'autenticazione della password.
        - Per i computer Windows consentire la porta 5985 (HTTP) di WinRM per permettere le chiamate WMI remote.
        - Per i computer Linux:
            1. Accedere a ogni computer Linux.
            2. Aprire il file sshd_config: vi /etc/ssh/sshd_config
            3. Nel file individuare la riga **PasswordAuthentication** e impostare il valore su **yes**.
            4. Salvare il file e chiuderlo. Riavviare il servizio ssh.
    - Se si usa un utente root per individuare le macchine virtuali Linux, assicurarsi che l'accesso root sia consentito nelle macchine virtuali.
        1. Accedere a ogni computer Linux
        2. Aprire il file sshd_config: vi /etc/ssh/sshd_config
        3. Nel file individuare la riga **PermitRootLogin** e impostare il valore su **yes**.
        4. Salvare il file e chiuderlo. Riavviare il servizio ssh.

2. Quindi, seguire questa [esercitazione](./tutorial-assess-gcp.md) per configurare un progetto e un'appliance di Azure Migrate per individuare e valutare le VM GCP.

Sebbene sia consigliabile provare a eseguire una valutazione, non è un passaggio obbligatorio per poter eseguire la migrazione delle macchine virtuali.



## <a name="prerequisites"></a>Prerequisiti 

- Assicurarsi che nelle VM GCP di cui eseguire la migrazione sia in esecuzione una versione supportata del sistema operativo. Le VM GCP vengono trattate come computer fisici ai fini della migrazione. Verificare le [versioni del sistema operativo e del kernel supportate](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) per il flusso di lavoro di migrazione dei server fisici. È possibile usare comandi standard come *hostnamectl* o *uname -a* per controllare le versioni del sistema operativo e del kernel per le macchine virtuali Linux.  È consigliabile eseguire una migrazione di test per verificare se la VM funziona come previsto prima di procedere con la migrazione effettiva.
- Assicurarsi che le VM GCP siano conformi alle [configurazioni supportate](./migrate-support-matrix-physical-migration.md#physical-server-requirements) per la migrazione ad Azure.
- Verificare che le VM GCP replicate in Azure siano conformi ai [requisiti delle macchine virtuali di Azure](./migrate-support-matrix-physical-migration.md#azure-vm-requirements).
- Prima di eseguire la migrazione delle VM ad Azure, è necessario apportarvi alcune modifiche.
    - Per alcuni sistemi operativi, Azure Migrate apporta automaticamente queste modifiche.
    - È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).

### <a name="prepare-azure-resources-for-migration"></a>Preparare le risorse di Azure per la migrazione

Preparare Azure per la migrazione con lo strumento Azure Migrate: Migrazione del server.

**Attività** | **Dettagli**
--- | ---
**Creare un progetto di Azure Migrate** | Per [creare un nuovo progetto](https://docs.microsoft.com/azure/migrate/create-manage-projects), l'account Azure deve avere autorizzazioni di Collaboratore o Proprietario.
**Verificare le autorizzazioni per l'account Azure** | L'account Azure necessita delle autorizzazioni per creare una macchina virtuale ed eseguire la scrittura su un disco gestito di Azure.

### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-azure-account-permissions"></a>Assegnare le autorizzazioni all'account Azure

Assegnare il ruolo Collaboratore Macchina virtuale all'account Azure. Questo ruolo fornisce le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere in un disco gestito di Azure. 

### <a name="create-an-azure-network"></a>Creare una rete di Azure

[Configurare](../virtual-network/manage-virtual-network.md#create-a-virtual-network) una rete virtuale di Azure. Quando si esegue la replica in Azure, le macchine virtuali di Azure create vengono aggiunte alla rete virtuale di Azure specificata durante la configurazione della migrazione.

## <a name="prepare-gcp-instances-for-migration"></a>Preparare le istanze di GCP per la migrazione

Prima della migrazione da GCP ad Azure, è necessario preparare e distribuire un'appliance di replica.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Preparare un computer per l'appliance di replica

Lo strumento Azure Migrate: Migrazione del server usa un'appliance di replica per replicare i computer in Azure. L'appliance di replica esegue i componenti seguenti.

- **Server di configurazione**: il server di configurazione coordina le comunicazioni tra le VM GCP e Azure, oltre a gestire la replica dei dati.
- **Server di elaborazione** Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione della cache in Azure.

Preparare la distribuzione dell'appliance come indicato di seguito:

- Configurare una VM GCP separata in cui ospitare l'appliance di replica. L'istanza deve eseguire Windows Server 2012 R2 o Windows Server 2016. [Verificare](./migrate-replication-appliance.md#appliance-requirements) i requisiti hardware, software e di rete per l'appliance.
- L'appliance non deve essere installata in una macchina virtuale di origine che si vuole replicare o nell'appliance di individuazione e valutazione di Azure Migrate eventualmente installata in precedenza. Deve essere distribuita in una VM diversa.
- Le VM GCP di origine di cui eseguire la migrazione dovranno essere in grado di comunicare in rete con l'appliance di replica. Configurare le regole del firewall necessarie per abilitare questa comunicazione. È consigliabile distribuire l'appliance di replica nella stessa rete privata virtuale delle VM di origine di cui eseguire la migrazione. Se l'appliance di replica deve trovarsi in una rete privata virtuale diversa, le due reti devono essere connesse tramite peering.
- Le VM GCP di origine comunicano con l'appliance di replica sulle porte HTTPS 443 (orchestrazione del canale di controllo) e TCP 9443 (trasporto dati) in ingresso per la gestione della replica e il relativo trasferimento dei dati. L'appliance di replica a sua volta orchestra e invia i dati di replica ad Azure sulla porta HTTPS 443 in uscita. Per configurare queste regole, modificare le regole in ingresso e in uscita del gruppo di sicurezza con le porte e le informazioni sull'indirizzo IP di origine appropriate.

   ![Regole del firewall di GCP](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Modificare le regole del firewall](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- L'appliance di replica usa MySQL. Esaminare le [opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
- Esaminare gli URL di Azure necessari all'appliance di replica per accedere ai cloud [pubblico](migrate-replication-appliance.md#url-access) e per [enti pubblici](migrate-replication-appliance.md#azure-government-url-access).

## <a name="set-up-the-replication-appliance"></a>Configurare l'appliance di replica

La prima fase del processo di migrazione è la configurazione dell'appliance di replica. Per configurare l'appliance per la migrazione delle VM GCP, è necessario scaricare il file del programma di installazione e quindi eseguirlo nella [VM preparata](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Scaricare il programma di installazione dell'appliance di replica

1. Nel progetto di Azure Migrate selezionare **Server**, quindi in **Azure Migrate: Migrazione server** fare clic su **Individua**.

    ![Individuare le VM](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **Area di destinazione** selezionare l'area di Azure in cui eseguire la migrazione delle macchine virtuali.
4. Selezionare **Confermare che l'area di destinazione della migrazione è <nome area>** .
5. Fare clic su **Crea risorse**. Verrà creato un insieme di credenziali di Azure Site Recovery in background.
    - Se è già stata configurata la migrazione con Migrazione server di Azure Migrate, l'opzione della destinazione non può essere configurata, perché le risorse sono state configurate in precedenza.
    - Dopo aver fatto clic su questo pulsante non è più possibile cambiare l'area di destinazione di questo progetto.
    - Per eseguire la migrazione delle VM in un'area diversa, è necessario creare un progetto di Azure Migrate nuovo o diverso.

6. In **Installare una nuova appliance di replica o aumentare le prestazioni della configurazione esistente?** selezionare **Installare un'appliance di replica**.
7. In **Scaricare e installare il software dell'appliance di replica** scaricare il programma di installazione dell'appliance e la chiave di registrazione. La chiave è necessaria per registrare l'appliance. È valida per cinque giorni dal momento in cui viene scaricata.

    ![Scaricare il provider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Copiare il file di installazione e il file della chiave nella VM Windows Server 2016 o Windows Server 2012 GCP creata per l'appliance di replica.
9. Eseguire il file di installazione dell'appliance di replica, come descritto nella procedura seguente.  
    9.1. In **Prima di iniziare** selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione) e quindi selezionare **Avanti**.   
    9.2 In **Third-Party Software License** (Licenza software di terze parti) selezionare **Accetto le condizioni di licenza di terze parti** e quindi selezionare **Avanti**.   
    9.3 In **Registrazione** selezionare **Sfoglia** e passare al percorso in cui si trova il file della chiave di registrazione dell'insieme di credenziali. Selezionare **Avanti**.  
    9.4 In **Impostazioni Internet** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy** e quindi selezionare **Avanti**.  
    9.5 Nella pagina **Controllo dei prerequisiti** vengono eseguiti i controlli di diversi elementi. Al termine, fare clic su **Avanti**.  
    9.6 In **Configurazione di MySQL** specificare una password per il database MySQL e quindi selezionare **Avanti**.  
    9.7 In **Dettagli ambiente** selezionare **No**. Non è necessario proteggere le VM. Quindi selezionare **Avanti**.  
    9.8 In **Percorso di installazione** selezionare **Avanti** per accettare il valore predefinito.  
    9.9 In **Selezione rete** selezionare **Avanti** per accettare il valore predefinito.  
    9.10 In **Riepilogo** selezionare **Installa**.   
    9.11 **Stato dell'installazione** visualizza informazioni sullo stato del processo di installazione. Al termine, selezionare **Fine**. Viene visualizzata una finestra di messaggio per il riavvio. Selezionare **OK**.   
    9.12 Viene poi visualizzata una finestra con un messaggio sulla passphrase di connessione al server di configurazione. Copiare la passphrase negli Appunti e salvarla in un file di testo temporaneo nelle VM di origine. Servirà in un secondo momento, durante il processo di installazione del servizio di mobilità.
10. Al termine dell'installazione, verrà avviata automaticamente la procedura guidata di configurazione dell'appliance (è anche possibile avviare la procedura guidata manualmente usando il collegamento cspsconfigtool creato sul desktop dell'appliance). Usare la scheda Gestisci account della procedura guidata per aggiungere i dettagli dell'account da usare per l'installazione push del servizio di mobilità. In questa esercitazione verrà installato manualmente il servizio di mobilità nelle VM da replicare, quindi occorre creare un account fittizio in questo passaggio e prima di procedere. È possibile specificare i dettagli seguenti per la creazione dell'account fittizio, usando "guest" come nome descrittivo, "username" come nome utente e "password" come password per l'account. Questo account fittizio verrà usato nella fase di abilitazione della replica. 
11. Una volta completata l'installazione e dopo il riavvio dell'appliance, in **Individua macchine virtuali** selezionare la nuova appliance in **Selezionare il server di configurazione** e fare clic su **Finalize registration** (Finalizza registrazione). Con la finalizzazione della registrazione vengono eseguite un paio di attività finali per preparare l'appliance di replica.

    ![Finalizzare la registrazione](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Installare il servizio Mobility

Nelle VM GCP di cui eseguire la migrazione deve essere installato un agente del servizio di mobilità. I programmi di installazione degli agenti sono disponibili nell'appliance di replica. Individuare quello corretto e installare l'agente in ogni computer di cui eseguire la migrazione. Seguire questa procedura:

1. Accedere all'appliance di replica.
2. Passare a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Trovare il programma di installazione per il sistema operativo e la versione delle VM GCP di origine. Verificare i [sistemi operativi supportati](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Copiare il file del programma di installazione nella VM GCP di origine di cui eseguire la migrazione.
5. Assicurarsi di avere il file di testo in cui è stata salvata la passphrase creata durante l'installazione dell'appliance di replica.
    - Se si è dimenticato di salvare la passphrase, è possibile visualizzarla nell'appliance di replica eseguendo questo passaggio. Dalla riga di comando eseguire **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** per visualizzare la passphrase corrente.
    - Copiare ora la passphrase negli Appunti e salvarla in un file di testo temporaneo nelle VM di origine.

### <a name="installation-guide-for-windows-gcp-vms"></a>Guida all'installazione per VM GCP Windows

1. Estrarre il contenuto del file del programma di installazione in una cartella locale (ad esempio C:\Temp) nella VM GCP, come indicato di seguito:

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

### <a name="installation-guide-for-linux-gcp-vms"></a>Guida all'installazione per VM GCP Linux

1. Estrarre il contenuto del tarball del programma di installazione in una cartella locale (ad esempio /tmp/MobSvcInstaller) della VM GCP, come indicato di seguito:
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

## <a name="enable-replication-for-gcp-vms"></a>Abilitare la replica per le VM GCP

> [!NOTE]
> Tramite il portale è possibile aggiungere fino a 10 VM contemporaneamente per la replica. Per replicare più VM contemporaneamente, è possibile aggiungerle in batch di 10.

1. Nel progetto di Azure Migrate selezionare **Server**, **Azure Migrate: Migrazione server**, quindi **Replica**.

    ![Replicare le VM](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. In **Replica** > **Impostazioni origine**  > **I computer sono virtualizzati?** selezionare **Non virtualizzato/Altro**.
3. In **Appliance locale** selezionare il nome dell'appliance di Azure Migrate configurata.
4. In **Server di elaborazione** selezionare il nome dell'appliance di replica. 
5. In **Credenziali guest** selezionare l'account fittizio creato in precedenza durante la [configurazione del programma di installazione della replica](#download-the-replication-appliance-installer) per installare manualmente il servizio di mobilità (l'installazione push non è supportata). Fare quindi clic su **Avanti: Macchine virtuali**.   
 
    ![Impostazioni di replica](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. In **Macchine virtuali**, in **Importare le impostazioni di migrazione da una valutazione?** , lasciare l'impostazione predefinita **No, specificherò le impostazioni di migrazione manualmente**.
7. Selezionare ogni macchina virtuale di cui si vuole eseguire la migrazione. Fare quindi clic su **Avanti: Impostazioni di destinazione**.

    ![Selezionare le VM](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. In **Impostazioni di destinazione** selezionare la sottoscrizione e l'area di destinazione in cui eseguire la migrazione e quindi specificare il gruppo di risorse in cui risiederanno le VM di Azure dopo la migrazione.
9. In **Rete virtuale** selezionare la rete virtuale e la subnet di Azure a cui verranno aggiunte le VM di Azure dopo la migrazione.
10. In **Opzioni di disponibilità** selezionare:
    -  Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Se si seleziona questa opzione, sarà necessario specificare la zona di disponibilità da usare per ogni macchina selezionata nella scheda Calcolo. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità
    -  Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità.
    - L'opzione La ridondanza dell'infrastruttura non è richiesta se non è necessaria una di queste configurazioni di disponibilità per le macchine migrate.
11. In **Vantaggio Azure Hybrid**:
    - Selezionare **No** se non si vuole applicare Vantaggio Azure Hybrid. Quindi fare clic su **Next**.
    - Selezionare **Sì** se si hanno computer Windows Server con copertura Software Assurance o sottoscrizioni di Windows Server attive e si vuole applicare il vantaggio alle VM di cui si sta eseguendo la migrazione. Quindi fare clic su **Next**.

    ![Impostazioni di destinazione](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. In **Calcolo** controllare il nome della macchina virtuale, le dimensioni, il tipo di disco del sistema operativo e la configurazione della disponibilità, se selezionata nel passaggio precedente. Le VM devono essere conformi ai [requisiti di Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Dimensioni macchina virtuale**: se si usano i consigli per la valutazione, l'elenco a discesa Dimensioni macchina virtuale mostra le dimensioni consigliate. In caso contrario, Azure Migrate seleziona le dimensioni più simili nella sottoscrizione di Azure. In alternativa, selezionare manualmente le dimensioni in **Dimensioni macchina virtuale di Azure**.
    - **Disco del sistema operativo**: specificare il disco del sistema operativo (di avvio) per la VM. È il disco che contiene il bootloader e il programma di installazione del sistema operativo.
    - **Zona di disponibilità**: specificare la zona di disponibilità da usare.
    - **Set di disponibilità**: specificare il set di disponibilità da usare.

![Impostazioni calcolo](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. In **Dischi** specificare se i dischi delle VM devono essere replicati in Azure e selezionare il tipo di disco in Azure (dischi gestiti Premium o SSD/HDD Standard). Quindi fare clic su **Next**.
    - È possibile escludere dischi dalla replica.
    - I dischi esclusi non saranno presenti nella VM di Azure dopo la migrazione. 

    ![Impostazioni dei dischi](./media/tutorial-migrate-physical-virtual-machines/disks.png)

14. In **Rivedi e avvia replica** verificare le impostazioni e fare clic su **Replica** per avviare la replica iniziale dei server.

> [!NOTE]
> È possibile aggiornare le impostazioni di replica in qualsiasi momento prima dell'avvio della replica, selezionando **Gestisci** > **Replica delle macchine virtuali**. Le impostazioni non possono essere modificate dopo l'avvio della replica.

## <a name="track-and-monitor-replication-status"></a>Rilevare e monitorare lo stato della replica

- Quando si fa clic su **Replica** viene avviato un processo Avvia replica.
- Al termine del processo, viene avviata la replica iniziale delle VM in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi delle VM GCP vengono replicate periodicamente nei dischi di replica in Azure.

È possibile tenere traccia dello stato del processo nelle notifiche del portale.

È possibile monitorare lo stato della replica facendo clic su **Replica dei server** in **Azure Migrate: Migrazione server**.  

![Monitorare la replica](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Eseguire una migrazione di test

All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. La migrazione di test è altamente consigliata e offre l'opportunità di individuare potenziali problemi e risolverli prima di procedere con la migrazione effettiva. È consigliabile eseguirla almeno una volta per ogni VM prima di eseguirne la migrazione.

- L'esecuzione del test consente di verificare che la migrazione funzioni nel modo previsto senza alcun impatto sulle VM GCP, che rimangono operative, e che la replica continui.
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


## <a name="migrate-gcp-vms"></a>Eseguire la migrazione delle VM GCP

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione delle VM GCP.

1. Nel progetto di Azure Migrate selezionare **Server** > **Azure Migrate: Migrazione server** e fare clic su **Replica dei server**.

    ![Replica dei server](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. In **Replica delle macchine virtuali** fare clic con il pulsante destro del mouse sulla VM e scegliere **Esegui la migrazione**.
3. In **Esegui la migrazione** > **Spegnere le macchine virtuali ed eseguire una migrazione pianificata senza perdita di dati** selezionare **Sì** > **OK**.
    - Se non si vuole arrestare la VM, selezionare **No**.
4. Verrà avviato un processo di migrazione per la VM. È possibile visualizzare lo stato del processo facendo clic sull'icona a forma di campana della notifica nella parte superiore destra della pagina del portale o passando alla pagina Processi dello strumento Migrazione del server (fare clic su Panoramica nel riquadro dello strumento e selezionare Processi dal menu a sinistra).
5. Al termine del processo, è possibile visualizzare e gestire la VM dalla pagina Macchine virtuali.

### <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione fare clic con il pulsante destro del mouse sulla VM e scegliere **Arresta migrazione**. Vengono eseguite le operazioni seguenti:
    - La replica per la VM GCP viene arrestata.
    - La VM GCP viene rimossa dal conteggio indicato in **Server in fase di replica** in Azure Migrate: Server Migration.
    - Esegue la pulizia delle informazioni sullo stato di replica per la macchina virtuale.
2. Installare l'agente [Windows](../virtual-machines/extensions/agent-windows.md) o [Linux](../virtual-machines/extensions/agent-linux.md) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
3. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
4. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
5. Trasferire il traffico all'istanza della VM di Azure di cui è stata eseguita la migrazione.
6. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure. 




## <a name="post-migration-best-practices"></a>Procedure consigliate dopo la migrazione

- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).
    - Distribuire [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
    - È consigliabile distribuire [Gestione costi di Azure](../cost-management-billing/cloudyn/overview.md) per monitorare l'utilizzo delle risorse e le spese.



## <a name="troubleshooting--tips"></a>Risoluzione dei problemi e suggerimenti

**Domanda:** La VM GCP non è presente nell'elenco dei server individuati per la migrazione   
**Risposta:** Verificare se l'appliance di replica soddisfa i requisiti. Assicurarsi che l'agente di mobilità sia installato nella VM di origine di cui eseguire la migrazione e che sia registrato con il server di configurazione. Controllare le regole del firewall per abilitare un percorso di rete tra l'appliance di replica e le VM GCP di origine.  

**Domanda:** Come si stabilisce se la migrazione della VM è riuscita?   
**Risposta:** Al termine della migrazione, è possibile visualizzare e gestire la VM dalla pagina Macchine virtuali. Connettersi alla VM di cui è stata eseguita la migrazione per verificare.  

**Domanda:** Non è possibile importare VM per la migrazione dai risultati della valutazione del server creati in precedenza   
**Risposta:** Attualmente non è supportata l'importazione di valutazioni per questo flusso di lavoro. Come soluzione alternativa, è possibile esportare la valutazione e quindi selezionare manualmente la raccomandazione sulla VM durante il passaggio di abilitazione della replica.
  
**Domanda:** Durante l'individuazione delle VM GCP viene visualizzato un messaggio di errore che informa che non è possibile recuperare il GUID BIOS   
**Risposta:** Usare l'account di accesso radice per l'autenticazione e non quello di uno pseudoutente. Se non è possibile usare un utente radice, assicurarsi che per l'utente siano impostate le funzionalità necessarie, come da istruzioni fornite nella [matrice di supporto](migrate-support-matrix-physical.md#physical-server-requirements). Verificare anche i sistemi operativi supportati per le VM GCP.  

**Domanda:** Lo stato della replica non procede   
**Risposta:** Verificare se l'appliance di replica soddisfa i requisiti. Assicurarsi di aver abilitato le porte richieste sulle porte TCP 9443 e HTTPS 443 dell'appliance di replica per il trasporto dei dati. Verificare che non siano presenti versioni duplicate non aggiornate dell'appliance di replica connesse allo stesso progetto.   

**Domanda:** Non è possibile individuare le istanze di GCP usando Azure Migrate a causa del codice di stato HTTP 504 dal servizio Gestione remota Windows    
**Risposta:** Verificare i requisiti dell'appliance di Azure Migrate e le esigenze di accesso agli URL. Controllare che nessuna impostazione proxy blocchi la registrazione dell'appliance.

**Domanda:** È necessario apportare modifiche prima di eseguire la migrazione delle VM GCP ad Azure?   
**Risposta:** potrebbe essere necessario apportare queste modifiche prima di eseguire la migrazione delle macchine virtuali EC2 in Azure:

- Se si usa cloud-init per il provisioning della macchina virtuale, è possibile disabilitare cloud-init nella macchina virtuale prima di eseguirne la replica in Azure. La procedura di provisioning eseguita da cloud-init nella macchina virtuale potrebbe essere specifica di GCP e non sarà valida dopo la migrazione ad Azure.  
- Esaminare la sezione [Prerequisiti](#prerequisites) per determinare se sono necessarie modifiche per il sistema operativo in uso prima della migrazione ad Azure.
- È sempre consigliabile eseguire una migrazione di test prima della migrazione finale.  

## <a name="next-steps"></a>Passaggi successivi

Esaminare il [percorso di migrazione al cloud](/azure/architecture/cloud-adoption/getting-started/migrate) in Azure Cloud Adoption Framework.