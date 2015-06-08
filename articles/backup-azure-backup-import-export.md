<properties
	pageTitle="Azure Backup - Backup non in linea o il Seeding iniziale utilizzando il servizio di importazione/esportazione di Azure"
	description="Informazioni su come il Backup di Azure consente di inviare i dati fuori rete utilizzando il servizio di importazione/esportazione di Azure. In questo articolo viene spiegato il seeding non in linea dei dati di backup iniziali utilizzando il servizio Azure importazione/esportazione"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/07/2015"
	ms.author="prvijay"/>

# Flusso di lavoro di Backup non in linea in Azure Backup

Backup di Azure è integrato con il servizio di importazione/esportazione di Azure che consente di trasferire rapidamente i dati di backup iniziali. Se si dispone di terabyte di dati di backup iniziali che devono essere trasferiti su una latenza elevata & rete a larghezza di banda ridotta, è possibile utilizzare il servizio di importazione/esportazione di Azure per spedire la copia di backup iniziale in uno o più dischi rigidi, in un data center Azure. In questo articolo viene fornita una panoramica dei passaggi necessari per completare il flusso di lavoro.

## Panoramica

Con Azure Backup e di importazione/esportazione di Azure, è semplice e diretto per caricare i dati in Azure non in linea tramite dischi. Invece di trasferimento della copia iniziale sulla rete, i dati di backup viene scritto un *posizione di gestione temporanea*. Potrebbe essere il percorso di gestione temporanea un'archiviazione diretta o una condivisione di rete. Una volta completata la copia iniziale, utilizzando il *strumento di importazione/esportazione di Azure*, tali dati vengono scritti in un'unità SATA alla fine viene fornita per il data center di Azure. A seconda delle dimensioni del backup iniziale, uno o più unità SATA potrebbe essere necessario per completare questa operazione. Lo strumento di importazione/esportazione di Azure account per questi scenari. Dopo che i backup vengono scritti su disco, possono essere spediti per la posizione del data center più vicina per il caricamento in Azure. Backup di Azure quindi copia i dati di backup all'archivio di Backup e i backup incrementali sono pianificati.

## Prerequisiti

1. È importante acquisire familiarità con il flusso di lavoro esportazione importazione Azure elencata [qui](storage-import-export-service.md).

2. Prima di avviare il flusso di lavoro, assicurarsi che è stato creato un archivio di Backup di Azure, le credenziali dell'insieme di credenziali sono stati scaricati, Azure Backup agent è stato installato nel server il Client di Windows Server e Windows o per System Center Data Protection Manager (SCDPM) e che il computer sia registrato con l'archivio di Backup di Azure.

3. Scaricare le impostazioni del file di pubblicazione Azure da [qui](https://manage.windowsazure.com/publishsettings) sul computer da cui si prevede di eseguire il backup dei dati.

4. Preparare un *posizione di gestione temporanea* che può essere una condivisione di rete o un disco aggiuntivo nel computer. Assicurarsi che il percorso di gestione temporanea disponga di spazio su disco sufficiente per contenere la copia iniziale. Per ad esempio, se si sta tentando di eseguire il backup di un file server da 500GB, assicurarsi che l'area di gestione temporanea sia almeno 500GB (anche se verrà utilizzata una quantità inferiore). Area di gestione temporanea è spazio di archiviazione temporaneo e viene utilizzato durante il flusso di lavoro.

5. Scrittura di unità SATA esterna e un'unità SATA da 3,5 esterna. con il servizio di importazione/esportazione sono supportati solo i dischi rigidi SATA II/III da 3,5 pollici. I dischi rigidi di dimensioni superiori a 4 TB non sono supportati. È possibile collegare un disco SATA II/III esternamente alla maggior parte dei computer mediante una scheda USB SATA II/III. Consultare la documentazione di importazione/esportazione di Azure per il set più recente delle unità che sono supportati dal servizio.

5. Attivare BitLocker su computer a cui è connesso il writer di unità SATA.

6. Scaricare lo strumento di importazione/esportazione di Azure da [qui](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) al computer a cui è connesso il writer di unità SATA.


## Flusso di lavoro
Le informazioni fornite in questa sezione sono per completare il **Backup non in linea** flusso di lavoro in modo che i dati possono essere recapitati a un data center di Azure e caricati nell'archiviazione di Azure. Se hai domande relative al servizio di importazione o l'aspetto del processo, vedere la panoramica del servizio di importazione a cui fa riferimento [sopra](storage-import-export-service.md).

### Avviare il Backup non in linea

1. Come parte della pianificazione di un backup, si verificheranno la schermata seguente (in Windows Server, client di Windows o SCDPM). <br/> ![ImportScreen][1]

  La schermata coresponding in SCDPM è simile al seguente. <br/> ![Schermata di importazione di DPM][8]

dove:

+ **Percorso gestione temporanea** -fa riferimento il percorso di archiviazione temporanea in cui verrà scritta la copia di backup iniziale. Potrebbe trattarsi di una condivisione di rete o sul computer locale.

+ **Nome del processo di importazione azure** -come parte del completamento del flusso di lavoro, è necessario creare un *processo di importazione* nel portale di Azure (illustrato nella parte successiva del documento). Fornire un input che si prevede di utilizzare in un secondo momento nel portale di Azure.

+ **Impostazioni di pubblicazione azure** -si tratta di un file XML che contiene informazioni sul profilo sottoscrizione. Include inoltre proteggere credenziali associate alla sottoscrizione. Il file può essere scaricato dal [qui](https://manage.windowsazure.com/publishsettings). Specificare il percorso locale per il file di impostazioni di pubblicazione.

+ **ID sottoscrizione di azure** -fornire l'id sottoscrizione di Azure in cui si intende avviare il processo di importazione di Azure. Se si dispone di più sottoscrizioni di Azure, utilizzare l'ID associato al processo di importazione.

+ **Account di archiviazione azure** -immettere il nome dell'account di archiviazione Azure che verrà associato a questo processo di importazione.

+ **Contenitore di archiviazione azure** -immettere il nome del blob di archiviazione di destinazione in cui verranno importati i dati del processo.


Completare il flusso di lavoro e selezionare **ora Backup** in mmc di Backup di Azure per avviare la copia di backup non in linea. Area di gestione temporanea come parte di questo passaggio viene scritto il backup iniziale.<br/>

  ![Esegui backup ora][2]

Facendo clic su è abilitato il flusso di lavoro corrispondente in SCDPM il **gruppo protezione dati** e scegliendo il **Crea punto di ripristino** opzione. Questo è seguito scegliendo il **Online Protection** opzione.<br/> ![Ora Backup DPM][9]

Una volta completata l'operazione, un *. AIBBlob* e *. BaseBlob* file viene creato nel percorso di gestione temporaneo. <br/> ![Output][3]

### Preparare l'unità SATA

1. Scaricare il [strumento di importazione/esportazione di Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) per il *computer copia*. Assicurarsi che il percorso di gestione temporanea (nel passaggio precedente) è accessibile dal computer in cui si prevede di eseguire il set successivo di comandi. Se necessario, il computer di copia può essere lo stesso del computer di origine.

2. Decomprimere il *WAImportExport.zip* file. Eseguire il *WAImportExport* strumento che consente di formattare l'unità SATA, scrivere i dati di backup sull'unità SATA e alla crittografia. Prima di eseguire il comando seguente, verificare che BitLocker sia abilitato nel computer. <br/>

*.\WAImportExport.exe /j: PrepImport < * Filejournal*> .jrn /id: < * SessionId * > /sk: < * Chiaveaccountarchiviazione * > /BlobType: * * PageBlob * * /t: < * Letteraunitàdestinazione * >/formato / crittografare /srcdir: < * staging percorso * > /dstdir: < * Directoryvirtualeblobdestinazione * > / *


| Parametro | Descrizione|
|-------------|-------------|
| /j: < * Filejournal * >| Il percorso del file journal. Ogni unità deve essere esattamente un solo file journal. Si noti che il file journal non deve trovarsi nell'unità di destinazione. L'estensione del file journal è .jrn e viene creato come parte dell'esecuzione del comando.|
|/ID: < * SessionId * > | La sessione ID identifica un *copiare sessione*. Viene utilizzato per garantire un recupero accurato di una sessione di copia interrotta. I file vengono copiati in una sessione di copia vengono archiviati in una directory denominata dopo che l'ID di sessione nell'unità di destinazione.|
| /SK: < * Chiaveaccountarchiviazione * > | La chiave di account per l'account di archiviazione a cui verranno importati i dati. |
| / BlobType | Specificare **PageBlob**, il flusso di lavoro avrà esito positivo solo se è specificata l'opzione PageBlob. Questo non è l'opzione predefinita e deve essere indicato in questo comando. |
|/t: < * Letteraunitàdestinazione * > | La lettera di unità del disco rigido di destinazione per la sessione di copia corrente, senza i due punti finali.|
|/Format | Specificare questo parametro quando l'unità deve essere formattato; in caso contrario, ometterlo. Prima di formattare l'unità, chiederà conferma dalla console. Per eliminare la conferma, specificare il parametro /silentmode..|
|/ crittografia | Specificare questo parametro se l'unità non è ancora stata crittografata con BitLocker e deve essere crittografato utilizzando lo strumento. Se l'unità è già stato crittografato con BitLocker, omettere questo parametro e specificare il parametro /bk, fornendo la chiave BitLocker esistente. Se si specifica il parametro /format, allora è necessario specificare anche il / crittografare il parametro. |
|/srcdir: < * SourceDirectory * > | La directory di origine che contiene i file da copiare nell'unità di destinazione. Il percorso della directory deve essere un percorso assoluto (non un percorso relativo).|
|/dstdir: < * Directoryvirtualeblobdestinazione * > | Il percorso alla directory virtuale di destinazione nell'account di archiviazione di Microsoft Azure. Assicurarsi di utilizzare nomi di contenitore validi quando si specificano BLOB o directory virtuale di destinazione. Tenere presente che i nomi dei contenitori devono essere minuscole.|

  >[AZURE.NOTE]Nella cartella WAImportExport che acquisisce le informazioni del flusso di lavoro intera, viene creato un file journal. Questo file sarà necessario durante la creazione di un processo di importazione nel portale di Azure.

  ![Output di PowerShell][4]

### Creare il processo di importazione nel portale di Azure
1. Passare all'account di archiviazione nel [portale di gestione](https://manage.windowsazure.com/), e fare clic su **importazione/esportazione** e quindi su **Crea processo di importazione** nel riquadro attività. <br/> ![Portale][5]

2. Nel passaggio 1 della procedura guidata indicare di aver preparato l'unità e che il file journal dell'unità è disponibile. Nel passaggio 2 della procedura guidata, è necessario fornire informazioni di contatto per la persona responsabile di questo processo di importazione.

3. Nel passaggio 3, caricare i file journal dell'unità ottenuti durante la sezione precedente.

4. Nel passaggio 4 immettere un nome descrittivo per il processo di importazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto verrà utilizzato per tenere traccia dei processi mentre sono in corso e dopo essere stati completati.

5. Selezionare quindi l'area geografica del data center dall'elenco. L'area del data center indicherà il centro dati e l'indirizzo a cui è necessario spedire il pacchetto. <br/> ![DC][6]

6. Nel passaggio 5 selezionare il vettore di ritorno dall'elenco, quindi immettere il numero di account del vettore. Microsoft usa questo account per inviare le unità al cliente al completamento del processo di importazione.

7. Spedire il disco e immettere il numero di rilevamento per tenere traccia dello stato della spedizione. Una volta che il disco arriva nel centro dati, viene copiato nell'account di archiviazione e lo stato viene aggiornato. <br/>

![Stato completo][7]

### Completamento del flusso di lavoro
Quando i dati di backup iniziali sono disponibili nell'account di archiviazione, l'agente Azure Backup copia il contenuto dei dati da questo account per l'account di archiviazione di backup con tenant multipli. Nella pianificazione backup alla successiva, l'agente di Backup di Azure esegue il backup incrementale tramite la copia di backup iniziale.

## Passaggi successivi
+ Per domande sul flusso di lavoro di importazione/esportazione di Azure, fare riferimento a questo [articolo](storage-import-export-service.md).

+ Vedere la sezione di Backup non in linea di Azure Backup [domande frequenti su](backup-azure-backup-faq.md) per domande sul flusso di lavoro

<!--Image references-->
[1]: ./media/backup-azure-backup-import-export/importscreen.png
[2]: ./media/backup-azure-backup-import-export/backupnow.png
[3]: ./media/backup-azure-backup-import-export/opbackupnow.png
[4]: ./media/backup-azure-backup-import-export/psoutput.png
[5]: ./media/backup-azure-backup-import-export/azureportal.png
[6]: ./media/backup-azure-backup-import-export/dc.png
[7]: ./media/backup-azure-backup-import-export/complete.png
[8]: ./media/backup-azure-backup-import-export/dpmoffline.png
[9]: ./media/backup-azure-backup-import-export/dpmbackupnow.png

<!---HONumber=GIT-SubDir-->