---
title: Eseguire il seedone del backup offline con il servizio Importazione/Esportazione di AzureSeted offline backup with the Azure Import/Export service
description: Informazioni su come usare Backup di Azure per inviare dati dalla rete usando il servizio Importazione/Esportazione di Azure.Learn how you can use Azure Backup to send data off the network by using the Azure Import/Export service. Questo articolo illustra il seeding offline dei dati di backup iniziali usando il servizio Importazione/Esportazione di Azure.This article explains the offline seeding of the initial backup data by using the Azure Import/Export service.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206759"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline backup workflow in Azure Backup

In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati di backup offline.

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md)di Azure. È possibile usare questo servizio per trasferire i dati di backup iniziali in Azure usando i dischi. Se si dispone di terabyte (TB) di dati di backup iniziali che devono essere trasferiti su una rete a latenza elevata e a larghezza di banda ridotta, è possibile usare il flusso di lavoro di seeding offline per spedire la copia di backup iniziale in uno o più dischi rigidi a un data center di Azure.If you have terabytes (TBs) of initial backup data that need to be transferred over a high-latency and low-bandwidth network, you can use the offline-seeding workflow to ship the initial backup copy, on one or more hard disk to an Azure datacenter. La figura seguente offre una panoramica dei passaggi del flusso di lavoro.

  ![Panoramica del processo del flusso di lavoro di importazione offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Il processo di backup offline prevede i seguenti passaggi:

1. Anziché inviare i dati di backup in rete, scriverli in un percorso di gestione temporanea.
1. Usare l'utilità *AzureOfflineBackupDiskPrep* per scrivere i dati nel percorso di gestione temporanea in uno o più dischi SATA.
1. Come parte del lavoro preparatorio, l'utilità AzureOfflineBackupDiskPrep crea un processo di importazione di Azure.As part of the preparatory work, the *AzureOfflineBackupDiskPrep* utility creates an Azure import job. Inviare le unità SATA al data center di Azure più vicino e fare riferimento al processo di importazione per connettere le attività.
1. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
1. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino e pianifica i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate

Le funzionalità o i carichi di lavoro di Backup di Azure seguenti supportano l'uso del backup offline per:The following Azure Backup features or workloads support the use of offline backup for:

> [!div class="checklist"]
>
> * Backup di file e cartelle con l'agente di Microsoft Azure Recovery Services (MARS), noto anche come agente di Backup di Azure.
> * Backup di tutti i carichi di lavoro e file con System Center Data Protection Manager (DPM).
> * Backup of all workloads and files with Microsoft Azure Backup Server.
 
   > [!NOTE]
   > Offline backup isn't supported for system state backups done by using the Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisiti

  > [!NOTE]
  > I prerequisiti e il flusso di lavoro seguenti si applicano solo al backup offline di file e cartelle usando l'agente di Servizi di ripristino [di Azure più recente.](https://aka.ms/azurebackup_agent) Per eseguire backup offline per i carichi di lavoro utilizzando System Center DPM o Azure Backup Server, vedere Flusso di lavoro di [backup offline per DPM e Server di Backup di Azure.](backup-azure-backup-server-import-export-.md)

Prima di avviare il flusso di lavoro di backup offline, completare i prerequisiti seguenti:

* Creare un [vault di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per creare un insieme di credenziali, attenersi alla procedura descritta in [Creare un insieme di credenziali dei servizi di ripristino](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Assicurarsi che nel client Windows Server o Windows sia installata solo la [versione più recente dell'agente di Backup](https://aka.ms/azurebackup_agent) di Azure, a seconda dei casi, il computer sia registrato nell'insieme di credenziali servizi di ripristino.
* Azure PowerShell 3.7.0 is required on the computer running the Azure Backup Agent. Scaricare e [installare la versione 3.7.0 di Azure PowerShell.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)
* Nel computer che esegue l'agente di Backup di Azure verificare che sia installato Microsoft Edge o Internet Explorer 11 e che JavaScript sia abilitato.
* Creare un account di archiviazione di Azure nella stessa sottoscrizione dell'insieme di credenziali dei servizi di ripristino.
* Assicurarsi di avere le [autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md) per creare l'applicazione Azure Active Directory. Il flusso di lavoro di backup offline crea un'applicazione Azure Active Directory nella sottoscrizione associata all'account di archiviazione di Azure.The offline backup workflow creates an Azure Active Directory application in the subscription associated with the Azure storage account. L'obiettivo dell'applicazione è fornire a Backup di Azure l'accesso sicuro e con ambito al servizio Importazione/Esportazione di Azure, necessario per il flusso di lavoro di backup offline.
* Registrare il provider di risorse Microsoft.ImportExport con la sottoscrizione che contiene l'account di archiviazione di Azure.Register the *Microsoft.ImportExport* resource provider with the subscription that contains the Azure storage account. Per registrare il provider di risorse:
    1. Nel menu principale selezionare **Abbonamenti**.
    1. Se si è abbonati a più sottoscrizioni, selezionare la sottoscrizione che si intende utilizzare per il backup offline. Se si usa una sola sottoscrizione, questa verrà visualizzata.
    1. Nel menu della sottoscrizione selezionare **Provider di** risorse per visualizzare l'elenco dei provider.
    1. Nell'elenco dei provider scorrere verso il basso fino a *Microsoft.ImportExport*. Se **lo stato** è **NotRegistered**, selezionare **Registra**.

        ![Registrare il provider di risorse](./media/backup-azure-backup-import-export/registerimportexport.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva nel computer, interna o esterna, con spazio su disco sufficiente per contenere la copia iniziale. Ad esempio, se si desidera eseguire il backup di un file server da 500 GB, assicurarsi che l'area di gestione temporanea sia di almeno 500 GB. Verrà tuttavia usata una quantità inferiore in virtù della compressione.
* Quando si inviano dischi in Azure, usare solo SSD da 2,5 pollici o unità disco rigido interne SATA II/III da 2,5 pollici o 3,5 pollici. È possibile usare dischi rigidi fino a 10 TB. Per informazioni sul set più recente di unità supportato dal servizio, vedere la [documentazione del servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware).
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del percorso di gestione temporanea nell'unità SATA. Verificare che BitLocker sia abilitato nel computer di copia.

## <a name="workflow"></a>Flusso di lavoro

Questa sezione descrive il flusso di lavoro di backup offline in modo che i dati possano essere recapitati a un data center di Azure e caricati in Archiviazione di Azure.This section describes the offline backup workflow so that your data can be delivered to an Azure datacenter and uploaded to Azure Storage. In caso di domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione relativa alla panoramica del servizio Importazione/Esportazione di Azure.If you have questions about the import service or any aspect of the process, see the [Azure Import/Export service overview documentation](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Avviare il backup offline

1. Quando si pianifica un backup nell'agente servizi di ripristino, viene visualizzata questa pagina.

    ![Pagina di importazione](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selezionare l'opzione **Trasferisci utilizzando i miei dischi**.

    > [!NOTE]
    > Usare l'opzione Azure Data Box per trasferire i dati di backup iniziali offline. Questa opzione consente di risparmiare lo sforzo necessario per procurarsi i propri dischi compatibili con Azure.This option saves the effort to procure your own Azure-compatible disks. Fornisce dispositivi Azure Data Box proprietari, sicuri e a prova di manomissione di Microsoft in cui i dati di backup possono essere scritti direttamente dall'agente dei servizi di ripristino.

1. Selezionare **Avanti**e compilare le caselle con attenzione.

    ![Inserisci i dettagli del disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   Le caselle che si compilano sono:

    * **Percorso di gestione temporanea**: posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Il percorso di gestione temporanea potrebbe trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, specificare il percorso di rete completo del percorso di gestione temporanea.
    * Account di archiviazione di Azure Resource Manager: nome dell'account di archiviazione di tipo Resource Manager (generico v1 o v2 generico) in qualsiasi sottoscrizione di Azure.Azure **Resource Manager Storage Account:** The name of the Resource Manager type storage account (general purpose v1 or general purpose v2) in any Azure subscription.
    * **Contenitore di archiviazione di Azure:** nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui vengono importati i dati di backup prima di essere copiati nell'insieme di credenziali di Servizi di ripristino.
    * **ID sottoscrizione di Azure:** ID per la sottoscrizione di Azure in cui viene creato l'account di archiviazione di Azure.Azure Subscription ID: The ID for the Azure subscription subscription where the Azure storage account is created.
    * **Nome processo di importazione di Azure:** nome univoco con cui il servizio Importazione/Esportazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati sui dischi in Azure.Azure Import Job Name : The unique name by which the Azure Import/Export service and Azure Backup track the transfer of data sent on disks to Azure.
  
   Dopo aver compilato le caselle, selezionare **Avanti**. Salvare le informazioni relative al percorso di gestione temporanea e al nome del processo di importazione di **Azure.Save** the **Staging Location** and the Azure Import Job Name information. È necessario per preparare i dischi.

1. Quando richiesto, accedere alla sottoscrizione di Azure.When prompted, sign in to your Azure subscription. È necessario accedere in modo che Backup di Azure possa creare l'applicazione Azure Active Directory.You must sign in so that Azure Backup can create the Azure Active Directory application. Immettere le autorizzazioni necessarie per accedere al servizio Importazione/Esportazione di Azure.Enter the required permissions to access the Azure Import/Export service.

    ![Pagina di accesso alla sottoscrizione di AzureAzure subscription sign-in page](./media/backup-azure-backup-import-export/azure-login.png)

1. Completare il flusso di lavoro. Nella console dell'agente di Backup di Azure selezionare **Esegui backup ora**.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

1. Nella pagina **Conferma** della procedura guidata selezionare **Backup**. Durante la configurazione il backup iniziale viene scritto nell'area di gestione temporanea.

   ![Confermare che si è pronti per eseguire il backup ora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Al termine dell'operazione, il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

   ![Pagina della procedura guidata Backup ora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e inviarle al data center di Azure

L'utilità *AzureOfflineBackupDiskPrep* prepara le unità SATA da inviare al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di Backup di Azure nel percorso seguente:This utility is available in the Azure Backup Agent installation directory in the following path:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Passare alla directory e copiare la directory *AzureOfflineBackupDiskPrep* in un altro computer a cui sono connesse le unità SATA. Sul computer con le unità SATA collegate, assicurarsi che:

   * Il computer di copia può accedere al percorso di gestione temporanea per il flusso di lavoro di seeding offline utilizzando lo stesso percorso di rete fornito nel flusso di lavoro nella sezione "Avvia backup offline".
   * Nel computer di copia è abilitato BitLocker.
   * Azure PowerShell 3.7.0 è installato.
   * Sono installati i browser compatibili più recenti (Microsoft Edge o Internet Explorer 11) e JavaScript è abilitato.
   * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può coincidere con il computer di origine.

     > [!IMPORTANT]
     > Se il computer di origine è una macchina virtuale, usare un server fisico diverso o un computer client come computer di copia del computer di origine.

1. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente. Eseguire il comando seguente:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso posizione di gestione temporanea*&gt; |Questo input obbligatorio viene utilizzato per fornire il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "Avvio backup offline". |
    | p:&lt;*Percorso di PublishSettingsFile*&gt; |Questo input facoltativo viene usato per fornire il percorso del file delle impostazioni di pubblicazione di Azure immesso nel flusso di lavoro nella sezione "Avvio del backup offline". |

    Quando si esegue il comando, l'utilità richiede la selezione del processo di importazione di Azure che corrisponde alle unità che devono essere preparate. Se al percorso di gestione temporanea fornito è associato un solo processo di importazione, verrà visualizzata una pagina simile alla seguente.

    ![Input dello strumento di preparazione del disco di AzureAzure disk preparation tool input](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Immettere la lettera di unità del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali.
1. Quando richiesto, confermare la formattazione dell'unità.
1. Viene richiesto di accedere alla sottoscrizione di Azure.You're prompted to sign in to your Azure subscription. Immettere le credenziali.

    ![Accesso alla sottoscrizione di AzureAzure subscription sign-in](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento nel caso in cui il disco fornito non disponga di spazio sufficiente per i dati di backup. <br/>

    Dopo aver eseguito correttamente lo strumento, il prompt dei comandi presenta tre tipi di informazioni:

   1. Uno o più dischi indicati sono stati preparati per la spedizione in Azure.
   1. Si riceve la conferma che il processo di importazione è stato creato. Il processo di importazione usa il nome specificato.
   1. Lo strumento consente di visualizzare l'indirizzo di spedizione per il data center di Azure.

      ![Preparazione del disco di Azure completata](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Al termine dell'esecuzione del comando, è possibile aggiornare le informazioni di spedizione.

1. Spedire i dischi all'indirizzo fornito dallo strumento. Mantenere il numero di ricerca per riferimento futuro.

   > [!IMPORTANT]
   > Due processi di importazione di Azure non possono avere lo stesso numero di rilevamento. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano fornite insieme in un unico pacchetto e che sia presente un unico numero di rilevamento univoco per il pacchetto. Non combinare le unità preparate come parte di processi di importazione di Azure separati in un singolo pacchetto.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aggiornare i dettagli di spedizione nel processo di importazione di AzureUpdate shipping details on the Azure import job

La procedura seguente aggiorna i dettagli di spedizione dei processi di importazione di Azure.The following procedure updates the Azure import job shipping details. Sono incluse le informazioni seguenti:

* Nome della portante che recapita i dischi in Azure.
* Restituire i dettagli di spedizione per i dischi.

1. Accedere alla sottoscrizione di Azure.
1. Nel menu principale selezionare **Tutti i servizi**. Nella finestra di dialogo **Tutti i servizi** immettere **Importa**. Quando viene visualizzato **Processi di importazione/esportazione**, selezionarlo.

    ![Inserire le informazioni di spedizione](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Viene visualizzato il menu **Importa/esporta processi** e viene visualizzato l'elenco di tutti i processi di importazione/esportazione nella sottoscrizione selezionata.

1. Se si dispone di più sottoscrizioni, selezionare la sottoscrizione utilizzata per importare i dati di backup. Selezionare quindi il processo di importazione appena creato per aprirne i dettagli.

    ![Esaminare le informazioni di spedizione](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Nel menu **Impostazioni** per il processo di importazione selezionare **Gestisci informazioni di spedizione**. Inserisci i dettagli di spedizione del reso.

    ![Memorizzare le informazioni di spedizione](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando si dispone del numero di tracciabilità del vettore di spedizione, selezionare il banner nella pagina Panoramica processo di importazione di Azure e immettere i dettagli seguenti.

   > [!IMPORTANT]
   > Verificare che vengano aggiornate le informazioni sul vettore e il numero di tracciabilità entro due settimane dalla creazione del processo di importazione di Azure. La mancata verifica di queste informazioni entro due settimane può comportare l'eliminazione del processo e la mancata elaborazione delle unità.

   ![Avviso di aggiornamento delle informazioni di rilevamento](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informazioni sul vettore e numero di tracciamento](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo di elaborazione delle unità

La quantità di tempo necessaria per elaborare un processo di importazione di Azure varia. Il tempo di processo si basa su fattori quali il tempo di spedizione, il tipo di lavoro, il tipo e le dimensioni dei dati da copiare e le dimensioni dei dischi forniti. Il servizio Importazione/Esportazione di Azure non dispone di un contratto di servizio. Dopo la ricezione dei dischi, il servizio si sforza di completare la copia dei dati di backup nell'account di archiviazione di Azure in 7-10 giorni.

### <a name="monitor-azure-import-job-status"></a>Monitorare lo stato del processo di importazione di AzureMonitor Azure import job status

È possibile monitorare lo stato del processo di importazione dal portale di Azure.You can monitor the status of your import job from the Azure portal. Vai alla pagina **Importa/Esporta lavori** e seleziona il tuo lavoro. Per altre informazioni sullo stato dei processi di importazione, vedere [Che cos'è il servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md)di Azure? .

### <a name="finish-the-workflow"></a>Terminare il flusso di lavoro

Dopo aver terminato correttamente il processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. Al momento del successivo backup pianificato, Backup di Azure copia il contenuto dei dati dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino.

   ![Copiare i dati nell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale.

### <a name="clean-up-resources"></a>Pulire le risorse

Al termine del backup iniziale, è possibile eliminare in modo sicuro i dati importati nel contenitore di Archiviazione di Azure e i dati di backup nel percorso di gestione temporanea.

## <a name="next-steps"></a>Passaggi successivi

* Per qualsiasi domanda sul flusso di lavoro del servizio Importazione/Esportazione di Azure, vedere [Usare il servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md)di Microsoft Azure per trasferire i dati nell'archiviazione BLOB.
