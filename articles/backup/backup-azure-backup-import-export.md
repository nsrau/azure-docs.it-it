---
title: Eseguire il seeding offline con il servizio importazione/esportazione di Azure
description: Informazioni su come usare backup di Azure per inviare dati dalla rete tramite il servizio importazione/esportazione di Azure. Questo articolo illustra il seeding offline dei dati di backup iniziali tramite il servizio importazione/esportazione di Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: f3cf44a34babab79d135923db040630a1c8e3dfe
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892015"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flusso di lavoro del backup offline in Backup di Azure

In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi, che trasferiscono solo i dati delta/incrementali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati di backup offline.

Il processo di seeding offline di backup di Azure è strettamente integrato con il [servizio importazione/esportazione di Azure](../storage/common/storage-import-export-service.md). È possibile usare questo servizio per trasferire i dati di backup iniziali in Azure usando dischi. Se si hanno terabyte (TBs) di dati di backup iniziali che devono essere trasferiti in una rete a bassa latenza e larghezza di banda ridotta, è possibile usare il flusso di lavoro di seeding offline per inviare la copia di backup iniziale in uno o più dischi rigidi a un Data Center di Azure. La figura seguente offre una panoramica dei passaggi del flusso di lavoro.

  ![Panoramica del processo del flusso di lavoro di importazione offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Il processo di backup offline prevede i passaggi seguenti:

1. Anziché inviare i dati di backup in rete, scriverli in un percorso di gestione temporanea.
1. Usare l'utilità *AzureOfflineBackupDiskPrep* per scrivere i dati nel percorso di gestione temporanea in uno o più dischi SATA.
1. Come parte del lavoro preparatorio, l'utilità *AzureOfflineBackupDiskPrep* crea un processo di importazione di Azure. Inviare le unità SATA al data center di Azure più vicino e fare riferimento al processo di importazione per connettere le attività.
1. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
1. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino e pianifica i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate

Le funzionalità o i carichi di lavoro di backup di Azure seguenti supportano l'uso del backup offline per:

> [!div class="checklist"]
>
> * Backup di file e cartelle con l'agente di Servizi di ripristino di Microsoft Azure (MARS), noto anche come agente di backup di Azure.
> * Backup di tutti i carichi di lavoro e i file con System Center Data Protection Manager (DPM).
> * Backup di tutti i carichi di lavoro e i file con Backup di Microsoft Azure server.

   > [!NOTE]
   > Il backup offline non è supportato per i backup dello stato del sistema eseguiti con l'agente di backup di Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisiti

  > [!NOTE]
  > I prerequisiti e il flusso di lavoro seguenti si applicano solo al backup offline di file e cartelle usando l' [agente di servizi di ripristino di Azure più recente](https://aka.ms/azurebackup_agent). Per eseguire i backup offline per i carichi di lavoro con System Center DPM o server di Backup di Azure, vedere [flusso di lavoro di backup offline per DPM e server di backup di Azure](backup-azure-backup-server-import-export.md).

Prima di avviare il flusso di lavoro di backup offline, completare i prerequisiti seguenti:

* Creare un insieme di credenziali di [servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per creare un insieme di credenziali, seguire i passaggi descritti in [creare un](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)insieme di credenziali di servizi di ripristino.
* Assicurarsi che sia installata solo la [versione più recente dell'agente di backup di Azure](https://aka.ms/azurebackup_agent) nel client Windows Server o Windows, come applicabile, e che il computer sia registrato con l'insieme di credenziali di servizi di ripristino.
* Azure PowerShell 3.7.0 è necessario nel computer che esegue l'agente di backup di Azure. Scaricare e [installare la versione 3.7.0 di Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Nel computer che esegue l'agente di backup di Azure verificare che sia installato Microsoft Edge o Internet Explorer 11 e che JavaScript sia abilitato.
* Creare un account di archiviazione di Azure nella stessa sottoscrizione dell'insieme di credenziali di servizi di ripristino.
* Assicurarsi di avere le [autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md) per creare l'applicazione Azure Active Directory. Il flusso di lavoro di backup offline crea un'applicazione Azure Active Directory nella sottoscrizione associata all'account di archiviazione di Azure. L'obiettivo dell'applicazione è fornire backup di Azure con accesso protetto e con ambito al servizio di importazione/esportazione di Azure, che è necessario per il flusso di lavoro di backup offline.
* Registrare il provider di risorse *Microsoft. importexport* con la sottoscrizione che contiene l'account di archiviazione di Azure. Per registrare il provider di risorse:
    1. Nel menu principale selezionare **sottoscrizioni**.
    1. Se si è iscritti a più sottoscrizioni, selezionare la sottoscrizione che si intende usare per il backup offline. Se si usa una sola sottoscrizione, viene visualizzata la sottoscrizione in uso.
    1. Scegliere **provider di risorse** dal menu sottoscrizione per visualizzare l'elenco dei provider.
    1. Nell'elenco dei provider scorrere verso il basso fino a *Microsoft. importexport*. Se lo **stato** è **NotRegistered**, selezionare **registra**.

        ![Registrare il provider di risorse](./media/backup-azure-backup-import-export/registerimportexport.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva (esterna o interna) del computer con spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si desidera eseguire il backup di un file server da 500 GB, assicurarsi che l'area di gestione temporanea sia almeno 500 GB. La capacità usata sarà inferiore a causa della compressione.
* Quando si inviano dischi ad Azure, usare solo i dischi rigidi interni SATA II/III a 2,5 2,5 pollice o 3,5-pollice. È possibile usare dischi rigidi con capacità fino a 10 TB. Vedere la documentazione del [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) per informazioni sul set di unità più recente supportato dal servizio.
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del percorso di gestione temporanea nell'unità SATA. Verificare che BitLocker sia abilitato nel computer di copia.

## <a name="workflow"></a>Flusso di lavoro

Questa sezione descrive il flusso di lavoro di backup offline in modo che i dati possano essere recapitati a un Data Center di Azure e caricati in archiviazione di Azure. Per domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione di [Panoramica del servizio importazione/esportazione di Azure](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Avviare il backup offline

1. Quando si pianifica un backup nell'agente di servizi di ripristino, viene visualizzata questa pagina.

    ![Importa pagina](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selezionare l'opzione **trasferimento usando i dischi**personali.

    > [!NOTE]
    > Utilizzare l'opzione Azure Data Box per trasferire i dati di backup iniziali offline. Questa opzione consente di risparmiare sul lavoro richiesto per ottenere i dischi compatibili con Azure. Offre dispositivi Microsoft, protetti e manomissione Azure Data Box in cui i dati di backup possono essere scritti direttamente dall'agente di servizi di ripristino.

1. Selezionare **Avanti**e compilare attentamente le caselle.

    ![Immettere i dettagli del disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   Le caselle compilate sono:

    * **Percorso di gestione temporanea**: Percorso di archiviazione temporanea in cui viene scritta la copia di backup iniziale. Il percorso di gestione temporanea potrebbe trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, specificare il percorso di rete completo del percorso di gestione temporanea.
    * **Account di archiviazione di Azure Resource Manager**: nome dell'account di archiviazione di tipo Resource Manager (utilizzo generico v1 o utilizzo generico v2) in qualsiasi sottoscrizione di Azure.
    * **Contenitore di archiviazione di Azure**: nome del contenitore di archiviazione BLOB di destinazione nell'account di archiviazione di Azure in cui vengono importati i dati di backup prima di essere copiati nell'insieme di credenziali di servizi di ripristino.
    * **ID sottoscrizione di Azure**: ID della sottoscrizione di Azure in cui viene creato l'account di archiviazione di Azure.
    * **Nome del processo di importazione di Azure**: nome univoco con cui il servizio importazione/esportazione di Azure e backup di Azure tengono traccia del trasferimento dei dati inviati su dischi ad Azure.
  
   Dopo aver compilato le caselle, fare clic su **Avanti**. Salvare il **percorso di gestione temporanea** e le informazioni sul **nome del processo di importazione di Azure** . È necessario preparare i dischi.

1. Quando richiesto, accedere alla sottoscrizione di Azure. È necessario eseguire l'accesso in modo che backup di Azure possa creare l'applicazione Azure Active Directory. Immettere le autorizzazioni necessarie per accedere al servizio importazione/esportazione di Azure.

    ![Pagina di accesso della sottoscrizione di Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Completare il flusso di lavoro. Nella console dell'agente di backup di Azure selezionare **Esegui backup ora**.

    ![Esegui backup](./media/backup-azure-backup-import-export/backupnow.png)

1. Nella pagina **conferma** della procedura guidata selezionare **backup**. Durante la configurazione il backup iniziale viene scritto nell'area di gestione temporanea.

   ![Confermare che si è pronti per eseguire il backup ora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Al termine dell'operazione il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

   ![Pagina backup della procedura guidata](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e spedire ad Azure

L'utilità *AzureOfflineBackupDiskPrep* prepara le unità SATA che vengono inviate al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di backup di Azure nel percorso seguente:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Passare alla directory e copiare la directory *AzureOfflineBackupDiskPrep* in un altro computer in cui sono connesse le unità SATA. Nel computer con le unità SATA connesse, verificare che:

   * Il computer di copia può accedere al percorso di gestione temporanea per il flusso di lavoro di seeding offline usando lo stesso percorso di rete fornito nel flusso di lavoro nella sezione "avvio del backup offline".
   * BitLocker è abilitato nel computer di copia.
   * Azure PowerShell 3.7.0 è installato.
   * Sono installati i browser compatibili più recenti (Microsoft Edge o Internet Explorer 11) e JavaScript è abilitato.
   * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può essere il computer di origine.

     > [!IMPORTANT]
     > Se il computer di origine è una macchina virtuale, il computer di copia deve essere un server fisico o un computer client diverso dal computer di origine.

1. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente. Eseguire il comando seguente:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso di gestione temporanea*&gt; |Questo input obbligatorio viene usato per specificare il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "avvio del backup offline". |
    | p:&lt;*Percorso file delle impostazioni di pubblicazione*&gt; |Questo input facoltativo viene usato per specificare il percorso del file di impostazioni di pubblicazione di Azure.  |

    Quando si esegue il comando, l'utilità richiede la selezione del processo di importazione di Azure corrispondente alle unità che devono essere preparate. Se al percorso di gestione temporanea fornito è associato un solo processo di importazione, verrà visualizzata una pagina simile alla seguente.

    ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Immettere la lettera di unità (senza i due punti finali) corrispondente al disco montato che si vuole preparare per il trasferimento ad Azure.
1. Quando richiesto, confermare la formattazione dell'unità.
1. Viene richiesto di accedere alla sottoscrizione di Azure. Immettere le credenziali.

    ![Accesso alla sottoscrizione di Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario alleghi dischi aggiuntivi quando richiesto dallo strumento se il disco fornito non dispone di spazio sufficiente per i dati di backup. <br/>

    Al termine dell'esecuzione dello strumento, il prompt dei comandi include tre informazioni:

   1. Uno o più dischi sono pronti per la spedizione ad Azure.
   1. Si riceverà la conferma che il processo di importazione è stato creato. Il processo di importazione usa il nome specificato.
   1. Lo strumento visualizza l'indirizzo di spedizione del data center Azure.

      ![Preparazione dischi di Azure completata](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Al termine dell'esecuzione del comando è possibile aggiornare le informazioni sulla spedizione.

1. Spedire i dischi all'indirizzo fornito dallo strumento. Mantieni il numero di tracciabilità per riferimenti futuri.

   > [!IMPORTANT]
   > Due processi di importazione di Azure non possono avere lo stesso numero di tracciabilità. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano rilasciate insieme in un unico pacchetto e che esista un solo numero di tracciabilità univoco per il pacchetto. Non combinare le unità preparate come parte di processi di importazione di Azure separati in un singolo pacchetto.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aggiornare i dettagli sulla spedizione nel processo di importazione di Azure

La procedura seguente aggiorna i dettagli di spedizione del processo di importazione di Azure. Le informazioni includono i dettagli seguenti:

* Nome del vettore che recapita i dischi ad Azure.
* Restituisce i dettagli relativi alla spedizione per i dischi.

1. Accedere alla sottoscrizione di Azure.
1. Nel menu principale selezionare **tutti i servizi**. Nella finestra di dialogo **tutti i servizi** immettere **Import**. Quando vengono visualizzati i **processi di importazione/esportazione**, selezionarli.

    ![Immettere le informazioni di spedizione](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Viene visualizzato il menu **processi di importazione/esportazione** e viene visualizzato l'elenco di tutti i processi di importazione/esportazione nella sottoscrizione selezionata.

1. Se si dispone di più sottoscrizioni, selezionare la sottoscrizione utilizzata per importare i dati di backup. Quindi selezionare il processo di importazione appena creato per aprirne i dettagli.

    ![Esaminare le informazioni sulla spedizione](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Nel menu **Impostazioni** del processo di importazione selezionare **Gestisci informazioni di spedizione**. Immettere i dettagli relativi alla spedizione di ritorno.

    ![Archivia informazioni di spedizione](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando si dispone del numero di tracciabilità del vettore di spedizione, selezionare il banner nella pagina Panoramica del processo di importazione di Azure e immettere i dettagli seguenti.

   > [!IMPORTANT]
   > Verificare che le informazioni del vettore e il numero di registrazione siano aggiornati entro due settimane dalla creazione del processo di importazione di Azure. La mancata verifica delle informazioni entro due settimane può comportare l'eliminazione del processo e l'elaborazione delle unità.

   ![Avviso di aggiornamento informazioni di rilevamento](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informazioni sul vettore e numero di tracciabilità](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo per l'elaborazione delle unità

La quantità di tempo necessaria per elaborare un processo di importazione di Azure varia. Il tempo di elaborazione si basa su fattori quali il tempo di spedizione, il tipo di processo, il tipo e le dimensioni dei dati copiati e le dimensioni dei dischi forniti. Il servizio importazione/esportazione di Azure non ha un contratto di servizio. Dopo la ricezione dei dischi, il servizio tenta di completare la copia dei dati di backup nell'account di archiviazione di Azure da 7 a 10 giorni.

### <a name="monitor-azure-import-job-status"></a>Monitorare lo stato del processo di importazione di Azure

È possibile monitorare lo stato del processo di importazione dal portale di Azure. Passare alla pagina **importazione/esportazione processi** e selezionare il processo. Per altre informazioni sullo stato dei processi di importazione, vedere [che cos'è il servizio importazione/esportazione di Azure?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Terminare il flusso di lavoro

Dopo aver terminato correttamente il processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. Al momento del backup pianificato successivo, backup di Azure copia il contenuto dei dati dall'account di archiviazione nell'insieme di credenziali di servizi di ripristino.

   ![Copiare i dati nell'insieme di credenziali di servizi di ripristino](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale.

### <a name="clean-up-resources"></a>Pulizia delle risorse

Al termine del backup iniziale, è possibile eliminare in modo sicuro i dati importati nel contenitore di archiviazione di Azure e i dati di backup nel percorso di gestione temporanea.

## <a name="next-steps"></a>Passaggi successivi

* Per domande sul flusso di lavoro del servizio importazione/esportazione di Azure, vedere [usare il servizio importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB](../storage/common/storage-import-export-service.md).
