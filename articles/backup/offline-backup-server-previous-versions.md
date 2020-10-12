---
title: Backup offline per Data Protection Manager (DPM) e server di Backup di Microsoft Azure (MAB)-versioni precedenti
description: Con backup di Azure è possibile inviare dati fuori rete usando il servizio importazione/esportazione di Azure. Questo articolo illustra il flusso di lavoro di backup offline per le versioni precedenti di DPM e server di Backup di Azure.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b747fd3c682dc1caf7312ba7279470a1e6b38bd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88890094"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Flusso di lavoro di backup offline per DPM e server di Backup di Azure (versioni precedenti)

>[!IMPORTANT]
>Questi passaggi sono applicabili per DPM 2019 RTM e versioni precedenti e per MAB V3 RTM e versioni precedenti.

In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi, che trasferiscono solo i dati delta/incrementali. Backup di Azure comprime i backup iniziali. Tramite il processo di seeding offline, Backup di Azure può usare i dischi per il caricamento offline dei dati di backup iniziali compressi in Azure.

Il processo di seeding offline di backup di Azure è strettamente integrato con il [servizio importazione/esportazione di Azure](../storage/common/storage-import-export-service.md). È possibile usare questo servizio per trasferire i dati in Azure usando dischi. Se si hanno terabyte (TBs) di dati di backup iniziali che devono essere trasferiti in una rete a bassa latenza e larghezza di banda ridotta, è possibile usare il flusso di lavoro di seeding offline per inviare la copia di backup iniziale su uno o più dischi rigidi a un Data Center di Azure. Questo articolo fornisce una panoramica e altri passaggi che completano il flusso di lavoro per System Center Data Protection Manager (DPM) e Backup di Microsoft Azure Server (MAB).

> [!NOTE]
> Il processo di backup non in linea per l'agente Servizi di ripristino di Microsoft Azure (MARS) è diverso da DPM e da MAB. Per informazioni sull'uso del backup offline con l'agente MARS, vedere [flusso di lavoro di backup offline in backup di Azure](backup-azure-backup-import-export.md). Il backup offline non è supportato per i backup dello stato del sistema eseguiti con l'agente di backup di Azure.
>

## <a name="overview"></a>Panoramica

Con la funzionalità di seeding offline di backup di Azure e il servizio importazione/esportazione di Azure, è semplice caricare i dati offline in Azure usando i dischi. Il processo di backup offline prevede i passaggi seguenti:

> [!div class="checklist"]
>
> * I dati di backup vengono scritti in un percorso di gestione temporanea anziché essere inviati in rete.
> * I dati nel percorso di gestione temporanea vengono quindi scritti in uno o più dischi SATA usando l'utilità *AzureOfflineBackupDiskPrep* .
> * Un processo di importazione di Azure viene creato automaticamente dall'utilità.
> * Le unità SATA vengono quindi inviate al Data Center di Azure più vicino.
> * Al termine del caricamento dei dati di backup in Azure, Backup di Azure copia i dati di backup nell'insieme di credenziali per il backup e quindi vengono pianificati i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate

Il backup offline è supportato per tutti i modelli di distribuzione di backup di Azure che eseguono il backup dei dati dall'ambiente locale al cloud Microsoft. Questi modelli includono:

> [!div class="checklist"]
>
> * Backup di file e cartelle con l'agente MARS o Azure Backup Agent.
> * Backup di tutti i carichi di lavoro e i file con DPM.
> * Backup di tutti i carichi di lavoro e i file con MAB.

>[!NOTE]
>Le sottoscrizioni CSP di Azure non sono supportate per l'uso con il seeding offline per DPM 2019 RTM e versioni precedenti e per le versioni precedenti e MAB V3 RTM e versioni precedenti. I backup online sulla rete sono ancora supportati.

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare il flusso di lavoro di backup offline, verificare che siano soddisfatti i seguenti prerequisiti:

* È stato creato un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per crearne uno, seguire i passaggi descritti in [creare un](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) insieme di credenziali di servizi di ripristino
* Un agente di backup di Azure o un oggetto MAB o DPM è stato installato in Windows Server o in un client Windows, come applicabile, e il computer è registrato con l'insieme di credenziali di servizi di ripristino. Assicurarsi di usare solo la [versione più recente di Backup di Azure](https://go.microsoft.com/fwlink/?linkid=229525).
* [Scaricare il file di impostazioni di pubblicazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) nel computer da cui si prevede di eseguire il backup dei dati. La sottoscrizione da cui viene scaricato il file di impostazioni di pubblicazione può essere diversa da quella che contiene l'insieme di credenziali di servizi di ripristino. Se la sottoscrizione si trova in cloud sovrani di Azure, usare i collegamenti seguenti per scaricare il file di impostazioni di pubblicazione di Azure.

    | Area geografica cloud sovrani | Collegamento al file delle impostazioni di pubblicazione di Azure |
    | --- | --- |
    | Stati Uniti | [Collegamento](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Cina | [Collegamento](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Un account di archiviazione di Azure con il modello di distribuzione Gestione risorse è stato creato nella sottoscrizione da cui è stato scaricato il file di impostazioni di pubblicazione. Nell'account di archiviazione, creare un nuovo contenitore BLOB, che verrà usato come destinazione.

  ![Creare un account di archiviazione con Gestione risorse sviluppo](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva (esterna o interna) del computer con spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si desidera eseguire il backup di un file server da 500 GB, assicurarsi che l'area di gestione temporanea sia almeno 500 GB. La capacità usata sarà inferiore a causa della compressione.
* Per i dischi inviati ad Azure, assicurarsi che vengano usati solo dischi rigidi SSD da 2,5 pollici o 2,5-pollice o 3,5-inch SATA II/III. È possibile usare dischi rigidi con capacità fino a 10 TB. Vedere la documentazione del [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) per informazioni sul set di unità più recente supportato dal servizio.
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del percorso di gestione temporanea nell'unità SATA. Verificare che BitLocker sia abilitato nel computer di copia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Preparare il server per il processo di backup offline

>[!NOTE]
> Se non è possibile trovare le utilità elencate, ad esempio *AzureOfflineBackupCertGen.exe*, durante l'installazione dell'agente Mars, scrivere in AskAzureBackupTeam@microsoft.com per ottenere l'accesso.

* Aprire un prompt dei comandi con privilegi elevati nel server ed eseguire il comando seguente:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Lo strumento crea un backup offline di Azure Active Directory applicazione se non ne esiste uno.

    Se un'applicazione esiste già, questo eseguibile richiede di caricare manualmente il certificato nell'applicazione nel tenant. Eseguire la procedura descritta in [questa sezione](#manually-upload-an-offline-backup-certificate) per caricare il certificato manualmente nell'applicazione.

* Lo strumento *AzureOfflineBackupCertGen.exe* genera un file di *OfflineApplicationParams.xml* . Copiare questo file nel server con MAB o DPM.
* Installare la [versione più recente dell'agente Mars](https://aka.ms/azurebackup_agent) nell'istanza DPM o nel server di backup di Azure.
* Registrare il server in Azure.
* Eseguire il comando seguente:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Il comando precedente crea il file `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Caricare manualmente un certificato di backup offline

Attenersi alla procedura seguente per caricare manualmente il certificato di backup offline in un'applicazione Azure Active Directory creata in precedenza per il backup offline.

1. Accedere al portale di Azure.
1. Passare a **Azure Active Directory** > **Registrazioni per l'app**.
1. Nella scheda **applicazioni di proprietà** individuare un'applicazione con il formato nome visualizzato `AzureOfflineBackup _<Azure User Id` .

    ![Scheda Individua applicazione in applicazioni di proprietà](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Selezionare l'applicazione. In **Gestisci** nel riquadro sinistro passare a **certificati & segreti**.
1. Verificare la presenza di certificati preesistenti o di chiavi pubbliche. Se non è presente alcun valore, è possibile eliminare l'applicazione in modo sicuro selezionando il pulsante **Elimina** nella pagina **Panoramica** dell'applicazione. Quindi, è possibile ripetere i passaggi per [preparare il server per il](#prepare-the-server-for-the-offline-backup-process) processo di backup non in linea e ignorare i passaggi seguenti. In caso contrario, continuare a seguire questa procedura dall'istanza di DPM o dal server di backup di Azure in cui si desidera configurare il backup offline.
1. Da **Start** : **Esegui**, digitare *Certlm. msc*. Nella finestra **certificati-computer locale** selezionare la scheda **certificati – personal computer locale**  >  **Personal** . cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Selezionare il certificato, fare clic con il pulsante destro del mouse su **tutte le attività**, quindi scegliere **Esporta**, senza una chiave privata, nel formato CER.
1. Passare all'applicazione di backup offline di Azure nel portale di Azure.
1. Selezionare **Manage**  >  **Certificates & Secrets**  >  **upload certificate**. Caricare il certificato esportato nel passaggio precedente.

    ![Caricare il certificato](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. Nel server aprire il registro di sistema immettendo **Regedit** nella finestra Esegui.
1. Passare alla voce del registro di sistema *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Fare clic con il pulsante destro del mouse su **CloudBackupProvider**e aggiungere un nuovo valore stringa con il nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Per trovare l'ID utente di Azure, eseguire una delle operazioni seguenti:
    >
    >* In PowerShell connesso ad Azure eseguire il comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"`.
    >* Passare al percorso del registro di sistema `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel passaggio 7. Quindi scegliere **OK**.
1. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato. Selezionare la scheda **Dettagli** e scorrere verso il basso fino a visualizzare il campo identificazione personale. Selezionare **identificazione personale**e copiare il valore.

    ![Copia valore dal campo identificazione personale](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Continuare con la sezione del [flusso di lavoro](#workflow) per procedere con il processo di backup non in linea.

## <a name="workflow"></a>Flusso di lavoro

Le informazioni contenute in questa sezione consentono di completare il flusso di lavoro di backup offline in modo che i dati possano essere recapitati a un Data Center di Azure e caricati in archiviazione di Azure. In caso di domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la [documentazione sulla panoramica del servizio di importazione](../storage/common/storage-import-export-service.md) a cui si fa riferimento in precedenza.

### <a name="initiate-offline-backup"></a>Avviare il backup offline

1. Quando si pianifica un backup, viene visualizzata la pagina seguente in Windows Server, un client Windows o DPM.

    ![Importa pagina](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Di seguito è illustrata la pagina corrispondente in DPM. <br/>

    ![Pagina di importazione di DPM e server di Backup di Azure](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    Le caselle compilate sono:

   * **Percorso di gestione temporanea**: Percorso di archiviazione temporanea in cui viene scritta la copia di backup iniziale. Il percorso di gestione temporanea potrebbe trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, specificare il percorso di rete completo del percorso di gestione temporanea.
   * **Impostazioni di pubblicazione di Azure**: percorso locale del file di impostazioni di pubblicazione.
   * **Nome del processo di importazione di Azure**: nome univoco con cui il servizio importazione/esportazione di Azure e backup di Azure tengono traccia del trasferimento dei dati inviati su dischi ad Azure.
   * **ID sottoscrizione**di Azure: ID sottoscrizione di Azure per la sottoscrizione da cui è stato scaricato il file di impostazioni di pubblicazione di Azure.
   * **Account di archiviazione di Azure**: nome dell'account di archiviazione nella sottoscrizione di Azure associata al file delle impostazioni di pubblicazione di Azure.
   * **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui verranno importati i dati del backup.

   Salvare il **percorso di gestione temporanea** e le informazioni sul **nome del processo di importazione di Azure** specificate. È necessario preparare i dischi.

1. Completare il flusso di lavoro. Per avviare la copia di backup non in linea, selezionare **Esegui** backup nella console di gestione dell'agente di backup di Azure. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Per completare il flusso di lavoro corrispondente in DPM o server di Backup di Azure, fare clic con il pulsante destro del mouse sul **gruppo protezione**dati. Selezionare l'opzione **Crea punto di ripristino** . Selezionare quindi l'opzione **protezione dati online** .

    ![Backup di DPM e MAB](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    Al termine dell'operazione il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

    ![Stato del backup](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e spedire ad Azure

L'utilità *AzureOfflineBackupDiskPrep* viene usata per preparare le unità SATA inviate al Data Center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di servizi di ripristino nel percorso seguente:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Passare alla directory e copiare la directory *AzureOfflineBackupDiskPrep* in un computer di copia a cui sono collegate le unità SATA da preparare. Assicurarsi che:

   * Il computer di copia può accedere al percorso di gestione temporanea per il flusso di lavoro di seeding offline usando lo stesso percorso di rete fornito nel flusso di lavoro nella sezione "avvio del backup offline".
   * BitLocker è abilitato nel computer di copia.
   * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può essere il computer di origine.

     > [!IMPORTANT]
     > Se il computer di origine è una macchina virtuale, è obbligatorio usare un server fisico o un computer client diverso come computer di copia.

1. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente. Eseguire il comando seguente:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso di gestione temporanea*&gt; |Questo input obbligatorio viene usato per specificare il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "avvio del backup offline". |
    | p:&lt;*Percorso file delle impostazioni di pubblicazione*&gt; |Questo input facoltativo viene usato per specificare il percorso del file di impostazioni di pubblicazione di Azure immesso nel flusso di lavoro nella sezione "avvio del backup offline". |

    > [!NOTE]
    > Il valore &lt;Percorso FileImpostazioniPubblicazione&gt; è obbligatorio quando il computer di copia e il computer di origine sono diversi.
    >
    >

    Quando si esegue il comando, l'utilità richiede la selezione del processo di importazione di Azure corrispondente alle unità che devono essere preparate. Se al percorso di gestione temporanea fornito è associato un solo processo di importazione, verrà visualizzata una pagina simile alla seguente.

    ![Input dello strumento di preparazione dischi di Azure](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Immettere la lettera di unità (senza i due punti finali) corrispondente al disco montato che si vuole preparare per il trasferimento ad Azure. Quando richiesto, fornire una conferma per la formattazione dell'unità.

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario alleghi dischi aggiuntivi quando richiesto dallo strumento se il disco fornito non dispone di spazio sufficiente per i dati di backup. <br/>

    Al termine dell'esecuzione dello strumento, uno o più dischi forniti verranno preparati per la spedizione ad Azure. Un processo di importazione con il nome specificato durante il flusso di lavoro nella sezione "avvio del backup offline" viene creato anche in Azure. Lo strumento visualizza infine l'indirizzo del data center di Azure a cui i dischi devono essere spediti.

    ![Preparazione dischi di Azure completata](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. Al termine dell'esecuzione del comando, viene visualizzata anche l'opzione per aggiornare le informazioni sulla spedizione.

    ![Opzione Aggiorna informazioni di spedizione](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. È possibile immettere direttamente i dettagli. Lo strumento illustra il processo che include una serie di input. Se non si dispone di informazioni come il numero di tracciabilità o altri dettagli relativi alla spedizione, è possibile terminare la sessione. In questo articolo vengono forniti i passaggi per aggiornare i dettagli sulla spedizione in un secondo momento.

1. Spedire i dischi all'indirizzo fornito dallo strumento. Mantieni il numero di tracciabilità per riferimenti futuri.

   > [!IMPORTANT]
   > Due processi di importazione di Azure non possono avere lo stesso numero di tracciabilità. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano rilasciate insieme in un unico pacchetto e che esista un solo numero di tracciabilità univoco per il pacchetto. Non combinare le unità preparate come parte dei diversi processi di importazione di Azure in un singolo pacchetto.

1. Quando si dispone delle informazioni sul numero di tracciabilità, passare al computer di origine, in attesa del completamento del processo di importazione. Eseguire il comando seguente in un prompt dei comandi con privilegi elevati con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Facoltativamente, è possibile eseguire il comando seguente da un computer diverso, ad esempio il computer di copia, con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametro | Descrizione |
    | --- | --- |
    | u: | Questo input obbligatorio viene usato per aggiornare i dettagli di spedizione per un processo di importazione di Azure. |
    | s:&lt;*Percorso di gestione temporanea*&gt; | Questo input obbligatorio viene usato quando il comando non viene eseguito nel computer di origine. Viene usato per specificare il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "avviare il backup offline". |
    | p:&lt;*Percorso file delle impostazioni di pubblicazione*&gt; | Questo input obbligatorio viene usato quando il comando non viene eseguito nel computer di origine. Viene usato per specificare il percorso del file di impostazioni di pubblicazione di Azure immesso nel flusso di lavoro nella sezione "avviare il backup offline". |

    L'utilità rileva automaticamente il processo di importazione in attesa del computer di origine o i processi di importazione associati al percorso di gestione temporanea quando il comando viene eseguito in un computer diverso. Fornisce quindi l'opzione per aggiornare le informazioni di spedizione tramite una serie di input.

    ![Immettere le informazioni di spedizione](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Dopo aver fornito tutti gli input, esaminare attentamente i dettagli ed eseguire il commit delle informazioni di spedizione fornite immettendo **Sì**.

    ![Esaminare le informazioni sulla spedizione](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Al termine dell'aggiornamento delle informazioni di spedizione, l'utilità fornisce un percorso locale in cui vengono archiviati i dettagli di spedizione immessi.

    ![Archivia informazioni di spedizione](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Verificare che le unità raggiungano il Data Center di Azure entro due settimane dalla fornitura delle informazioni di spedizione tramite l'utilità *AzureOfflineBackupDiskPrep* . In caso contrario, le unità potrebbero non venire elaborate.

Dopo aver completato i passaggi precedenti, il Data Center di Azure è pronto per ricevere le unità ed elaborarle ulteriormente per trasferire i dati di backup dalle unità all'account di archiviazione di Azure di tipo classico creato.

### <a name="time-to-process-the-drives"></a>Tempo per l'elaborazione delle unità

La quantità di tempo necessaria per elaborare un processo di importazione di Azure varia. Il tempo di elaborazione dipende da fattori quali il tempo di spedizione, il tipo di processo, il tipo e le dimensioni dei dati da copiare e la dimensione dei dischi forniti. Il servizio importazione/esportazione di Azure non ha un contratto di servizio. Dopo la ricezione dei dischi, il servizio tenta di completare la copia dei dati di backup nell'account di archiviazione di Azure da 7 a 10 giorni. La sezione successiva descrive come è possibile monitorare lo stato del processo di importazione di Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorare lo stato del processo di importazione di Azure

Mentre le unità sono in transito o nel Data Center di Azure da copiare nell'account di archiviazione, l'agente di backup di Azure o DPM o la console di MAB nel computer di origine Mostra lo stato del processo seguente per i backup pianificati:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Per verificare lo stato del processo di importazione:

1. Aprire un prompt dei comandi con privilegi elevati nel computer di origine ed eseguire il comando seguente:

     `AzureOfflineBackupDiskPrep.exe u:`

1. L'output Mostra lo stato corrente del processo di importazione.

    ![Controllare lo stato del processo di importazione](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Per altre informazioni sui vari Stati del processo di importazione di Azure, vedere [visualizzare lo stato dei processi di importazione/esportazione di Azure](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Terminare il flusso di lavoro

Al termine del processo di importazione, i dati di backup iniziali saranno disponibili nell'account di archiviazione. Al momento del backup pianificato successivo, backup di Azure copia il contenuto dei dati dall'account di archiviazione nell'insieme di credenziali di servizi di ripristino.

   ![Copiare i dati nell'insieme di credenziali di servizi di ripristino](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale rispetto alla copia di backup iniziale.

## <a name="next-steps"></a>Passaggi successivi

* Per domande sul flusso di lavoro del servizio importazione/esportazione di Azure, vedere [usare il servizio importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB](../storage/common/storage-import-export-service.md).
