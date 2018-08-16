---
title: Ripristinare dati in Azure in un computer Windows Server o in un client Windows
description: Informazioni su come ripristinare i dati archiviati in Azure in un computer di Windows Server o in un client Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: saurse
ms.openlocfilehash: ddde297de49edb5f6543d03dfdb972771533301b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576186"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Ripristinare file in un computer di Windows Server o in un client Windows con il modello di distribuzione di Resource Manager

Questo articolo illustra come ripristinare i dati da un insieme di credenziali di backup. Per ripristinare i dati, usare la procedura di ripristino guidato dei dati nell'agente di Servizi di ripristino di Microsoft Azure (MARS). Quando si ripristinano dati, è possibile:

* Ripristinare i dati nello stesso computer in cui sono stati eseguiti i backup.
* Ripristinare i dati in un'altra macchina.

A gennaio 2017 Microsoft ha rilasciato un aggiornamento in anteprima all'agente MARS. Insieme alle correzioni di bug, questo aggiornamento abilita la funzione di ripristino istantaneo, che consente di montare uno snapshot del punto di ripristino scrivibile come volume di ripristino. È quindi possibile esplorare il volume di ripristino e i file di copia in un computer locale, ripristinando quindi i file in modo selettivo.

> [!NOTE]
> Se si vuole usare questa funzione per ripristinare i dati, è necessario l'[aggiornamento di Backup di Azure di gennaio 2017](https://support.microsoft.com/en-us/help/3216528?preview). È necessario anche che i dati di backup siano protetti in insiemi di credenziali nelle impostazioni locali elencate nell'articolo del supporto tecnico. Consultare l'[aggiornamento di Azure Backup di gennaio 2017](https://support.microsoft.com/en-us/help/3216528?preview) per un elenco aggiornato delle impostazioni locali che supportano la funzione di ripristino istantaneo. Il ripristino istantaneo **non** è attualmente disponibile in tutte le impostazioni locali.
>

Usare la funzione di ripristino istantaneo con Servizi di ripristino nel portale di Azure. I dati archiviati negli insiemi di credenziali di backup sono stati convertiti in insiemi di credenziali di Servizi di ripristino. Se si vuole usare la funzione di ripristino istantaneo, scaricare l'aggiornamento di MARS e seguire le procedure relative al ripristino istantaneo.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
> [!IMPORTANT]
> L'opzione per ripristinare *singoli file e cartelle* richiede .NET Framework versione 4.5.2 o successiva. Se l'opzione *Singoli file e cartelle* non viene visualizzata, è necessario eseguire l'aggiornamento di .NET Framework alla versione 4.5.2 o successiva e riprovare.

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
    > Se non si fa clic su Smonta, il volume di ripristino resterà montato per 6 ore. Il tempo di montaggio viene tuttavia esteso fino a un massimo di 24 ore in caso di copia dei file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >


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

    ![Ricerca](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

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
    > Se non si fa clic su Smonta, il volume di ripristino resterà montato per 6 ore. Il tempo di montaggio viene tuttavia esteso fino a un massimo di 24 ore in caso di copia dei file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).
