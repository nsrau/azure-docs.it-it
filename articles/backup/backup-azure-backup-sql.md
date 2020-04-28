---
title: Backup di SQL Server in Azure come carico di lavoro DPM
description: Introduzione al backup dei database di SQL Server con il servizio backup di Azure
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 01504fcfd81040d75e57ce62a9f77a5bb248d59b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183790"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Backup di SQL Server in Azure come carico di lavoro DPM

Questo articolo illustra i passaggi di configurazione per eseguire il backup SQL Server database usando backup di Azure.

Per eseguire il backup dei database di SQL server in Azure è necessario un account Azure. Se non si dispone di un account, è possibile crearne uno gratuito in pochi minuti. Per altre informazioni, vedere [creare un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).

Per eseguire il backup di un database di SQL Server in Azure e ripristinarlo da Azure:

1. Creare un criterio di backup per proteggere i database di SQL Server in Azure.
1. Creare copie di backup su richiesta in Azure.
1. Ripristinare il database da Azure.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare di aver soddisfatto i [prerequisiti](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per l'uso di backup di Azure per proteggere i carichi di lavoro. Di seguito sono riportate alcune delle attività essenziali:

* Creare un insieme di credenziali per il backup.
* Scaricare le credenziali dell'insieme di credenziali.
* Installare Azure Backup Agent.
* Registrare il server con l'insieme di credenziali.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Per proteggere SQL Server database in Azure, creare prima di tutto un criterio di backup:

1. Nel server Data Protection Manager (DPM) selezionare l'area di lavoro **protezione** .
1. Selezionare **nuovo** per creare un gruppo protezione dati.

    ![Creare un gruppo protezione dati](./media/backup-azure-backup-sql/protection-group.png)
1. Nella pagina iniziale esaminare le linee guida per la creazione di un gruppo protezione dati. Fare quindi clic su **Avanti**.
1. Selezionare **Server**.

    ![Selezionare il tipo di gruppo protezione dati server](./media/backup-azure-backup-sql/pg-servers.png)
1. Espandere il computer SQL Server in cui si trovano i database di cui si desidera eseguire il backup. Verranno visualizzate le origini dati di cui è possibile eseguire il backup da tale server. Espandere **tutte le condivisioni SQL** , quindi selezionare i database di cui si desidera eseguire il backup. In questo esempio si seleziona ReportServer $ MSDPM2012 e ReportServer $ MSDPM2012TempDB. Fare quindi clic su **Avanti**.

    ![Selezionare un database di SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Assegnare un nome al gruppo protezione dati e quindi selezionare **protezione dati online**.

    ![Scegliere un metodo di protezione dei dati: protezione del disco a breve termine o protezione di Azure Online](./media/backup-azure-backup-sql/pg-name.png)
1. Nella pagina **Specifica obiettivi a breve termine** includere gli input necessari per creare punti di backup sul disco.

    In questo esempio, il periodo di **mantenimento** dati è impostato su *5 giorni*. La **frequenza di sincronizzazione** del backup è impostata su una volta ogni *15 minuti*. Il **backup completo rapido** è impostato su *8:00 PM*.

    ![Configurare gli obiettivi a breve termine per la protezione dei backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In questo esempio viene creato un punto di backup ogni giorno alle 8:00 PM. I dati modificati dopo il punto di backup 8:00 PM del giorno precedente vengono trasferiti. Questo processo è detto **Backup completo rapido**. Sebbene i log delle transazioni vengano sincronizzati ogni 15 minuti, se è necessario ripristinare il database alle 9:00, il punto viene creato riproducendo i log dall'ultimo punto di backup completo rapido, ovvero 8:00 PM in questo esempio.
   >
   >

1. Selezionare **Avanti**. DPM Mostra lo spazio di archiviazione globale disponibile. Mostra anche il potenziale utilizzo dello spazio su disco.

    ![Imposta allocazione dischi](./media/backup-azure-backup-sql/pg-storage.png)

    Per impostazione predefinita, DPM crea un volume per origine dati (SQL Server database). Il volume viene usato per la copia di backup iniziale. In questa configurazione, gestione dischi logici (LDM) limita la protezione DPM a 300 origini dati (SQL Server database). Per porre rimedio a questa limitazione, selezionare **Condividi percorso dati nel pool di archiviazione DPM**. Se si utilizza questa opzione, DPM utilizza un singolo volume per più origini dati. Questa configurazione consente a DPM di proteggere fino a 2.000 database SQL Server.

    Se si seleziona aumenta **automaticamente i volumi**, DPM può tenere conto dell'incremento del volume di backup man mano che i dati di produzione aumentano. Se non si seleziona **aumenta automaticamente i volumi**, DPM limita l'archiviazione di backup alle origini dati nel gruppo protezione dati.

1. Se si è un amministratore, è possibile scegliere di trasferire automaticamente questo backup iniziale **sulla rete** e scegliere l'ora del trasferimento. In alternativa, scegliere di trasferire **manualmente** il backup. Fare quindi clic su **Avanti**.

    ![Scegliere un metodo di creazione della replica](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (SQL Server database). I dati di backup vengono spostati dal server di produzione (SQL Server computer) al server DPM. Se il backup è di grandi dimensioni, il trasferimento dei dati in rete potrebbe causare congestione della larghezza di banda. Per questo motivo, gli amministratori possono scegliere di utilizzare supporti rimovibili per trasferire **manualmente**il backup iniziale. Oppure possono trasferire i dati **automaticamente in rete** a un'ora specificata.

    Al termine del backup iniziale, i backup continuano in modo incrementale sulla copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.

1. Scegliere quando eseguire una verifica di coerenza. Fare quindi clic su **Avanti**.

    ![Scegliere quando eseguire una verifica di coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM è in grado di eseguire una verifica di coerenza sull'integrità del punto di backup. Calcola il checksum del file di backup nel server di produzione (il computer SQL Server in questo esempio) e i dati di cui è stato eseguito il backup per il file in DPM. Se il controllo rileva un conflitto, si presuppone che il file di cui è stato eseguito il backup in DPM sia danneggiato. DPM corregge i dati di backup inviando i blocchi che corrispondono alla mancata corrispondenza del checksum. Poiché la verifica della coerenza è un'operazione che richiede un utilizzo intensivo delle prestazioni, gli amministratori possono scegliere di pianificare la verifica della coerenza o di eseguirla automaticamente.

1. Selezionare le origini dati da proteggere in Azure. Fare quindi clic su **Avanti**.

    ![Selezionare le origini dati da proteggere in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se si è un amministratore, è possibile scegliere le pianificazioni di backup e i criteri di conservazione che soddisfano i criteri dell'organizzazione.

    ![Scegliere le pianificazioni e i criteri di conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio, i backup vengono eseguiti ogni giorno alle ore 12:00 e 8:00.

    > [!TIP]
    > Per un ripristino rapido, conserva alcuni punti di ripristino a breve termine sul disco. Questi punti di recupero vengono usati per il recupero operativo. Azure funge da luogo fuori sede, garantendo contratti di servizio più elevati e disponibilità garantita.
    >
    > Usare DPM per pianificare i backup di Azure al termine dei backup del disco locale. Quando si segue questa procedura, il backup su disco più recente viene copiato in Azure.
    >

1. Scegliere la pianificazione per i criteri di conservazione. Per altre informazioni sul funzionamento dei criteri di conservazione, vedere [usare backup di Azure per sostituire l'infrastruttura](backup-azure-backup-cloud-as-tape.md)basata su nastro.

    ![Scegliere un criterio di conservazione](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Esempio:

    * I backup vengono eseguiti ogni giorno alle ore 12:00 e 8:00. Sono conservati per 180 giorni.
    * Il backup di sabato alle 12:00 PM viene mantenuto per 104 settimane.
    * Il backup dell'ultimo sabato del mese alle 12:00 PM viene mantenuto per 60 mesi.
    * Il backup dell'ultimo sabato del marzo alle 12:00 PM viene mantenuto per 10 anni.

    Dopo aver scelto un criterio di conservazione, fare clic su **Avanti**.

1. Scegliere come trasferire la copia di backup iniziale in Azure.

    * L'opzione **automatica sulla rete** segue la pianificazione del backup per trasferire i dati in Azure.
    * Per ulteriori informazioni sul **backup offline**, vedere [Panoramica del backup offline](offline-backup-overview.md).

    Dopo aver scelto un meccanismo di trasferimento, fare clic su **Avanti**.

1. Nella pagina **Riepilogo** esaminare i dettagli dei criteri. Selezionare quindi **Crea gruppo**. È possibile selezionare **Chiudi** e controllare lo stato di avanzamento del processo nell'area di lavoro **monitoraggio** .

    ![Stato di avanzamento della creazione del gruppo protezione dati](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Creazione di copie di backup su richiesta di un database di SQL Server

Quando si verifica il primo backup, viene creato un punto di ripristino. Anziché attendere l'esecuzione della pianificazione, è possibile attivare manualmente la creazione di un punto di ripristino:

1. Nel gruppo protezione dati, verificare che lo stato del database sia **OK**.

    ![Un gruppo protezione dati che mostra lo stato del database](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Fare clic con il pulsante destro del mouse sul database e scegliere **Crea punto di ripristino**.

    ![Scegliere di creare un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Nel menu a discesa selezionare **protezione dati online**. Quindi selezionare **OK** per avviare la creazione di un punto di ripristino in Azure.

    ![Avviare la creazione di un punto di ripristino in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. È possibile visualizzare lo stato di avanzamento del processo nell'area di lavoro **monitoraggio** .

    ![Visualizzare lo stato di avanzamento del processo nella console di monitoraggio](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Ripristinare un database SQL Server da Azure

Per ripristinare un'entità protetta, ad esempio un database di SQL Server, da Azure:

1. Aprire la console di gestione del server DPM. Passare all'area di lavoro **ripristino** per visualizzare i server in cui viene eseguito il backup di DPM. Selezionare il database (in questo esempio, ReportServer $ MSDPM2012). Selezionare un **tempo di ripristino** che termina con **online**.

    ![selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Fare clic con il pulsante destro del mouse sul nome del database e scegliere **Ripristina**.

    ![Ripristinare un database da Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM mostra i dettagli del punto di ripristino. Selezionare **Avanti**. Per sovrascrivere il database, selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Fare quindi clic su **Avanti**.

    ![Ripristinare un database nel percorso originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio DPM consente il ripristino del database in un'altra istanza di SQL Server o in una cartella di rete autonoma.
1. Nella pagina **Specifica opzioni di ripristino** è possibile selezionare le opzioni di ripristino. Ad esempio, è possibile scegliere **limitazione all'utilizzo della larghezza di banda** per limitare la larghezza di banda utilizzata dal ripristino. Fare quindi clic su **Avanti**.
1. Nella pagina **Riepilogo** viene visualizzata la configurazione di ripristino corrente. Selezionare **Ripristina**.

    Lo stato del ripristino indica il database da ripristinare. È possibile selezionare **Chiudi** per chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area di lavoro **monitoraggio** .

    ![Avviare il processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Al termine del ripristino, il database ripristinato è coerente con l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [domande frequenti su backup di Azure](backup-azure-backup-faq.md).
