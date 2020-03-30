---
title: Eseguire il backup dei computer Windows utilizzando l'agente MARS
description: Usare l'agente di Microsoft Azure Recovery Services (MARS) per eseguire il backup dei computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408913"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Eseguire il backup dei computer Windows utilizzando l'agente MARS di Azure Backup

In questo articolo viene illustrato come eseguire il backup di computer Windows utilizzando il servizio Backup di [Azure](backup-overview.md) e l'agente di Microsoft Azure Recovery Services (MARS). MARS è anche noto come agente di Backup di Azure.MARS is also known as the Azure Backup agent.

In questo articolo verrà spiegato come:

> [!div class="checklist"]
>
> * Verificare i prerequisiti
> * Creare un criterio di backup e una pianificazione.
> * Eseguire un backup su richiesta.

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni su come [Azure Backup usa l'agente MARS per eseguire il backup](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)dei computer Windows.
* Informazioni [sull'architettura](backup-architecture.md#architecture-back-up-to-dpmmabs) di backup che esegue l'agente MARS su un server MABS o Data Protection Manager secondario.
* Esaminare gli elementi supportati e gli elementi di [cui è possibile eseguire](backup-support-matrix-mars-agent.md) il backup dall'agente MARS.
* [Verificare l'accesso a Internet](install-mars-agent.md#verify-internet-access) nei computer di cui si desidera eseguire il backup.
* Se l'agente MARS non è installato, scopri come installarlo [qui](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

I criteri di backup specificano quando eseguire snapshot dei dati per creare punti di ripristino. Specifica inoltre per quanto tempo mantenere i punti di ripristino. Utilizzare l'agente MARS per configurare un criterio di backup.

Backup di Azure non prende automaticamente in considerazione l'ora legale (DST). Questa impostazione predefinita potrebbe causare alcune discrepanze tra l'ora effettiva e l'ora di backup pianificata.

Per creare un criterio di backup:

1. Dopo aver scaricato e registrato l'agente MARS, aprire la console dell'agente. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.  

1. In **Azioni**selezionare **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. Nella Pianificazione guidata backup selezionare **Guida introduttiva** > **successiva**.
1. In **Selezionare gli elementi di cui eseguire**il backup selezionare Aggiungi **elementi**.

    ![Aggiungere elementi di cui eseguire il backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Nella casella **Seleziona elementi** selezionare gli elementi di cui eseguire il backup e quindi scegliere **OK**.

    ![Seleziona elementi per backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Nella pagina **Selezione elementi di cui eseguire il backup** selezionare **Avanti**.
1. Nella pagina **Specifica pianificazione backup** specificare quando eseguire backup giornalieri o settimanali. Quindi selezionare **Avanti**.

    * Un punto di ripristino viene creato quando viene eseguito un backup.
    * Il numero di punti di ripristino creati nell'ambiente dipende dalla pianificazione del backup.
    * È possibile pianificare fino a tre backup giornalieri al giorno. Nell'esempio seguente si verificano due backup giornalieri, uno a mezzanotte e uno alle 18:00.

        ![Impostare una pianificazione di backup giornaliera](./media/backup-configure-vault/day-schedule.png)

    * È anche possibile eseguire backup settimanali. Nell'esempio seguente, i backup vengono eseguiti ogni domenica e mercoledì alternativi alle 9:30 e alle 1:00.

        ![Impostare una pianificazione di backup settimanale](./media/backup-configure-vault/week-schedule.png)

1. Nella pagina **Selezione criteri di conservazione** specificare come archiviare le copie cronologiche dei dati. Quindi selezionare **Avanti**.

    * Le impostazioni di conservazione specificano i punti di ripristino da archiviare e il tempo di archiviazione.
    * Per un'impostazione di conservazione giornaliera, si indica che al momento specificato per la conservazione giornaliera, il punto di ripristino più recente verrà mantenuto per il numero di giorni specificato. In alternativa, è possibile specificare un criterio di conservazione mensile per indicare che il punto di ripristino creato il 30 di ogni mese deve essere archiviato per 12 mesi.
    * La conservazione per i punti di ripristino giornalieri e settimanali coincide in genere con la pianificazione del backup. Pertanto, quando la pianificazione attiva un backup, il punto di ripristino creato dal backup viene archiviato per la durata specificata dai criteri di conservazione giornalieri o settimanali.
    * Nell'esempio seguente:

        * I backup giornalieri a mezzanotte e alle 18:00 vengono conservati per sette giorni.
        * I backup effettuati di sabato a mezzanotte e alle 18:00 vengono conservati per quattro settimane.
        * I backup effettuati l'ultimo sabato del mese a mezzanotte e le 18:00 vengono conservati per 12 mesi.
        * I backup effettuati l'ultimo sabato di marzo sono conservati per 10 anni.

        ![Esempio di criterio di conservazione](./media/backup-configure-vault/retention-example.png)

1. Nella pagina Scegliere il **tipo di backup iniziale** decidere se eseguire il backup iniziale in rete o utilizzare il backup offline. Per eseguire il backup iniziale in rete, selezionare **Automaticamente in rete** > **Avanti**.

    Per altre informazioni sul backup offline, vedere [Usare Azure Data Box per il backup offline.](offline-backup-azure-data-box.md)

    ![Scegliere un tipo di backup iniziale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Nella pagina **Conferma** esaminare le informazioni e quindi selezionare **Fine**.

    ![Confermare il tipo di backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Al termine della creazione della pianificazione del backup da parte della procedura guidata, selezionare **Chiudi**.

    ![Visualizzare lo stato di avanzamento della pianificazione del backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Creare un criterio in ogni computer in cui è installato l'agente.

### <a name="do-the-initial-backup-offline"></a>Eseguire il backup iniziale non in linea

È possibile eseguire automaticamente un backup iniziale in rete oppure eseguire il backup offline. Il seeding offline per un backup iniziale è utile se si dispone di grandi quantità di dati che richiederanno molta larghezza di banda di rete per il trasferimento.

Per effettuare un trasferimento offline:

1. Scrivere i dati di backup in un percorso di gestione temporanea.
1. Usare lo strumento AzureOfflineBackupDiskPrep per copiare i dati dal percorso di gestione temporanea a uno o più dischi SATA.

    Lo strumento crea un processo di importazione di Azure.The tool creates an Azure Import job. Per altre informazioni, vedere [Che cos'è il servizio Importazione/Esportazione](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)di Azure.
1. Inviare i dischi SATA a un data center di Azure.Send the SATA disks to an Azure datacenter.

    Nel data center, i dati del disco vengono copiati in un account di archiviazione di Azure.At the datacenter, the disk data is copied to an Azure storage account. Backup di Azure copia i dati dall'account di archiviazione all'insieme di credenziali e vengono pianificati i backup incrementali.

Per altre informazioni sul seeding offline, vedere [Usare Azure Data Box per il backup offline.](offline-backup-azure-data-box.md)

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda

È possibile controllare il modo in cui l'agente MARS utilizza la larghezza di banda di rete abilitando la limitazione delle richieste di rete. La limitazione delle richieste è utile se è necessario eseguire il backup dei dati durante le ore lavorative, ma si desidera controllare la larghezza di banda utilizzata dall'attività di backup e ripristino.

La limitazione delle richieste di rete in Backup di Azure usa [la qualità del servizio (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) nel sistema operativo locale.Network throttling in Azure Backup uses Quality of Service (QoS) on the local operating system.

La limitazione delle richieste di rete per i backup è disponibile in Windows Server 2012 e versioni successive e In Windows 8 e versioni successive.Network throttling for backups is available on Windows Server 2012 and later, and on Windows 8 and later. I sistemi operativi devono eseguire i service pack più recenti.

Per abilitare la limitazione delle richieste di rete:

1. Nell'agente MARS selezionare **Cambia proprietà**.
1. Nella scheda **Limitazione della** larghezza di banda selezionare **Abilita limitazione all'utilizzo della larghezza di banda Internet per le operazioni**di backup.

    ![Configurare la limitazione delle richieste di rete per le operazioni di backupSet up network throttling for backup operations](./media/backup-configure-vault/throttling-dialog.png)
1. Specificare la larghezza di banda consentita durante le ore lavorative e le ore non lavorative. I valori di larghezza di banda iniziano a 512 Kbps e vanno fino a 1.023 MBps. Quindi selezionare **OK**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

1. Nell'agente MARS selezionare **Backup ora**.

    ![Esegui il backup ora in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se la versione dell'agente MARS è 2.0.9169.0 o successiva, è possibile impostare una data di conservazione personalizzata. Nella sezione **Mantieni cassa di backup** scegliere una data dal calendario.

   ![Utilizzare il calendario per personalizzare una data di conservazione](./media/backup-configure-vault/mars-ondemand.png)

1. Nella pagina **Conferma** rivedere le impostazioni e selezionare **Backup**.
1. Selezionare **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima del completamento del backup, l'esecuzione della procedura guidata continua in background.

Al termine del backup iniziale, lo stato **Processo completato** viene visualizzato nella console di backup.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Impostare il comportamento di conservazione dei criteri di backup su richiesta

> [!NOTE]
> Queste informazioni si applicano solo alle versioni degli agenti MARS precedenti alla 2.0.9169.0.This information applies only to MARS agent versions that are older than 2.0.9169.0.
>

| Opzione di pianificazione del backup | Durata della conservazione dei dati
| -- | --
| Giorno | **Conservazione predefinita**: Equivalente alla "conservazione in giorni per i backup giornalieri". <br/><br/> **Eccezione:** se un backup pianificato giornaliero impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta che viene attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il successivo backup pianificato viene considerato per la conservazione a lungo termine.<br/><br/> **Scenario di esempio:** il backup pianificato di giovedì alle 08:00 non è riuscito. Questo backup doveva essere considerato per la conservazione settimanale, mensile o annuale. Pertanto, il primo backup su richiesta attivato prima del successivo backup pianificato il venerdì alle 8:00 viene contrassegnato automaticamente per la conservazione settimanale, mensile o annuale. Questo backup sostituisce il backup di giovedì 8:00 AM.
| Week | **Conservazione predefinita**: Un giorno. I backup su richiesta eseguiti per un'origine dati con criteri di backup settimanali vengono eliminati il giorno successivo. Vengono eliminati anche se si tratta dei backup più recenti per l'origine dati. <br/><br/> **Eccezione:** se un backup pianificato settimanale impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta che viene attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il successivo backup pianificato viene considerato per la conservazione a lungo termine. <br/><br/> **Scenario di esempio:** il backup pianificato di giovedì alle 08:00 non è riuscito. Questo backup doveva essere considerato per la conservazione mensile o annuale. Pertanto, il primo backup su richiesta che viene attivato prima del successivo backup pianificato il giovedì alle 8:00 viene automaticamente contrassegnato per la conservazione mensile o annuale. Questo backup sostituisce il backup di giovedì 8:00 AM.

Per ulteriori informazioni, vedere [Creare un criterio di backup.](#create-a-backup-policy)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare i file in Azure.](backup-azure-restore-windows-server.md)
* Trovare [domande comuni sul backup di file e cartelle](backup-azure-file-folder-backup-faq.md)

