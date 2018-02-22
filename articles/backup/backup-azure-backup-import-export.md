---
title: 'Backup di Azure: backup offline o seeding iniziale con il servizio Importazione/Esportazione di Azure | Microsoft Docs'
description: Informazioni sull'uso di Backup di Azure per l'invio di dati offline tramite il servizio Importazione/Esportazione di Azure. Questo articolo illustra il seeding offline dei dati del backup iniziale tramite il servizio Importazione/Esportazione di Azure.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 306c4c7498601cf3ab7e918ba6ce6bfef173236a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flusso di lavoro del backup offline in Backup di Azure
In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Backup di Azure comprime i backup iniziali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati compressi dei backup iniziali.  

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md) che consente di trasferire dati in Azure usando dischi. Se si hanno terabyte (TB) di dati del backup iniziale da trasferire su una rete con latenza elevata e larghezza di banda ridotta, è possibile usare il flusso di lavoro del seeding offline per spedire la copia di backup iniziale su uno o più dischi rigidi a un data center di Azure. Questo articolo offre una panoramica e altri dettagli dei passaggi necessari per completare il flusso di lavoro.


## <a name="overview"></a>Panoramica
Con la funzionalità di seeding offline di Backup di Azure e Importazione/Esportazione di Azure, caricare i dati offline in Azure tramite dischi è molto semplice. Il processo di backup offline prevede i passaggi seguenti:

> [!div class="checklist"]
> * I dati di backup, anziché inviati in rete, vengono scritti in un *percorso di gestione temporanea*
> * I dati nel *percorso di gestione temporanea* vengono quindi scritti in uno o più dischi SATA mediante l'utilità *AzureOfflineBackupDiskPrep*
> * Un processo di importazione di Azure viene creato automaticamente dall'utilità
> * Le unità SATA vengono quindi inviate al data center di Azure più vicino
> * Al termine del caricamento dei dati di backup in Azure, i dati di backup vengono copiati da Backup di Azure nell'insieme di credenziali per il backup e vengono pianificati i backup incrementali.

## <a name="supported-configurations"></a>Configurazioni supportate 
Il backup offline è supportato per tutti i modelli di distribuzione di Backup di Azure che trasferiscono i dati di backup dal computer locale a Microsoft Cloud. Sono inclusi:

> [!div class="checklist"]
> * Backup di file e cartelle con l'agente di Servizi di ripristino di Microsoft Azure o di Backup di Azure. 
> * Backup di tutti i carichi di lavoro e i file con System Center Data Protection Manager (SC DPM) 
> * Backup di tutti i carichi di lavoro e i file con il server di Backup di Microsoft Azure <br/>

   > [!NOTE]
   > Il backup offline non è supportato per i backup dello stato del sistema eseguiti tramite l'agente di Backup di Azure. 

## <a name="prerequisites"></a>prerequisiti
Verificare che i seguenti prerequisiti siano soddisfatti prima di avviare il flusso di lavoro di backup offline
* Deve essere stato creato un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md). Per crearne uno, vedere la procedura descritta in [questo articolo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* L'agente o il server di Backup di Azure oppure System Center Data Protection Manager deve essere installato su Windows Server o nel client Windows, a seconda dei casi, e il computer deve essere registrato con l'insieme di credenziali di Servizi di ripristino. Assicurarsi di usare solo la [versione più recente di Backup di Azure](https://go.microsoft.com/fwlink/?linkid=229525). 
* [Scaricare il file di impostazioni di pubblicazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) nel computer da cui si prevede di eseguire il backup dei dati. La sottoscrizione da cui si scarica il file di impostazioni di pubblicazione può essere diversa dalla sottoscrizione che contiene l'insieme di credenziali di Servizi di ripristino. Se la sottoscrizione si trova in cloud sovrani di Azure, usare i collegamenti seguenti appropriati per scaricare il file di impostazioni di pubblicazione di Azure.

    | Area geografica cloud sovrani | Collegamento al file delle impostazioni di pubblicazione di Azure |
    | --- | --- |
    | Stati Uniti | [Collegamento](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Cina | [Collegamento](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Nella sottoscrizione da cui è stato scaricato il file di impostazioni di pubblicazione è stato creato un account di archiviazione di Azure con modello di distribuzione *classico*, come illustrato di seguito: 

 ![Creazione di un account di archiviazione classico](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Viene creato un percorso di gestione temporanea, che può essere una condivisione di rete o qualsiasi unità aggiuntiva nel computer, interna o esterna, con spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si prevede di eseguire il backup di un file server da 500 GB, verificare che la dimensione dell'area di staging sia di almeno 500 GB. Verrà tuttavia usata una quantità inferiore in virtù della compressione.
* Per quanto riguarda i dischi che verranno spediti a Azure, assicurarsi che vengano usate solo unità SSD da 2,5 pollici o unità disco rigido interne SATA II/III da 2,5 o 3,5 pollici. È possibile usare dischi rigidi fino a 10 TB. Per informazioni sul set più recente di unità supportato dal servizio, vedere la [documentazione del servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md#hard-disk-drives).
* Le unità SATA devono essere connesse a un computer (denominato *computer di copia*) da cui vengono copiati i dati di backup del *percorso di gestione temporanea* nell'unità SATA. Verificare che nel *computer di copia* sia abilitato BitLocker 

## <a name="workflow"></a>Flusso di lavoro
Le informazioni presenti in questa sezione consentono di completare il flusso di lavoro di backup offline in modo che i dati possano essere inviati a un data center di Azure e caricati nell'archiviazione di Azure. Per domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione sulla [panoramica del servizio di importazione](../storage/common/storage-import-export-service.md) citata in precedenza.

### <a name="initiate-offline-backup"></a>Avviare il backup offline
1. Durante la pianificazione di un backup verrà visualizzata la schermata seguente in Windows Server, nel client Windows o in System Center Data Protection Manager.

    ![Schermata di importazione](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Di seguito è riportata la schermata corrispondente in System Center Data Protection Manager:  <br/>
    ![Schermata di importazione di SC DPM e del server di Backup di Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    Di seguito è riportata la descrizione degli input.

    * **Percorso di gestione temporanea**: posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Il percorso di gestione temporanea può trovarsi in una condivisione di rete o in un computer locale. Se il computer di copia e il computer di origine sono diversi, è consigliabile specificare il percorso di rete completo per la gestione temporanea.
    * **Nome processo di importazione di Azure**: nome univoco con cui il servizio di importazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati in Azure usando dischi.
    * **Impostazioni di pubblicazione di Azure**: specificare il percorso locale del file di impostazioni di pubblicazione.
    * **ID sottoscrizione di Azure**: ID della sottoscrizione di Azure da cui è stato scaricato il file di impostazioni di pubblicazione di Azure. 
    * **Account di Archiviazione di Microsoft Azure**: il nome dell'account di archiviazione nella sottoscrizione di Azure associato al file delle impostazioni di pubblicazione di Azure.
    * **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui verranno importati i dati del backup.

     Salvare il *percorso di gestione temporanea* e il *nome del processo di importazione di Azure* specificato perché sono necessari per preparare i dischi.  
     
2. Completare il flusso di lavoro e, per inizializzare la copia di backup offline, fare clic su **Esegui backup** nella console di gestione dell'agente di Backup di Azure. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

    Per completare il flusso di lavoro corrispondente in System Center Data Protection Manager o nel server di Backup di Azure, fare clic con il pulsante destro del mouse su **Gruppo protezione dati** e scegliere l'opzione **Crea punto di ripristino**. Scegliere quindi l'opzione **Protezione dati online** .

    ![Verrà eseguito il backup di SC DPM e il del server di Backup di Azure](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Al termine dell'operazione, il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

    ![Stato del backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparare le unità SATA e inviarle al data center di Azure
L'utilità *AzureOfflineBackupDiskPrep* consente di preparare le unità SATA da inviare al data center di Azure più vicino. Questa utilità è disponibile nella directory di installazione dell'agente di Servizi di ripristino nel percorso seguente:

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Passare alla directory e copiare la directory **AzureOfflineBackupDiskPrep** in un computer di copia a cui sono collegate le unità SATA da preparare. Assicurarsi che il computer di copia soddisfi i requisiti seguenti:

    * Il percorso di gestione temporanea specificato per il flusso di lavoro del seeding offline è accessibile dal computer di copia usando lo stesso percorso di rete specificato durante il flusso di lavoro di **avvio del backup offline** .
    * Nel computer di copia è abilitato BitLocker.
    * Il computer di copia può accedere al portale di Azure.

    Se necessario, il computer di copia può coincidere con il computer di origine. 
    
    > [!IMPORTANT] 
    > Se il computer di origine è una macchina virtuale, è obbligatorio usare un server fisico diverso o un computer client come computer di copia.
    
    
2. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente ed eseguire il comando seguente:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametro | DESCRIZIONE |
    | --- | --- |
    | s:&lt;*Percorso posizione staging*&gt; |Input obbligatorio usato per specificare il percorso di gestione temporanea immesso durante il flusso di lavoro di **avvio del backup offline** . |
    | p:&lt;*Percorso FileImpostazioniPubblicazione*&gt; |Input facoltativo usato per specificare il percorso del file delle **impostazioni di pubblicazione di Azure** immesso durante il flusso di lavoro di **avvio del backup offline**. |

    > [!NOTE]
    > Il valore &lt;Percorso FileImpostazioniPubblicazione&gt; è obbligatorio quando il computer di copia e il computer di origine sono diversi.
    >
    >

    Quando si esegue il comando, l'utilità richiede di selezionare il processo di importazione di Azure corrispondente alle unità da preparare. Se al percorso di gestione temporanea specificato è associato un unico processo di importazione, viene visualizzata una schermata come la seguente.

    ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Immettere la lettera di unità del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali. Quando richiesto, confermare la formattazione dell'unità.

    Lo strumento inizia quindi a preparare il disco e a copiare i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento, nel caso in cui lo spazio sul disco specificato non sia sufficiente a contenere i dati di backup. <br/>

    Al termine della corretta esecuzione dello strumento, uno o più dischi specificati vengono preparati per la spedizione ad Azure. In Azure viene anche creato un processo di importazione con il nome specificato durante il flusso di lavoro di **backup offline**. Lo strumento visualizza infine l'indirizzo del data center di Azure a cui i dischi devono essere spediti.

    ![Preparazione dischi di Azure completata](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. Al termine dell'esecuzione del comando viene anche visualizzata un'opzione per aggiornare le informazioni di spedizione, come illustrato di seguito:

    ![Opzione di aggiornamento delle informazioni di spedizione](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. È possibile immettere direttamente i dettagli. Lo strumento guida l'utente durante il processo che richiede una serie di input. Se non si dispone di informazioni come il numero di tracciabilità o altri dettagli relativi alla spedizione, è possibile terminare la sessione. In questo articolo vengono forniti i passaggi per aggiornare i dettagli sulla spedizione in un secondo momento. 

6. Spedire i dischi all'indirizzo indicato nello strumento e conservare il numero di tracciabilità come riferimento futuro.

   > [!IMPORTANT] 
   > Due processi di importazione di Azure non possono avere lo stesso numero di tracciabilità. Verificare che le unità preparate dall'utilità in un singolo processo di importazione di Azure vengano spedite insieme in un unico pacchetto e che vi sia un solo numero di tracciabilità per il pacchetto. Non combinare unità preparate con processi di importazione di Azure **diversi** in un singolo pacchetto. 

5. Quando si dispone del numero di tracciabilità, passare al computer di origine, che è in attesa del completamento del processo di importazione, ed eseguire il comando seguente in un prompt dei comandi con privilegi elevati con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Facoltativamente, è possibile eseguire il comando seguente in un computer diverso come il *computer di copia* con la directory dell'utilità *AzureOfflineBackupDiskPrep* come directory corrente:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametro | DESCRIZIONE |
    | --- | --- |
    | u: | Input obbligatorio usato per aggiornare i dettagli sulla spedizione per un processo di importazione di Azure |
    | s:&lt;*Percorso posizione staging*&gt; | Input obbligatorio quando il comando non viene eseguito nel computer di origine. Usato per specificare il percorso di gestione temporanea immesso durante il flusso di lavoro di **avvio del backup offline**. |
    | p:&lt;*Percorso FileImpostazioniPubblicazione*&gt; | Input obbligatorio quando il comando non viene eseguito nel computer di origine. Usato per specificare il percorso del file delle **impostazioni di pubblicazione di Azure** immesso durante il flusso di lavoro di **avvio del backup offline**. |
    
    L'utilità rileva automaticamente il processo di importazione che il computer di origine sta attendendo o i processi di importazione associati al percorso di gestione temporanea quando il comando viene eseguito in un computer diverso. Fornisce quindi la possibilità di aggiornare le informazioni di spedizione attraverso una serie di input, come illustrato di seguito: 
    
    ![Immissione di informazioni sulla spedizione](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Quando vengono forniti tutti gli input, esaminare con attenzione i dettagli e confermare le informazioni di spedizione fornite digitando *yes*. 

    ![Verifica delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Durante l'aggiornamento delle informazioni di spedizione, l'utilità fornisce un percorso locale in cui sono archiviati i dettagli relativi alla spedizione immessi dall'utente come illustrato di seguito 

    ![Archiviazione delle informazioni sulla spedizione](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Verificare che le unità raggiungano il data center di Azure entro due settimane dalla definizione delle informazioni di spedizione con l'utilità *AzureOfflineBackupDiskPrep*. In caso contrario, le unità potrebbero non venire elaborate.  

Dopo il completamento dei passaggi precedenti, il data center di Azure è pronto per ricevere le unità ed elaborarle ulteriormente per trasferire i dati di backup dalle unità all'account di archiviazione di Azure di tipo classico creato. 

### <a name="time-to-process-the-drives"></a>Tempo di elaborazione delle unità 
Il tempo impiegato per elaborare un processo di importazione di Azure varia in base a diversi fattori, ad esempio i tempi di spedizione, il tipo di processo, il tipo e la dimensione dei dati da copiare e la dimensione dei dischi forniti. Il servizio di importazione/esportazione di Azure non dispone di un contratto di servizio, ma dopo la ricezione dei dischi il servizio tenta di completare la copia dei dati di backup nell'account di archiviazione di Azure in un intervallo compreso tra i 7 e i 10 giorni. La sezione successiva illustra in dettaglio come monitorare lo stato del processo di importazione di Azure. 

### <a name="monitoring-azure-import-job-status"></a>Monitoraggio dello stato del processo di importazione di Azure
Mentre le unità sono in transito o si trovano nel data center di Azure per essere copiate nell'account di archiviazione, l'agente di Backup di Azure o SC DPM oppure la console del server di Backup di Azure nel computer di origine mostra lo stato del processo seguente per i backup pianificati. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Attenersi alla procedura seguente per controllare lo stato del processo di importazione. 
1. Aprire un prompt dei comandi con privilegi elevati nel computer di origine ed eseguire il comando seguente:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  L'output mostra lo stato corrente del processo di importazione, come illustrato di seguito: 

    ![Controllo dello stato del processo di importazione](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Per altre informazioni sui diversi stati del processo di importazione di Azure, vedere [questo articolo](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work)

### <a name="complete-the-workflow"></a>Completare il flusso di lavoro
Al termine del processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. In occasione del backup pianificato successivo, Backup di Azure copia il contenuto dei dati dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino, come illustrato di seguito: 

   ![Copia dei dati nell'insieme di credenziali di Servizi di ripristino](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Al successivo backup pianificato, Backup di Azure eseguirà il backup incrementale rispetto alla copia di backup iniziale.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul flusso di lavoro di Importazione/Esportazione di Azure, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../storage/common/storage-import-export-service.md).
* Per domande sul flusso di lavoro, vedere la sezione relativa al backup offline delle [domande frequenti](backup-azure-backup-faq.md) di Backup di Azure.
