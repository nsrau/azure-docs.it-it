<properties
   pageTitle="Backup di Azure - Backup e ripristino da un computer che esegue Windows Server o un client Windows"
   description="Informazioni su come eseguire operazioni di backup e ripristino da un computer che esegue Windows Server o un client Windows L'articolo descrive anche il ripristino su un server alternativo."
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Backup e ripristino da un computer che esegue Windows Server o un client Windows
Questo articolo illustra i passaggi necessari per eseguire il backup da un computer che esegue Windows Server o un client Windows. L'articolo descrive anche i passaggi necessari per ripristinare i file di backup nello stesso computer e quelli necessari per ripristinare tali file in un altro computer.

## File di backup
1. Dopo aver registrato il computer, aprire lo snap-in MMC di Backup di Microsoft Azure. <br/> ![Risultato della ricerca][1]

2. Fare clic su **Pianifica backup** <br/> ![Pianificazione di un backup][2].

3. Selezionare gli elementi che da sottoporre a backup. Backup di Azure in un computer che esegue Windows Server o un client Windows (ovvero senza System Center Data Protection Manager) consente di proteggere file e cartelle. <br/> ![Elementi da sottoporre a backup][3]

4. Specificare i criteri di conservazione e pianificazione del backup descritti in dettaglio nell'[articolo](backup-azure-backup-cloud-as-tape.md) seguente.

5. Scegliere il metodo di invio del backup iniziale. La scelta di eseguire il seeding iniziale dipende dalla quantità di dati da sottoporre a backup e dalla velocità del collegamento Internet durante il caricamento. Se si prevede di sottoporre a backup dati di dimensioni molto grandi su una latenza elevata e connessioni a larghezza di banda ridotta, si consiglia di completare il backup inviando un disco al data center di Azure più vicino. Questa procedura viene definita "Backup offline" e viene descritta in dettaglio in questo [articolo](https://msdn.microsoft.com/library/azure/dn894419.aspx). Se sono disponibili connessioni a larghezza di banda sufficiente, si consiglia di completare il backup iniziale sulla rete. <br/> ![Backup iniziale][4]

6. Dopo aver completato il flusso di lavoro, accedere nuovamente allo snap-in MMC e fare clic su **Esegui backup** per completare il seeding iniziale sulla rete. <br/> ![Esegui backup ora][5]

7. Dopo aver completato il seeding iniziale, la vista **Processi** nella console di Backup di Azure indica lo stato. <br/> ![Completamento infrarossi][6]

## Recuperare i dati nello stesso computer
Se un file è stato eliminato accidentalmente e si vuole ripristinare un volume o un file nello stesso computer da cui è stato eseguito il backup, attenersi alla seguente procedura.

1. Aprire lo snap-in di **Backup di Microsoft Azure**.

2. Fare clic su **Ripristina dati** per avviare il flusso di lavoro. <br/> ![Ripristino dei dati][7]

3. Selezionare l'opzione **Questo server (*nomecomputer*)**, in quanto si è deciso di ripristinare il file di backup nello stesso computer. <br/> ![Nello stesso computer][8]

4. È possibile scegliere di selezionare ** Sfoglia elenco file** o **Cerca file**. Se si intende ripristinare uno o più file con un percorso noto, lasciare l'opzione predefinita. Se non si è certi della struttura di cartelle, ma si vuole cercare un file, selezionare l'opzione **Cerca file**. Ai fini di questa sezione, si procede con l'opzione predefinita. <br/> ![Ricerca dei file][9]

5. Nella schermata successiva selezionare il volume da cui si vuole ripristinare il file. Questa schermata consente di ripristinare i file in qualsiasi momento. Le date visualizzate in **grassetto** nel controllo calendario indicano la disponibilità di un punto di ripristino. Dopo aver selezionato una data, in base alla pianificazione del backup e alla riuscita di un'operazione di backup, è possibile selezionare un orario dall'elenco a discesa **Ora**. <br/> ![Volume e dati][10]

6. Nella schermata successiva selezionare gli elementi da ripristinare. È possibile selezionare più cartelle e file per il ripristino. <br/> ![Selezione dei file][11]

7. Nella schermata successiva specificare i parametri di ripristino. <br/> ![Opzioni di ripristino][12]
  + È possibile scegliere di ripristinare i file nel percorso originale (in cui il file o la cartella verrà sovrascritta) o in un altro percorso nello stesso computer.

  + Se il file o la cartella da ripristinare esiste nel percorso di destinazione, è possibile creare copie (due versioni dello stesso file), sovrascrivere i file nel percorso di destinazione oppure ignorare il ripristino dei file esistenti nel database di destinazione.

  + È consigliabile lasciare l'opzione predefinita che prevede il ripristino degli elenchi di controllo di accesso sui file che vengono recuperati.

8. Dopo aver definito questi dati di input, viene avviato il flusso di lavoro di ripristino che consente di ripristinare i file nel computer specificato.

## Recuperare i dati in un altro computer
Se l'intero server viene perso, è comunque possibile recuperare il volume o il file in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

La nomenclatura usata nei passaggi è la seguente: + *Computer di origine*: il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.

  + *Computer di destinazione*: il computer in cui i dati vengono ripristinati.

  + *Insieme di credenziali di esempio*: l'insieme di credenziali di Backup in cui il *computer di origine* e il *computer di destinazione* sono registrati. <br/>

> [AZURE.NOTE]I backup eseguiti da un determinato computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, se i backup vengono eseguiti da un computer che esegue Windows 7, è possibile ripristinare i dati in un computer con Windows 8 o versione successiva. Tuttavia non è possibile eseguire l'operazione inversa.

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *computer di destinazione*.

2. Assicurarsi che il *computer di destinazione* e il *computer di origine* vengano ripristinati nello stesso insieme di credenziali di Backup (in questo articolo *insieme di credenziali di esempio*).

3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro. <br/> ![Ripristino dei dati][7]

4. Selezionare **Un altro server** <br/> ![In un altro server][13].

5. Specificare il file delle credenziali dell'insieme di credenziali che corrisponde all'*insieme di credenziali di esempio*. Se il file delle credenziali dell'insieme di credenziali non è valido (o è scaduto), scaricarne uno nuovo dall'*insieme di credenziali di esempio* nel portale di Azure. Dopo aver specificato il file delle credenziali dell'insieme di credenziali, in quest'ultimo viene visualizzato l'insieme di credenziali di backup.

6. Selezionare il *computer di origine* dall'elenco dei computer visualizzati. <br/> ![Elenco di computer][14]

7. Selezionare come in precedenza l'opzione **Cerca file** o **Sfoglia elenco file**. Ai fini di questa sezione, si userà l'opzione **Cerca file**. <br/> ![Search][15]

8. Nella schermata successiva selezionare la data e il volume. Cercare il nome delle cartelle e dei file da ripristinare. <br/> ![Ricerca di elementi][16]

9. Selezionare il percorso in cui devono essere ripristinati i file. <br/> ![Percorso di ripristino][17]

10. Specificare la passphrase di crittografia fornita durante la registrazione del *computer di origine* all'*insieme di credenziali di esempio*. <br/> ![Crittografia][18]

Dopo aver specificato i dati di input, fare clic sul pulsante **Ripristina** che attiva le operazioni di ripristino dei file di backup nella destinazione specificata.

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png
 

<!---HONumber=62-->