<properties
   pageTitle="Backup di Azure: Backup offline o seeding iniziale tramite il servizio di importazione/esportazione di Azure"
   description="Informazioni sull'uso di Backup di Azure per l'invio di dati offline tramite il servizio di importazione/esportazione di Azure. Questo articolo illustra il seeding offline dei dati del backup iniziale tramite il servizio di importazione/esportazione di Azure"
   services="backup"
   documentationCenter=""
   authors="nkolli1"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="06/14/2016"
   ms.author="jimpark;nkolli;trinadhk;saurse"/>

# Flusso di lavoro di Backup offline in Backup di Azure
In Backup di Azure sono integrate molte funzionalità che consentono di ridurre in modo efficiente i costi di rete e archiviazione. Backup di Azure non solo comprime i dati, ma esegue anche il backup dell'intero contenuto una sola volta e quindi solo backup incrementali/differenziali. Di conseguenza, se viene eseguito il backup di un volume di file di 10 TB, Backup di Azure invierà 10 TB come parte della replica iniziale e solo il contenuto differenziale come parte della replica differenziale. Durante la replica iniziale è quindi necessaria la massima larghezza di banda WAN. Per ridurre la dipendenza della WAN durante la replica iniziale, Backup di Azure supporta il backup offline con il servizio di importazione/esportazione di Azure.

Backup di Azure è integrato con il servizio di importazione/esportazione di Azure che consente di trasferire rapidamente i dati del backup iniziale. Se si hanno terabyte di dati del backup iniziale da trasferire tramite una rete con latenza elevata e larghezza di banda ridotta, è possibile usare il servizio di importazione/esportazione di Azure per spedire la copia di backup iniziale in uno o più dischi rigidi a un data center di Azure. Questo articolo offre una panoramica dei passaggi necessari per completare il flusso di lavoro.

## Panoramica

Con Backup di Azure e il servizio di importazione/esportazione di Azure, caricare i dati offline in Azure tramite dischi è molto semplice. Anziché trasferire la copia iniziale tramite rete, i dati di backup vengono scritti in un *percorso di gestione temporanea*. Il percorso di gestione temporanea potrebbe essere un archivio collegato o una condivisione di rete. Dopo il completamento della copia iniziale, tramite lo *strumento di importazione/esportazione di Azure* i dati vengono scritti in un'unità SATA, che viene quindi spedita al data center di Azure. A seconda delle dimensioni del backup iniziale, l'operazione può richiedere una o più unità SATA. Lo strumento di importazione/esportazione di Azure tiene in considerazione questi diversi scenari. Al termine della scrittura dei backup su disco, è possibile spedirli al data center più vicino per il caricamento in Azure. Backup di Azure copia quindi i dati di backup nell'insieme di credenziali per il backup e vengono pianificati i backup incrementali.

## Prerequisiti

1. È importante acquisire familiarità con il flusso di lavoro di importazione/esportazione di Azure elencato [in questa pagina](../storage/storage-import-export-service.md).
2. Prima di avviare il flusso di lavoro, verificare che sia stato creato un insieme di credenziali per Backup di Azure, che siano state scaricate le credenziali di insieme, che l'agente Backup di Azure sia stato installato in Windows Server, nel client Windows o nel server System Center Data Protection Manager (SCDPM) e che il computer sia registrato nell'insieme di credenziali per Backup di Azure.
3. Scaricare il file di impostazioni di pubblicazione di Azure [da questa pagina](https://manage.windowsazure.com/publishsettings) sul computer da cui si prevede di eseguire il backup dei dati.
4. Preparare un *percorso di gestione temporanea* che può essere una condivisione di rete o un disco aggiuntivo nel computer. Verificare che nel percorso di gestione temporanea sia disponibile spazio su disco sufficiente per contenere la copia iniziale. Se ad esempio si prevede di eseguire il backup di un file server da 500 GB, verificare che la dimensione dell'area di gestione temporanea sia almeno di 500 GB, anche se verrà usata una quantità inferiore. L'area di gestione temporanea è uno spazio di archiviazione temporaneo usato durante il flusso di lavoro.
5. Unità di scrittura SATA esterna e disco rigido SATA esterno da 3,5 pollici. con il servizio di importazione/esportazione sono supportati solo i dischi rigidi SATA II/III da 3,5 pollici. I dischi rigidi di dimensioni superiori a 8 TB non sono supportati. È possibile collegare un disco SATA II/III esternamente alla maggior parte dei computer mediante una scheda USB SATA II/III. Consultare la documentazione di importazione/esportazione di Azure per il set più recente di unità supportate dal servizio.
6. Attivare BitLocker nel computer a cui è connessa l'unità di scrittura SATA.
7. Scaricare lo strumento di importazione/esportazione di Azure da [questa pagina](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) nel computer a cui è connessa l'unità di scrittura SATA.

## Flusso di lavoro
Le informazioni presenti in questa sezione consentono di completare il flusso di lavoro di **Backup offline** in modo che i dati possano essere inviati a un data center di Azure e caricati nell'archiviazione di Azure. Per domande sul servizio di importazione o su qualsiasi aspetto del processo, vedere la documentazione sulla [panoramica del servizio di importazione](../storage/storage-import-export-service.md) citata in precedenza.

### Avviare Backup offline

1. Durante la pianificazione di un backup verrà visualizzata la schermata seguente (in Windows Server, nei client di Windows o in SCDPM).

    ![ImportScreen](./media/backup-azure-backup-import-export/importscreen.png)

    La schermata corrispondente in SCDPM è simile alla seguente. <br/> ![Schermata di importazione di DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    dove:

    - **Percorso di gestione temporanea**: fa riferimento al percorso di archiviazione temporanea in cui verrà scritta la copia di backup iniziale. Può essere una condivisione di rete o il computer locale.
    - **Nome del processo di importazione di Azure**: per completare il flusso di lavoro, sarà necessario creare un *Processo di importazione* nel portale di Azure (illustrato più avanti in questo argomento). Specificare un input che si prevede di usare in seguito nel portale di Azure.
    - **Impostazioni di pubblicazione Azure**: si tratta di un file XML contenente informazioni sul profilo di sottoscrizione. Il file include anche le credenziali protette associate alla sottoscrizione. È possibile scaricarlo da [questa pagina](https://manage.windowsazure.com/publishsettings). Specificare il percorso locale del file di impostazioni di pubblicazione.
    - **ID sottoscrizione di Azure**: specificare l'ID sottoscrizione di Azure in cui si intende avviare il processo di importazione di Azure. Se si hanno più sottoscrizioni di Azure, usare l'ID associato al processo di importazione.
    - **Account di archiviazione di Azure**: immettere il nome dell'account di archiviazione di Azure che verrà associato a questo processo di importazione.
    - **Contenitore di archiviazione di Azure**: immettere il nome del BLOB di archiviazione di destinazione in cui verranno importati i dati del processo.

Salvare queste informazioni separatamente perché dovranno essere reinserite in passaggi successivi.


2. Completare il flusso di lavoro e selezionare **Esegui backup ora** nella console MMC di Backup di Azure per avviare la copia di backup offline. Durante questo passaggio il backup iniziale viene scritto nell'area di gestione temporanea.

    ![Esegui backup ora](./media/backup-azure-backup-import-export/backupnow.png)

    Per abilitare il flusso di lavoro corrispondente in SCDPM, fare clic su **Gruppo protezione dati** e scegliere l'opzione **Crea punto di ripristino**. Scegliere quindi l'opzione **Protezione dati online**.

    ![Esegui backup di DPM ora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Al termine dell'operazione, viene creato un file *.AIBBlob* e *.BaseBlob* nel percorso di gestione temporaneo.

    ![Output](./media/backup-azure-backup-import-export/opbackupnow.png)

### Preparare l'unità SATA

1. Scaricare lo [strumento di importazione/esportazione di Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) nel *computer di copia*. Verificare che il percorso di gestione temporanea del passaggio precedente sia accessibile dal computer in cui si prevede di eseguire il set successivo di comandi. Se necessario, il computer di copia può essere lo stesso del computer di origine.

2. Decomprimere il file *WAImportExport.zip*. Eseguire lo strumento *WAImportExport* che consente di formattare l'unità SATA, scrivere i dati di backup nell'unità SATA e crittografarli. Prima di eseguire il comando seguente, verificare che BitLocker sia abilitato nel computer. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| Parametro | Descrizione
|-------------|-------------|
| /j:<*JournalFile*>| Percorso del file journal. Ogni unità deve contenere esattamente un file journal. Si noti che il file journal non deve trovarsi nell'unità di destinazione. Il file journal ha estensione jrn e viene creato durante l'esecuzione del comando.|
|/id:<*SessionId*> | L'ID di sessione identifica una *sessione di copia*. Viene usato per garantire il recupero accurato di una sessione di copia interrotta. I file copiati in una sessione di copia vengono archiviati in una directory denominata in base all'ID di sessione nell'unità di destinazione.|
| /sk:<*StorageAccountKey*> | Chiave account per l'account di archiviazione in cui verranno importati i dati. Deve essere lo stessa immessa durante la creazione del gruppo di criteri di backup/protezione.|
| /BlobType | Specificare **PageBlob**. Il flusso di lavoro avrà esito positivo solo se viene specificata l'opzione PageBlob. Questa non è l'opzione predefinita e deve essere indicata nel comando. |
|/t:<*TargetDriveLetter*> | Lettera di unità del disco rigido di destinazione per la sessione di copia corrente, senza i due punti finali.|
|/format | Specificare questo parametro se è necessario formattare l'unità. In caso contrario, ometterlo. Prima di formattare l'unità, verrà chiesta una conferma dalla console. Per evitare questa richiesta di conferma, specificare il parametro /silentmode.|
|/encrypt | Specificare questo parametro se l'unità non è ancora stata crittografata con BitLocker e deve essere crittografata tramite lo strumento. Se l'unità è già stata crittografata con BitLocker, omettere questo parametro e specificare il parametro /bk, fornendo la chiave BitLocker esistente. Se si specifica il parametro /format, sarà necessario specificare anche il parametro /encrypt. |
|/srcdir:<*SourceDirectory*> | Directory di origine contenente i file da copiare nell'unità di destinazione. Verificare che il nome della directory specificato qui sia il percorso completo, non un percorso relativo.|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Percorso della directory virtuale di destinazione nell'account di archiviazione di Microsoft Azure. Assicurarsi di usare nomi di contenitore validi quando si specificano BLOB o directory virtuali di destinazione. Tenere presente che i nomi di contenitore devono essere costituiti da lettere minuscole. Questo nome di contenitore deve essere lo stesso specificato durante la creazione del gruppo di criteri di backup/protezione.|

  > [AZURE.NOTE] Nella cartella WAImportExport viene creato un file journal che acquisisce tutte le informazioni relative al flusso di lavoro. Questo file sarà necessario durante la creazione di un processo di importazione nel portale di Azure.

  ![Output di PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Creare un processo di importazione nel portale di Azure
1. Passare all'account di archiviazione nel [portale di gestione](https://manage.windowsazure.com/), fare clic su **Importazione/Esportazione** e quindi su **Crea processo di importazione** nel riquadro attività.

    ![Portale](./media/backup-azure-backup-import-export/azureportal.png)

2. Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile. Nel passaggio 2 della procedura guidata specificare le informazioni di contatto per la persona responsabile di questo processo di importazione.
3. Nel passaggio 3 caricare i file journal dell'unità ottenuti nella sezione precedente.
4. Nel passaggio 4 immettere un nome descrittivo per il processo di importazione come quello immesso durante la creazione del gruppo di criteri di backup/protezione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà usato per tenere traccia dei processi mentre sono in corso e dopo essere stati completati.
5. Selezionare quindi l'area geografica del data center dall'elenco. L'area geografica del data center indica il data center e l'indirizzo per la spedizione del pacchetto.

    ![DC](./media/backup-azure-backup-import-export/dc.png)

6. Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di importazione.

7. Spedire il disco e immettere il numero di tracciabilità per tenere traccia dello stato della spedizione. Dopo l'arrivo nel data center, il disco viene copiato nell'account di archiviazione e lo stato viene aggiornato.

    ![Stato completo](./media/backup-azure-backup-import-export/complete.png)

### Completamento del flusso di lavoro
Dopo che i dati del backup iniziale sono disponibili nell'account di archiviazione, l'agente Backup di Azure copia il contenuto dei dati da questo account all'account di archiviazione del backup multi-tenant. Al successivo backup pianificato, l'agente Backup di Azure esegue il backup incrementale sulla copia di backup iniziale.

## Passaggi successivi
- Per domande sul flusso di lavoro di importazione/esportazione di Azure, fare riferimento a questo [articolo](../storage/storage-import-export-service.md).
- Per domande sul flusso di lavoro, fare riferimento alla sezione relativa al backup offline delle [domande frequenti](backup-azure-backup-faq.md) di Backup di Azure.

<!---HONumber=AcomDC_0615_2016-->