---
title: Backup offline per DPM e server di Backup di Azure
description: Con backup di Azure è possibile inviare dati fuori rete usando il servizio importazione/esportazione di Azure. Questo articolo illustra il flusso di lavoro di backup offline per DPM e server di Backup di Azure.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 368ae846a24ec04ee4b7da9b5971c00180be611d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378458"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Flusso di lavoro di backup offline per DPM e server di Backup di Azure (MAB)

>[!IMPORTANT]
> Questi passaggi sono applicabili per DPM 2019 UR1 (o versione successiva) e MAB V3 UR1 (o versione successiva).

System Center Data Protection Manager and server di Backup di Azure (MAB) si integrano con backup di Azure e usano diverse efficienze predefinite che consentono di risparmiare sui costi di rete e di archiviazione durante i backup completi iniziali dei dati in Azure. I backup completi iniziali in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete rispetto ai backup successivi, che trasferiscono solo i dati delta/incrementali. Backup di Azure comprime i backup iniziali. Tramite il processo di seeding offline, Backup di Azure può usare i dischi per il caricamento offline dei dati di backup iniziali compressi in Azure.

Il processo di seeding offline di backup di Azure è strettamente integrato con il [servizio importazione/esportazione di Azure](../storage/common/storage-import-export-service.md). È possibile usare questo servizio per trasferire i dati in Azure usando dischi. Se si hanno terabyte (TBs) di dati di backup iniziali che devono essere trasferiti in una rete a bassa latenza e larghezza di banda ridotta, è possibile usare il flusso di lavoro di seeding offline per inviare la copia di backup iniziale su uno o più dischi rigidi a un Data Center di Azure. Questo articolo fornisce una panoramica e altri passaggi che completano il flusso di lavoro per System Center Data Protection Manager (DPM) e Backup di Microsoft Azure Server (MAB).

> [!NOTE]
> Il processo di backup non in linea per l'agente Servizi di ripristino di Microsoft Azure (MARS) è diverso da DPM e da MAB. Per informazioni sull'uso del backup offline con l'agente MARS, vedere [flusso di lavoro di backup offline in backup di Azure](backup-azure-backup-import-export.md). Il backup offline non è supportato per i backup dello stato del sistema eseguiti con l'agente di backup di Azure.
>
> L'aggiornamento di UR1 di MAB consente inoltre di visualizzare l'anteprima per il backup offline usando Azure Data Box in MAB. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Per ulteriori informazioni, contattare.

## <a name="overview"></a>Panoramica

Con la funzionalità di seeding offline di backup di Azure e il servizio importazione/esportazione di Azure, è semplice caricare i dati offline in Azure usando i dischi. Il processo di backup offline prevede i passaggi seguenti:

> [!div class="checklist"]
>
> * I dati di backup vengono scritti in un percorso di gestione temporanea anziché essere inviati in rete.
> * I dati nel percorso di gestione temporanea vengono quindi scritti in uno o più dischi SATA usando l'utilità *AzureOfflineBackupDiskPrep* .
> * Un processo di importazione di Azure viene creato automaticamente dall'utilità.
> * Le unità SATA vengono quindi inviate al Data Center di Azure più vicino.
> * Al termine del caricamento dei dati di backup in Azure, Backup di Azure copia i dati di backup nell'insieme di credenziali per il backup e quindi vengono pianificati i backup incrementali.

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare il flusso di lavoro di backup offline, verificare che siano soddisfatti i seguenti prerequisiti:

* È stato creato un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per crearne uno, seguire i passaggi descritti in [creare un](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)insieme di credenziali di servizi di ripristino esercitazione-backup-Windows-Server-to-Azure # create-a-Recovery-Services-Vault.
* Assicurarsi che sia installata solo la [versione più recente di servizi di ripristino di Microsoft Azure Agent](https://aka.ms/azurebackup_agent) in SC DPM o MAB e registrata nell'insieme di credenziali di servizi di ripristino.
* L'aggiornamento cumulativo 1 è installato in SC DPM 2019 o MAB V3.

  > [!NOTE]
  > Con DPM 2019 UR1 e MAB V3 UR1 il seeding offline esegue l'autenticazione con Azure Active Directory.

* Nel server DPM o MAB verificare che sia installato Microsoft Edge o Internet Explorer 11 e che JavaScript sia abilitato.
* Creare un account di Archiviazione di Azure nella stessa sottoscrizione dell'insieme di credenziali di Servizi di ripristino.
* Assicurarsi di avere le [autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md) per creare l'applicazione Azure Active Directory. Il flusso di lavoro di backup offline crea un'applicazione Azure Active Directory nella sottoscrizione associata all'account di Archiviazione di Azure. L'obiettivo dell'applicazione è offrire a Backup di Azure un accesso protetto e dotato di ambito al servizio di importazione di Azure, necessario per il flusso di lavoro di backup offline.
* Registrare il provider di risorse Microsoft.ImportExport con la sottoscrizione che contiene l'account di Archiviazione di Azure. Per registrare il provider di risorse:
    1. Nel menu principale selezionare **sottoscrizioni**.
    2. Se si è effettuata la sottoscrizione a più sottoscrizioni, selezionare la sottoscrizione in uso per il backup offline. Se si usa una sola sottoscrizione, viene visualizzata la sottoscrizione in uso.
    3. Nel menu sottoscrizione selezionare provider di **risorse** per visualizzare l'elenco dei provider.
    4. Nell'elenco dei provider scorrere verso il basso fino a Microsoft.ImportExport. Se lo stato è NotRegistered, selezionare **registra**.

       ![Registrazione del provider di risorse](./media/backup-azure-backup-server-import-export/register-import-export.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva (esterna o interna) del computer con spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si desidera eseguire il backup di un file server da 500 GB, assicurarsi che l'area di gestione temporanea sia almeno 500 GB. La capacità usata sarà inferiore a causa della compressione.
* Per i dischi inviati ad Azure, assicurarsi che vengano usati solo dischi rigidi SSD da 2,5 pollici o 2,5-pollice o 3,5-inch SATA II/III. È possibile usare dischi rigidi con capacità fino a 10 TB. Vedere la documentazione del [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) per informazioni sul set di unità più recente supportato dal servizio.
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del percorso di gestione temporanea nell'unità SATA. Verificare che BitLocker sia abilitato nel computer di copia.

## <a name="workflow"></a>Flusso di lavoro

Le informazioni incluse in questa sezione assistono nel completamento del flusso di lavoro di backup offline, in modo che i dati possano essere recapitati a un data center di Azure e caricati in Archiviazione di Azure. Per informazioni sul servizio Importazione o su qualsiasi aspetto del processo, vedere la documentazione della [panoramica del servizio Importazione](../storage/common/storage-import-export-service.md) citata in precedenza.

## <a name="initiate-offline-backup"></a>Avviare il backup offline

1. Quando si crea un nuovo gruppo protezione dati con protezione online o si aggiunge la protezione online a un gruppo protezione dati esistente, viene visualizzata la schermata seguente. Per selezionare il metodo di replica online iniziale, selezionare **Trasferisci usando il disco personale** e fare clic su **Avanti**.

    ![Schermata di importazione](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Viene visualizzata la pagina di accesso di Azure. Accedere con l'account utente di Azure, che ha l'autorizzazione del ruolo *proprietario* per la sottoscrizione di Azure.

    ![Pagina di accesso di Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Specificare gli input nella pagina **Usare i dettagli dei propri dischi**.

   ![Input per l'uso del disco](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   La descrizione degli input è la seguente:

   * **Percorso di gestione temporanea**: Percorso di archiviazione temporanea in cui viene scritta la copia di backup iniziale. Il percorso di gestione temporanea potrebbe trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, specificare il percorso di rete completo del percorso di gestione temporanea.
   * **Account di archiviazione di Azure Resource Manager**: nome dell'account di archiviazione di tipo Resource Manager (utilizzo generico v1 o utilizzo generico v2) in qualsiasi sottoscrizione di Azure.
   * **Contenitore di Archiviazione di Azure**: nome del contenitore di archiviazione BLOB di destinazione nell'account di archiviazione di Azure in cui vengono importati i dati di backup.
   * **ID sottoscrizione di Azure**: ID della sottoscrizione di Azure in cui viene creato l'account di archiviazione di Azure.
   * **Nome processo di importazione di Azure**: Nome univoco con cui il servizio di importazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati su dischi ad Azure.

    Salvare il **percorso di gestione temporanea** e le informazioni sul **nome del processo di importazione di Azure** specificate. È necessario preparare i dischi.

4. Completare il flusso di lavoro per creare o aggiornare la protezione. Per avviare la creazione della copia di backup offline, fare clic con il pulsante destro del mouse sul **Gruppo protezione dati** e quindi scegliere l'opzione **Crea punto di ripristino**. Scegliere quindi l'opzione **Protezione dati online**.

   ![Crea punto di ripristino](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Monitorare il processo Creazione della replica online nel riquadro di monitoraggio. Il processo verrà completato correttamente con l'avviso *In attesa del completamento del processo di importazione di Azure*.

   ![Punto di ripristino completo](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Al termine dell'operazione il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e spedire ad Azure

L'utilità *AzureOfflineBackupDiskPrep* prepara le unità SATA che vengono inviate al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di backup di Azure (nel percorso seguente): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Passare alla directory e copiare la directory **AzureOfflineBackupDiskPrep** in un altro computer al quale sono connesse le unità SATA. Nel computer con le unità SATA connesse, verificare quanto segue:

   * Il computer di copia può accedere al percorso di gestione temporanea per il flusso di lavoro di seeding offline usando lo stesso percorso di rete fornito nel flusso di lavoro nella sezione "avvio del backup offline".
   * BitLocker è abilitato nel computer di copia.
   * Azure PowerShell 3.7.0 è installato nel computer di copia (non necessario se si esegue l'utilità AzureOfflineBackupDiskPrep sul server DPM o MAB).
   * Sono installati i browser compatibili più recenti (Microsoft Edge o Internet Explorer 11) e JavaScript è abilitato.
   * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può essere il computer di origine.

     > [!IMPORTANT]
     > Se il computer di origine è una macchina virtuale, è obbligatorio usare un server fisico o un computer client diverso come computer di copia.

1. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente. Eseguire il comando seguente:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso di gestione temporanea*&gt; |Questo input obbligatorio viene usato per specificare il percorso del percorso di gestione temporanea immesso nel flusso di lavoro nella sezione "avvio del backup offline". |
    | p:&lt;*Percorso file delle impostazioni di pubblicazione*&gt; |Questo input facoltativo viene usato per specificare il percorso del file di impostazioni di pubblicazione di Azure. |

    Quando si esegue il comando, l'utilità richiede la selezione del processo di importazione di Azure corrispondente alle unità che devono essere preparate. Se al percorso di gestione temporanea indicato è associato un solo processo di importazione, viene visualizzata una schermata simile alla seguente.

      ![Console preparazione disco](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Immettere la lettera di unità (senza i due punti finali) corrispondente al disco montato che si vuole preparare per il trasferimento ad Azure.
1. Quando richiesto, confermare la formattazione dell'unità.
1. Viene richiesto di accedere alla sottoscrizione di Azure. Immettere le credenziali.

    ![Schermata di accesso di Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario alleghi dischi aggiuntivi quando richiesto dallo strumento se il disco fornito non dispone di spazio sufficiente per i dati di backup. <br/>

    Al termine dell'esecuzione dello strumento, il prompt dei comandi include tre informazioni:
    * Uno o più dischi sono pronti per la spedizione ad Azure.
    * Si riceve la conferma che il processo di importazione è stato creato. Il processo di importazione usa il nome specificato.
    * Lo strumento visualizza l'indirizzo di spedizione del data center Azure.

     ![Preparazione dischi di Azure completata](./media/backup-azure-backup-server-import-export/console.png)

1. Al termine dell'esecuzione del comando, viene visualizzata anche l'opzione per aggiornare le informazioni sulla spedizione.

1. Spedire i dischi all'indirizzo specificato dallo strumento e conservare il numero di registrazione per riferimenti futuri.

   > [!IMPORTANT]
   > Due processi di importazione di Azure non possono avere lo stesso numero di tracciabilità. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano rilasciate insieme in un unico pacchetto e che esista un solo numero di tracciabilità univoco per il pacchetto. Non combinare le unità preparate come parte dei diversi processi di importazione di Azure in un singolo pacchetto.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aggiornare i dettagli sulla spedizione nel processo di importazione di Azure

La procedura seguente aggiorna i dettagli di spedizione del processo di importazione di Azure. Le informazioni includono i dettagli seguenti:

* nome del vettore che consegna i dischi ad Azure
* dettagli della spedizione per reso dei dischi

   1. Accedere alla sottoscrizione di Azure.
   2. Nel menu principale selezionare tutti i **Servizi** e nella finestra di dialogo tutti i servizi digitare Import. Quando vengono visualizzati i **processi di importazione/esportazione**, selezionarli.
       ![Immissione delle informazioni di spedizione](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Viene visualizzato il menu **Processi di importazione/esportazione** con l'elenco dei processi di importazione/esportazione della sottoscrizione selezionata.

   3. Se si hanno più sottoscrizioni, assicurarsi di selezionare la sottoscrizione usata per importare i dati di backup. Quindi selezionare il processo di importazione appena creato per aprire i dettagli corrispondenti.

       ![Esaminare le informazioni sulla spedizione](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. Nel menu impostazioni del processo di importazione selezionare **Gestisci informazioni di spedizione** e immettere i dettagli relativi alla spedizione di ritorno.

       ![Archiviazione delle informazioni di spedizione](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Quando si dispone del numero di tracciabilità del vettore di spedizione, selezionare il banner nella pagina Panoramica del processo di importazione di Azure e immettere i dettagli seguenti:

      > [!IMPORTANT]
      > Verificare che le informazioni del vettore e il numero di registrazione siano aggiornati entro due settimane dalla creazione del processo di importazione di Azure. Se non si verificano le informazioni entro due settimane è possibile che il processo venga eliminato e che le unità non vengano elaborate.

      ![Panoramica del processo](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Informazioni di rilevamento](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo per l'elaborazione delle unità

La quantità di tempo necessaria per elaborare un processo di importazione di Azure varia. Il tempo di elaborazione dipende da fattori quali il tempo di spedizione, il tipo di processo, il tipo e le dimensioni dei dati da copiare e la dimensione dei dischi forniti. Il servizio importazione/esportazione di Azure non ha un contratto di servizio. Dopo la ricezione dei dischi, il servizio tenta di completare la copia dei dati di backup nell'account di archiviazione di Azure da 7 a 10 giorni. La sezione successiva descrive come è possibile monitorare lo stato del processo di importazione di Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorare lo stato del processo di importazione di Azure

È possibile monitorare lo stato del processo di importazione nel portale di Azure, passando alla pagina **Processi di importazione/esportazione** e selezionando il processo. Per altre informazioni sullo stato dei processi di importazione, vedere l'articolo relativo al [servizio Importazione/Esportazione](../storage/common/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Completare il flusso di lavoro

Al termine del processo di importazione, i dati di backup iniziali saranno disponibili nell'account di archiviazione. Al momento del backup pianificato successivo, backup di Azure copia il contenuto dei dati dall'account di archiviazione nell'insieme di credenziali di servizi di ripristino.

Al momento del successivo processo di creazione di replica online pianificato, Data Protection Manager esegue il backup incrementale sulla copia di backup iniziale.

## <a name="next-steps"></a>Passaggi successivi

* Per domande sul flusso di lavoro del servizio importazione/esportazione di Azure, vedere [usare il servizio importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB](../storage/common/storage-import-export-service.md).
