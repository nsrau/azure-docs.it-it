<properties
   pageTitle="Backup di Azure: Backup offline o seeding iniziale tramite il servizio di importazione/esportazione di Azure"
   description="Informazioni sull'uso di Backup di Azure per l'invio di dati offline tramite il servizio di importazione/esportazione di Azure. Questo articolo illustra il seeding offline dei dati del backup iniziale tramite il servizio di importazione/esportazione di Azure"
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="jimpark;saurabhsensharma;nkolli;trinadhk"/>  

# Flusso di lavoro di Backup offline in Backup di Azure
In Backup di Azure sono incorporate diverse funzionalità che consentono di ridurre in modo efficiente i costi di archiviazione e di rete durante i backup "completi" iniziali dei dati in Azure. I backup "completi" iniziali comportano in genere il trasferimento di grandi quantità di dati e richiedono quindi una larghezza di banda di rete superiore rispetto ai backup successivi con cui vengono trasferiti solo backup differenziali/incrementali. Oltre a comprimere i backup iniziali, Backup di Azure offre con il processo di seeding online un meccanismo per caricare offline in Azure tramite dischi i dati compressi dei backup iniziali.

Il processo di seeding offline di Backup di Azure è strettamente integrato con il [servizio Importazione/Esportazione di Azure](../storage/storage-import-export-service.md) che consente di trasferire rapidamente dati in Azure usando dischi. Se si hanno terabyte di dati del backup iniziale da trasferire su una rete con latenza elevata e larghezza di banda ridotta, è possibile usare il flusso di lavoro del seeding offline per spedire la copia di backup iniziale su uno o più dischi rigidi a un data center di Azure. Questo articolo offre una panoramica dei passaggi necessari per completare il flusso di lavoro.

## Overview

Con la funzionalità di seeding offline di Backup di Azure e Importazione/Esportazione di Azure, caricare i dati offline in Azure tramite dischi è molto semplice. Anziché trasferire la copia iniziale tramite rete, i dati di backup vengono scritti in un *percorso di gestione temporanea*. Al termine della copia iniziale nella *posizione di staging* con lo *strumento Importazione/Esportazione di Azure*, i dati vengono scritti in una o più unità SATA, a seconda delle dimensioni del backup iniziale. Queste unità vengono infine spedite al data center di Azure più vicino.

L'[aggiornamento di agosto 2016 di Backup di Azure e le versioni successive](http://go.microsoft.com/fwlink/?LinkID=229525&clcid=0x409) includono uno *strumento di preparazione dischi di Azure* denominato **AzureOfflineBackupDiskPrep** che:

   - Consente di preparare facilmente le unità per l'importazione in Azure con lo strumento Importazione/Esportazione di Azure
   - Crea automaticamente un processo di importazione di Azure per il servizio Importazione/Esportazione di Azure nel [portale di Azure classico](https://manage.windowsazure.com) in sostituzione della creazione manuale necessaria con le versioni precedenti di Backup di Azure

Al termine del caricamento dei dati di backup in Azure, i dati di backup vengono copiati da Backup di Azure nell'insieme di credenziali per il backup e vengono pianificati i backup incrementali.

  > [AZURE.NOTE] Per usare lo strumento di preparazione dischi di Azure, assicurarsi di aver installato l'aggiornamento di agosto 2016 di Backup di Azure (o versioni successive) ed eseguire tutti i passaggi del flusso di lavoro con tale versione. Se si usa una versione precedente di Backup di Azure, è possibile preparare l'unità SATA con lo *strumento Importazione/Esportazione di Azure* come descritto nelle sezioni successive di questo articolo.

## Prerequisiti

1. È importante acquisire familiarità con il flusso di lavoro di Importazione/Esportazione di Azure illustrato [qui](../storage/storage-import-export-service.md).
2. Prima di avviare il flusso di lavoro, verificare che sia stato creato un insieme di credenziali per Backup di Azure, che siano state scaricate le credenziali dell'insieme di credenziali, che l'agente di Backup di Azure sia stato installato in Windows Server, nel client Windows o nel server System Center Data Protection Manager (SCDPM) e che il computer sia registrato nell'insieme di credenziali per Backup di Azure.
3. Scaricare il file di impostazioni di pubblicazione di Azure [da questa pagina](https://manage.windowsazure.com/publishsettings) sul computer da cui si prevede di eseguire il backup dei dati.
4. Preparare una *posizione di staging*, che può essere una condivisione di rete o un'unità aggiuntiva nel computer. La posizione di staging è una risorsa di archiviazione temporanea usata durante il flusso di lavoro. Verificare che nel percorso di gestione temporanea sia disponibile spazio su disco sufficiente per contenere la copia iniziale. Se si prevede di eseguire il backup di un file server da 500 GB, ad esempio, verificare che l'area di staging sia di almeno 500 GB, anche se grazie alla compressione verrà usato uno spazio inferiore.
5. Unità di scrittura SATA esterna e unità SATA esterna da 3,5 pollici. Con il servizio di importazione/esportazione sono supportati solo i dischi rigidi SATA II/III da 3,5 pollici. Non sono supportati dischi rigidi di dimensioni superiori a 8 TB. È possibile collegare un disco SATA II/III esternamente alla maggior parte dei computer mediante una scheda USB SATA II/III. Per informazioni sul set più recente di unità supportato dal servizio, vedere la documentazione di Importazione/Esportazione di Azure.
6. Attivare BitLocker nel computer a cui è connessa l'unità di scrittura SATA.
7. Scaricare lo strumento di importazione/esportazione di Azure da [questa pagina](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) nel computer a cui è connessa l'unità di scrittura SATA. Questo passaggio non è necessario se si è scaricato e installato l'aggiornamento di agosto 2016 di Backup di Azure (o una versione successiva).

## Flusso di lavoro
Le informazioni presenti in questa sezione consentono di completare il flusso di lavoro di **Backup offline** in modo che i dati possano essere inviati a un data center di Azure e caricati nell'archiviazione di Azure. Per domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione sulla [panoramica del servizio di importazione](../storage/storage-import-export-service.md) citata in precedenza.

### Avviare Backup offline

1. Durante la pianificazione di un backup verrà visualizzata la schermata seguente (in Windows Server, nel client Windows o in SCDPM).

    ![ImportScreen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
    
    La schermata corrispondente in SCDPM si presenta come illustrato di seguito. <br/> ![Schermata di importazione di DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    Di seguito è riportata la descrizione degli input.

    - **Posizione per la gestione temporanea**: fa riferimento alla posizione di archiviazione temporanea in cui verrà scritta la copia del backup iniziale. Può essere una condivisione di rete o il computer locale. Se il *computer di copia* e il *computer di origine* sono diversi, è consigliabile specificare il percorso di rete completo della posizione di staging.
    - **Nome del processo di importazione di Azure**: fa riferimento al nome univoco con cui il servizio di importazione di Azure e Backup di Azure tengono traccia del trasferimento dei dati inviati in Azure usando dischi.
    - **Impostazioni di pubblicazione di Azure**: file XML contenente informazioni sul profilo di sottoscrizione. Contiene anche le credenziali sicure associate alla sottoscrizione. È possibile scaricarlo da [questa pagina](https://manage.windowsazure.com/publishsettings). Specificare il percorso locale del file di impostazioni di pubblicazione.
    - **ID sottoscrizione di Azure**: ID sottoscrizione di Azure in cui si intende avviare il processo di importazione di Azure. Se si hanno più sottoscrizioni di Azure, usare l'ID a cui si vuole associare il processo di importazione.
    - **Account di archiviazione di Azure**: account di archiviazione di tipo "classico" nella sottoscrizione di Azure specificata che verrà associato al processo di importazione di Azure.
    - **Contenitore di archiviazione di Azure**: nome del BLOB di archiviazione di destinazione nell'account di archiviazione di Azure in cui verranno importati i dati del processo.
    
    > [AZURE.NOTE] Se il server è stato registrato in un insieme di credenziali dei servizi di ripristino dal [nuovo portale di Azure](https://ms.portal.azure.com) per i backup e non in una sottoscrizione di un provider di servizi cloud, è comunque possibile creare un account di archiviazione di tipo "classico" dal nuovo portale di Azure e usarlo per il flusso di lavoro del backup offline.
    
    Salvare queste informazioni separatamente perché dovranno essere immesse di nuovo in passaggi successivi. Se si preparano i dischi con lo *strumento di preparazione dischi di Azure*, è necessaria solo la *posizione di staging*.
    

2. Completare il flusso di lavoro e selezionare **Esegui backup ora** nella console di gestione di Backup di Azure per avviare la copia di backup offline. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)  
    
    Per abilitare il flusso di lavoro corrispondente in SCDPM, fare clic con il pulsante destro del mouse su **Gruppo protezione dati** e scegliere l'opzione **Crea punto di ripristino**. Questo passaggio viene eseguito scegliendo l'opzione **Protezione dati online**.

    ![Esegui backup di DPM ora](./media/backup-azure-backup-import-export/dpmbackupnow.png)  

    Al termine dell'operazione, la posizione di staging è pronta per essere usata per la preparazione dei dischi.

    ![Output](./media/backup-azure-backup-import-export/opbackupnow.png)

###Preparare l'unità SATA e creare il processo di importazione di Azure con lo *strumento di preparazione dischi di Azure*
Lo *strumento di preparazione dischi di Azure* è disponibile nella directory di installazione dell'agente di Servizi di ripristino di Microsoft Azure (aggiornamento di agosto 2016 e versioni successive) nel percorso seguente:

   *\\Microsoft* *Azure* *Recovery* *Services Agent\\Utils*

1. Passare alla directory sopra indicata e copiare la directory **AzureOfflineBackupDiskPrep** in un *computer di copia* in cui sono montate le unità da preparare. Assicurarsi che il *computer di copia* soddisfi i requisiti seguenti:

      - La *posizione di staging* specificata per il flusso di lavoro del seeding offline è accessibile dal *computer di copia* usando lo stesso percorso di rete specificato durante il flusso di lavoro di **avvio del backup offline**.
      
      - Nel computer è abilitato BitLocker.
      
      - Il computer ha accesso al portale di Azure.
      
      Se necessario, il *computer di copia* può coincidere con il *computer di origine*.

2. Aprire un prompt dei comandi con privilegi elevati nel computer di copia con la directory dello strumento di preparazione dischi di Azure come directory corrente ed eseguire il comando seguente:

      *.\\AzureOfflineBackupDiskPrep.exe* s:<*Percorso posizione staging*> [p:<*Percorso FileImpostazioniPubblicazione*>]

| Parametro | Descrizione
|-------------|-------------|
|s:<*Percorso posizione staging*> | Input obbligatorio usato per specificare il percorso della posizione di staging immesso durante l'avvio del backup offline |
|p:<*Percorso FileImpostazioniPubblicazione*> | Input facoltativo usato per specificare il percorso del file di impostazioni di pubblicazione immesso durante l'avvio del backup offline |

   > [AZURE.NOTE] Il file di impostazioni di pubblicazione costituisce un input obbligatorio quando il *computer di copia* e il *computer di origine* sono diversi.

Quando viene eseguito il comando, lo strumento richiede di selezionare il processo di importazione di Azure per cui devono essere preparate le unità. Se alla posizione di staging specificata è associato un unico processo di importazione, viene visualizzata una schermata come la seguente.

   ![Input dello strumento di preparazione dischi di Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Immettere la **lettera di unità** del disco montato che si vuole preparare per il trasferimento in Azure, senza i due punti finali. Quando richiesto, confermare quindi la formattazione dell'unità.

Lo strumento inizia quindi a preparare il disco con i dati di backup. Potrebbe essere necessario collegare dischi aggiuntivi quando richiesto dallo strumento, nel caso in cui lo spazio sul disco specificato non sia sufficiente a contenere i dati di backup. <br/>

Al termine dell'esecuzione dello strumento, uno o più dischi sono pronti per essere spediti ad Azure e nel portale di Azure classico viene creato un processo di importazione con il nome specificato durante il flusso di lavoro di **avvio del backup offline**. Lo strumento visualizza anche l'indirizzo di spedizione del data center di Azure a cui i dischi devono essere spediti nonché il collegamento per individuare il processo di importazione nel portale di Azure classico.

   ![Preparazione dischi di Azure completata](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
4. Spedire i dischi all'indirizzo indicato nello strumento e conservare il numero di tracciabilità come riferimento futuro.<br/>
5. Visitando il collegamento visualizzato nello strumento si accede all'**account di archiviazione di Azure** specificato durante il flusso di lavoro del backup offline. Nella scheda **IMPORTA/ESPORTA** dell'account di archiviazione verrà visualizzato il processo di importazione appena creato.

   ![Processo di importazione creato](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>
6. Fare clic su **INFORMAZIONI SULLA SPEDIZIONE** nella parte inferiore della pagina per aggiornare **Dettagli contatto** come illustrato di seguito. Queste informazioni vengono usate da Microsoft per restituire i dischi all'utente al termine del processo di importazione.

   ![Informazioni di contatto](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>
7. Nella successiva schermata **Dettagli spedizione** immettere le informazioni come illustrato di seguito. Specificare i dettagli di Vettore recapito e Numero tracciabilità corrispondenti ai dischi spediti al data center di Azure.
   
   ![Informazioni sulla spedizione](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### Completamento del flusso di lavoro
Al termine del processo di importazione, i dati del backup iniziale saranno disponibili nell'account di archiviazione. L'agente di Servizi di ripristino di Microsoft Azure copierà quindi il contenuto dei dati dall'account all'insieme di credenziali per il backup o all'insieme di credenziali dei servizi di ripristino, a seconda di quale sia applicabile. Al successivo backup pianificato, l'agente di Backup di Azure eseguirà il backup incrementale rispetto alla copia di backup iniziale.


> [AZURE.NOTE] Le sezioni successive si applicano agli utenti di versioni precedenti di Backup di Azure che non hanno accesso allo *strumento di preparazione dischi di Azure*.

### Preparare l'unità SATA

1. Scaricare lo [strumento di importazione/esportazione di Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) nel *computer di copia*. Verificare che il percorso di gestione temporanea del passaggio precedente sia accessibile dal computer in cui si prevede di eseguire il set successivo di comandi. Se necessario, il computer di copia può coincidere con il computer di origine.

2. Decomprimere il file *WAImportExport.zip*. Eseguire lo strumento *WAImportExport* che consente di formattare l'unità SATA, scrivere i dati di backup nell'unità SATA e crittografarli. Prima di eseguire il comando seguente, verificare che BitLocker sia abilitato nel computer. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*FileJournal*>.jrn /id: <*IDSessione*> /sk:<*ChiaveAccountArchiviazione*> /BlobType:**PageBlob** /t:<*LetteraUnitàDestinazione*> /format /encrypt /srcdir:<*posizione di staging*> /dstdir: <*DirectoryVirtualeBLOBDestinazione*>/*
    
    > [AZURE.NOTE] Se si è installato l'aggiornamento di agosto 2016 di Backup di Azure (o una versione successiva), assicurarsi che la *posizione di staging* immessa sia la stessa visualizzata nella schermata **Esegui backup ora** e che contenga i file *AIB* e del *BLOB di base*.

| Parametro | Description
|-------------|-------------|
| /j:<*FileJournal*>| Percorso del file journal. Ogni unità deve contenere esattamente un file journal. Il file journal non deve trovarsi nell'unità di destinazione. Il file journal ha estensione jrn e viene creato durante l'esecuzione del comando.|
|/id:<*IDSessione*> | L'ID di sessione identifica una *sessione di copia*. Viene usato per garantire il recupero accurato di una sessione di copia interrotta. I file copiati in una sessione di copia vengono archiviati in una directory denominata in base all'ID di sessione nell'unità di destinazione.|
| /sk:<*ChiaveAccountArchiviazione*> | Chiave dell'account di archiviazione in cui verranno importati i dati. Deve essere lo stessa chiave immessa durante la creazione del gruppo di criteri di backup/protezione.|
| /BlobType | Specificare **PageBlob**. Il flusso di lavoro avrà esito positivo solo se viene specificata l'opzione PageBlob. Questa non è l'opzione predefinita e deve essere indicata nel comando. |
|/t:<*LetteraUnitàDestinazione*> | Lettera di unità del disco rigido di destinazione per la sessione di copia corrente, senza i due punti finali.|
|/format | Specificare questo parametro se è necessario formattare l'unità. In caso contrario, ometterlo. Prima di formattare l'unità, lo strumento chiede una conferma dalla console. Per evitare questa richiesta di conferma, specificare il parametro /silentmode.|
|/encrypt | Specificare questo parametro se l'unità non è ancora stata crittografata con BitLocker e deve essere crittografata tramite lo strumento. Se l'unità è già stata crittografata con BitLocker, omettere questo parametro e specificare il parametro /bk, fornendo la chiave BitLocker esistente. Se si specifica il parametro /format, sarà necessario specificare anche il parametro /encrypt. |
|/srcdir:<*DirectoryOrigine*> | Directory di origine contenente i file da copiare nell'unità di destinazione. Verificare che il nome della directory specificato qui sia il percorso completo, non un percorso relativo.|
|/dstdir:<*DirectoryVirtualeBLOBDestinazione*> | Percorso della directory virtuale di destinazione nell'account di archiviazione di Microsoft Azure. Assicurarsi di usare nomi di contenitore validi quando si specificano BLOB o directory virtuali di destinazione. Tenere presente che i nomi di contenitore devono essere costituiti da lettere minuscole. Questo nome di contenitore deve essere lo stesso specificato durante la creazione del gruppo di criteri di backup/protezione.|

  > [AZURE.NOTE] Nella cartella WAImportExport viene creato un file journal che acquisisce tutte le informazioni relative al flusso di lavoro. Questo file è necessario durante la creazione di un processo di importazione nel portale di Azure.

  ![Output di PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Creare un processo di importazione nel portale di Azure
1. Passare all'account di archiviazione nel [portale di Azure classico](https://manage.windowsazure.com/), fare clic su **Importa/Esporta** e quindi su **Crea processo di importazione** nel riquadro attività.

    ![di Microsoft Azure](./media/backup-azure-backup-import-export/azureportal.png)  

2. Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile. Nel passaggio 2 della procedura guidata specificare le informazioni di contatto per la persona responsabile di questo processo di importazione.
3. Nel passaggio 3 caricare i file journal dell'unità ottenuti nella sezione precedente.
4. Nel passaggio 4 immettere un nome descrittivo per il processo di importazione come quello immesso durante la creazione del gruppo di criteri di backup/protezione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà usato per tenere traccia dei processi mentre sono in corso e dopo che sono stati completati.
5. Selezionare quindi l'area geografica del data center dall'elenco. L'area geografica del data center indica il data center e l'indirizzo per la spedizione del pacchetto.

    ![DC](./media/backup-azure-backup-import-export/dc.png)  

6. Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Questo account viene usato da Microsoft per restituire le unità all'utente al termine del processo di importazione.

7. Spedire il disco e immettere il numero di tracciabilità per tenere traccia dello stato della spedizione. Dopo l'arrivo nel data center, il disco viene copiato nell'account di archiviazione e lo stato viene aggiornato.

    ![Stato completo](./media/backup-azure-backup-import-export/complete.png)  

### Completamento del flusso di lavoro
Quando i dati del backup iniziale sono disponibili nell'account di archiviazione, l'agente di Servizi di ripristino di Microsoft Azure copia il contenuto dei dati dall'account all'insieme di credenziali per il backup o all'insieme di credenziali dei servizi di ripristino, a seconda di quale sia applicabile. Al successivo backup pianificato, l'agente Backup di Azure esegue il backup incrementale sulla copia di backup iniziale.

## Passaggi successivi
- Per chiarimenti sul flusso di lavoro di Importazione/Esportazione di Azure, vedere questo [articolo](../storage/storage-import-export-service.md).
- Per domande sul flusso di lavoro, fare riferimento alla sezione relativa al backup offline delle [domande frequenti](backup-azure-backup-faq.md) di Backup di Azure.

<!---HONumber=AcomDC_0817_2016-->