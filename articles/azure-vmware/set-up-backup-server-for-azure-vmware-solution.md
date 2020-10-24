---
title: Configurare server di Backup di Azure per la soluzione VMware di Azure
description: Configurare l'ambiente della soluzione VMware di Azure per eseguire il backup di macchine virtuali con server di Backup di Azure.
ms.topic: how-to
ms.date: 10/23/2020
ms.openlocfilehash: e71ec19402d22643d51f1435d1abcf56b20a290b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517379"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Configurare server di Backup di Azure per la soluzione VMware di Azure

Server di Backup di Azure contribuisce alla strategia di continuità aziendale e ripristino di emergenza (BCDR). Con la soluzione VMware di Azure è possibile configurare un backup a livello di macchina virtuale (VM) solo usando server di Backup di Azure. 

Server di Backup di Azure possibile archiviare i dati di backup in:

- **Disco**: per l'archiviazione a breve termine, server di backup di Azure esegue il backup dei dati nei pool di dischi.
- **Azure**: per l'archiviazione a breve e a lungo termine in locale, è possibile eseguire il backup dei dati server di backup di Azure archiviati nei pool di dischi nel cloud Microsoft Azure con backup di Azure.

Usare server di Backup di Azure per ripristinare i dati nell'origine o in un percorso alternativo. In questo modo, se i dati originali non sono disponibili a causa di problemi pianificati o imprevisti, è possibile ripristinare i dati in un percorso alternativo.

Questo articolo illustra come preparare l'ambiente della soluzione VMware di Azure per eseguire il backup di macchine virtuali usando server di Backup di Azure. Vengono illustrati i passaggi per: 

> [!div class="checklist"]
> * Determinare le dimensioni e il tipo di disco della macchina virtuale consigliati da usare.
> * Creare un insieme di credenziali dei servizi di ripristino in cui archiviare i punti di ripristino.
> * Impostare la replica di archiviazione per un insieme di credenziali di servizi di ripristino.
> * Aggiungere spazio di archiviazione a server di Backup di Azure.

## <a name="supported-vmware-features"></a>Funzionalità di VMware supportate

- **Backup senza agenti:** Server di Backup di Azure non richiede l'installazione di un agente nel server vCenter o ESXi per eseguire il backup della macchina virtuale. Al contrario, è sufficiente fornire l'indirizzo IP o il nome di dominio completo (FQDN) e le credenziali di accesso usate per autenticare il server VMware con server di Backup di Azure.
- **Backup integrato nel cloud:** Server di Backup di Azure protegge i carichi di lavoro su disco e nel cloud. Il flusso di lavoro di backup e ripristino di server di Backup di Azure consente di gestire la conservazione a lungo termine e il backup fuori sede.
- **Rilevare e proteggere le macchine virtuali gestite da vCenter:** Server di Backup di Azure rileva e protegge le macchine virtuali distribuite in un server vCenter o ESXi. Server di Backup di Azure rileva anche le macchine virtuali gestite da vCenter, in modo che sia possibile proteggere le distribuzioni di grandi dimensioni.
- **Protezione automatica a livello di cartella:** vCenter consente di organizzare le macchine virtuali in cartelle VM. Server di Backup di Azure rileva queste cartelle. È possibile usarlo per proteggere le macchine virtuali a livello di cartella, incluse tutte le sottocartelle. Quando si proteggono le cartelle, server di Backup di Azure protegge le macchine virtuali in tale cartella e protegge le VM aggiunte in un secondo momento. Server di Backup di Azure rileva le nuove macchine virtuali quotidianamente e le protegge automaticamente. Quando si organizzano le macchine virtuali in cartelle ricorsive, server di Backup di Azure rileva e protegge automaticamente le nuove macchine virtuali distribuite nelle cartelle ricorsive.
- **Server di backup di Azure continua a proteggere le macchine virtuali vMotion all'interno del cluster:** Poiché le macchine virtuali sono vMotion per il bilanciamento del carico all'interno del cluster, server di Backup di Azure rileva automaticamente e continua la protezione delle macchine virtuali.
- **Ripristinare i file necessari più velocemente:** Server di Backup di Azure possibile ripristinare i file o le cartelle da una VM Windows senza ripristinare l'intera macchina virtuale.

## <a name="limitations"></a>Limitazioni

- È necessario installare l'aggiornamento cumulativo 1 per server di Backup di Azure V3.
- Non è possibile eseguire il backup degli snapshot utente prima del primo backup del server di Backup di Azure. Al termine del primo backup server di Backup di Azure possibile eseguire il backup degli snapshot utente.
- Server di Backup di Azure non è in grado di proteggere le macchine virtuali VMware con dischi pass-through e mapping di dispositivi non elaborati fisici (pRDMs).
- Server di Backup di Azure non è in grado di rilevare o proteggere vApp VMware.

Per configurare server di Backup di Azure per la soluzione VMware di Azure, è necessario completare i passaggi seguenti:

- Configurare i prerequisiti e l'ambiente.
- Creare un insieme di credenziali dei servizi di ripristino.
- Scaricare e installare server di Backup di Azure.
- Aggiungere spazio di archiviazione a server di Backup di Azure.

### <a name="deployment-architecture"></a>Architettura di distribuzione

Server di Backup di Azure viene distribuito come macchina virtuale IaaS (Infrastructure as a Service) di Azure per proteggere le macchine virtuali della soluzione VMware di Azure.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Server di Backup di Azure viene distribuito come macchina virtuale IaaS (Infrastructure as a Service) di Azure per proteggere le macchine virtuali della soluzione VMware di Azure." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Prerequisiti per l'ambiente del server di Backup di Azure

Considerare i consigli in questa sezione quando si installa server di Backup di Azure nell'ambiente Azure.

### <a name="azure-virtual-network"></a>Rete virtuale di Azure

Assicurarsi di [configurare la rete per il cloud privato VMware in Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>Determinare le dimensioni della macchina virtuale

Seguire le istruzioni riportate nell'esercitazione [creare la prima VM Windows nell'portale di Azure](../virtual-machines/windows/quick-create-portal.md) .  La VM verrà creata nella rete virtuale creata nel passaggio precedente. Iniziare con un'immagine della raccolta di Windows Server 2019 datacenter per eseguire il server di Backup di Azure. 

La tabella riepiloga il numero massimo di carichi di lavoro protetti per ogni server di Backup di Azure dimensioni della macchina virtuale. Le informazioni si basano su test di prestazioni e scalabilità interni con valori canonici per dimensioni e varianza dei carichi di lavoro. Le dimensioni effettive del carico di lavoro possono essere maggiori, ma devono essere gestite dai dischi collegati alla macchina virtuale server di Backup di Azure.

| Numero massimo di carichi di lavoro protetti | Dimensioni medie del carico di lavoro | Varianza media del carico di lavoro (giornaliera) | IOPS di archiviazione minima | Dimensioni/tipo di disco consigliati      | Dimensione VM consigliata |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Varianza del 5% netto                   | 2.000             | HDD Standard (dimensione 8 TB o superiore per disco)  | A4V2       |
| 40                      | 150 GB                | Varianza del 10% netto                  | 4\.500             | SSD Premium * (dimensione 1 TB o superiore per disco) | DS3_V2     |
| 60                      | 200 GB                | Varianza del 10% netto                  | 10.500            | SSD Premium * (dimensioni di 8 TB o superiori per ogni disco) | DS3_V2     |

* Per ottenere le operazioni di i/o al secondo necessarie, usare dischi minimi consigliati o di dimensioni maggiori. I dischi di dimensioni inferiori offrono IOPs inferiori.

> [!NOTE]
> Il server di Backup di Azure è progettato per essere eseguito su un server dedicato, con un unico scopo. Non è possibile installare server di Backup di Azure in un computer che:
> * Viene eseguito come controller di dominio.
> * È installato il ruolo server applicazioni.
> * È un server di gestione System Center Operations Manager.
> * Esegue Exchange Server.
> * È un nodo di un cluster.

### <a name="disks-and-storage"></a>Dischi e archiviazione

Server di Backup di Azure richiede dischi per l'installazione. 

| Requisito                      | Dimensioni consigliate  |
|----------------------------------|-------------------------|
| Installazione server di Backup di Azure                | Percorso di installazione: 3 GB<br />Unità per i file del database: 900 MB<br />Unità di sistema: 1 GB per l'installazione di SQL Server<br /><br />Sarà inoltre necessario spazio per server di Backup di Azure copiare il catalogo dei file in un percorso di installazione temporaneo quando si archivia.      |
| Disco per il pool di archiviazione<br />(USA volumi di base, non può trovarsi su un disco dinamico) | Da due a tre volte la dimensione dei dati protetti.<br />Per informazioni dettagliate sul calcolo dell'archiviazione, vedere [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

Per informazioni su come alleghi un nuovo disco dati gestito a una macchina virtuale di Azure esistente, vedere l'articolo relativo alla [connessione di un disco dati gestito a una macchina virtuale Windows tramite il portale di Azure](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Un singolo server di Backup di Azure ha un limite flessibile di 120 TB per il pool di archiviazione.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Archiviare i dati di backup in un disco locale e in Azure

L'archiviazione dei dati di backup in Azure riduce l'infrastruttura di backup nella macchina virtuale server di Backup di Azure. Per il ripristino operativo (backup), server di Backup di Azure archivia i dati di backup nei dischi di Azure collegati alla macchina virtuale. Quando i dischi e lo spazio di archiviazione sono collegati alla macchina virtuale, server di Backup di Azure gestisce automaticamente l'archiviazione. La quantità di spazio di archiviazione dipende dal numero e dalle dimensioni dei dischi collegati a ogni macchina virtuale di Azure. Ogni dimensione della macchina virtuale di Azure ha un numero massimo di dischi che possono essere collegati. Ad esempio, a2 è quattro dischi, a3 è otto dischi e a4 è 16 dischi. Anche in questo caso, le dimensioni e il numero di dischi determinano la capacità totale del pool di archiviazione di backup.

> [!IMPORTANT]
> *Non* è consigliabile mantenere i dati di ripristino operativo nei dischi collegati a server di backup di Azure per più di cinque giorni. Se i dati sono più vecchi di cinque giorni, archiviarli in un insieme di credenziali di servizi di ripristino.

Per archiviare i dati di backup in Azure, creare o usare un insieme di credenziali di Servizi di ripristino. Quando si prepara il backup del carico di lavoro server di Backup di Azure, si configura l'insieme di credenziali di [servizi di ripristino](#create-a-recovery-services-vault). Una volta configurata, ogni volta che viene eseguito un processo di backup online, viene creato un punto di ripristino nell'insieme di credenziali. Ogni insieme di credenziali di servizi di ripristino include fino a 9.999 punti di ripristino. A seconda del numero di punti di ripristino creati e del tempo mantenuto, è possibile mantenere i dati di backup per molti anni. Ad esempio, è possibile creare punti di ripristino mensili e mantenerli per cinque anni.

> [!IMPORTANT]
> Indipendentemente dal fatto che i dati di backup vengano inviati ad Azure o conservati localmente, è necessario registrare server di Backup di Azure con un insieme di credenziali di servizi di ripristino.

### <a name="scale-deployment"></a>Ridimensionare la distribuzione

Se si vuole ridimensionare la distribuzione, sono disponibili le opzioni seguenti:

- **Scalabilità verticale**: aumentare le dimensioni della macchina virtuale server di backup di Azure da una serie a una serie DS3 e aumentare la risorsa di archiviazione locale.
- **Offload dei dati**: inviare i dati meno recenti ad Azure e conservare solo i dati più recenti nello spazio di archiviazione collegato al computer server di backup di Azure.
- **Scalabilità orizzontale**: aggiungere altri computer server di backup di Azure per proteggere i carichi di lavoro.

### <a name="net-framework"></a>.NET Framework

Per la macchina virtuale deve essere installato .NET Framework 3,5 SP1 o versione successiva.

### <a name="join-a-domain"></a>Aggiunta a un dominio

Il server di Backup di Azure macchina virtuale deve essere aggiunto a un dominio. Un utente di dominio con privilegi di amministratore per la macchina virtuale deve installare server di Backup di Azure.

Server di Backup di Azure distribuite in una macchina virtuale di Azure può eseguire il backup dei carichi di lavoro nelle macchine virtuali della soluzione VMware di Azure. I carichi di lavoro devono trovarsi nello stesso dominio per abilitare l'operazione di backup.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino è un'entità di archiviazione che archivia i punti di ripristino creati nel corso del tempo. Contiene inoltre criteri di backup associati a elementi protetti.

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

1. Nel menu a sinistra selezionare **Tutti i servizi**.

   ![Nel menu a sinistra selezionare tutti i servizi.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Nella finestra di dialogo **tutti i servizi** immettere **servizi di ripristino** e selezionare insiemi di credenziali **dei servizi di ripristino** dall'elenco.

   ![Immettere e scegliere insiemi di credenziali dei servizi di ripristino.](../backup/media/backup-create-rs-vault/all-services.png)

   Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

1. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Aggiungi**.

   ![Aggiungere un insieme di credenziali di servizi di ripristino.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Si apre la finestra di dialogo **Insieme di credenziali dei Servizi di ripristino**.

1. Immettere i valori per **nome**, **sottoscrizione**, **gruppo di risorse**e **località**.

   ![Configurare l'insieme di credenziali di servizi di ripristino.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco nella sottoscrizione di Azure. Specificare un nome con almeno due caratteri, ma non più di 50 caratteri. Il nome deve iniziare con una lettera e deve contenere solo lettere, numeri e trattini.
   - **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
   - **Gruppo di risorse**: Usare un gruppo di risorse esistente oppure crearne uno nuovo. Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome.
   - **Località**: selezionare l'area geografica per l'insieme di credenziali. Per creare un insieme di credenziali per proteggere le macchine virtuali della soluzione VMware di Azure, l'insieme di credenziali *deve* trovarsi nella stessa area del cloud privato della soluzione VMware di Azure.

1. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, selezionare **Crea**.

   ![Creare l'insieme di credenziali di servizi di ripristino.](../backup/media/backup-create-rs-vault/click-create-button.png)

   La creazione dell'insieme di credenziali di Servizi di ripristino può richiedere del tempo. Monitorare le notifiche di stato nell'area **notifiche** nell'angolo superiore destro del portale. Dopo aver creato l'insieme di credenziali, l'insieme di credenziali è visibile nell'elenco degli insiemi di credenziali dei servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

   ![Aggiornare l'elenco degli insiemi di credenziali di backup.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Impostare la replica di archiviazione

L'opzione di replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica (impostazione predefinita) e l'archiviazione con ridondanza locale. L'archiviazione con ridondanza geografica copia i dati nell'account di archiviazione in un'area secondaria, rendendo durevoli i dati. L'archiviazione con ridondanza locale è un'opzione più economica che non è durevole. Per altre informazioni sulle opzioni di archiviazione con ridondanza geografica e con ridondanza locale, vedere [ridondanza di archiviazione di Azure](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> La modifica dell'impostazione del **tipo di replica di archiviazione localmente ridondante/con ridondanza geografica** per un insieme di credenziali di servizi di ripristino deve essere eseguita prima di configurare i backup nell'insieme di credenziali. Dopo aver configurato i backup, l'opzione per modificarla è disabilitata e non è possibile modificare il tipo di replica di archiviazione.

1. Da insiemi di credenziali **dei servizi di ripristino**selezionare il nuovo insieme di credenziali. 

1. In **Impostazioni** selezionare **Proprietà**. In **configurazione backup**selezionare **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

## <a name="download-and-install-the-software-package"></a>Scaricare e installare il pacchetto software

Attenersi alla procedura descritta in questa sezione per scaricare, estrarre e installare il pacchetto software.

### <a name="download-the-software-package"></a>Scaricare il pacchetto software

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Se è già aperto un insieme di credenziali dei servizi di ripristino, continuare con il passaggio successivo. Se non si dispone di un insieme di credenziali di servizi di ripristino aperto e ci si trova nella portale di Azure, scegliere **Sfoglia**dal menu principale.

   1. Nell'elenco delle risorse immettere **servizi di ripristino**.

   1. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

   ![Creare l'insieme di credenziali di servizi di ripristino passaggio 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

   Viene aperto il dashboard dell'insieme di credenziali selezionato.

   ![Viene aperto il dashboard dell'insieme di credenziali selezionato.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Per impostazione predefinita, viene visualizzata l'opzione **Impostazioni** . Se chiuso, selezionare **Impostazioni** per aprirlo.

   ![Per impostazione predefinita, viene visualizzata l'opzione impostazioni. Se chiuso, selezionare impostazioni per aprirlo.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Selezionare **backup** per aprire la procedura guidata **Introduzione** .

   ![Selezionare Backup per aprire la procedura guidata Introduzione.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Nella finestra che viene visualizzata:

   1. Dal menu **dove è in esecuzione il carico di lavoro?** selezionare **locale**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Server di Backup di Azure viene distribuito come macchina virtuale IaaS (Infrastructure as a Service) di Azure per proteggere le macchine virtuali della soluzione VMware di Azure.":::

   1. Selezionare i carichi di lavoro da proteggere utilizzando server di Backup di Azure dal menu di cui si desidera eseguire il **backup?** .

   1. Selezionare **preparare l'infrastruttura** per scaricare e installare server di backup di Azure e le credenziali dell'insieme di credenziali.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Server di Backup di Azure viene distribuito come macchina virtuale IaaS (Infrastructure as a Service) di Azure per proteggere le macchine virtuali della soluzione VMware di Azure.":::

1. Nella finestra **prepara infrastruttura** visualizzata:

   1. Selezionare il collegamento **download** per installare server di backup di Azure.

   1. 1. Selezionare **già scaricato o usare l'installazione server di backup di Azure più recente** e quindi **scaricare** per scaricare le credenziali dell'insieme di credenziali. Queste credenziali verranno usate quando si registra il server di Backup di Azure nell'insieme di credenziali di servizi di ripristino. I collegamenti consentono di passare all'area download, in cui è possibile scaricare il pacchetto software.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Server di Backup di Azure viene distribuito come macchina virtuale IaaS (Infrastructure as a Service) di Azure per proteggere le macchine virtuali della soluzione VMware di Azure.":::

1. Nella pagina di download selezionare tutti i file e fare clic su **Avanti**.

   > [!NOTE]
   > È necessario scaricare tutti i file nella stessa cartella. Poiché le dimensioni del download dei file insieme sono maggiori di 3 GB, per il completamento del download potrebbero essere necessari fino a 60 minuti. 

   ![Nella pagina di download selezionare tutti i file e fare clic su Avanti.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Estrarre il pacchetto software

Se il pacchetto software è stato scaricato in un altro server, copiare i file nella macchina virtuale creata per la distribuzione di server di Backup di Azure.

> [!WARNING]
> Per estrarre i file di installazione sono necessari almeno 4 GB di spazio disponibile.

1. Dopo aver scaricato tutti i file, fare doppio clic su **MicrosoftAzureBackupInstaller.exe** per aprire l'installazione guidata di **backup di Microsoft Azure** , quindi selezionare **Avanti**.

1. Selezionare il percorso in cui estrarre i file e selezionare **Avanti**.

1. Selezionare **Estrai** per avviare il processo di estrazione.

   ![Selezionare Estrai per avviare il processo di estrazione.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Una volta estratto, selezionare l'opzione per **eseguire setup.exe** e quindi fare clic su **fine**.

> [!TIP]
> È anche possibile individuare il file di setup.exe dalla cartella in cui è stato estratto il pacchetto software.

### <a name="install-the-software-package"></a>Installare il pacchetto software

1. Nella finestra di installazione in **Installa**selezionare **backup di Microsoft Azure** per aprire l'installazione guidata.

   ![Nella finestra di installazione in Installa selezionare Backup di Microsoft Azure per aprire l'installazione guidata.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Nella schermata **iniziale** selezionare **Avanti** per passare alla pagina controlli dei **prerequisiti** .

1. Selezionare di **nuovo verifica** per determinare se l'hardware e il software soddisfano i prerequisiti per server di backup di Azure. Se l'operazione è stata completata, fare clic su **Avanti**.

   ![ Selezionare di nuovo verifica per determinare se l'hardware e il software soddisfano i prerequisiti per server di Backup di Azure. Se l'operazione è stata completata, fare clic su Avanti.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Il pacchetto di installazione di server di Backup di Azure viene fornito con i file binari SQL Server appropriati necessari. Quando si avvia una nuova installazione di server di Backup di Azure, selezionare l'opzione **installa nuova istanza di SQL Server con questa impostazione** . Selezionare quindi **Controlla e installa**.

   ![Il pacchetto di installazione di server di Backup di Azure viene fornito con i file binari SQL Server appropriati necessari.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Se si vuole usare un'istanza di SQL Server personalizzata, le versioni SQL Server supportate sono SQL Server 2014 SP1 o versione successiva, 2016 e 2017. Tutte le versioni di SQL Server devono essere Standard o Enterprise a 64 bit. L'istanza utilizzata da server di Backup di Azure deve essere solo locale; non può essere remoto. Se si utilizza un'istanza di SQL Server esistente per server di Backup di Azure, il programma di installazione di supporta solo l'utilizzo di *istanze denominate* di SQL Server.

   Se si verifica un errore con una raccomandazione per riavviare il computer, eseguire questa operazione e selezionare di **nuovo verifica**. Per eventuali problemi di configurazione di SQL Server, riconfigurare SQL Server in base alle linee guida SQL Server. Quindi riprovare a installare o aggiornare server di Backup di Azure usando l'istanza esistente di SQL Server.

   **Configurazione manuale**

   Quando si usa un'istanza di SQL Server personalizzata, assicurarsi di aggiungere builtin\Administrators al ruolo sysadmin al ruolo sysadmin del database master.

   **Configurare Reporting Services con SQL Server 2017**

   Se si usa l'istanza di SQL Server 2017, è necessario configurare manualmente SQL Server 2017 Reporting Services (SSRS). Dopo la configurazione di SSRS, assicurarsi di impostare la proprietà di **inizializzazione** di SSRS su **true**. Se impostato su **true**, server di backup di Azure presuppone che SSRS sia già configurato e ignori la configurazione di SSRS.

   Per controllare lo stato di configurazione di SSRS, eseguire:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Per la configurazione di SSRS, usare i valori seguenti:

   * **Account del servizio**: **usare l'account predefinito** deve essere **servizio di rete**.
   * **URL servizio Web**: la **directory virtuale** deve **essere \<SQLInstanceName> ReportServer_**.
   * **Database**: **DatabaseName** deve essere **ReportServer $ \<SQLInstanceName> **.
   * **URL del portale Web**: la **directory virtuale** deve essere **reports_ \<SQLInstanceName> **.

   [Altre informazioni](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) sulla configurazione di SSRS.

   > [!NOTE]
   > Le [condizioni di Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) regolano la gestione delle licenze per SQL Server usato come database per server di backup di Azure. In base a OST, usare solo SQL Server in bundle con server di Backup di Azure come database per server di Backup di Azure.

1. Una volta completata l'installazione, fare clic su **Avanti**.

1. Specificare un percorso per l'installazione dei file di Backup di Microsoft Azure server e selezionare **Avanti**.

   > [!NOTE]
   > Il percorso dei file temporanei è obbligatorio per il backup in Azure. Verificare che il percorso dei file temporanei sia pari almeno al 5% dei dati pianificati per il backup nel cloud. Per la protezione del disco, i dischi separati devono essere configurati al termine dell'installazione. Per altre informazioni sui pool di archiviazione, vedere [configurare i pool di archiviazione e l'archiviazione su disco](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Specificare un percorso per l'installazione dei file di Backup di Microsoft Azure server e selezionare Avanti.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Specificare una password complessa per gli account utente locali con restrizioni e selezionare **Avanti**.

   ![Specificare una password complessa per gli account utente locali con restrizioni e selezionare Avanti.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selezionare se si desidera utilizzare Microsoft Update per verificare la disponibilità di aggiornamenti e selezionare **Avanti**.

   > [!NOTE]
   > Si consiglia di Windows Update reindirizzare a Microsoft Update, che offre sicurezza e aggiornamenti importanti per Windows e altri prodotti come server di Backup di Azure.

   ![Selezionare se si desidera utilizzare Microsoft Update per verificare la disponibilità di aggiornamenti e selezionare Avanti.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Esaminare il **Riepilogo delle impostazioni**e selezionare **Installa**.

   L'installazione avviene a fasi. 
   - La prima fase consente di installare l'agente di Servizi di ripristino di Microsoft Azure.
   - La seconda fase verifica la connettività Internet. Se disponibile, è possibile continuare l'installazione. Se non è disponibile, è necessario specificare i dettagli del proxy per la connessione a Internet. 
   - La fase finale controlla i prerequisiti software. Se non è installato, il software mancante verrà installato insieme all'agente Servizi di ripristino di Microsoft Azure.

1. Selezionare **Sfoglia** per individuare le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali di servizi di ripristino e quindi fare clic su **Avanti**.

1. Selezionare una passphrase per crittografare o decrittografare i dati inviati tra Azure e la propria sede.

   > [!TIP]
   > È possibile generare automaticamente una passphrase o fornire una passphrase di 16 caratteri minima.

1. Immettere il percorso in cui salvare la passphrase, quindi selezionare **Avanti** per registrare il server.

   > [!IMPORTANT]
   > Salvare la passphrase in una posizione sicura diversa da quella del server locale. È consigliabile usare la Azure Key Vault per archiviare la passphrase.

   Al termine dell'installazione dell'agente di Servizi di ripristino di Microsoft Azure, il passaggio di installazione passa all'installazione e alla configurazione dei componenti di SQL Server e server di Backup di Azure.

   ![Al termine dell'installazione dell'agente di Servizi di ripristino di Microsoft Azure, il passaggio di installazione passa all'installazione e alla configurazione dei componenti di SQL Server e server di Backup di Azure.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Al termine del passaggio di installazione, selezionare **Chiudi**.

### <a name="install-update-rollup-1"></a>Installare l'aggiornamento cumulativo 1

L'installazione dell'aggiornamento cumulativo 1 per server di Backup di Azure V3 è obbligatoria prima che sia possibile proteggere i carichi di lavoro.  Le correzioni di bug e le istruzioni di installazione sono disponibili nell' [articolo della Knowledge base](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Aggiungere risorse di archiviazione al server di Backup di Azure

Server di Backup di Azure V3 supporta Modern Backup Storage che offre:

-  Risparmio di spazio di archiviazione del 50%.
-  Backup tre volte più veloci.
-  Archiviazione più efficiente.
-  Archiviazione con supporto del carico di lavoro.

### <a name="volumes-in-azure-backup-server"></a>Volumi in server di Backup di Azure

Se non è già stato aggiunto, aggiungere i dischi dati con la capacità di archiviazione necessaria della macchina virtuale server di Backup di Azure.

Server di Backup di Azure V3 accetta solo i volumi di archiviazione. Quando si aggiunge un volume, server di Backup di Azure formatta il volume in Resilient file System (ReFS), che Modern Backup Storage richiede.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Aggiungere volumi a server di Backup di Azure spazio di archiviazione su disco

1. Nel riquadro di **gestione** , ripetere l'analisi dell'archiviazione e quindi selezionare **Aggiungi**. 

1. Selezionare i volumi disponibili da aggiungere al pool di archiviazione. 

1. Dopo aver aggiunto i volumi disponibili, assegnare loro un nome descrittivo per facilitarne la gestione. 

1. Selezionare **OK** per formattare questi volumi in refs in modo che server di backup di Azure possa usare Modern backup storage vantaggi.


## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come configurare un backup di macchine virtuali VMware in esecuzione nella soluzione VMware di Azure usando server di Backup di Azure.

> [!div class="nextstepaction"]
> [Configurare il backup di macchine virtuali della soluzione VMware di Azure](backup-azure-vmware-solution-virtual-machines.md)
