---
title: 'Backup di Azure: backup offline o seeding iniziale con il servizio Importazione/Esportazione di Azure'
description: Informazioni sull'uso di Backup di Azure per l'invio di dati offline tramite il servizio Importazione/Esportazione di Azure. Questo articolo illustra il seeding offline dei dati del backup iniziale tramite il servizio Importazione/Esportazione di Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: b55c5bc6096186e338d6960190169d5f4acc777d
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955134"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flusso di lavoro del backup offline in Backup di Azure
In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati di backup offline.

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md) che consente di trasferire i dati del backup iniziale in Azure usando dischi. Se si hanno terabyte (TB) di dati del backup iniziale da trasferire su una rete con latenza elevata e larghezza di banda ridotta, è possibile usare il flusso di lavoro del seeding offline per spedire la copia di backup iniziale su uno o più dischi rigidi a un data center di Azure. La figura seguente offre una panoramica dei passaggi del flusso di lavoro.

  ![Panoramica del processo del flusso di lavoro di importazione offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Il processo di backup offline prevede i passaggi seguenti:

1. Anziché inviare i dati di backup in rete, scriverli in un percorso di gestione temporanea.
2. Usare l'utilità AzureOfflineBackupDiskPrep per scrivere i dati nel percorso di gestione temporanea in uno o più dischi SATA.
3. Come parte delle operazioni preparatorie, l'utilità AzureOfflineBackupDiskPrep crea un processo di Importazione di Azure. Inviare le unità SATA al data center di Azure più vicino e fare riferimento al processo di importazione per connettere le attività.
4. Nel data center di Azure i dati sui dischi vengono copiati in un account di archiviazione di Azure.
5. Backup di Azure copia i dati di backup dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino e pianifica i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate 
Le funzionalità o i carichi di lavoro seguenti di Backup di Azure supportano l'uso del backup offline.

> [!div class="checklist"]
> * Backup di file e cartelle con l'agente di Servizi di ripristino di Microsoft Azure, definito anche agente di Backup di Azure. 
> * Backup di tutti i carichi di lavoro e i file con System Center Data Protection Manager (SC DPM) 
> * Backup di tutti i carichi di lavoro e i file con il server di Backup di Microsoft Azure <br/>

   > [!NOTE]
   > Il backup offline non è supportato per i backup dello stato del sistema eseguiti tramite l'agente di Backup di Azure. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Prerequisiti

  > [!NOTE]
  > I prerequisiti e il flusso di lavoro seguenti si applicano solo al backup offline di file e cartelle mediante l'[agente di Servizi di ripristino di Microsoft Azure più recente](https://aka.ms/azurebackup_agent). Per eseguire i backup offline dei carichi di lavoro con System Center DPM o il server di Backup di Azure, fare riferimento a [questo articolo](backup-azure-backup-server-import-export-.md). 

Prima di avviare il flusso di lavoro del backup offline completare i prerequisiti seguenti: 
* Creare un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per crearne uno, vedere la procedura descritta in [questo articolo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Assicurarsi di aver installato solo l'[ultima versione dell'agente di Backup di Azure](https://aka.ms/azurebackup_agent) su Windows Server o nel client Windows, a seconda dei casi, e di aver registrato il computer con l'insieme di credenziali di Servizi di ripristino.
* Sui computer che eseguono l'agente di Backup di Azure è necessario usare Azure PowerShell 3.7.0. È consigliabile scaricare e [installare la versione 3.7.0 di Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Nel computer che esegue l'agente di Backup di Azure verificare che siano installati Microsoft Edge o Internet Explorer 11 e che JavaScript sia abilitato. 
* Creare un account di Archiviazione di Azure nella stessa sottoscrizione dell'insieme di credenziali dei Servizi di ripristino. 
* Assicurarsi di avere le [autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md) per creare l'applicazione Azure Active Directory. Il flusso di lavoro del backup offline crea un'applicazione di Azure Active Directory nella sottoscrizione associata all'account di Archiviazione di Azure. L'obiettivo dell'applicazione consiste nel garantire a Backup di Azure un accesso protetto con ambito al servizio Importazione di Azure, necessario per il flusso di lavoro del backup offline. 
* Registrare il provider di risorse Microsoft.ImportExport nella sottoscrizione che contiene l'account di Archiviazione di Azure. Per registrare il provider di risorse:
    1. Nel menu principale fare clic su **Sottoscrizioni**.
    2. Se sono state sottoscritte più sottoscrizioni, selezionare la sottoscrizione in uso per il backup offline. Se si usa una sola sottoscrizione, questa verrà visualizzata.
    3. Nel menu della sottoscrizione fare clic su **Provider di risorse** per visualizzare l'elenco dei provider.
    4. Nell'elenco dei provider scorrere fino a Microsoft.ImportExport. Se lo stato è NotRegistered fare clic su **Registra**.
    ![Registrazione del provider di risorse](./media/backup-azure-backup-import-export/registerimportexport.png)
* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva nel computer, interna o esterna, con spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si prevede di eseguire il backup di un file server da 500 GB, verificare che la dimensione dell'area di staging sia di almeno 500 GB. Verrà tuttavia usata una quantità inferiore in virtù della compressione.
* Quando si inviano i dischi in Azure, usare solo unità SSD da 2,5 pollici o dischi rigidi interni SATA II/III da 2,5 o da 3,5 pollici. È possibile usare dischi rigidi fino a 10 TB. Per informazioni sul set più recente di unità supportato dal servizio, vedere la [documentazione del servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-requirements.md#supported-hardware).
* Le unità SATA devono essere connesse a un computer, denominato *computer di copia*, da cui vengono copiati i dati di backup del *percorso di gestione temporanea* nell'unità SATA. Verificare che nel *computer di copia* sia abilitato BitLocker.

## <a name="workflow"></a>Flusso di lavoro
Questa sezione descrive il flusso di lavoro di backup offline in modo che i dati possano essere inviati a un data center di Azure e caricati in Archiviazione di Azure. Per domande sul servizio Importazione o su qualsiasi aspetto del processo, vedere la [documentazione sulla panoramica del servizio Importazione](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Avviare il backup offline
1. Quando si pianifica un backup per l'agente di Servizi di ripristino di Microsoft Azure, viene visualizzata la schermata seguente.

    ![Schermata di importazione](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Di seguito è riportata la descrizione degli input.

    * **Percorso di gestione temporanea**: posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Il percorso di gestione temporanea può trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, è consigliabile specificare il percorso di rete completo per la gestione temporanea.
    * **Azure Resource Manager Storage Acccount** ( Account di archiviazione di Azure Resource Manager): nome dell'account di archiviazione di tipo Resource Manager in un sottoscrizione di Azure.
    * **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di Archiviazione di Azure in cui verranno importati i dati del backup prima di essere copiati nell'insieme di credenziali di Servizi di ripristino.
    * **ID sottoscrizione di Azure**: ID per la sottoscrizione di Azure in cui viene creato l'account di Archiviazione di Azure.
    * **Nome processo di importazione di Azure**: nome univoco con cui il servizio di importazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati in Azure usando dischi. 
  
  Specificare gli input sullo schermo, quindi fare clic su **Avanti**. Salvare i valori di *Percorso di gestione temporanea* e *Nome del processo di importazione di Azure* specificati perché sono necessari per preparare i dischi.

2. Quando verrà richiesto, accedere alla sottoscrizione di Azure. È necessario accedere in modo che Backup di Azure possa creare l'applicazione di Azure Active Directory e dare le autorizzazioni necessarie per accedere al servizio Importazione di Azure.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/azurelogin.png)

3. Completare il flusso di lavoro e nella console dell'agente di Backup di Azure fare clic su **Back Up Now** (Esegui backup).

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

4. Nella pagina di conferma della procedura guidata fare clic su **Backup**. Durante la configurazione il backup iniziale viene scritto nell'area di gestione temporanea.

   ![Confermare di essere pronti a eseguire il backup](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Al termine dell'operazione, il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

   ![Esegui backup ora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e inviarle al data center di Azure
L'utilità *AzureOfflineBackupDiskPrep* prepara le unità SATA da inviare al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di Backup di Azure al percorso seguente:

   *\Agente di Servizi di ripristino di Microsoft Azure\Utilità\\*

1. Passare alla directory e copiare la directory **AzureOfflineBackupDiskPrep** in un altro computer in cui sono collegate le unità SATA. Nel computer con le unità SATA collegate assicurarsi che:

    * Il percorso di gestione temporanea specificato per il flusso di lavoro del seeding offline è accessibile dal computer di copia usando lo stesso percorso di rete specificato durante il flusso di lavoro di **avvio del backup offline** .
    * Nel computer di copia è abilitato BitLocker.
    * Azure PowerShell 3.7.0 è installato.
    * Siano installate le versioni più recenti di browser compatibili, ovvero Microsoft Edge o Internet Explorer 11, e che JavaScript è abilitato. 
    * Il computer di copia può accedere al portale di Azure. Se necessario, il computer di copia può coincidere con il computer di origine.
    
    > [!IMPORTANT] 
    > Se il computer di origine è una macchina virtuale, usare un server fisico diverso o un computer client come computer di copia del computer di origine.

2. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente ed eseguire il comando seguente:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametro | DESCRIZIONE |
    | --- | --- |
    | s:&lt;*Percorso posizione staging*&gt; |Input obbligatorio usato per specificare il percorso di gestione temporanea immesso durante il flusso di lavoro di **avvio del backup offline**. |
    | p:&lt;*Percorso FileImpostazioniPubblicazione*&gt; |Input facoltativo usato per specificare il percorso del file delle **impostazioni di pubblicazione di Azure** immesso durante il flusso di lavoro di **avvio del backup offline**. |

    Quando si esegue il comando, l'utilità richiede di selezionare il processo di importazione di Azure corrispondente alle unità da preparare. Se al percorso di gestione temporanea specificato è associato un unico processo di importazione, viene visualizzata una schermata come la seguente.

    ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Immettere la lettera di unità del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali. 
4. Quando richiesto, confermare la formattazione dell'unità.
5. Verrà richiesto di accedere alla sottoscrizione di Azure. Fornire le credenziali.

    ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento, nel caso in cui lo spazio sul disco specificato non sia sufficiente a contenere i dati di backup. <br/>

    Dopo aver eseguito correttamente lo strumento, il prompt dei comandi presenta tre tipi di informazioni:
    1. Uno o più dischi indicati sono stati preparati per la spedizione in Azure. 
    2. Si riceve la conferma della creazione del processo di importazione. Il processo di importazione usa il nome specificato.
    3. Lo strumento consente di visualizzare l'indirizzo di spedizione per il data center di Azure.

    ![Preparazione dischi di Azure completata](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Al termine dell'esecuzione del comando, è possibile aggiornare le informazioni di spedizione.

7. Spedire i dischi all'indirizzo indicato nello strumento e conservare il numero di tracciabilità come riferimento futuro.

   > [!IMPORTANT] 
   > Due processi di importazione di Azure non possono avere lo stesso numero di tracciabilità. Verificare che le unità preparate dall'utilità in un unico processo di importazione di Azure vengano spedite insieme in un unico pacchetto e che vi sia un solo numero di tracciabilità univoco per il pacchetto. Non combinare unità preparate previste dai processi di Importazione di Azure in un singolo pacchetto.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aggiornare i dettagli di spedizione nel processo di Importazione di Azure

La procedura seguente aggiorna i dettagli di spedizione del processo di Importazione di Azure. Sono incluse le informazioni seguenti:
* nome del vettore che recapita i dischi in Azure
* informazioni di restituzione per i dischi

1. Accedere alla sottoscrizione di Azure.
2. Nel menu principale fare clic su **Tutti i servizi** e nella finestra di dialogo Tutti i servizi digitare Importa. Quando viene visualizzato **Processi di importazione/esportazione** fare clic su di esso.
    ![Immissione di informazioni sulla spedizione](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Si apre l'elenco del menu **Processi di importazione/esportazione** e viene visualizzato l'elenco di tutti i processi di importazione/esportazione nella sottoscrizione selezionata. 

3. Se si dispone di più sottoscrizioni, assicurarsi di selezionare la sottoscrizione usata per importare i dati di backup. Selezionare quindi il processo di importazione appena creato per aprirne i dettagli.

    ![Verifica delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Nel menu Impostazioni del processo di importazione fare clic su **Gestisci informazioni sulla spedizione** e immettere i dettagli di restituzione.

    ![Archiviazione delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Quando si recupera il numero di tracciabilità dal vettore di spedizione, fare clic sul banner nella pagina di panoramica del processo di Importazione di Azure e immettere i dettagli seguenti:

   > [!IMPORTANT] 
   > Verificare che vengano aggiornate le informazioni sul vettore e il numero di tracciabilità entro due settimane dalla creazione del processo di importazione di Azure. Nel caso non si effettui questa verifica entro due settimane è possibile che il processo venga cancellato e che le unità non vengano elaborate.

   ![Archiviazione delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Archiviazione delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo di elaborazione delle unità 
Il tempo impiegato per elaborare un processo di importazione di Azure varia in base a diversi fattori, ad esempio i tempi di spedizione, il tipo di processo, il tipo e la dimensione dei dati da copiare e la dimensione dei dischi forniti. Il servizio di importazione/esportazione di Azure non dispone di un contratto di servizio, ma dopo la ricezione dei dischi il servizio tenta di completare la copia dei dati di backup nell'account di archiviazione di Azure in un intervallo compreso tra i 7 e i 10 giorni. 

### <a name="monitoring-azure-import-job-status"></a>Monitoraggio dello stato del processo di importazione di Azure
È possibile monitorare lo stato del processo di importazione nel portale di Azure passando alla pagina **Processi di importazione/esportazione** e selezionando il processo. Per altre informazioni sullo stato dei processi di importazione, vedere l'articolo sul [servizio di archiviazione dei processi di importazione/esportazione](../storage/common/storage-import-export-service.md).

### <a name="complete-the-workflow"></a>Completare il flusso di lavoro
Dopo aver terminato correttamente il processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. In occasione del backup pianificato successivo, Backup di Azure copia il contenuto dei dati dall'account di archiviazione nell'insieme di credenziali di Servizi di ripristino, come illustrato di seguito: 

   ![Copia dei dati nell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale.

### <a name="cleaning-up-resources"></a>Pulizia delle risorse
Dopo aver completato il backup iniziale, è possibile eliminare in modo sicuro i dati importati nel contenitore di Archiviazione di Azure e i dati di backup nel percorso di gestione temporanea.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul flusso di lavoro di Importazione/Esportazione di Azure, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../storage/common/storage-import-export-service.md).
* Per domande sul flusso di lavoro, vedere la sezione relativa al backup offline delle [domande frequenti](backup-azure-backup-faq.md) di Backup di Azure.
