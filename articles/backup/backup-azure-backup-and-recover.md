<properties
	pageTitle="Azure Backup - backup e ripristino da un Client di Windows o Windows Server"
	description="Informazioni su come eseguire il backup e ripristino da un Client di Windows o Windows Server. L'articolo tratta inoltre di ripristino su server alternativo"
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

# Backup e ripristino da un server di Windows o un computer client Windows
In questo articolo vengono illustrati i passaggi necessari per backup da un server di Windows o un computer client Windows. Vengono inoltre descritti i passaggi necessari per ripristinare i backup dei file nel computer stesso e i passaggi necessari per ripristinare il backup dei file in un altro computer.

## File di backup
1. Una volta registrato il computer, aprire lo snap-in mmc di Microsoft Azure Backup. <br/> ![Risultato della ricerca][1]

2. Fare clic su **pianificare Backup** <br/> ![Pianifica Backup][2]

3. Selezionare gli elementi che si desidera eseguire il backup. Backup di Azure in un Client di Windows del Server di Windows (ovvero senza System Center Data Protection Manager) consente di proteggere i file e cartelle. <br/> ![Elementi per Backup][3]

4. Specificare i criteri di conservazione e pianificazione di backup descritta in dettaglio di seguito [articolo](backup-azure-backup-cloud-as-tape.md)

5. Scegliere il metodo di invio del primo backup. La scelta di eseguire il seeding iniziale è dipenda dalla quantità di dati che si desidera effettuare il backup e la velocità di collegamento internet caricamento. Se si prevede di backup GB / TB di dati su una latenza elevata, bassa connessione larghezza di banda, si consiglia di completare il backup iniziale per la spedizione di un disco al centro dati di Azure più vicino. Viene chiamato "Offline Backup" e viene descritto in dettaglio in questa [articolo](https://msdn.microsoft.com/library/azure/dn894419.aspx). Se si dispone di sufficiente larghezza di banda della connessione si consiglia di completare il backup iniziale sulla rete. <br/> ![Backup iniziale][4]

6. Una volta completato il flusso di lavoro, accedere nuovamente a mmc snap-in e fare clic su **Esegui backup** per completare il seeding iniziale sulla rete. <br/> ![Esegui backup ora][5]

7. Una volta completato il seeding iniziale, il **processi** visualizzazione nella console di Azure Backup indica lo stato. <br/> ![IR completo][6]

## Ripristinare i dati nello stesso computer
Se è stato eliminato accidentalmente un file e si desidera ripristinare un volume file/nello stesso computer (da cui viene eseguito il backup), la procedura seguente sarebbe consentono di recuperare i dati.

1. Aprire il **Microsoft Azure Backup** snap in.

2. Fare clic su **ripristinare dati** per avviare il flusso di lavoro. <br/> ![Ripristinare i dati][7]

3. Selezionare **questo server (* * nomemacchina)** opzione come si intende ripristinare il backup di file nello stesso computer. <br/> ![Nello stesso computer][8]

4. È possibile scegliere di **cercare file** o **Cerca file**. Se si intende ripristinare uno o più file con un percorso è noto, lasciare l'opzione predefinita. Se si dubbi sulla struttura di cartelle ma si desidera cercare un file, selezionare il **Cerca file** opzione. In questa sezione si procede con l'opzione predefinita. <br/> ![Sfoglia file][9]

5. Nella schermata successiva selezionare il volume da cui si desidera ripristinare il file. Consente di schermata che ripristinare da qualsiasi punto nel tempo. Le date visualizzate in **grassetto** nel controllo calendario indicare la disponibilità di un punto di ripristino. Dopo aver selezionata una data, in base alla pianificazione del backup e il successo di un'operazione di backup, è possibile selezionare un punto nel tempo dal **tempo** elenco a discesa. <br/> ![Volume e alla data][10]

6. Nella schermata successiva selezionare gli elementi che si desidera ripristinare. È possibile selezioni più cartelle e file che si desidera ripristinare. <br/> ![Selezionare i file][11]

7. Nella schermata successiva, specificare i parametri di ripristino. <br/> ![Opzioni di ripristino][12]
  + Si dispone di un'opzione di ripristino nel percorso originale (in cui il file o la cartella verranno sovrascritti) o in un altro percorso sullo stesso computer.

  + Se il file o la cartella che si desidera ripristinare, esiste nel percorso di destinazione, è possibile creare copie (due versioni dello stesso file), o sovrascrivere i file nel percorso di destinazione o ignorare il ripristino dei file che esiste nel database di destinazione.

  + È consigliabile lasciare l'opzione predefinita di ripristino degli ACL sui file che vengano ripristinati.

8. Una volta forniti i dati di input, viene avviato il flusso di lavoro di ripristino che consente di ripristinare i file di questo computer.

## Ripristina in un computer alternativo
Se l'intero server viene perso, è comunque possibile ripristinare il volume o del file in un computer diverso. La procedura seguente illustra il flusso di lavoro.

La nomenclatura utilizzata nei passaggi sono i seguenti: + *macchina di origine* : il computer originale da cui è stato eseguito il backup e non è attualmente disponibile.

  + *Computer di destinazione* : il computer in cui i dati vengono recuperati.

  + *Insieme di credenziali di esempio* – insieme di credenziali per il Backup a cui il *macchina di origine* e *computer di destinazione* vengono registrati. <br/>

> [AZURE.NOTE]Impossibile ripristinare i backup eseguiti da un computer in un computer che esegue una versione precedente del sistema operativo. Ad esempio, se vengono eseguiti backup da una macchina Windows 7, è possibile ripristinare in un Windows 8 o versione successiva del computer. Tuttavia il viceversa non ha valore true.

1. Aprire il **Microsoft Azure Backup** snap nel *computer di destinazione*.

2. Assicurarsi che il *computer di destinazione* e *macchina di origine* vengono ripristinati lo stesso archivio di Backup (in questo articolo - *insieme di credenziali di esempio*).

3. Fare clic su **ripristinare dati** per avviare il flusso di lavoro. <br/> ![Ripristinare i dati][7]

4. Selezionare **un altro server** <br/> ![Un altro Server][13]

5. Fornire il file delle credenziali insieme di credenziali che corrisponde alla *insieme di credenziali di esempio*. Se il file delle credenziali di archivio non è valido (o scaduti), scaricare un nuovo file di archivio credenziali dal *insieme di credenziali di esempio* nel portale di Azure. Una volta che viene fornito il file delle credenziali di archivio, viene visualizzato l'archivio di backup nel file di archivio delle credenziali.

6. Selezionare il *macchina di origine* dall'elenco di computer visualizzato. <br/> ![Elenco di computer][14]

7. Selezionare come in precedenza il **Cerca file** o **cercare file** opzione. In questa sezione si utilizzerà il **Cerca file** opzione. <br/> ![Search][15]

8. Nella schermata successiva, selezionare la data e il volume. Cercare il nome di cartelle e file che si desidera ripristinare. <br/> ![Elementi di ricerca][16]

9. Selezionare il percorso in cui deve essere ripristinato i file. <br/> ![Percorso di ripristino][17]

10. Fornire la passphrase di crittografia è stata fornita durante *macchina di origine* registrazione *insieme di credenziali di esempio*. <br/> ![Crittografia][18]

Una volta che viene fornito l'input, fare clic sui **ripristinare** pulsante che attiva i ripristini il backup dei file nella destinazione forniti.

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

<!---HONumber=GIT-SubDir--> 