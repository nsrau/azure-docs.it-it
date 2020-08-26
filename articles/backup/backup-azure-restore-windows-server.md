---
title: Ripristinare i file in Windows Server con l'agente di Servizi di ripristino di Microsoft Azure
description: Questo articolo illustra come ripristinare i dati archiviati in Azure in un computer Windows Server o Windows con l'agente di Servizi di ripristino di Microsoft Azure.
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: e61ad7083a7b6979222566112820bad133022844
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892508"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Ripristinare i file in Windows Server con l'agente di Servizi di ripristino di Microsoft Azure

Questo articolo illustra come ripristinare i dati da un insieme di credenziali di backup. Per ripristinare i dati, usare il Ripristino guidato dei dati nell'agente di Servizi di ripristino di Microsoft Azure. È possibile:

* Ripristinare i dati nello stesso computer in cui sono stati eseguiti i backup.
* Ripristinare i dati in un'altra macchina.

Usare la funzionalità di ripristino istantaneo per montare uno snapshot del punto di ripristino scrivibile come volume di ripristino. È quindi possibile esplorare il volume di ripristino e copiare i file in un computer locale, in modo da ripristinare in modo selettivo i file.

> [!NOTE]
> Se si vuole usare questa funzione per ripristinare i dati, è necessario l'[aggiornamento di Backup di Azure di gennaio 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar). È necessario anche che i dati di backup siano protetti in insiemi di credenziali nelle impostazioni locali elencate nell'articolo del supporto tecnico. Consultare l'[aggiornamento di Azure Backup di gennaio 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) per un elenco aggiornato delle impostazioni locali che supportano la funzione di ripristino istantaneo.
>

Usare la funzione di ripristino istantaneo con Servizi di ripristino nel portale di Azure. I dati archiviati negli insiemi di credenziali di backup sono stati convertiti in insiemi di credenziali di Servizi di ripristino. Se si vuole usare la funzione di ripristino istantaneo, scaricare l'aggiornamento di Servizi di ripristino di Microsoft Azure e seguire le procedure relative al ripristino istantaneo.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Usare la funzione di ripristino istantaneo per ripristinare i dati sullo stesso computer

Se un file è stato eliminato accidentalmente e lo si vuole ripristinare nello stesso computer in cui è stato eseguito il backup, la procedura seguente consente di recuperarlo.

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server.

    L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

2. Selezionare **Ripristina dati** per avviare la procedura guidata.

    ![Screenshot di backup di Azure con i dati di ripristino evidenziati (ripristino nello stesso computer)](./media/backup-azure-restore-windows-server/recover.png)

3. Nella pagina **Guida introduttiva** selezionare l'opzione **This server (Questo server) (`<server name>`)**  > **Avanti** per ripristinare i dati nello stesso server o computer.

    ![Screenshot della procedura guidata ripristino dati Introduzione pagina (ripristinare lo stesso computer)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Nella pagina **Seleziona modalità di ripristino** scegliere **Singoli file e cartelle**>**Avanti**.

    ![Screenshot della procedura guidata Ripristina dati selezionare la modalità di ripristino pagina (Ripristina nello stesso computer)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > L'opzione per ripristinare singoli file e cartelle richiede .NET Framework versione 4.5.2 o successiva. Se non viene visualizzata l'opzione **singoli file e cartelle** , è necessario aggiornare .NET Framework alla versione 4.5.2 o successiva e riprovare.

   > [!TIP]
   > L'opzione **Individual files and folders** (Singoli file e cartelle) consente l'accesso rapido ai dati del punto di ripristino. È adatto per il ripristino di singoli file ed è consigliato per una dimensione totale inferiore a 80 GB. Offre velocità di trasferimento o copia fino a 6 MBps durante il ripristino. L'opzione **Volume** consente di ripristinare tutti i dati di cui è stato eseguito il backup in un volume specificato. Questa opzione offre velocità di trasferimento più veloci (fino a 40 MBps) ed è consigliata per il ripristino di dati di grandi dimensioni o interi volumi.

5. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file e le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

    ![Screenshot della procedura guidata ripristino dati selezionare la pagina volume e data (Ripristina nello stesso computer)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Dopo aver scelto il punto di ripristino, selezionare **Mount** (Monta).

    Backup di Azure monta il punto di ripristino locale e lo usa come volume di ripristino.

7. Nella pagina **Cerca e ripristina file** fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Screenshot della pagina Cerca e Ripristina file della procedura guidata ripristino dati (Ripristina nello stesso computer)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. In Esplora risorse copiare i file e le cartelle da ripristinare e incollarle in qualsiasi posizione locale nel server o nel computer. È possibile aprire o trasmettere in streaming i file direttamente dal volume di ripristino e verificare che siano state ripristinate le versioni corrette.

    ![Screenshot di Esplora risorse, con la copia evidenziata (ripristino nello stesso computer)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Al termine, nella pagina **Browse and Recover Files** selezionare **unmount**. Fare clic su **Sì** per confermare che si intende smontare il volume.

    ![Screenshot della pagina Cerca e Ripristina file della procedura guidata ripristino dati (Ripristina nello stesso computer)-conferma smontaggio volume di ripristino](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si seleziona **Smonta**, il volume di ripristino rimarrà montato per 6 ore dal momento in cui è stato montato. Tuttavia, il tempo di montaggio viene esteso fino a un massimo di 24 ore in caso di copia di file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usare il ripristino immediato per ripristinare i dati in un altro computer

Se l'intero server viene perso, è comunque possibile recuperare dati da Backup di Azure in un computer diverso. I passaggi seguenti illustrano il flusso di lavoro.

Tali passaggi usano la terminologia seguente:

* *Computer di origine*: il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
* *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
* *Insieme di credenziali di esempio*: l'insieme di credenziali dei servizi di ripristino in cui il computer di origine e il computer di destinazione sono registrati.

> [!NOTE]
> Non è possibile ripristinare i backup in un computer di destinazione che esegue una versione precedente del sistema operativo. Un backup eseguito in un computer con Windows 7, ad esempio, può essere ripristinato in un computer con Windows 7 o versioni successive. Un backup eseguito su un computer con Windows 8 non può essere ripristinato in un computer con Windows 7.
>
>

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel computer di destinazione.

2. Assicurarsi che il computer di destinazione e il computer di origine siano registrati nello stesso insieme di credenziali dei servizi di ripristino.

3. Selezionare **Ripristina dati** per aprire il **Ripristino guidato dei dati**.

    ![Screenshot di backup di Azure con i dati di ripristino evidenziati (ripristino in un computer alternativo)](./media/backup-azure-restore-windows-server/recover.png)

4. Nella pagina **Guida introduttiva** selezionare **Another server** (Un altro server).

    ![Screenshot della procedura guidata ripristino dati Introduzione pagina (ripristino nel computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all'insieme di credenziali di esempio, quindi fare clic su **Avanti**.

    Se il file dell'insieme di credenziali non è valido (o è scaduto), è necessario scaricarne uno nuovo dall'insieme di credenziali di esempio nel portale di Azure. Dopo aver specificato un insieme di credenziali valido, viene visualizzato il nome dell'insieme di credenziali di backup corrispondente.

6. Nel riquadro **Seleziona server di backup** selezionare il computer di origine nell'elenco dei computer visualizzati e specificare la passphrase. Fare quindi clic su **Avanti**.

    ![Screenshot della procedura guidata ripristino dati pagina Selezione server di backup (Ripristina computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Nella pagina **Seleziona modalità di ripristino** selezionare **Individual files and folders** > **Next** (Singoli file e cartelle > Avanti).

    ![Screenshot della procedura guidata ripristino dati pagina Seleziona modalità di ripristino (Ripristina computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Nella pagina **Seleziona volume e data** selezionare il volume che contiene i file e le cartelle da ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

    ![Screenshot della procedura guidata ripristino dati selezionare la pagina volume e data (Ripristina in un computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Fare clic su **Mount** (Mount) per montare localmente il punto di ripristino come volume di ripristino sul computer di destinazione.

10. Nella pagina **Cerca e ripristina file** fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati.

    ![Screenshot della pagina Cerca e Ripristina file della procedura guidata ripristino dati (Ripristina computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. In Esplora risorse copiare i file e/o le cartelle dal volume di ripristino e incollarli nella posizione del computer di destinazione. È possibile aprire o trasmettere i file direttamente dal volume di ripristino e verificare che vengano ripristinate le versioni corrette.

    ![Screenshot di Esplora risorse, con la copia evidenziata (ripristino nel computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Al termine, nella pagina **Browse and Recover Files** selezionare **unmount**. Fare clic su **Sì** per confermare che si intende smontare il volume.

    ![Smontare il volume (ripristinare il computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se non si seleziona **Smonta**, il volume di ripristino rimarrà montato per 6 ore dal momento in cui è stato montato. Tuttavia, il tempo di montaggio viene esteso fino a un massimo di 24 ore in caso di copia di file in corso. Mentre il volume è montato, non vengono eseguite operazioni di backup. Qualsiasi operazione di backup pianificata durante il periodo in cui il volume è montato verrà eseguita dopo lo smontaggio del volume di ripristino.
    >

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).

* Vedere [Domande frequenti sul backup di file e cartelle](backup-azure-file-folder-backup-faq.md).
