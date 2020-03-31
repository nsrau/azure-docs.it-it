---
title: Backup offline per DPM e server di Backup di Azure
description: Con Backup di Azure è possibile inviare dati dalla rete usando il servizio Importazione/Esportazione di Azure.With Azure Backup, you can send data off the network by using the Azure Import/Export service. In questo articolo viene illustrato il flusso di lavoro di backup offline per DPM e il server di backup di Azure.This article explains the offline backup workflow for DPM and Azure Backup Server.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197065"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Flusso di lavoro di backup offline per DPM e il server di backup di Azure

In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Backup di Azure comprime i backup iniziali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati compressi dei backup iniziali.

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md)di Azure. È possibile usare questo servizio per trasferire dati in Azure usando i dischi. Se si dispone di terabyte (TB) di dati di backup iniziali che devono essere trasferiti su una rete a latenza elevata e a larghezza di banda ridotta, è possibile usare il flusso di lavoro di seeding offline per spedire la copia di backup iniziale su uno o più dischi rigidi a un data center di Azure.If you have terabytes (TBs) of initial backup data that need to be transferred over a high-latency and low-bandwidth network, you can use the offline-seeding workflow to ship the initial backup copy on one or more hard disk to an Azure datacenter. In questo articolo vengono forniti cenni preliminari e ulteriori passaggi che completano questo flusso di lavoro per System Center Data Protection Manager (DPM) e Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Il processo di backup offline per l'agente di Microsoft Azure Recovery Services (MARS) è distinto da DPM e MABS. Per informazioni sull'uso del backup offline con l'agente MARS, vedere Flusso di lavoro di backup offline in Backup di [Azure.](backup-azure-backup-import-export.md) Offline backup isn't supported for system state backups done by using the Azure Backup Agent.
>

## <a name="overview"></a>Panoramica

Con la funzionalità di seeding offline di Backup di Azure e il servizio Importazione/Esportazione di Azure, è semplice caricare i dati offline in Azure usando i dischi. Il processo di backup offline prevede i passaggi seguenti:

> [!div class="checklist"]
>
> * I dati di backup vengono scritti in un percorso di gestione temporanea anziché essere inviati in rete.
> * I dati nel percorso di gestione temporanea vengono quindi scritti in uno o più dischi SATA usando l'utilità *AzureOfflineBackupDiskPrep.The* data on the staging location is then written to one or more SATA disks by using the AzureOfflineBackupDiskPrep utility.
> * Un processo di importazione di Azure viene creato automaticamente dall'utilità.
> * Le unità SATA vengono quindi inviate al data center di Azure più vicino.
> * Al termine del caricamento dei dati di backup in Azure, i dati di backup vengono copiati da Backup di Azure nell'insieme di credenziali per il backup e vengono pianificati i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate

Il backup offline è supportato per tutti i modelli di distribuzione di Backup di Azure che esegue il backup dei dati dall'ambiente locale al cloud Microsoft.Offline backup is supported for all deployment models of Azure Backup that back up data from on-premises to the Microsoft cloud. Questi modelli includono:

> [!div class="checklist"]
>
> * Backup di file e cartelle con l'agente MARS o l'agente di Backup di Azure.
> * Backup di tutti i carichi di lavoro e file con DPM.
> * Backup di tutti i carichi di lavoro e file con MABS.

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare il flusso di lavoro di backup offline, verificare che siano soddisfatti i prerequisiti seguenti:

* Deve essere stato creato un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per crearne uno, seguire la procedura descritta in [Creare un insieme di credenziali dei servizi di ripristino](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Un agente di Backup di Azure o MABS o DPM è stato installato in Windows Server o un client Windows, a seconda dei casi, e il computer è registrato con l'insieme di credenziali di Servizi di ripristino. Assicurarsi di usare solo la [versione più recente di Backup di Azure](https://go.microsoft.com/fwlink/?linkid=229525).
* [Scaricare il file](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) delle impostazioni di pubblicazione di Azure nel computer da cui si prevede di eseguire il backup dei dati. La sottoscrizione da cui si scarica il file delle impostazioni di pubblicazione può essere diversa dalla sottoscrizione che contiene l'insieme di credenziali di Servizi di ripristino. Se la sottoscrizione si trova in cloud di Azure sovrani, usare i collegamenti seguenti in base alle esigenze per scaricare il file delle impostazioni di pubblicazione di Azure.If your subscription is on sovereign Azure clouds, then use the following links as appropriate to download the Azure publish settings file.

    | Area geografica cloud sovrani | Collegamento al file delle impostazioni di pubblicazione di AzureAzure publish settings file link |
    | --- | --- |
    | Stati Uniti | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Cina | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Nella sottoscrizione da cui è stato scaricato il file delle impostazioni di pubblicazione è stato creato un account di archiviazione di Azure con il modello di distribuzione di Resource Manager.

  ![Creare un account di archiviazione con lo sviluppo di Resource ManagerCreate a storage account with Resource Manager development](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva nel computer, interna o esterna, con spazio su disco sufficiente per contenere la copia iniziale. Ad esempio, se si desidera eseguire il backup di un file server da 500 GB, assicurarsi che l'area di gestione temporanea sia di almeno 500 GB. Verrà tuttavia usata una quantità inferiore in virtù della compressione.
* Per i dischi inviati in Azure, assicurarsi che vengano utilizzati solo unità SSD da 2,5 pollici o sATA II/III da 2,5 pollici o 3,5 pollici. È possibile usare dischi rigidi fino a 10 TB. Per informazioni sul set più recente di unità supportato dal servizio, vedere la [documentazione del servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware).
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del percorso di gestione temporanea nell'unità SATA. Verificare che BitLocker sia abilitato nel computer di copia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Preparare il server per il processo di backup offline

>[!NOTE]
> Se non è possibile trovare le utilità elencate, ad esempio *AzureOfflineBackupCertGen.exe*, AskAzureBackupTeam@microsoft.com nell'installazione dell'agente MARS, scrivere a per accedervi.

* Aprire un prompt dei comandi con privilegi elevati nel server ed eseguire il comando seguente:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Lo strumento crea un'applicazione Di Azure Offline Backup Active Directory se non ne esiste una.

    Se esiste già un'applicazione, questo eseguibile richiede di caricare manualmente il certificato nell'applicazione nel tenant. Seguire i passaggi in [questa sezione](#manually-upload-an-offline-backup-certificate) per caricare manualmente il certificato nell'applicazione.

* Lo strumento *AzureOfflineBackup.exe* genera un file *OfflineApplicationParams.xml.The AzureOfflineBackup.exe* tool generates an OfflineApplicationParams.xml file. Copiare questo file nel server con MABS o DPM.
* Installare [l'agente MARS più recente](https://aka.ms/azurebackup_agent) nell'istanza DPM o nel server di Backup di Azure.
* Registrare il server in Azure.Register the server to Azure.
* Eseguire il comando seguente:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Il comando precedente `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`crea il file .

## <a name="manually-upload-an-offline-backup-certificate"></a>Caricare manualmente un certificato di backup offline

Seguire questi passaggi per caricare manualmente il certificato di backup offline in un'applicazione Azure Active Directory creata in precedenza destinata al backup offline.

1. Accedere al portale di Azure.
1. Passare a**Registrazioni**di **Azure Active Directory** > App .
1. Nella scheda **Applicazioni di** proprietà individuare un'applicazione con il formato `AzureOfflineBackup _<Azure User Id`del nome visualizzato .

    ![Individuare l'applicazione nella scheda Applicazioni di proprietà](./media/backup-azure-backup-import-export/owned-applications.png)

1. Selezionare l'applicazione. In **Gestisci** nel riquadro sinistro passare a **Certificati & segreti**.
1. Verificare la presenza di certificati o chiavi pubbliche preesistenti. Se non sono presenti, è possibile eliminare l'applicazione in modo sicuro selezionando il pulsante **Elimina** nella pagina **Panoramica** dell'applicazione. È quindi possibile ripetere i passaggi per [preparare il server per il](#prepare-the-server-for-the-offline-backup-process) processo di backup offline e ignorare i passaggi seguenti. In caso contrario, continuare a seguire questa procedura dall'istanza DPM o dal server di Backup di Azure in cui si desidera configurare il backup offline.
1. Selezionare la scheda > **Personale** `CB_AzureADCertforOfflineSeeding_<ResourceId>` **dell'applicazione Gestisci certificato computer.** Cercare il certificato con il nome .
1. Selezionare il certificato, fare clic con il pulsante destro del mouse su **Tutte le attività**, quindi scegliere **Esporta**, senza una chiave privata, nel formato CER.
1. Passare all'applicazione di backup offline di Azure nel portale di Azure.Go to the Azure offline backup application in the Azure portal.
1. Selezionare **Gestisci** > **certificati & segreti** > **Carica certificato**. Caricare il certificato esportato nel passaggio precedente.

    ![Caricare il certificato](./media/backup-azure-backup-import-export/upload-certificate.png)

1. Sul server, aprire il Registro di sistema immettendo **regedit** nella finestra Esegui.
1. Passare alla voce del Registro di sistema *Computer HKEY_LOCAL_MACHINE .*
1. Fare clic con il pulsante destro del mouse `AzureADAppCertThumbprint_<Azure User Id>`su **CloudBackupProvider**e aggiungere un nuovo valore stringa con il nome .

    >[!NOTE]
    > Per trovare l'ID utente di Azure, eseguire una delle operazioni seguenti:
    >
    >* Da PowerShell connesso ad Azure `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` eseguire il comando.
    >* Passare al percorso `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`del Registro di sistema .

1. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **Modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel passaggio 7. Quindi selezionare **OK**.
1. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato. Selezionare la scheda **Dettagli** e scorrere verso il basso fino a visualizzare il campo di identificazione personale. Selezionare **Identificazione personale**e copiare il valore.

    ![Copiare il valore dal campo dell'identificazione personale](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Passare alla sezione [Flusso di lavoro](#workflow) per procedere con il processo di backup offline.

## <a name="workflow"></a>Flusso di lavoro

The information in this section helps you finish the offline backup workflow so that your data can be delivered to an Azure datacenter and uploaded to Azure Storage. In caso di domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la [documentazione relativa](../storage/common/storage-import-export-service.md) alla panoramica del servizio di importazione a cui si fa riferimento in precedenza.

### <a name="initiate-offline-backup"></a>Avviare il backup offline

1. Quando si pianifica un backup, viene visualizzata la pagina seguente in Windows Server, un client Windows o DPM.

    ![Pagina di importazione](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Ecco la pagina corrispondente in DPM. <br/>
    
    ![Pagina di importazione di DPM e Server di Backup di Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    Le caselle che si compilano sono:

   * **Percorso di gestione temporanea**: posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Il percorso di gestione temporanea potrebbe trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, specificare il percorso di rete completo del percorso di gestione temporanea.
   * **Impostazioni di pubblicazione di Azure:** percorso locale del file delle impostazioni di pubblicazione.
   * **Nome processo di importazione di Azure:** nome univoco con cui il servizio Importazione/Esportazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati sui dischi in Azure.Azure Import Job Name : The unique name by which the Azure Import/Export service and Azure Backup track the transfer of data sent on disks to Azure.
   * **ID sottoscrizione di Azure:** ID sottoscrizione di Azure per la sottoscrizione da cui è stato scaricato il file delle impostazioni di pubblicazione di Azure.Azure Subscription ID: The Azure subscription ID for the subscription from where where you downloaded the Azure publish settings file.
   * **Account di archiviazione di Azure:** nome dell'account di archiviazione nella sottoscrizione di Azure associata al file delle impostazioni di pubblicazione di Azure.Azure Storage Account : The name of the storage account in the Azure subscription associated with the Azure publish settings file.
   * **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui verranno importati i dati del backup.

   Salvare le informazioni relative al percorso di gestione **temporanea** e al nome del processo di importazione di **Azure** fornite. È necessario per preparare i dischi.

1. Completare il flusso di lavoro. Per avviare la copia di backup offline, selezionare Esegui backup ora nella console di gestione dell'agente di Backup di Azure.To initiate the offline backup copy, **select Back Up Now** on the Azure Backup Agent management console. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

    Per completare il flusso di lavoro corrispondente in DPM o nel server di backup di Azure, fare clic con il pulsante destro del mouse sul **gruppo protezione dati**. Selezionare l'opzione **Crea punto di ripristino.** Selezionare quindi l'opzione **Protezione online.**

    ![DPM e MABS eseguire il backup ora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Al termine dell'operazione, il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

    ![Stato del backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e inviarle al data center di Azure

L'utilità *AzureOfflineBackupDiskPrep* viene usata per preparare le unità SATA inviate al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente Servizi di ripristino nel seguente percorso:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Passare alla directory e copiare la directory *AzureOfflineBackupDiskPrep* in un computer di copia a cui sono collegate le unità SATA da preparare. Assicurarsi che:

   * Il computer di copia può accedere al percorso di gestione temporanea per il flusso di lavoro di seeding offline utilizzando lo stesso percorso di rete fornito nel flusso di lavoro nella sezione "Avvia backup offline".
   * Nel computer di copia è abilitato BitLocker.
   * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può coincidere con il computer di origine.

     > [!IMPORTANT]
     > Se il computer di origine è una macchina virtuale, è obbligatorio utilizzare un server fisico o un computer client diverso come computer di copia.

1. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente. Eseguire il comando seguente:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso posizione di gestione temporanea*&gt; |Questo input obbligatorio viene utilizzato per fornire il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "Avvio backup offline". |
    | p:&lt;*Percorso di PublishSettingsFile*&gt; |Questo input facoltativo viene usato per fornire il percorso del file delle impostazioni di pubblicazione di Azure immesso nel flusso di lavoro nella sezione "Avvio del backup offline". |

    > [!NOTE]
    > Il valore &lt;Percorso FileImpostazioniPubblicazione&gt; è obbligatorio quando il computer di copia e il computer di origine sono diversi.
    >
    >

    Quando si esegue il comando, l'utilità richiede la selezione del processo di importazione di Azure che corrisponde alle unità che devono essere preparate. Se al percorso di gestione temporanea fornito è associato un solo processo di importazione, verrà visualizzata una pagina simile alla seguente.

    ![Input dello strumento di preparazione del disco di AzureAzure disk preparation tool input](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Immettere la lettera di unità del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali. Quando richiesto, fornire la conferma per la formattazione dell'unità.

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento nel caso in cui il disco fornito non disponga di spazio sufficiente per i dati di backup. <br/>

    Al termine dello strumento, uno o più dischi forniti vengono preparati per la spedizione in Azure.After the tool finishes successfully, one or more disks that you provided are prepared for shipping to Azure. In Azure viene creato anche un processo di importazione con il nome fornito durante il flusso di lavoro nella sezione "Avvio del backup offline" in Azure.An import job with the name you provided during the workflow in the "Initiate offline backup" section also is created in Azure. Lo strumento visualizza infine l'indirizzo del data center di Azure a cui i dischi devono essere spediti.

    ![Preparazione del disco di Azure completata](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. Al termine dell'esecuzione del comando, viene visualizzata anche l'opzione per aggiornare le informazioni di spedizione.

    ![Opzione Aggiorna informazioni sulla spedizione](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. È possibile immettere direttamente i dettagli. Lo strumento guida l'utente attraverso il processo che coinvolge una serie di input. Se non si dispone di informazioni come il numero di ricerca o altri dettagli relativi alla spedizione, è possibile terminare la sessione. In questo articolo vengono forniti i passaggi per aggiornare i dettagli sulla spedizione in un secondo momento.

1. Spedire i dischi all'indirizzo fornito dallo strumento. Mantenere il numero di ricerca per riferimento futuro.

   > [!IMPORTANT]
   > Due processi di importazione di Azure non possono avere lo stesso numero di rilevamento. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano fornite insieme in un unico pacchetto e che sia presente un unico numero di rilevamento univoco per il pacchetto. Non combinare le unità preparate come parte di diversi processi di importazione di Azure in un singolo pacchetto.

1. Quando si dispone delle informazioni sul numero di tracciabilità, passare al computer di origine, in attesa del completamento del processo di importazione. Eseguire il comando seguente in un prompt dei comandi con privilegi elevati con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Facoltativamente, è possibile eseguire il comando seguente da un computer diverso, ad esempio il computer di copia, con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametro | Descrizione |
    | --- | --- |
    | u: | Questo input obbligatorio viene usato per aggiornare i dettagli di spedizione per un processo di importazione di Azure.This mandatory input is used to update shipping details for an Azure import job. |
    | s:&lt;*Percorso posizione di gestione temporanea*&gt; | Questo input obbligatorio viene utilizzato quando il comando non viene eseguito nel computer di origine. Viene utilizzato per fornire il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "Avvio backup offline". |
    | p:&lt;*Percorso di PublishSettingsFile*&gt; | Questo input obbligatorio viene utilizzato quando il comando non viene eseguito nel computer di origine. Viene usato per fornire il percorso del file delle impostazioni di pubblicazione di Azure immesso nel flusso di lavoro nella sezione "Avvio del backup offline". |

    L'utilità rileva automaticamente il processo di importazione che il computer di origine è in attesa o i processi di importazione associati al percorso di gestione temporanea quando il comando viene eseguito in un computer diverso. Fornisce quindi la possibilità di aggiornare le informazioni di spedizione attraverso una serie di input.

    ![Inserire le informazioni di spedizione](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Dopo aver fornito tutti gli input, rivedere attentamente i dettagli e confermare le informazioni di spedizione fornite immettendo **sì**.

    ![Esaminare le informazioni di spedizione](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Dopo aver aggiornato correttamente le informazioni di spedizione, l'utilità fornisce una posizione locale in cui vengono memorizzati i dettagli di spedizione immessi.

    ![Memorizzare le informazioni di spedizione](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Verificare che le unità raggiungano il data center di Azure entro due settimane dalla fornitura delle informazioni di spedizione tramite l'utilità *AzureOfflineBackupDiskPrep.* In caso contrario, le unità potrebbero non venire elaborate. 

Dopo aver completato i passaggi precedenti, il data center di Azure è pronto per ricevere le unità ed elaborarle ulteriormente per trasferire i dati di backup dalle unità all'account di archiviazione di Azure di tipo classico creato.

### <a name="time-to-process-the-drives"></a>Tempo di elaborazione delle unità

La quantità di tempo necessaria per elaborare un processo di importazione di Azure varia. Il tempo di elaborazione dipende da fattori quali il tempo di spedizione, il tipo di lavoro, il tipo e le dimensioni dei dati copiati e le dimensioni dei dischi forniti. Il servizio Importazione/Esportazione di Azure non dispone di un contratto di servizio. Dopo la ricezione dei dischi, il servizio si sforza di completare la copia dei dati di backup nell'account di archiviazione di Azure in 7-10 giorni. Nella sezione successiva viene descritto come monitorare lo stato del processo di importazione di Azure.The next section describes how you can monitor the status of the Azure import job.

### <a name="monitor-azure-import-job-status"></a>Monitorare lo stato del processo di importazione di AzureMonitor Azure import job status

Mentre le unità sono in transito o nel data center di Azure da copiare nell'account di archiviazione, l'agente di Backup di Azure o DPM o la console MABS nel computer di origine mostra lo stato del processo seguente per i backup pianificati:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Per verificare lo stato del processo di importazione:

1. Aprire un prompt dei comandi con privilegi elevati nel computer di origine ed eseguire il comando seguente:

     `AzureOfflineBackupDiskPrep.exe u:`

1. L'output mostra lo stato corrente del processo di importazione.

    ![Controllare lo stato del processo di importazione](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Per altre informazioni sui vari stati del processo di importazione di Azure, vedere Visualizzare lo stato dei processi di importazione/esportazione di Azure.For more information on the various states of the Azure import job, see [View the status of Azure Import/Export jobs](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Terminare il flusso di lavoro

Al termine del processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. Al momento del successivo backup pianificato, Backup di Azure copia il contenuto dei dati dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino.

   ![Copiare i dati nell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale rispetto alla copia di backup iniziale.

## <a name="next-steps"></a>Passaggi successivi

* Per qualsiasi domanda sul flusso di lavoro del servizio Importazione/Esportazione di Azure, vedere [Usare il servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md)di Microsoft Azure per trasferire i dati nell'archiviazione BLOB.
