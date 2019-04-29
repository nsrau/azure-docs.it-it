---
title: Installare il server di Backup di Azure in Azure Stack | Microsoft Docs
description: Usare il server di Backup di Azure per proteggere o eseguire il backup dei carichi di lavoro in Azure Stack.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d3a2ffdedda7f541fb1a3f37a8b40bc7af3dcb57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852140"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installare il server di Backup di Azure in Azure Stack

In questo articolo viene descritto come installare il server di Backup di Azure in Azure Stack. Con il server di Backup di Azure è possibile proteggere i carichi di lavoro IaaS (infrastruttura distribuita come servizio), ad esempio le macchine virtuali in esecuzione in Azure Stack. Un vantaggio dell'uso del server di Backup di Azure per proteggere i carichi di lavoro è che è possibile gestire tutta la protezione dei carichi di lavoro da una singola console.

> [!NOTE]
> Per informazioni sulle funzionalità di sicurezza, vedere la [documentazione sulle funzionalità di sicurezza di Backup di Azure](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matrice di protezione del server di Backup di Azure
Il server di Backup di Azure consente di proteggere i carichi di lavoro della macchina virtuale di Azure Stack seguenti.

| Origine dati protetta | Protezione e ripristino |
| --------------------- | ----------------------- |
| Windows Server Canale semestrale - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| SQL Server 2016 | Database |
| SQL Server 2014 | Database |
| SQL Server 2012 SP1 | Database |
| SharePoint 2016 | Farm, database, front-end, server Web |
| SharePoint 2013 | Farm, database, front-end, server Web |
| SharePoint 2010 | Farm, database, front-end, server Web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Prerequisiti per l'ambiente del server di Backup di Azure

Quando si installa il server di Backup di Azure nell'ambiente Azure Stack, tenere presenti gli elementi consigliati in questa sezione. Il programma di installazione del server di Backup di Azure verifica che l'ambiente soddisfi i prerequisiti necessari, ma preparare l'ambiente prima di eseguire l'installazione consente di risparmiare tempo.

### <a name="determining-size-of-virtual-machine"></a>Definizione delle dimensioni della macchina virtuale
Per eseguire il server di Backup di Azure in una macchina virtuale di Azure Stack, usare la dimensione A2 o superiore. Per ricevere assistenza nella scelta di una dimensione di macchina virtuale, scaricare il [calcolatore di dimensioni della VM di Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Reti virtuali nelle macchine virtuali di Azure Stack
Tutte le macchine virtuali usate in un carico di lavoro di Azure Stack devono appartenere alla stessa rete virtuale e sottoscrizione di Azure.

### <a name="azure-backup-server-vm-performance"></a>Prestazioni della VM del server di Backup di Azure
Se condivisi con altre macchine virtuali, le dimensioni dell'account di archiviazione e i limiti delle operazioni di I/O al secondo influiscono sulle prestazioni della VM del server di Backup di Azure. Per questo motivo, è necessario usare un account di archiviazione separato per la macchina virtuale del server di Backup di Azure. L'agente di Backup di Azure in esecuzione nel server di Backup di Azure necessita di un'archiviazione temporanea per:
- Uso proprio (percorso cache)
- Dati ripristinati dal cloud (area di gestione temporanea)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurazione dell'archiviazione su disco temporaneo di Backup di Azure
Ogni macchina virtuale di Azure Stack dispone di uno spazio di archiviazione su disco temporaneo, disponibile per l'utente come volume `D:\`. L'area di gestione temporanea locale necessaria per Backup di Azure può essere configurata per risiedere in `D:\` e il percorso della cache può essere inserito in `C:\`. In questo modo, non è necessario che lo spazio di archiviazione venga suddiviso nei dischi dati collegati alla macchina virtuale del server di Backup di Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Archiviazione dei dati di backup in un disco locale e in Azure
Il server di Backup di Azure archivia i dati di backup sui dischi di Azure collegati alla macchina virtuale per il ripristino operativo. Dopo aver collegato i dischi e lo spazio di archiviazione alla macchina virtuale, il server di Backup di Azure gestisce l'archiviazione per conto dell'utente. La quantità di spazio di archiviazione dei dati di backup dipende dal numero e dalla dimensione dei dischi collegati a ogni [macchina virtuale di Azure Stack](/azure-stack/user/azure-stack-storage-overview). Ogni dimensione di VM di Azure Stack ha un numero massimo di dischi che può essere collegato alla macchina virtuale. Ad esempio, per A2 il numero massimo è quattro dischi. Per A3 è otto dischi. Per A4 è 16 dischi. Anche in questo caso, le dimensioni e il numero di dischi determina il pool di archiviazione di backup totale.

> [!IMPORTANT]
> **Non** è necessario conservare i dati di ripristino operativo (backup) nei dischi collegati al server di Backup di Azure per più di cinque giorni.
>

L'archiviazione dei dati di backup in Azure riduce l'infrastruttura di backup in Azure Stack. Se i dati sono meno recenti di cinque giorni, devono essere archiviati in Azure.

Per archiviare i dati di backup in Azure, creare o usare un insieme di credenziali di Servizi di ripristino. Nella fase di preparazione all'esecuzione del backup del carico di lavoro del server di Backup di Azure [configurare l'insieme di credenziali di Servizi di ripristino](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Al termine della configurazione, ogni volta che viene eseguito un processo di backup, nell'insieme di credenziali viene creato un punto di ripristino. Ogni insieme di credenziali di Servizi di ripristino contiene fino a 9999 punti di ripristino. A seconda del numero di punti di ripristino creati e del periodo di conservazione, è possibile conservare i dati di backup per molti anni. È ad esempio possibile creare punti di ripristino mensili e conservarli per cinque anni.
 
### <a name="scaling-deployment"></a>Ridimensionamento della distribuzione
Se si vuole ridimensionare la distribuzione, sono disponibili le opzioni seguenti:
  - Aumentare le prestazioni: aumento delle dimensioni della macchina virtuale del server di Backup di Azure dalla serie A alla serie D e aumento dello spazio di archiviazione locale [in base alle istruzioni della macchina virtuale di Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
  - Offload dei dati: invio dei dati meno recenti ad Azure e conservazione solo dei dati più recenti nello spazio di archiviazione collegato al server di Backup di Azure.
  - Aumentare le istanze: aggiunta di altri server di Backup di Azure per proteggere i carichi di lavoro.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 SP1 o versione successiva deve essere installato nella macchina virtuale.

### <a name="joining-a-domain"></a>Aggiunta a un dominio

La macchina virtuale del server di Backup di Azure deve appartenere a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Azure nella macchina virtuale.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Uso di una VM IaaS in Azure Stack

Quando si sceglie un server per il server di Backup di Azure, usare come punto di partenza un'immagine della raccolta di Windows Server 2012 R2 Datacenter o Windows Server 2016 Datacenter. L'articolo [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) offre un'esercitazione che consente di acquisire familiarità con la macchina virtuale consigliata. I requisiti minimi consigliati per la macchina virtuale server sono A2 Standard con due core e 3,5 GB di RAM.

La protezione dei carichi di lavoro con il server di Backup di Azure è piuttosto complessa. L'articolo [Installare DPM come una macchina virtuale di Azure](https://technet.microsoft.com/library/jj852163.aspx)ne illustra i diversi aspetti. Prima di distribuire la macchina leggere interamente questo articolo.

> [!NOTE]
> Il server di Backup di Azure è progettato per essere eseguito su una macchina virtuale dedicata, con un unico scopo. Non è possibile installare il server di Backup di Azure su:
> - Un computer in esecuzione come controller di dominio
> - Un computer in cui è installato il ruolo di server applicazioni
> - Un computer su cui è in esecuzione Exchange Server
> - Un computer che sia un nodo di un cluster

Aggiungere sempre il server di backup di Azure a un dominio. Se è necessario spostare il server di Backup di Azure in un altro dominio, per prima cosa installare il server di Backup di Azure, quindi aggiungerlo al nuovo dominio. Dopo aver distribuito il server di Backup di Azure, non è possibile spostarlo in un nuovo dominio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Impostare la replica di archiviazione

La replica di archiviazione dell'insieme di credenziali di Servizi di ripristino consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino usano l'archiviazione con ridondanza geografica. Se questo insieme di credenziali è quello primario, lasciare l'opzione di archiviazione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma meno durevole, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/common/storage-redundancy-grs.md) e con [ridondanza locale](../storage/common/storage-redundancy-lrs.md), vedere la panoramica [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Selezionare l'insieme di credenziali per aprire il dashboard dell'insieme di credenziali e il menu Impostazioni. Se il menu **Impostazioni** non si apre, fare clic su **Tutte le impostazioni** nel dashboard dell'insieme di credenziali.
2. Nel menu **Impostazioni** fare clic su **Infrastruttura di backup** > **Configurazione di backup** per aprire il menu **Configurazione di backup**. Nel menu **Configurazione di backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Scaricare il programma di installazione del server di Backup di Azure

Esistono due modi per scaricare il programma di installazione del server di Backup di Azure. Il programma di installazione del server di Backup di Azure può essere scaricato dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55269). È inoltre possibile scaricare il programma di installazione del server di Backup di Azure durante la configurazione di un insieme di credenziali di Servizi di ripristino. I passaggi seguenti consentono di eseguire il download del programma di installazione dal portale di Azure durante la configurazione di un insieme di credenziali di Servizi di ripristino.

1. Dalla macchina virtuale di Azure Stack [accedere alla propria sottoscrizione di Azure nel portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Scegliere l'opzione Tutti i servizi nel menu principale](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Nella finestra di dialogo **Tutti i servizi** digitare *Servizi di ripristino*. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Quando viene visualizzata, selezionare l'opzione **Insiemi di credenziali di Servizi di ripristino**.

    ![Digitare Servizi di ripristino nella finestra di dialogo Tutti i servizi](./media/backup-mabs-install-azure-stack/all-services.png)

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

4. Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare l'insieme di credenziali per aprire il relativo dashboard.

    ![Digitare Servizi di ripristino nella finestra di dialogo Tutti i servizi](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Fare clic su **Backup** nel menu Attività iniziali dell'insieme di credenziali per aprire la relativa procedura guidata.

    ![Attività iniziali di backup](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Viene visualizzato il menu di backup.

    ![Backup-obiettivi-predefinito-aperto](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Nel menu di backup selezionare **Locale** dal menu **Dove è in esecuzione il carico di lavoro**. Dal menu a discesa **Elementi di cui eseguire il backup** selezionare i carichi di lavoro da proteggere con il server di Backup di Azure. In caso di dubbio riguardo ai carichi di lavoro da selezionare, scegliere **Macchine virtuali Hyper-V** e quindi fare clic su **Preparare l'infrastruttura**.

    ![locale e carichi di lavoro come obiettivi](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Si apre il menu **Preparare l'infrastruttura**.

7. Nel menu **Preparare l'infrastruttura** fare clic su **Scarica** per aprire una pagina Web da cui scaricare i file di installazione del server di Backup di Azure.

    ![Attività iniziali guidate modifica](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Viene visualizzata la pagina Web di Microsoft che ospita i file scaricabili per il server di Backup di Azure.

8. Nella pagina di download del server di Backup di Microsoft Azure selezionare una lingua e fare clic su **Scarica**.

    ![Viene visualizzata l'area download](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Il programma di installazione del server di Backup di Azure è costituito da otto file, un programma di installazione e sette file BIN. Usare **Nome file** per selezionare tutti i file necessari e fare clic su **Avanti**. Scaricare tutti i file nella stessa cartella.

    ![Area download 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Le dimensioni del download di tutti i file di installazione sono superiori a 3 GB. Con un collegamento di download a 10 Mbps, scaricare tutti i file di installazione può richiedere fino a 60 minuti. I file vengono scaricati nel percorso di download specificato.

## <a name="extract-azure-backup-server-install-files"></a>Estrarre i file di installazione del server di Backup di Azure

Dopo aver scaricato tutti i file nella macchina virtuale di Azure Stack, passare al percorso di download. La prima fase dell'installazione del server di Backup di Azure consiste nell'estrazione dei file.

![Area download 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Per avviare l'installazione, nell'elenco dei file scaricati fare clic su **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Per estrarre i file di installazione sono necessari almeno 4 GB di spazio libero.
    >

2. Nella procedura guidata del server di Backup di Azure fare clic su **Avanti** per continuare.

    ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Scegliere il percorso per i file del server di Backup di Azure e fare clic su **Avanti**.

   ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verificare il percorso di estrazione e fare clic su **Estrai**.

   ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. La procedura guidata estrae i file e predispone il processo di installazione.

   ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Completato il processo di estrazione, fare clic su **Fine**. L'opzione **Esegui setup.exe** è selezionata per impostazione predefinita. Quando fa clic su **Fine** Setup.exe installa il server di Backup di Microsoft Azure nel percorso specificato.

   ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installare il pacchetto software

Nel passaggio precedente si è fatto clic su **Fine** per uscire dalla fase di estrazione e avviare l'installazione guidata del server di Backup di Azure.

![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Il server di Backup di Azure condivide codice con Data Protection Manager. Verranno visualizzati riferimenti a Data Protection Manager e DPM nel programma di installazione del server di Backup di Azure. Benché il server di Backup di Azure e Data Protection Manager siano prodotti distinti, sono strettamente correlati.

1. Fare clic su **Server di Backup di Microsoft Azure** per avviare l'installazione guidata.

   ![Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Nella **schermata iniziale** fare clic su **Avanti**.

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Nella schermata di **controllo dei prerequisiti** fare clic su **Controlla** per determinare se i prerequisiti hardware e software per il server di Backup di Azure sono stati soddisfatti.

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se l'ambiente soddisfa tutti i prerequisiti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Fare clic su **Avanti**.  

    ![Server di Backup di Azure - Controllo dei prerequisiti superato](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se l'ambiente non soddisfa i prerequisiti necessari, verranno specificati i problemi. I prerequisiti non soddisfatti sono riportati anche in DpmSetup.log. Risolvere gli errori relativi ai prerequisiti e quindi eseguire **di nuovo il controllo**. L'installazione non può continuare finché non sono soddisfatti tutti i prerequisiti.

    ![Server di Backup di Azure - prerequisiti di installazione non soddisfatti](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Il server di Backup di Microsoft Azure richiede SQL Server. Il pacchetto di installazione del server di Backup di Azure include i file binari appropriati di SQL Server. Se si preferisce, è possibile usare la propria installazione di SQL. Tuttavia, la scelta consigliata è consentire al programma di installazione di aggiungere una nuova istanza di SQL Server. Per verificare che l'opzione scelta funzioni con l'ambiente, fare clic su **Controlla e installa**.

   > [!NOTE]
   > Il server di Backup di Azure non funzionerà con un'istanza remota di SQL Server. L'istanza usata dal server di Backup di Azure deve essere locale.
   >

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Dopo il controllo, se la macchina virtuale ha i prerequisiti necessari per installare il server di Backup di Azure, fare clic su **Avanti**.

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se si verifica un errore con l'indicazione di riavviare il computer, eseguire questa operazione. Dopo il riavvio del computer, riavviare il programma di installazione e quando si accede alla schermata **Impostazioni SQL** fare clic su **Controlla di nuovo**.

5. Nelle **impostazioni di installazione** specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    La disponibilità di uno spazio di lavoro è obbligatoria per il backup in Azure. Verificare che la dimensione dello spazio di lavoro sia equivalente ad almeno il 5% dei dati pianificati per il backup in Azure. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

6. Nella schermata **Impostazioni di sicurezza** specificare una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Nella schermata del **consenso esplicito per Microsoft Update** indicare se si vuole usare *Microsoft Update* per verificare la disponibilità di aggiornamenti e fare clic su **Avanti**.

   > [!NOTE]
   > È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.
   >

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando il server di Backup di Azure termina l'installazione, il programma di installazione avvia immediatamente l'installazione dell'agente di Servizi di ripristino di Microsoft Azure.

9. Il programma di installazione dell'agente di Servizi di ripristino di Microsoft Azure si apre e verifica la connettività Internet. Se la connettività Internet è disponibile, procedere con l'installazione. Se non c'è connettività, specificare i dettagli del proxy per connettersi a Internet. Dopo aver specificato le impostazioni del proxy, fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Per installare l'agente di Servizi di ripristino di Microsoft Azure, fare clic su **Installa**.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    L'agente di Servizi di ripristino di Microsoft Azure, detto anche agente di Backup di Azure, configura il server di Backup di Azure nell'insieme di credenziali di Servizi di ripristino. Dopo la configurazione, il server di Backup di Azure eseguirà il backup dei dati sempre nello stesso insieme di credenziali di Servizi di ripristino.

11. Quando l'agente di Servizi di ripristino di Microsoft Azure termina l'installazione, fare clic su **Avanti** per avviare la fase successiva: la registrazione del server di Backup di Azure con l'insieme di credenziali di Servizi di ripristino.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Il programma di installazione avvia la **registrazione guidata del server**.

12. Passare alla sottoscrizione di Azure e all'insieme di credenziali di Servizi di ripristino. Nel menu **Preparare l'infrastruttura** fare clic su **Scarica** per scaricare le credenziali dell'insieme di credenziali. Se il pulsante **Scarica** del passaggio 2 non è attivo, indicare che **il download è già stato eseguito che è in uso l'installazione più recente del server di Backup di Azure** per attivare il pulsante. Le credenziali vengono scaricate nel percorso in cui si archiviano i download. Tenere presente questo percorso poiché sarà necessario per il passaggio successivo.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Nel menu **Identificazione insieme di credenziali** fare clic su **Sfoglia** per trovare le credenziali dell'insieme di credenziali di Servizi di ripristino.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Nella finestra di dialogo **Seleziona l'insieme di credenziali** passare al percorso di download, selezionare l'insieme di credenziali e fare clic su **Apri**.

    Il percorso per le credenziali viene visualizzato nel menu di identificazione dell'insieme di credenziali. Fare clic su **Avanti** per passare all'impostazione della crittografia.

14. Nella finestra di dialogo **Impostazione crittografia** indicare una passphrase per la crittografia dei backup e un percorso in cui archiviare la passphrase e fare clic su **Avanti**.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    È possibile specificare la propria passphrase o usare il generatore di passphrase per crearne una. La passphrase è personale e Microsoft non la salva né la gestisce. Per essere pronti in caso di emergenza, salvare la passphrase in un percorso accessibile.

    Quando si fa clic su **Avanti** il server di Backup di Azure viene registrato con l'insieme di credenziali di Servizi di ripristino. Il programma di installazione continua a installare SQL Server e il server di Backup di Azure.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Al termine del programma di installazione, lo stato indica che tutto il software è stato installato correttamente.

    ![PreReq2 Server di Backup di Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Al termine dell'installazione, sul desktop del server vengono create la console del server di Backup di Azure e le icone di PowerShell del server di Backup di Azure.

## <a name="add-backup-storage"></a>Aggiungere risorse di archiviazione di backup

La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Add Modern Backup storage](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801) (Aggiungere Modern Backup Storage).

> [!NOTE]
> È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura del server di Backup di Azure, l'insieme di credenziali di Servizi di ripristino contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima (obbligatoria) copia di backup.
>
>

## <a name="network-connectivity"></a>Connettività di rete

Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per convalidare la connettività del computer ad Azure, usare il cmdlet ```Get-DPMCloudConnection``` nella console di PowerShell del server di Backup di Azure. C'è connettività solo se l'output del cmdlet è TRUE.

Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro. Per verificare lo stato della sottoscrizione e gestirla, accedere al [portale delle sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Dopo avere verificato lo stato della connettività di Azure e della sottoscrizione di Azure, è possibile usare la tabella seguente per scoprire l'impatto della funzionalità di backup/ripristino offerta.

| Stato di connettività | Sottoscrizione di Azure | Eseguire il backup in Azure | Eseguire il backup su disco | Ripristino da Azure | Ripristino da disco |
| --- | --- | --- | --- | --- | --- |
| Connesso |Attivo |Consentito |Consentito |Consentito |Consentito |
| Connesso |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connesso |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |
| Connettività persa > 15 giorni |Attivo |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |

### <a name="recovering-from-loss-of-connectivity"></a>Recupero dalla perdita di connettività

Se un firewall o un proxy impedisce l'accesso ad Azure, aggiungere all'elenco elementi consentiti gli indirizzi di dominio seguenti nel profilo del firewall/proxy:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Dopo il ripristino della connettività ad Azure nel server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. Quando il server è **connesso**, usare la tabella in [Connettività di rete](backup-mabs-install-azure-stack.md#network-connectivity) per visualizzare le operazioni disponibili.

### <a name="handling-subscription-states"></a>Gestione degli stati della sottoscrizione

È possibile modificare lo stato di una sottoscrizione di Azure da *Scaduta* o *Deprovisioning eseguito* ad *Attiva*. Mentre lo stato della sottoscrizione non è *Attiva*:

- Mentre è *sottoposta a deprovisioning*, la sottoscrizione perde le funzionalità. Il ripristino dello stato *Attiva* della sottoscrizione consente di riattivare le funzionalità di backup e ripristino. Se i dati di backup sono stati mantenuti nel disco locale per un periodo di conservazione sufficientemente elevato, possono essere ripristinati. I dati di backup in Azure, invece, vengono persi definitivamente al passaggio della sottoscrizione allo stato *Deprovisioning eseguito*.
- Una sottoscrizione *scaduta* perde le funzionalità. I backup pianificati non vengono eseguiti quando una sottoscrizione è *scaduta*.

## <a name="troubleshooting"></a>risoluzione dei problemi

In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, di backup o ripristino, vedere il [documento relativo ai codici di errore](https://support.microsoft.com/kb/3041338).
È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passaggi successivi

L'articolo sulla [preparazione dell'ambiente per DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801) contiene informazioni sulle configurazioni supportate per il server di Backup di Azure.

È possibile usare gli articoli seguenti per acquisire una comprensione più profonda della protezione dei carichi di lavoro con il server di Backup di Microsoft Azure.

- [Backup di SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Backup di SharePoint Server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Backup del server alternativo](backup-azure-alternate-dpm-server.md)
