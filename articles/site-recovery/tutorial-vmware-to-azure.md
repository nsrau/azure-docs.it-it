---
title: Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: ee445c8af2fc6620385d9c462d4c6551da3d7367
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali

In questa esercitazione viene illustrato come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali che eseguono Windows. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali di Servizi di ripristino per Site Recovery
> * Configurare gli ambienti di replica di origine e di destinazione
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md)

Prima di iniziare, è utile [esaminare l'architettura](concepts-vmware-to-azure-architecture.md) per uno scenario di ripristino di emergenza.

## <a name="configure-vmware-account-permissions"></a>Configurare le autorizzazioni dell'account VMware

1. Creare un ruolo a livello di vCenter. Assegnare al ruolo il nome **Azure_Site_Recovery**.
2. Assegnare le autorizzazioni seguenti al ruolo **Azure_Site_Recovery**.

   **Task** | **Ruolo/Autorizzazioni** | **Dettagli**
   --- | --- | ---
   **Individuazione di macchine virtuali** | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | Almeno un utente di sola lettura.<br/><br/> L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
   **Replica completa, failover, failback** |  Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

3. Creare un utente nel server vCenter o nell'host vSphere. Assegnare il ruolo all'utente.

## <a name="specify-what-you-want-to-replicate"></a>Specificare gli elementi da replicare

Il servizio Mobility deve essere installato in ogni VM da replicare. Site Recovery installa il servizio automaticamente quando si abilita la replica per la macchina virtuale. Per l'installazione automatica è necessario preparare un account che Site Recovery userà per accedere alla macchina virtuale.

È possibile usare un account di dominio o locale. Per le macchine virtuali Linux l'account deve essere un account radice nel server Linux di origine. Per le macchine virtuali Windows, se non si usa un account di dominio, disabilitare il Controllo dell'accesso utente remoto nel computer locale:

  - Nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** e impostare il valore su 1.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine è necessario scaricare l'installazione unificata di Site Recovery, impostare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le macchine virtuali.

Il server di configurazione è una singola macchina virtuale VMware locale che ospita tutti i componenti di Site Recovery. Questa macchina virtuale esegue il server di configurazione, il server di elaborazione e il server di destinazione master.

- Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali nei server VMware locali.
- Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

La macchina virtuale del server di configurazione deve essere una macchina virtuale VMware a disponibilità elevata che soddisfa i requisiti seguenti:

| **Requisito** | **Dettagli** |
|-----------------|-------------|
| Numero di core CPU| 8 |
| RAM | 12 GB |
| Numero di dischi | 3 - Disco del sistema operativo, disco della cache del server di elaborazione, unità di conservazione (per il failback) |
| Spazio libero su disco (cache del server di elaborazione) | 600 GB |
| Spazio libero su disco (disco di conservazione) | 600 GB |
| Versione del sistema operativo | Windows Server 2012 R2 |
| Impostazioni locali del sistema operativo | Inglese (en-us) |
| Versione di VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Ruoli di Windows Server | Non abilitare questi ruoli: Active Directory Domain Services, Internet Information Services, Hyper-V |
| Tipo di scheda di interfaccia di rete | VMXNET3 |
| Tipo di indirizzo IP | statico |
| Porte | 443 (orchestrazione del canale di controllo)<br/>9443 (trasporto dei dati)|

Nella macchina virtuale del server di configurazione verificare che il clock di sistema sia sincronizzato con un server di riferimento ora.
L'orario deve essere sincronizzato entro i 15 minuti. Se la differenza di orario è maggiore di 15 minuti, il programma di installazione non riesce.

Verificare che la macchina virtuale del server di configurazione sia in grado di accedere a questi URL:

- *.accesscontrol.windows.net. Usato per il controllo di accesso e la gestione delle identità.
- *.backup.windowsazure.com. Usato per il coordinamento e il trasferimento dei dati di replica.
- *.blob.core.windows.net. Usato per l'accesso all'account di archiviazione in cui sono archiviati i dati replicati.
- *.hypervrecoverymanager.windowsazure.com. Usato per il coordinamento e le operazioni di gestione della replica.
- time.nist.gov e time.windows.com. Usati per controllare la sincronizzazione tra ora di sistema e ora globale.

URL per Azure Government Cloud:

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

Tutte le regole del firewall basate sull'indirizzo IP devono consentire la comunicazione con gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e le porte 443 (HTTPS) e 9443 (replica dei dati). Assicurarsi di consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

### <a name="download-the-site-recovery-unified-setup"></a>Scaricare l'Installazione unificata di Site Recovery

1. Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Tutte le risorse**.
2. Fare clic sull'insieme di credenziali di Servizi di ripristino denominato **ContosoVMVault**.
3. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
4. Selezionare **Locale** per indicare dove si trovano i computer, **In Azure** per indicare in quale destinazione si vuole eseguire la replica dei computer e **Sì con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.
5. Nel riquadro Prepara origine fare clic su **+Server di configurazione**.
6. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
7. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
8. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

1. Eseguire il file di installazione per l'Installazione unificata.
2. In **Prima di iniziare** selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione) e quindi fare clic su **Avanti**.

3. In **Third Party Software License** (Licenza software di terze parti) fare clic su **Accetto** per scaricare e installare MySQL e quindi fare clic su **Avanti**.

4. In **Registrazione** selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

5. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet.

   - Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
   - Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
   - Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connect with custom proxy settings** (Connetti con le impostazioni proxy personalizzate) e specificare indirizzo, porta e credenziali.

   ![Firewall](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

   ![Prerequisiti](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. In **MySQL Configuration** (Configurazione MySQL) creare le credenziali per l'accesso all'istanza del server MySQL che viene installata.

8. In **Dettagli ambiente** selezionare **Sì** per proteggere le macchine virtuali VMware. Il programma di installazione verifica che PowerCLI 6.0 sia installato.

9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. L'unità selezionata deve avere almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

10. In **Network Selection** (Selezione rete) specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione deve inviare e ricevere i dati di replica. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Viene aperta anche la porta 443, usata per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

11. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Il programma di installazione installa il server di configurazione e registra il servizio Azure Site Recovery.

    ![Riepilogo](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro. Il server viene visualizzato nel riquadro **Impostazioni** > **Server** nell'insieme di credenziali.

### <a name="configure-automatic-discovery"></a>Configurare l'individuazione automatica

Per individuare le macchine virtuali, il server di configurazione deve connettersi ai server VMware locali. Ai fini di questa esercitazione, aggiungere il server vCenter o gli host vSphere usando un account con privilegi di amministratore nel server.

1. Nel server di configurazione avviare **CSPSConfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella *percorso di installazione*\home\svsystems\bin.

2. Fare clic su **Gestisci account** > **Aggiungi account**.

   ![Aggiungi account](./media/tutorial-vmware-to-azure/credentials1.png)

3. In **Dettagli dell'account** aggiungere l'account che verrà usato per l'individuazione automatica.

   ![Dettagli](./media/tutorial-vmware-to-azure/credentials2.png)

Per aggiungere un server:

1. Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Tutte le risorse**.
2. Fare clic sull'insieme di credenziali di Servizi di ripristino denominato **ContosoVMVault**.
3. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Origine**
4. Selezionare **+ vCenter** per connettersi a un server vCenter o a un host vSphere ESXi.
5. In **Aggiungi vCenter** specificare un nome descrittivo per il server. Quindi specificare l'indirizzo IP o il nome di dominio completo.
6. Mantenere l'impostazione della porta 443, a meno che i server VMware non ascoltino le richieste su una porta diversa.
7. Selezionare l'account da usare per la connessione al server. Fare clic su **OK**.

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

   ![Criteri](./media/tutorial-vmware-to-azure/replication-policy.png)

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
