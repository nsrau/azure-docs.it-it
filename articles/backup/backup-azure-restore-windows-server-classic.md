<properties
   pageTitle="Ripristinare i dati in Windows Server o in un client di Windows da Azure con il modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come eseguire operazioni di ripristino da un computer che esegue Windows Server o un client Windows."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="05/09/2016"
	 ms.author="trinadhk; jimpark; markgal;"/>

# Ripristinare file in un computer di Windows Server o in un client di Windows con il modello di distribuzione classica

> [AZURE.SELECTOR]
- [Portale classico](backup-azure-restore-windows-server-classic.md)
- [Portale di Azure](backup-azure-restore-windows-server.md)

In questo articolo viene illustrata la procedura necessaria per eseguire due tipi di operazioni di ripristino:

- Ripristinare i dati nello stesso computer in cui sono stati eseguiti i backup.
- Ripristinare i dati in un altro computer.

In entrambi i casi, i dati vengono recuperati dall'insieme di credenziali di Backup di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Recuperare i dati nello stesso computer
Se un file è stato eliminato accidentalmente e lo si vuole ripristinare nello stesso computer in cui è stato eseguito il backup, la seguente procedura permette di recuperarlo.

1. Aprire lo snap-in di **Backup di Microsoft Azure**.
2. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristino dei dati](./media/backup-azure-restore-windows-server-classic/recover.png)

3. Selezionare l'opzione **Questo server (*nomecomputer*)**, se si è deciso di ripristinare il file di backup nello stesso computer.

    ![Nello stesso computer](./media/backup-azure-restore-windows-server-classic/samemachine.png)

4. È possibile scegliere di selezionare **Sfoglia elenco file** o **Cerca file**.

    Se si intende ripristinare uno o più file con un percorso noto, lasciare l'opzione predefinita. Se non si è certi della struttura di cartelle, ma si vuole cercare un file, selezionare l'opzione **Cerca file**. Ai fini di questa sezione, si procede con l'opzione predefinita.

    ![Ricerca dei file](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)

5. Nella schermata successiva selezionare il volume da cui si vuole ripristinare il file.

    È possibile ripristinare da qualsiasi data. Le date visualizzate in **grassetto** nel controllo calendario indicano la disponibilità di un punto di ripristino. Dopo aver selezionato una data, in base alla pianificazione del backup (e alla riuscita di un'operazione di backup), è possibile selezionare un orario dall'elenco a discesa **Ora**.

    ![Volume e dati](./media/backup-azure-restore-windows-server-classic/volanddate.png)

6. Selezionare gli elementi da ripristinare. È possibile selezionare più cartelle e file per il ripristino.

    ![Selezione dei file](./media/backup-azure-restore-windows-server-classic/selectfiles.png)

7. Specificare i parametri di ripristino.

    ![Opzioni di ripristino](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

  - È possibile scegliere di ripristinare i file nel percorso originale (in cui il file o la cartella verrà sovrascritta) o in un altro percorso nello stesso computer.
  - Se il file o la cartella da ripristinare esiste nel percorso di destinazione, è possibile creare copie (due versioni dello stesso file), sovrascrivere i file nel percorso di destinazione oppure ignorare il ripristino dei file esistenti nel database di destinazione.
  - È consigliabile lasciare l'opzione predefinita che prevede il ripristino degli elenchi di controllo di accesso sui file che vengono recuperati.

8. Una volta forniti i dati di input, fare clic su **Avanti**. Il flusso di lavoro di ripristino, che consente di ripristinare i file in questo computer, verrà avviato.

## Recuperare i dati in un altro computer
Se l'intero server viene perso, è comunque possibile recuperare dati da Backup di Azure in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

Include la terminologia utilizzata in questi passaggi:

- *Computer di origine*: il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
- *Computer di destinazione*: il computer in cui i dati vengono ripristinati.
- *Insieme di credenziali di esempio*: l'insieme di credenziali di Backup in cui il *computer di origine* e il *computer di destinazione* sono registrati. <br/>

> [AZURE.NOTE] I backup eseguiti da un determinato computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, se i backup vengono eseguiti da un computer che esegue Windows 7, è possibile ripristinare i dati in un computer con Windows 8 o versione successiva. Tuttavia non è possibile eseguire l'operazione inversa.

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *Computer di destinazione*.
2. Assicurarsi che il *Computer di destinazione* e il *Computer di origine* siano registrati nello stesso insieme di credenziali di Backup.
3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristino dei dati](./media/backup-azure-restore-windows-server-classic/recover.png)

4. Selezionare **Un altro server**

    ![In un altro server](./media/backup-azure-restore-windows-server-classic/anotherserver.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all'*Insieme di credenziali di esempio*. Se il file dell'insieme di credenziali non è valido (o è scaduto), scaricarne uno nuovo dall'*Insieme di credenziali di esempio* nel portale classico di Azure. Dopo aver specificato il file delle credenziali dell'insieme di credenziali, in quest'ultimo viene visualizzato l'insieme di credenziali di backup.

6. Selezionare il *computer di origine* dall'elenco dei computer visualizzati.

    ![Elenco di computer](./media/backup-azure-restore-windows-server-classic/machinelist.png)

7. Selezionare come in precedenza l'opzione **Cerca file** o **Sfoglia elenco file**. Ai fini di questa sezione, si userà l'opzione **Cerca file**.

    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)

8. Nella schermata successiva selezionare la data e il volume. Cercare il nome delle cartelle e dei file da ripristinare.

    ![Ricerca di elementi](./media/backup-azure-restore-windows-server-classic/searchitems.png)

9. Selezionare il percorso in cui devono essere ripristinati i file.

    ![Percorso di ripristino](./media/backup-azure-restore-windows-server-classic/restorelocation.png)

10. Specificare la passphrase di crittografia fornita durante la registrazione del *computer di origine* all'*insieme di credenziali di esempio*.

    ![Crittografia](./media/backup-azure-restore-windows-server-classic/encryption.png)

11. Dopo aver specificato i dati di input, fare clic sul pulsante **Ripristina** che attiva le operazioni di ripristino dei file di backup nella destinazione specificata.

## Passaggi successivi
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## Altre informazioni
- [Panoramica di Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425)
- [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms-introduction.md)
- [Eseguire il backup dei carichi di lavoro di Microsoft](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0629_2016-->