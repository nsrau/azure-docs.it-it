<properties
	pageTitle="Azure Backup - backup e ripristino da un Client di Windows o Windows Server | Microsoft Azure " 
	description="informazioni su come eseguire il backup e ripristino da un Client di Windows o Windows Server. L'articolo tratta inoltre di ripristino su server alternativo"
	services="backup"
	documentationCenter=""
	authors="Jim Parker"
	manager="jwhit"
	editor=""/>
	
<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="jimpark"; "aashishr"/>

# Backup e ripristino da un computer che esegue Windows Server o un client Windows
Questo articolo illustra i passaggi necessari per eseguire il backup da un computer che esegue Windows Server o un client Windows. L'articolo descrive anche i passaggi necessari per ripristinare i file di backup nello stesso computer e quelli necessari per ripristinare tali file in un altro computer.

## File di backup

1. Dopo aver registrato il computer, aprire lo snap-in MMC di Backup di Microsoft Azure.

    ![Risultato della ricerca](./media/backup-azure-backup-and-recover/result.png)

2. Fare clic su **Pianifica backup**.

    ![Pianificazione di un backup](./media/backup-azure-backup-and-recover/schedulebackup.png)

3. Selezionare gli elementi che da sottoporre a backup. Backup di Azure in un computer che esegue Windows Server o un client Windows (ovvero senza System Center Data Protection Manager) consente di proteggere file e cartelle.

    ![Elementi da sottoporre a backup](./media/backup-azure-backup-and-recover/items.png)

4. Specificare i criteri di conservazione e pianificazione del backup descritti in dettaglio nell'[articolo](backup-azure-backup-cloud-as-tape.md) seguente.

5. Scegliere il metodo di invio del backup iniziale. La scelta di eseguire il seeding iniziale dipende dalla quantità di dati da sottoporre a backup e dalla velocità del collegamento Internet durante il caricamento. Se si prevede di sottoporre a backup dati di dimensioni molto grandi su una latenza elevata e connessioni a larghezza di banda ridotta, si consiglia di completare il backup inviando un disco al data center di Azure più vicino. Questa procedura viene definita "Backup offline" e viene descritta in dettaglio in questo [articolo](backup-azure-backup-import-export.md). Se sono disponibili connessioni a larghezza di banda sufficiente, si consiglia di completare il backup iniziale sulla rete.

    ![Backup iniziale](./media/backup-azure-backup-and-recover/initialbackup.png)

6. Dopo aver completato il flusso di lavoro, accedere nuovamente allo snap-in MMC e fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Esegui backup ora](./media/backup-azure-backup-and-recover/backupnow.png)

7. Dopo aver completato il seeding iniziale, la vista **Processi** nella console di Backup di Azure indica lo stato.

    ![Completamento infrarossi](./media/backup-azure-backup-and-recover/ircomplete.png)

## Recuperare i dati nello stesso computer
Se un file è stato eliminato accidentalmente e si vuole ripristinare un volume o un file nello stesso computer da cui è stato eseguito il backup, attenersi alla seguente procedura.

1. Aprire lo snap-in di **Backup di Microsoft Azure**.

2. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristino dei dati](./media/backup-azure-backup-and-recover/recover.png)

3. Selezionare l'opzione **Questo server (*nomecomputer*)**, in quanto si è deciso di ripristinare il file di backup nello stesso computer.

    ![Nello stesso computer](./media/backup-azure-backup-and-recover/samemachine.png)

4. È possibile scegliere di selezionare ** Sfoglia elenco file** o **Cerca file**. Se si intende ripristinare uno o più file con un percorso noto, lasciare l'opzione predefinita. Se non si è certi della struttura di cartelle, ma si vuole cercare un file, selezionare l'opzione **Cerca file**. Ai fini di questa sezione, si procede con l'opzione predefinita.

    ![Ricerca dei file](./media/backup-azure-backup-and-recover/browseandsearch.png)

5. Nella schermata successiva selezionare il volume da cui si vuole ripristinare il file. Questa schermata consente di ripristinare i file in qualsiasi momento. Le date visualizzate in **grassetto** nel controllo calendario indicano la disponibilità di un punto di ripristino. Dopo aver selezionato una data, in base alla pianificazione del backup e alla riuscita di un'operazione di backup, è possibile selezionare un orario dall'elenco a discesa **Ora**.

    ![Volume e dati](./media/backup-azure-backup-and-recover/volanddate.png)

6. Selezionare gli elementi da ripristinare. È possibile selezionare più cartelle e file per il ripristino.

    ![Selezione dei file](./media/backup-azure-backup-and-recover/selectfiles.png)

7. Specificare i parametri di ripristino.

    ![Opzioni di ripristino](./media/backup-azure-backup-and-recover/recoveroptions.png)

  - È possibile scegliere di ripristinare i file nel percorso originale (in cui il file o la cartella verrà sovrascritta) o in un altro percorso nello stesso computer.
  - Se il file o la cartella da ripristinare esiste nel percorso di destinazione, è possibile creare copie (due versioni dello stesso file), sovrascrivere i file nel percorso di destinazione oppure ignorare il ripristino dei file esistenti nel database di destinazione.
  - È consigliabile lasciare l'opzione predefinita che prevede il ripristino degli elenchi di controllo di accesso sui file che vengono recuperati.

8. Dopo aver definito questi dati di input, viene avviato il flusso di lavoro di ripristino che consente di ripristinare i file nel computer specificato.

## Recuperare i dati in un altro computer
Se l'intero server viene perso, è comunque possibile recuperare il volume o il file in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

La nomenclatura utilizzata nei passaggi riportati di seguito sono:-*macchina di origine*– computer originale da cui è stato eseguito il backup e non è attualmente disponibile. -*computer di destinazione*: il computer in cui i dati vengono recuperati. -*insieme di credenziali di esempio*– insieme di credenziali di Backup a cui il*macchina di origine*e*computer di destinazione*vengono registrati.<br/>

> [AZURE.NOTE]I backup eseguiti da un determinato computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, se i backup vengono eseguiti da un computer che esegue Windows 7, è possibile ripristinare i dati in un computer con Windows 8 o versione successiva. Tuttavia non è possibile eseguire l'operazione inversa.

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *computer di destinazione*.

2. Assicurarsi che il *computer di destinazione* e il *computer di origine* vengano ripristinati nello stesso insieme di credenziali di Backup (in questo articolo insieme di credenziali di esempio).

3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristino dei dati](./media/backup-azure-backup-and-recover/recover.png)

4. Selezionare **Un altro server**

    ![In un altro server](./media/backup-azure-backup-and-recover/anotherserver.png)

5. Specificare il file delle credenziali dell'insieme di credenziali che corrisponde all'*insieme di credenziali di esempio*. Se il file delle credenziali dell'insieme di credenziali non è valido (o è scaduto), scaricarne uno nuovo dall'*insieme di credenziali di esempio* nel portale di Azure. Dopo aver specificato il file delle credenziali dell'insieme di credenziali, in quest'ultimo viene visualizzato l'insieme di credenziali di backup.

6. Selezionare il *computer di origine* dall'elenco dei computer visualizzati.

    ![Elenco di computer](./media/backup-azure-backup-and-recover/machinelist.png)

7. Selezionare come in precedenza l'opzione **Cerca file** o **Sfoglia elenco file**. Ai fini di questa sezione, si userà l'opzione **Cerca file**.

    ![Search](./media/backup-azure-backup-and-recover/search.png)

8. Nella schermata successiva selezionare la data e il volume. Cercare il nome delle cartelle e dei file da ripristinare.

    ![Ricerca di elementi](./media/backup-azure-backup-and-recover/searchitems.png)

9. Selezionare il percorso in cui devono essere ripristinati i file.

    ![Percorso di ripristino](./media/backup-azure-backup-and-recover/restorelocation.png)

10. Specificare la passphrase di crittografia fornita durante la registrazione del *computer di origine* all'*insieme di credenziali di esempio*.

    ![Crittografia](./media/backup-azure-backup-and-recover/encryption.png)

11. Dopo aver specificato i dati di input, fare clic sul pulsante **Ripristina** che attiva le operazioni di ripristino dei file di backup nella destinazione specificata.

## Passaggi successivi
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

<!---HONumber=July15_HO3-->