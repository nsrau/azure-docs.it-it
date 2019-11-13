---
title: Ripristinare i dati in Azure in un server Windows-backup di Azure
description: Questo articolo illustra come ripristinare i dati archiviati in Azure in un computer Windows Server o Windows con l'agente di Servizi di ripristino di Microsoft Azure (MARS).
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: dacurwin
ms.openlocfilehash: 4d9ab47d83caadda9046481c15dfb6af18aee146
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012186"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Ripristinare i file in Windows usando il modello di distribuzione Azure Resource Manager

Questo articolo illustra come ripristinare i dati da un insieme di credenziali di backup. Per ripristinare i dati, usare il Ripristino guidato dei dati nell'agente di Servizi di ripristino di Microsoft Azure (MARS). È possibile:

* Ripristinare i dati nello stesso computer in cui sono stati eseguiti i backup.
* Ripristinare i dati in un'altra macchina.

Usare la funzionalità di ripristino istantaneo per montare uno snapshot del punto di ripristino scrivibile come volume di ripristino. È quindi possibile esplorare il volume di ripristino e i file di copia in un computer locale, ripristinando quindi i file in modo selettivo.

> [!NOTE]
> Se si vuole usare questa funzione per ripristinare i dati, è necessario l'[aggiornamento di Backup di Azure di gennaio 2017](https://support.microsoft.com/help/3216528?preview). È necessario anche che i dati di backup siano protetti in insiemi di credenziali nelle impostazioni locali elencate nell'articolo del supporto tecnico. Consultare l'[aggiornamento di Azure Backup di gennaio 2017](https://support.microsoft.com/help/3216528?preview) per un elenco aggiornato delle impostazioni locali che supportano la funzione di ripristino istantaneo.
>

Usare la funzione di ripristino istantaneo con Servizi di ripristino nel portale di Azure. I dati archiviati negli insiemi di credenziali di backup sono stati convertiti in insiemi di credenziali di Servizi di ripristino. Se si vuole usare la funzione di ripristino istantaneo, scaricare l'aggiornamento di MARS e seguire le procedure relative al ripristino istantaneo.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Usare la funzione di ripristino istantaneo per ripristinare i dati sullo stesso computer

Se un file è stato eliminato accidentalmente e lo si vuole ripristinare nello stesso computer in cui è stato eseguito il backup, la procedura seguente consente di recuperarlo.

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server.

    L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

2. Selezionare **Ripristina dati** per avviare la procedura guidata.

    ![Schermata di Backup di Azure, con ripristino dei dati evidenziato](./media/backup-azure-restore-windows-server/recover.png)

3. Nella pagina **Guida introduttiva** selezionare l'opzione **This server (Questo server) (`<server name>`)**  > **Avanti** per ripristinare i dati nello stesso server o computer.

    ![Schermata della pagina introduttiva del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Nella pagina **Seleziona modalità di ripristino** scegliere **file e cartelle singoli** > **Avanti**.

    ![Schermata della pagina Seleziona modalità di ripristino del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > L'opzione per ripristinare singoli file e cartelle richiede .NET Framework versione 4.5.2 o successiva. Se l'opzione **Individual files and folders** (Singoli file e cartelle) non viene visualizzata, è necessario eseguire l'aggiornamento di .NET Framework alla versione 4.5.2 o successiva e riprovare.
 
   > [!TIP]
   > L'opzione **Individual files and folders** (Singoli file e cartelle) consente l'accesso rapido ai dati del punto di ripristino. Tale opzione è adatta per il ripristino di singoli file, con dimensioni massime di 80 GB e offre velocità di trasferimento o copia offerte fino a 6 MBps durante il ripristino. L'opzione **Volume** consente di ripristinare tutti i dati di cui è stato eseguito il backup in un volume specificato. Questa opzione offre maggiore velocità di trasferimento (fino a 60 MBps), ideale per il ripristino di dati di grandi dimensioni o di interi volumi.

5. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file e le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

    ![Schermata della pagina Seleziona volume e dati del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Dopo aver scelto il punto di ripristino, selezionare **Mount** (Monta).

    Backup di Azure monta il punto di ripristino locale e lo usa come volume di ripristino.

7. Nella pagina **Cerca e Ripristina file** selezionare **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Schermata della pagina di ricerca e ripristino dei file del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. In Esplora risorse copiare i file e le cartelle da ripristinare e incollarle in qualsiasi posizione locale nel server o nel computer. È possibile aprire o trasmettere i file direttamente dal volume di ripristino e verificare che vengano ripristinate le versioni corrette.

    ![Schermata di Windows Explorer, con la funzione di copia evidenziata](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Al termine dell'operazione, nella pagina **Browse and Recover Files** (Cerca e ripristina fine) selezionare **Unmount** (Smonta). Fare clic su **Sì** per confermare che si intende smontare il volume.

    ![Schermata della pagina di ricerca e ripristino dei file del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si fa clic su **Unmount** (Smonta), il volume di ripristino rimane montato per 6 ore. Il tempo di montaggio viene tuttavia esteso fino a un massimo di 24 ore in caso di copia dei file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usare il ripristino immediato per ripristinare i dati in un altro computer

Se l'intero server viene perso, è comunque possibile recuperare dati da Backup di Azure in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

Tali passaggi usano la terminologia seguente:

* *Computer di origine*: il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
* *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
* *Insieme di credenziali di esempio*: l'insieme di credenziali dei servizi di ripristino in cui il computer di origine e il computer di destinazione sono registrati.

> [!NOTE]
> Non è possibile ripristinare i backup in un computer di destinazione che esegue una versione precedente del sistema operativo. Ad esempio, è possibile ripristinare un backup effettuato da un computer Windows 7 in un computer Windows 7 (o versione successiva). Un backup eseguito su un computer con Windows 8 non può essere ripristinato in un computer con Windows 7.
>
>

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel computer di destinazione.

2. Assicurarsi che il computer di destinazione e il computer di origine siano registrati nello stesso insieme di credenziali dei servizi di ripristino.

3. Selezionare **Ripristina dati** per aprire il **Ripristino guidato dei dati**.

    ![Schermata di Backup di Azure, con ripristino dei dati evidenziato](./media/backup-azure-restore-windows-server/recover.png)

4. Nella pagina **Guida introduttiva** selezionare **Another server** (Un altro server).

    ![Schermata della pagina introduttiva del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all'insieme di credenziali di esempio, quindi fare clic su **Avanti**.

    Se il file dell'insieme di credenziali non è valido (o è scaduto), è necessario scaricarne uno nuovo dall'insieme di credenziali di esempio nel portale di Azure. Dopo aver specificato un insieme di credenziali valido, viene visualizzato il nome dell'insieme di credenziali di backup corrispondente.

6. Nel riquadro **Seleziona server di backup** selezionare il computer di origine nell'elenco dei computer visualizzati e specificare la passphrase. Quindi selezionare **Avanti**.

    ![Schermata della pagina Seleziona server di backup del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Nella pagina **Seleziona modalità di ripristino** selezionare **Individual files and folders** > **Next** (Singoli file e cartelle > Avanti).

    ![Schermata della pagina Seleziona modalità di ripristino del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file e le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

    ![Schermata della pagina Seleziona volume e dati del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Fare clic su **Mount** (Mount) per montare localmente il punto di ripristino come volume di ripristino sul computer di destinazione.

10. Nella pagina **Cerca e Ripristina file** selezionare **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Schermata della pagina di ricerca e ripristino dei file del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. In Esplora risorse copiare i file e/o le cartelle dal volume di ripristino e incollarli nella posizione del computer di destinazione. È possibile aprire o trasmettere i file direttamente dal volume di ripristino e verificare che vengano ripristinate le versioni corrette.

    ![Schermata di Windows Explorer, con la funzione di copia evidenziata](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Al termine dell'operazione, nella pagina **Browse and Recover Files** (Cerca e ripristina fine) selezionare **Unmount** (Smonta). Fare clic su **Sì** per confermare che si intende smontare il volume.

    ![Schermata della pagina di ricerca e ripristino dei file del Ripristino guidato dei dati](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si fa clic su **Unmount** (Smonta), il volume di ripristino rimane montato per 6 ore. Il tempo di montaggio viene tuttavia esteso fino a un massimo di 24 ore in caso di copia dei file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >

## <a name="next-steps"></a>Passaggi successivi

Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).
