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
ms.date: 4/20/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 074d21269206b243f8b0e8747811544132805229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Flusso di lavoro del backup offline in Backup di Azure
In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup completi iniziali dei dati in Azure. I backup completi iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Backup di Azure comprime i backup iniziali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati compressi dei backup iniziali.  

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md) che consente di trasferire dati in Azure usando dischi. Se si hanno terabyte (TB) di dati del backup iniziale da trasferire su una rete con latenza elevata e larghezza di banda ridotta, è possibile usare il flusso di lavoro del seeding offline per spedire la copia di backup iniziale su uno o più dischi rigidi a un data center di Azure. Questo articolo offre una panoramica dei passaggi necessari per completare il flusso di lavoro.

## <a name="overview"></a>Overview
Con la funzionalità di seeding offline di Backup di Azure e Importazione/Esportazione di Azure, caricare i dati offline in Azure tramite dischi è molto semplice. Anziché trasferire la copia iniziale tramite rete, i dati di backup vengono scritti in un *percorso di gestione temporanea*. Al termine della copia nel percorso di gestione temporanea con lo strumento di importazione/esportazione di Azure, i dati vengono scritti in una o più unità SATA, a seconda della quantità di dati. Queste unità vengono infine spedite al data center di Azure più vicino.

L' [aggiornamento di agosto 2016 di Backup di Azure e le versioni successive](http://go.microsoft.com/fwlink/?LinkID=229525) includono uno *strumento di preparazione dischi di Azure*denominato AzureOfflineBackupDiskPrep che:

* Consente di preparare le unità per l'importazione in Azure con lo strumento di importazione/esportazione di Azure.
* Crea automaticamente un processo di importazione di Azure per il servizio Importazione/Esportazione di Azure nel [portale di Azure classico](https://manage.windowsazure.com) in sostituzione della creazione manuale necessaria con le versioni precedenti di Backup di Azure.

Al termine del caricamento dei dati di backup in Azure, i dati di backup vengono copiati da Backup di Azure nell'insieme di credenziali per il backup e vengono pianificati i backup incrementali.

> [!NOTE]
> Per usare lo strumento di preparazione dischi di Azure, assicurarsi di aver installato l'aggiornamento di agosto 2016 di Backup di Azure o versioni successive ed eseguire tutti i passaggi del flusso di lavoro con tale versione. Se si usa una versione precedente di Backup di Azure, è possibile preparare l'unità SATA con lo strumento di importazione/esportazione di Azure come descritto nelle sezioni successive di questo articolo.
>
>

## <a name="prerequisites"></a>Prerequisiti
* [Acquisire familiarità con il flusso di lavoro di Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md).
* Prima di avviare il flusso di lavoro, verificare quanto segue:
  * È stato creato un insieme di credenziali di Backup di Azure.
  * Le credenziali dell'insieme di credenziali sono state scaricate.
  * L'agente di Backup di Azure è stato installato in Windows Server, nel client Windows o nel server di System Center Data Protection Manager e il computer è registrato con l'insieme di credenziali di Backup di Azure.
* [Scaricare il file di impostazioni di pubblicazione di Azure](https://manage.windowsazure.com/publishsettings) nel computer da cui si prevede di eseguire il backup dei dati.
* Preparare un percorso di gestione temporanea che può essere una condivisione di rete o un'unità aggiuntiva nel computer. Il percorso di gestione temporanea è una risorsa di archiviazione temporanea usata durante questo flusso di lavoro. Verificare che nel percorso di gestione temporanea sia disponibile spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si prevede di eseguire il backup di un file server da 500 GB, verificare che la dimensione dell'area di staging sia di almeno 500 GB. Verrà tuttavia usata una quantità inferiore in virtù della compressione.
* Verificare che sia in uso un'unità supportata. Con il servizio Importazione/Esportazione sono supportati solo le unità SSD da 2,5 pollici o i dischi rigidi interni SATA II/III da 2,5 o 3,5 pollici. È possibile usare dischi rigidi fino a 10 TB. Per informazioni sul set più recente di unità supportato dal servizio, vedere la [documentazione del servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md#hard-disk-drives).
* Abilitare BitLocker nel computer a cui è connessa l'unità di scrittura SATA.
* [Scaricare lo strumento di importazione/esportazione di Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) nel computer a cui è connessa l'unità di scrittura SATA. Questo passaggio non è necessario se si è scaricato e installato l'aggiornamento di agosto 2016 di Backup di Azure o una versione successiva.

## <a name="workflow"></a>Flusso di lavoro
Le informazioni presenti in questa sezione consentono di completare il flusso di lavoro di backup offline in modo che i dati possano essere inviati a un data center di Azure e caricati nell'archiviazione di Azure. Per domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione sulla [panoramica del servizio di importazione](../storage/common/storage-import-export-service.md) citata in precedenza.

### <a name="initiate-offline-backup"></a>Avviare il backup offline
1. Durante la pianificazione di un backup verrà visualizzata la schermata seguente in Windows Server, nel client Windows o in System Center Data Protection Manager.

    ![Schermata di importazione](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Di seguito è riportata la schermata corrispondente in System Center Data Protection Manager:  <br/>
    ![Schermata di importazione di DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    Di seguito è riportata la descrizione degli input.

    * **Percorso di gestione temporanea**: posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Può essere una condivisione di rete o un computer locale. Se il computer di copia e il computer di origine sono diversi, è consigliabile specificare il percorso di rete completo per la gestione temporanea.
    * **Nome processo di importazione di Azure**: nome univoco con cui il servizio di importazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati in Azure usando dischi.
    * **Impostazioni di pubblicazione di Azure**: file XML contenente informazioni sul profilo di sottoscrizione. Contiene anche le credenziali sicure associate alla sottoscrizione. È possibile [scaricare il file](https://manage.windowsazure.com/publishsettings). Specificare il percorso locale del file di impostazioni di pubblicazione.
    * **ID sottoscrizione di Azure**: ID della sottoscrizione di Azure in cui si intende avviare il processo di importazione di Azure. Se si hanno più sottoscrizioni di Azure, usare l'ID della sottoscrizione a cui si vuole associare il processo di importazione.
    * **Account di archiviazione di Azure**: account di archiviazione di tipo classico nella sottoscrizione di Azure specificata che verrà associato al processo di importazione di Azure.
    * **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui verranno importati i dati del processo.

    > [!NOTE]
    > Se il server è stato registrato in un insieme di credenziali di Servizi di ripristino di Azure dal [portale di Azure](https://portal.azure.com) per i backup e non in una sottoscrizione di Cloud Solution Provider (CSP), è comunque possibile creare un account di archiviazione di tipo classico dal portale di Azure e usarlo per il flusso di lavoro del backup offline.
    >
    >

     Salvare tutte queste informazioni perché è necessario immetterle nuovamente nella procedura seguente. Se si preparano i dischi con lo strumento di preparazione dischi di Azure, è necessario solo il *percorso di gestione temporanea* .    
2. Completare il flusso di lavoro e quindi selezionare **Esegui backup** nella console di gestione di Backup di Azure per avviare la copia di backup offline. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

    Per completare il flusso di lavoro corrispondente in System Center Data Protection Manager, fare clic con il pulsante destro del mouse su **Gruppo protezione dati** e scegliere l'opzione **Crea punto di ripristino**. Scegliere quindi l'opzione **Protezione dati online** .

    ![Esegui backup di DPM ora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Al termine dell'operazione, il percorso di gestione temporanea è pronto per essere usato per la preparazione dei dischi.

    ![Stato del backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Preparare l'unità SATA e creare un processo di importazione di Azure con lo strumento di preparazione dischi di Azure
Lo strumento di preparazione dischi di Azure è disponibile nella directory di installazione dell'agente di Servizi di ripristino (aggiornamento di agosto 2016 e versioni successive) nel percorso seguente.

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Passare alla directory e copiare la directory **AzureOfflineBackupDiskPrep** in un computer di copia in cui sono montate le unità da preparare. Assicurarsi che il computer di copia soddisfi i requisiti seguenti:

    * Il percorso di gestione temporanea specificato per il flusso di lavoro del seeding offline è accessibile dal computer di copia usando lo stesso percorso di rete specificato durante il flusso di lavoro di **avvio del backup offline** .
    * Nel computer è abilitato BitLocker.
    * Il computer può accedere al portale di Azure.

    Se necessario, il computer di copia può coincidere con il computer di origine.
2. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dello strumento di preparazione dischi di Azure come directory corrente ed eseguire il comando seguente:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Parametro | Descrizione |
    | --- | --- |
    | s:&lt;*Percorso posizione staging*&gt; |Input obbligatorio usato per specificare il percorso di gestione temporanea immesso durante il flusso di lavoro di **avvio del backup offline** . |
    | p:&lt;*Percorso FileImpostazioniPubblicazione*&gt; |Input facoltativo usato per specificare il percorso del file delle **impostazioni di pubblicazione di Azure** immesso durante il flusso di lavoro di **avvio del backup offline**. |

    > [!NOTE]
    > Il valore &lt;Percorso FileImpostazioniPubblicazione&gt; è obbligatorio quando il computer di copia e il computer di origine differiscono.
    >
    >

    Quando si esegue il comando, lo strumento richiede di selezionare il processo di importazione di Azure corrispondente alle unità da preparare. Se al percorso di gestione temporanea specificato è associato un unico processo di importazione, viene visualizzata una schermata come la seguente.

    ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Immettere la lettera di unità del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali. Quando richiesto, confermare la formattazione dell'unità.

    Lo strumento inizia quindi a preparare il disco con i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento, nel caso in cui lo spazio sul disco specificato non sia sufficiente a contenere i dati di backup. <br/>

    Al termine della corretta esecuzione dello strumento, uno o più dischi specificati vengono preparati per la spedizione ad Azure. Nel portale di Azure classico viene anche creato un processo di importazione con il nome specificato durante il flusso di lavoro di **backup offline** . Lo strumento visualizza infine l'indirizzo del data center di Azure a cui i dischi devono essere spediti nonché il collegamento per individuare il processo di importazione nel portale di Azure classico.

    ![Preparazione dischi di Azure completata](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Spedire i dischi all'indirizzo indicato nello strumento e conservare il numero di tracciabilità come riferimento futuro.<br/>

5. Visitando il collegamento visualizzato nello strumento si accede all'account di archiviazione di Azure specificato durante il flusso di lavoro di **del backup offline** . Nella scheda **IMPORTA/ESPORTA** dell'account di archiviazione verrà visualizzato il processo di importazione appena creato.

    ![Processo di importazione creato](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Fare clic su **INFORMAZIONI SULLA SPEDIZIONE** nella parte inferiore della pagina per aggiornare i dettagli di contatto come illustrato nella schermata seguente. Queste informazioni vengono usate da Microsoft per restituire i dischi all'utente al termine del processo di importazione.

    ![Informazioni di contatto](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Immettere i dettagli di spedizione nella schermata successiva. Specificare i dati per **Vettore recapito** e **Numero tracciabilità** corrispondenti ai dischi spediti al data center di Azure.

    ![INFORMAZIONI SULLA SPEDIZIONE](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Completare il flusso di lavoro
Al termine del processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. L'agente di Servizi di ripristino copierà quindi il contenuto dei dati dall'account all'insieme di credenziali di Backup o di Servizi di ripristino, a seconda di quale sia applicabile. Al successivo backup pianificato, l'agente di Backup di Azure eseguirà il backup incrementale rispetto alla copia di backup iniziale.

> [!NOTE]
> Le sezioni successive si applicano agli utenti di versioni precedenti di Backup di Azure che non hanno accesso allo strumento di preparazione dischi di Azure.
>
>

### <a name="prepare-a-sata-drive"></a>Preparare un'unità SATA
1. Scaricare lo [strumento di importazione/esportazione di Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) nel computer di copia. Verificare che il percorso di gestione temporanea sia accessibile dal computer in cui si prevede di eseguire il set successivo di comandi. Se necessario, il computer di copia può coincidere con il computer di origine.

2. Decomprimere il file WAImportExport.zip. Eseguire lo strumento WAImportExport che consente di formattare l'unità SATA, scrivere i dati di backup nell'unità SATA e crittografarli. Prima di eseguire il comando seguente, verificare che BitLocker sia abilitato nel computer. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Se si è installato l'aggiornamento di agosto 2016 di Backup di Azure o una versione successiva, assicurarsi che il percorso di gestione temporanea immesso sia lo stesso visualizzato nella schermata **Esegui backup** e che contenga i file AIB e del BLOB di base.
    >
    >

| Parametro | Description |
| --- | --- |
| /j:<*FileJournal*> |Percorso del file journal. Ogni unità deve contenere esattamente un file journal. Il file journal non deve trovarsi nell'unità di destinazione. Il file journal ha estensione jrn e viene creato durante l'esecuzione del comando. |
| /id:<*IDSessione*> |L'ID di sessione identifica una sessione di copia. Viene usato per garantire il recupero accurato di una sessione di copia interrotta. I file copiati in una sessione di copia vengono archiviati in una directory denominata in base all'ID di sessione nell'unità di destinazione. |
| /sk:<*ChiaveAccountArchiviazione*> |Chiave dell'account di archiviazione in cui verranno importati i dati. Deve essere lo stessa chiave immessa durante la creazione del gruppo di criteri di backup/protezione. |
| /BlobType |Il tipo di BLOB. Il flusso di lavoro verrà completato correttamente solo se viene specificato **PageBlob** . Questa non è l'opzione predefinita e deve essere indicata nel comando. |
| /t: <*LetteraUnitàDestinazione*> |Lettera di unità del disco rigido di destinazione per la sessione di copia corrente, senza i due punti finali. |
| /format |Opzione per la formattazione dell'unità. Specificare questo parametro se è necessario formattare l'unità. In caso contrario, ometterlo. Prima di formattare l'unità, lo strumento chiede una conferma dalla console. Per evitare questa richiesta di conferma, specificare il parametro /silentmode. |
| /encrypt |Opzione per la crittografia dell'unità. Specificare questo parametro se l'unità non è ancora stata crittografata con BitLocker e deve essere crittografata tramite lo strumento. Se l'unità è già stata crittografata con BitLocker omettere questo parametro, specificare il parametro /bk e indicare la chiave BitLocker esistente. Se si specifica il parametro /format, sarà necessario specificare anche il parametro /encrypt. |
| /srcdir: <*DirectoryOrigine*> |Directory di origine contenente i file da copiare nell'unità di destinazione. Verificare che il nome della directory specificato sia il percorso completo, non un percorso relativo. |
| /dstdir: <*DirectoryVirtualeBLOBDestinazione*> |Percorso della directory virtuale di destinazione nell'account di archiviazione di Azure. Assicurarsi di usare nomi di contenitore validi quando si specificano BLOB o directory virtuali di destinazione. Tenere presente che i nomi di contenitore devono essere costituiti da lettere minuscole.  Questo nome di contenitore deve essere lo stesso specificato durante la creazione del gruppo di criteri di backup/protezione. |

> [!NOTE]
> Nella cartella WAImportExport viene creato un file journal che acquisisce tutte le informazioni relative al flusso di lavoro. Questo file è necessario durante la creazione di un processo di importazione nel portale di Azure.
>
>

  ![Output di PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Creazione di un processo di importazione nel portale di Azure
1. Passare all'account di archiviazione nel [portale di Azure classico](https://manage.windowsazure.com/), fare clic su **Importa/Esporta** e quindi su **Crea processo di importazione** nel riquadro attività.

    ![Scheda Importazione/Esportazione nel portale di Azure](./media/backup-azure-backup-import-export/azureportal.png)

2. Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile.

3. Nel passaggio 2 della procedura guidata specificare le informazioni di contatto per la persona responsabile di questo processo di importazione.

4. Nel passaggio 3 caricare i file journal dell'unità ottenuti nella sezione precedente.

5. Nel passaggio 4 immettere un nome descrittivo per il processo di importazione come quello immesso durante la creazione del gruppo di criteri di backup/protezione. Il nome immesso può contenere solo lettere minuscole, numeri, segni meno e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà usato per tenere traccia dei processi mentre sono in corso e dopo che sono stati completati.

6. Selezionare quindi l'area geografica del data center dall'elenco. L'area geografica del data center indica il data center e l'indirizzo per la spedizione del pacchetto.

    ![Selezionare l'area geografica del data center](./media/backup-azure-backup-import-export/dc.png)

7. Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Questo account viene usato da Microsoft per restituire le unità all'utente al termine del processo di importazione.

8. Spedire il disco e immettere il numero di tracciabilità per tenere traccia dello stato della spedizione. Dopo l'arrivo nel data center, il disco viene copiato nell'account di archiviazione e lo stato viene aggiornato.

    ![Stato di completamento](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Completare il flusso di lavoro
Quando i dati del backup iniziale sono disponibili nell'account di archiviazione, l'agente di Servizi di ripristino di Microsoft Azure copia il contenuto dei dati dall'account all'insieme di credenziali di Backup o di Servizi di ripristino, a seconda di quale sia applicabile. Al successivo backup pianificato, l'agente Backup di Azure esegue il backup incrementale sulla copia di backup iniziale.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul flusso di lavoro di Importazione/Esportazione di Azure, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../storage/common/storage-import-export-service.md).
* Per domande sul flusso di lavoro, vedere la sezione relativa al backup offline delle [domande frequenti](backup-azure-backup-faq.md) di Backup di Azure.
