---
title: Ripristinare i dati in Windows Server o in un client di Windows da Azure con il modello di distribuzione classica | Documentazione Microsoft
description: Informazioni su come eseguire operazioni di ripristino da un computer che esegue Windows Server o un client Windows.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: cb08d5c80954e1c231bf6e8b781b09700ef0959d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Ripristinare file in un computer di Windows Server o in un client di Windows con il modello di distribuzione classica
> [!div class="op_single_selector"]
> * [Portale classico](backup-azure-restore-windows-server-classic.md)
> * [Portale di Azure](backup-azure-restore-windows-server.md)
>
>

Questo articolo spiega come recuperare i dati da un insieme di credenziali di backup e ripristinarli in un server o in un computer. A partire da marzo 2017 non è più possibile creare insiemi di credenziali di backup nel portale classico.

> [!IMPORTANT]
> È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino.<br/> Dopo il **30 novembre 2017** non sarà possibile usare PowerShell per creare insiemi di credenziali di backup. <br/> **A partire dal 30 novembre 2017**:
>- Eventuali insiemi di credenziali di Backup rimanenti verranno automaticamente aggiornati a insiemi di credenziali di servizi di ripristino
>- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.
>

Per ripristinare i dati, usare la procedura di ripristino guidato dei dati nell'agente di Servizi di ripristino di Microsoft Azure (MARS). Quando si ripristinano dati, è possibile:

* Ripristinare i dati nello stesso computer in cui sono stati eseguiti i backup.
* Ripristinare i dati in un'altra macchina.

A gennaio 2017 Microsoft ha rilasciato un aggiornamento in anteprima all'agente MARS. Insieme alle correzioni di bug, questo aggiornamento abilita la funzione di ripristino istantaneo, che consente di montare uno snapshot del punto di ripristino scrivibile come volume di ripristino. È quindi possibile esplorare il volume di ripristino e i file di copia in un computer locale, ripristinando quindi i file in modo selettivo.

> [!NOTE]
> Se si vuole usare questa funzione per ripristinare i dati, è necessario l'[aggiornamento di Backup di Azure di gennaio 2017](https://support.microsoft.com/en-us/help/3216528?preview). È necessario anche che i dati di backup siano protetti in insiemi di credenziali nelle impostazioni locali elencate nell'articolo del supporto tecnico. Consultare l'[aggiornamento di Azure Backup di gennaio 2017](https://support.microsoft.com/en-us/help/3216528?preview) per un elenco aggiornato delle impostazioni locali che supportano la funzione di ripristino istantaneo. Il ripristino istantaneo **non** è attualmente disponibile in tutte le impostazioni locali.
>

È disponibile per l'uso negli insiemi di credenziali di Servizi di ripristino nel portale di Azure e negli insiemi di credenziali di Backup nel portale classico. Se si vuole usare la funzione di ripristino istantaneo, scaricare l'aggiornamento di MARS e seguire le procedure relative al ripristino istantaneo.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Usare la funzione di ripristino istantaneo per ripristinare i dati sullo stesso computer

Se un file è stato eliminato accidentalmente e lo si vuole ripristinare nello stesso computer in cui è stato eseguito il backup, la seguente procedura permette di recuperarlo.

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server.

    L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

2. Fare clic su **Ripristina dati** per avviare la procedura guidata.

    ![Ripristina dati](./media/backup-azure-restore-windows-server/recover.png)

3. Nel riquadro **Guida introduttiva** selezionare l'opzione **This server (Questo server) (`<server name>`)** e fare clic su **Avanti** per ripristinare i dati nello stesso server o computer.

    ![Scegliere l'opzione This server (Questo server) per ripristinare i dati nello stesso computer](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Nel riquadro **Seleziona modalità di ripristino** selezionare **Singoli file e cartelle** e fare clic su **Avanti**.

    ![Ricerca dei file](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. Nel riquadro **Seleziona volume e data** selezionare il volume che contiene i file e/o le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere quello appropriato dall'elenco a discesa **Ora**.

    ![Volume e dati](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Dopo aver selezionato il punto di ripristino, fare clic su **Monta**.

    Backup di Azure monta il punto di ripristino locale e lo usa come volume di ripristino.

7. Nel riquadro **Cerca e ripristina file** fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Opzioni di ripristino](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. In Esplora risorse copiare i file e/o le cartelle che si desidera ripristinare e incollarle in qualsiasi posizione locale nel server o nel computer. È possibile aprire o trasmettere i file direttamente dal volume di ripristino e verificare che vengano ripristinate le versioni corrette.

    ![Copiare e incollare file e cartelle dal volume montato alla posizione locale](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Dopo aver terminato il ripristino di file e/o cartelle, fare clic su **Smonta** nel riquadro **Cerca e ripristina file**. Fare clic su **Sì** per confermare che si desidera smontare il volume.

    ![Smontare il volume e confermare](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si fa clic su Smonta, il volume di ripristino resterà montato per sei ore. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >


## <a name="recover-data-to-the-same-machine"></a>Recuperare i dati nello stesso computer
Se un file è stato eliminato accidentalmente e lo si vuole ripristinare nello stesso computer in cui è stato eseguito il backup, la seguente procedura permette di recuperarlo.

1. Aprire lo snap-in di **Backup di Microsoft Azure** .
2. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristina dati](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Selezionare l'opzione **Questo server (*nomecomputer*)** per ripristinare il file di backup nello stesso computer.

    ![Nello stesso computer](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. È possibile scegliere **Sfoglia elenco file** o **Cerca file**.

    Se si intende ripristinare uno o più file con un percorso noto, lasciare l'opzione predefinita. Se non si è certi della struttura di cartelle, ma si vuole cercare un file, selezionare l'opzione **Cerca file** . Ai fini di questa sezione, si procede con l'opzione predefinita.

    ![Ricerca dei file](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Nella schermata successiva selezionare il volume da cui si vuole ripristinare il file.

    È possibile ripristinare da qualsiasi data. Le date visualizzate in **grassetto** nel controllo calendario indicano la disponibilità di un punto di ripristino. Dopo aver selezionato una data, in base alla pianificazione del backup (e alla riuscita di un'operazione di backup), è possibile selezionare un orario dall'elenco a discesa **Ora** .

    ![Volume e dati](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Selezionare gli elementi da ripristinare. È possibile selezionare più cartelle e file per il ripristino.

    ![Selezione dei file](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Specificare i parametri di ripristino.

    ![Opzioni di ripristino](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * È possibile scegliere di ripristinare i file nel percorso originale (in cui il file o la cartella verrà sovrascritta) o in un altro percorso nello stesso computer.
   * Se il file o la cartella da ripristinare esiste nel percorso di destinazione, è possibile creare copie (due versioni dello stesso file), sovrascrivere i file nel percorso di destinazione oppure ignorare il ripristino dei file esistenti nel database di destinazione.
   * È consigliabile lasciare l'opzione predefinita che prevede il ripristino degli elenchi di controllo di accesso sui file che vengono recuperati.
8. Una volta forniti i dati di input, fare clic su **Avanti**. Il flusso di lavoro di ripristino, che consente di ripristinare i file in questo computer, verrà avviato.

## <a name="recover-to-an-alternate-machine"></a>Recuperare i dati in un altro computer
Se l'intero server viene perso, è comunque possibile recuperare dati da Backup di Azure in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.  

Include la terminologia utilizzata in questi passaggi:

* *Computer di origine* : il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
* *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
* *Insieme di credenziali di esempio*: insieme di credenziali di backup in cui *computer di origine* e *computer di destinazione* sono registrati. <br/>

> [!NOTE]
> I backup eseguiti da un determinato computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, se i backup vengono eseguiti da un computer che esegue Windows 7, è possibile ripristinare i dati in un computer con Windows 8 o versione successiva. Tuttavia non è possibile eseguire l'operazione inversa.
>
>

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *Computer di destinazione*.
2. Verificare che il *computer di destinazione* e il *computer di origine* siano registrati nello stesso insieme di credenziali di backup.
3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristina dati](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Selezionare **Un altro server**

    ![Un altro server](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Specificare il file dell'insieme di credenziali che corrisponde all' *Insieme di credenziali di esempio*. Se il file dell'insieme di credenziali non è valido (o è scaduto), scaricarne uno nuovo dall' *Insieme di credenziali di esempio* nel portale classico di Azure. Dopo aver specificato il file delle credenziali dell'insieme di credenziali, in quest'ultimo viene visualizzato l'insieme di credenziali di backup.
6. Selezionare il *computer di origine* dall'elenco dei computer visualizzati.

    ![Elenco di computer](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Selezionare l'opzione **Cerca file** o **Sfoglia elenco file**. Ai fini di questa sezione, si userà l'opzione **Cerca file** .

    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)
8. Nella schermata successiva selezionare la data e il volume. Cercare il nome delle cartelle e dei file da ripristinare.

    ![Ricerca di elementi](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Selezionare il percorso in cui devono essere ripristinati i file.

    ![Percorso di ripristino](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Specificare la passphrase di crittografia indicata durante la registrazione del *computer di origine* all'*insieme di credenziali di esempio*.

    ![Crittografia](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Dopo aver specificato i dati di input, fare clic sul pulsante **Ripristina**che attiva le operazioni di ripristino dei file di backup nella destinazione specificata.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usare il ripristino immediato per ripristinare i dati in un altro computer
Se l'intero server viene perso, è comunque possibile recuperare dati da Backup di Azure in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

Include la terminologia utilizzata in questi passaggi:

* *Computer di origine* : il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
* *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
* *Insieme di credenziali di esempio*: l'insieme di credenziali dei servizi di ripristino in cui il *computer di origine* e il *computer di destinazione* sono registrati. <br/>

> [!NOTE]
> Non è possibile ripristinare i backup in un computer di destinazione che esegue una versione precedente del sistema operativo. Ad esempio, un backup eseguito su un computer con Windows 7 può essere ripristinato in un computer con Windows 8 o versioni successive. Un backup eseguito su un computer con Windows 8 non può essere ripristinato in un computer con Windows 7.
>
>

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *Computer di destinazione*.

2. Assicurarsi che il *computer di destinazione* e il *computer di origine* siano registrati nello stesso insieme di credenziali dei servizi di ripristino.

3. Fare clic su **Ripristina dati** per aprire il **ripristino guidato dei dati**.

    ![Ripristina dati](./media/backup-azure-restore-windows-server/recover.png)

4. Nel riquadro **Guida introduttiva** selezionare **Another server** (Un altro server)

    ![Un altro server](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all'*insieme di credenziali di esempio*, quindi fare clic su **Avanti**.

    Se il file dell'insieme di credenziali non è valido (o è scaduto), è necessario scaricarne uno nuovo dall'*insieme di credenziali di esempio* nel Portale di Azure. Dopo aver specificato un insieme di credenziali valido, viene visualizzato il nome dell'insieme di credenziali di backup corrispondente.

6. Nel riquadro **Seleziona server di backup** selezionare il *computer di origine* dall'elenco dei computer visualizzati e specificare la passphrase. Quindi fare clic su **Next**.

    ![Elenco di computer](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Nel riquadro **Seleziona modalità di ripristino** selezionare **Singoli file e cartelle** e fare clic su **Avanti**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Nel riquadro **Seleziona volume e data** selezionare il volume che contiene i file e/o le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere quello appropriato dall'elenco a discesa **Ora**.

    ![Ricerca di elementi](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Fare clic su **Monta** per montare localmente il punto di ripristino come volume di ripristino sul *computer di destinazione*.

10. Nel riquadro **Cerca e ripristina file** fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Crittografia](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. In Esplora risorse copiare i file e/o le cartelle dal volume di ripristino e incollarli nella posizione del *computer di destinazione*. È possibile aprire o trasmettere i file direttamente dal volume di ripristino e verificare che vengano ripristinate le versioni corrette.

    ![Crittografia](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Dopo aver terminato il ripristino di file e/o cartelle, fare clic su **Smonta** nel riquadro **Cerca e ripristina file**. Fare clic su **Sì** per confermare che si desidera smontare il volume.

    ![Crittografia](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si fa clic su Smonta, il volume di ripristino resterà montato per sei ore. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >


## <a name="next-steps"></a>Passaggi successivi
* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
* Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Altre informazioni
* [Panoramica di Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Eseguire il backup di macchine virtuali di Azure](backup-azure-vms-introduction.md)
* [Eseguire il backup dei carichi di lavoro di Microsoft](backup-azure-dpm-introduction.md)
