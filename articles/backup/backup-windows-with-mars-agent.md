---
title: Eseguire il backup di computer Windows usando l'agente MARS
description: Utilizzare l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup dei computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 4f0e605185be6db8629144e05f5f39309a3831ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604846"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Eseguire il backup di file e cartelle di Windows Server in Azure

Questo articolo illustra come eseguire il backup di computer Windows usando il servizio [backup di Azure](backup-overview.md) e l'agente di servizi di ripristino di Microsoft Azure (Mars). MARS è anche noto come Azure Backup Agent.

In questo articolo verrà spiegato come:

> [!div class="checklist"]
>
> * Verificare i prerequisiti
> * Creare una pianificazione e un criterio di backup.
> * Eseguire un backup su richiesta.

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni [su come backup di Azure usa l'agente Mars per eseguire il backup dei computer Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Informazioni sull' [architettura di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) che esegue l'agente Mars in un server mab o Data Protection Manager secondario.
* Esaminare gli [elementi supportati e gli elementi di cui è possibile eseguire il backup](backup-support-matrix-mars-agent.md) dall'agente Mars.
* [Verificare l'accesso a Internet](install-mars-agent.md#verify-internet-access) nei computer di cui si vuole eseguire il backup.
* Se l'agente MARS non è installato, Scopri come installarlo [qui](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Il criterio di backup specifica quando creare snapshot dei dati per creare punti di ripristino. Specifica anche per quanto tempo devono essere conservati i punti di ripristino. Usare l'agente MARS per configurare un criterio di backup.

Backup di Azure non prende automaticamente in considerazione l'ora legale (DST). Questa impostazione predefinita può causare una discrepanza tra l'ora effettiva e l'ora di backup pianificata.

Per creare un criterio di backup:

1. Dopo aver scaricato e registrato l'agente MARS, aprire la console di Agent. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.  

1. In **azioni**selezionare **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. Nella pianificazione guidata backup selezionare **Guida introduttiva**  >  **Avanti**.
1. In **Seleziona elementi di cui eseguire il backup**selezionare **Aggiungi elementi**.

    ![Aggiungere elementi di cui eseguire il backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Nella casella **Seleziona elementi** selezionare gli elementi di cui eseguire il backup e quindi fare clic su **OK**.

    ![Seleziona elementi per backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Nella pagina **selezionare gli elementi di cui eseguire il backup** selezionare **Avanti**.
1. Nella pagina **specificare la pianificazione del backup** specificare quando eseguire i backup giornalieri o settimanali. Selezionare quindi **Avanti**.

    * Quando viene creato un backup, viene creato un punto di ripristino.
    * Il numero di punti di ripristino creati nell'ambiente dipende dalla pianificazione del backup.
    * È possibile pianificare fino a tre backup giornalieri al giorno. Nell'esempio seguente vengono eseguiti due backup giornalieri, uno a mezzanotte e uno alle 6:00 PM.

        ![Configurare una pianificazione di backup giornaliera](./media/backup-configure-vault/day-schedule.png)

    * È anche possibile eseguire backup settimanali. Nell'esempio seguente, i backup vengono eseguiti ogni domenica alternativa e mercoledì alle 9:30 AM e 1:00 AM.

        ![Configurare una pianificazione di backup settimanale](./media/backup-configure-vault/week-schedule.png)

1. Nella pagina **Seleziona criteri di conservazione** specificare come archiviare le copie cronologiche dei dati. Selezionare quindi **Avanti**.

    * Le impostazioni di conservazione specificano i punti di ripristino da archiviare e la durata dell'archiviazione.
    * Per un'impostazione di conservazione giornaliera, si indica che al momento specificato per la conservazione giornaliera, il punto di ripristino più recente verrà mantenuto per il numero di giorni specificato. In alternativa, è possibile specificare un criterio di conservazione mensile per indicare che il punto di ripristino creato il 30 di ogni mese deve essere archiviato per 12 mesi.
    * La conservazione dei punti di ripristino giornalieri e settimanali coincide in genere con la pianificazione del backup. Quindi, quando la pianificazione attiva un backup, viene archiviato il punto di ripristino creato dal backup per la durata specificata dai criteri di conservazione giornalieri o settimanali.
    * Nell'esempio seguente:

        * I backup giornalieri a mezzanotte e 6:00 PM vengono conservati per sette giorni.
        * I backup eseguiti a mezzanotte e 6:00 PM vengono conservati per quattro settimane.
        * I backup eseguiti l'ultimo sabato del mese a mezzanotte e 6:00 PM vengono conservati per 12 mesi.
        * I backup eseguiti l'ultimo sabato nel marzo sono conservati per 10 anni.

        ![Esempio di criteri di conservazione](./media/backup-configure-vault/retention-example.png)

1. Nella pagina **scegliere il tipo di backup iniziale** decidere se si vuole eseguire il backup iniziale in rete o usare il backup offline. Per eseguire il backup iniziale sulla rete, selezionare **automaticamente in rete**  >  **Avanti**.

    Per ulteriori informazioni sul backup offline, vedere [utilizzare Azure Data Box per il backup offline](offline-backup-azure-data-box.md).

    ![Scegliere un tipo di backup iniziale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Nella pagina **conferma** esaminare le informazioni e quindi fare clic su **fine**.

    ![Confermare il tipo di backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Al termine della creazione della pianificazione del backup da parte della procedura guidata, selezionare **Chiudi**.

    ![Visualizzare lo stato di avanzamento della pianificazione del backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Creare un criterio in ogni computer in cui è installato l'agente.

### <a name="do-the-initial-backup-offline"></a>Eseguire il backup iniziale offline

È possibile eseguire un backup iniziale automaticamente in rete oppure è possibile eseguire il backup offline. Il seeding offline per un backup iniziale è utile se si dispone di grandi quantità di dati che richiedono una quantità elevata di larghezza di banda di rete da trasferire.

Per eseguire un trasferimento offline:

1. Scrivere i dati di backup in un percorso di gestione temporanea.
1. Usare lo strumento AzureOfflineBackupDiskPrep per copiare i dati dal percorso di gestione temporanea a uno o più dischi SATA.

    Lo strumento crea un processo di importazione di Azure. Per altre informazioni, vedere [che cos'è il servizio importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
1. Inviare i dischi SATA a un Data Center di Azure.

    Nel Data Center i dati del disco vengono copiati in un account di archiviazione di Azure. Backup di Azure copia i dati dall'account di archiviazione all'insieme di credenziali e vengono pianificati i backup incrementali.

Per altre informazioni sul seeding offline, vedere [usare Azure Data Box per il backup offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda

È possibile controllare la modalità di utilizzo della larghezza di banda per l'agente MARS abilitando la limitazione delle richieste di rete. La limitazione è utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma si desidera controllare la quantità di larghezza di banda utilizzata dall'attività di backup e ripristino.

La limitazione di rete in backup di Azure usa la [qualità del servizio (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) nel sistema operativo locale.

La limitazione di rete per i backup è disponibile in Windows Server 2012 e versioni successive e in Windows 8 e versioni successive. I sistemi operativi devono eseguire i Service Pack più recenti.

Per abilitare la limitazione della larghezza di banda:

1. Nell'agente MARS selezionare **modifica proprietà**.
1. Nella scheda **limitazione** , selezionare **Abilita limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Configurare la limitazione delle richieste di rete per le operazioni di backup](./media/backup-configure-vault/throttling-dialog.png)
1. Specificare la larghezza di banda consentita durante le ore lavorative e non lavorative. I valori della larghezza di banda iniziano a 512 kbps e passano fino a 1.023 MBps. Selezionare **OK**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

1. Nell'agente MARS selezionare **Esegui backup ora**.

    ![Esegui backup ora in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se la versione dell'agente MARS è 2.0.9169.0 o successiva, è possibile impostare una data di conservazione personalizzata. Nella sezione **Mantieni backup fino** a scegliere una data dal calendario.

   ![Usare il calendario per personalizzare una data di conservazione](./media/backup-configure-vault/mars-ondemand.png)

1. Nella pagina **conferma** rivedere le impostazioni e selezionare **backup**.
1. Selezionare **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima del completamento del backup, la procedura guidata continua a essere eseguita in background.

Al termine del backup iniziale, lo stato del **processo completato** viene visualizzato nella console di backup.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Configurare il comportamento di conservazione dei criteri di backup su richiesta

> [!NOTE]
> Queste informazioni si applicano solo alle versioni degli agenti MARS precedenti a 2.0.9169.0.
>

| Backup-pianificazione-opzione | Durata della conservazione dei dati
| -- | --
| Giorno | **Conservazione predefinita**: equivalente alla "conservazione in giorni per i backup giornalieri". <br/><br/> **Eccezione**: se un backup pianificato giornaliero impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine.<br/><br/> **Scenario di esempio**: il backup pianificato su giovedì alle 8:00 non è riuscito. Questo backup doveva essere considerato per la conservazione settimanale, mensile o annuale. Quindi, il primo backup su richiesta attivato prima del successivo backup pianificato il venerdì alle 8:00 AM viene automaticamente contrassegnato per la conservazione settimanale, mensile o annuale. Questo backup sostituisce il backup del giovedì 8:00.
| Settimana | **Conservazione predefinita**: un giorno. I backup su richiesta che vengono eseguiti per un'origine dati con criteri di backup settimanali vengono eliminati il giorno successivo. Vengono eliminati anche se sono i backup più recenti per l'origine dati. <br/><br/> **Eccezione**: se un backup pianificato settimanale impostato per la conservazione a lungo termine (settimane, mesi o anni) ha esito negativo, un backup su richiesta attivato subito dopo l'errore viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine. <br/><br/> **Scenario di esempio**: il backup pianificato su giovedì alle 8:00 non è riuscito. Questo backup doveva essere considerato per la conservazione mensile o annuale. Quindi, il primo backup su richiesta che viene attivato prima del successivo backup pianificato giovedì alle 8:00 AM viene automaticamente contrassegnato per la conservazione mensile o annuale. Questo backup sostituisce il backup del giovedì 8:00.

Per altre informazioni, vedere [creare un criterio di backup](#create-a-backup-policy).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare i file in Azure](backup-azure-restore-windows-server.md).
* Trovare [domande frequenti sul backup di file e cartelle](backup-azure-file-folder-backup-faq.md)
