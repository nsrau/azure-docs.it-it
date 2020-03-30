---
title: Backup di SQL Server in Azure come carico di lavoro DPM
description: Introduzione al backup dei database di SQL Server tramite il servizio Backup di Azure
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505926"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Backup di SQL Server in Azure come carico di lavoro DPM

Questo articolo illustra i passaggi di configurazione per eseguire il backup dei database di SQL Server tramite Backup di Azure.This article leads you through the configuration steps to back up SQL Server databases by using Azure Backup.

Per eseguire il backup dei database di SQL server in Azure è necessario un account Azure. Se non ne hai uno, puoi creare un account gratuito in pochi minuti. Per altre informazioni, vedere [Creare l'account gratuito di Azure.For](https://azure.microsoft.com/pricing/free-trial/)more information, see Create your Azure free account .

To back up a SQL Server database to Azure and to recover it from Azure:

1. Creare criteri di backup per proteggere i database di SQL Server in Azure.Create a backup policy to protect SQL Server databases in Azure.
1. Creare copie di backup su richiesta in Azure.Create on-demand backup copies in Azure.
1. Ripristinare il database da Azure.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare di aver soddisfatto [i prerequisiti](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per l'uso di Backup di Azure per proteggere i carichi di lavoro. Di seguito sono riportate alcune delle attività preliminari: 
* Creare un insieme di credenziali di backup.
* Scaricare le credenziali dell'insieme di credenziali. 
* Installare l'agente di Backup di Azure.Install the Azure Backup agent.
* Registrare il server con l'insieme di credenziali.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup 

Per proteggere i database di SQL Server in Azure, creare innanzitutto un criterio di backup:To protect SQL Server databases in Azure, first create a backup policy:

1. Nel server Data Protection Manager (DPM) selezionare l'area di lavoro **Protezione dati.**
1. Selezionare **Nuovo** per creare un gruppo protezione dati.

    ![Creare un gruppo protezione dati](./media/backup-azure-backup-sql/protection-group.png)
1. Nella pagina iniziale esaminare le indicazioni sulla creazione di un gruppo protezione dati. Quindi selezionare **Avanti**.
1. Selezionare **Server**.

    ![Selezionare il tipo di gruppo protezione dati Server](./media/backup-azure-backup-sql/pg-servers.png)
1. Espandere il computer SQL Server in cui si trovano i database di cui si desidera eseguire il backup. Vengono visualizzate le origini dati di cui è possibile eseguire il backup da tale server. Espandere **Tutte le condivisioni SQL** e quindi selezionare i database di cui si desidera eseguire il backup. In questo esempio, si seleziona ReportServer, MSDPM2012 e ReportServer, MSDPM2012TempDB. Quindi selezionare **Avanti**.

    ![Selezionare un database di SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Assegnare un nome al gruppo protezione dati e quindi selezionare **Si desidera la protezione online**.

    ![Scegliere un metodo di protezione dei dati - protezione del disco a breve termine o protezione di Azure onlineChoose a data-protection method - short-term disk protection or online Azure protection](./media/backup-azure-backup-sql/pg-name.png)
1. Nella pagina **Specifica obiettivi a breve termine** includere gli input necessari per creare punti di backup sul disco.

    In questo esempio, **L'intervallo di conservazione** è impostato su *5 giorni*. La **frequenza di sincronizzazione** del backup è impostata su una volta ogni *15 minuti*. **Express Full Backup** è impostato su *8:00 PM*.

    ![Configurare obiettivi a breve termine per la protezione del backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In questo esempio, un punto di backup viene creato alle 20:00 ogni giorno. I dati modificati dal punto di backup delle 20:00 del giorno precedente vengono trasferiti. Questo processo è detto **Backup completo rapido**. Anche se i registri delle transazioni vengono sincronizzati ogni 15 minuti, se è necessario ripristinare il database alle 21:00, il punto viene creato riproducendo i registri dall'ultimo punto di backup completo rapido, ovvero le 20:00 in questo esempio.
   >
   >

1. Fare clic su **Avanti**. DPM mostra lo spazio di archiviazione complessivo disponibile. Mostra anche il potenziale utilizzo dello spazio su disco.

    ![Impostare l'allocazione dei dischi](./media/backup-azure-backup-sql/pg-storage.png)

    Per impostazione predefinita, DPM crea un volume per ogni origine dati (database DI SQL Server). Il volume viene utilizzato per la copia di backup iniziale. In questa configurazione, Gestione dischi logici (LDM) limita la protezione DPM a 300 origini dati (database di SQL Server). Per porre rimedio a questa limitazione, selezionare **Condividi percorso dati nel pool di archiviazione DPM**. Se si utilizza questa opzione, DPM utilizza un singolo volume per più origini dati. Questo programma di installazione consente a DPM di proteggere fino a 2.000 database di SQL Server.

    Se si seleziona **Aumenta automaticamente i volumi**, DPM può tenere conto dell'aumento del volume di backup man mano che i dati di produzione aumentano. Se non si seleziona **Essenza automatica dei volumi**, DPM limita l'archiviazione di backup alle origini dati nel gruppo protezione dati.

1. Se sei un amministratore, puoi scegliere di trasferire automaticamente questo backup iniziale **in rete** e scegliere l'ora del trasferimento. In alternativa, scegliere di trasferire **manualmente** il backup. Quindi selezionare **Avanti**.

    ![Scegliere un metodo di creazione di repliche](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (database di SQL Server). I dati di backup vengono spostati dal server di produzione (computer SQL Server) al server DPM. Se questo backup è di grandi dimensioni, il trasferimento dei dati in rete potrebbe causare la congestione della larghezza di banda. Per questo motivo, gli amministratori possono scegliere di utilizzare supporti rimovibili per trasferire manualmente il **backup**iniziale. Oppure possono trasferire i dati **automaticamente in rete** in un momento specificato.

    Al termine del backup iniziale, i backup continuano in modo incrementale nella copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.
    
1. Scegliere quando eseguire una verifica di coerenza. Quindi selezionare **Avanti**.

    ![Scegliere quando eseguire una verifica di coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM può eseguire una verifica di coerenza sull'integrità del punto di backup. Calcola il checksum del file di backup nel server di produzione (il computer SQL Server in questo esempio) e i dati di backup per tale file in DPM. Se il controllo rileva un conflitto, si presuppone che il file di cui è stato eseguito il backup in DPM sia danneggiato. DPM corregge i dati di cui è stato eseguito il backup inviando i blocchi che corrispondono alla mancata corrispondenza del checksum. Poiché la verifica coerenza è un'operazione che richiede un utilizzo intensivo delle prestazioni, gli amministratori possono scegliere di pianificare la verifica di coerenza o di eseguirla automaticamente.

1. Selezionare le origini dati da proteggere in Azure.Select the data sources to protect in Azure. Quindi selezionare **Avanti**.

    ![Selezionare le origini dati da proteggere in AzureSelect data sources to protect in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Gli amministratori possono scegliere le pianificazioni di backup e i criteri di conservazione adatti ai criteri dell'organizzazione.

    ![Scegliere le pianificazioni e i criteri di conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio, i backup vengono eseguiti ogni giorno alle 12:00 e alle 20:00.

    > [!TIP]
    > Per un ripristino rapido, mantenere alcuni punti di ripristino a breve termine sul disco. Questi punti di recupero vengono usati per il recupero operativo. Azure funge da buona posizione fuori sede, fornendo contratti di servizio più elevati e disponibilità garantita.
    >
    > Utilizzare DPM per pianificare i backup di Azure al termine dei backup del disco locale. Quando si segue questa procedura, il backup del disco più recente viene copiato in Azure.When follow this practice, the latest disk backup is copied to Azure.
    >

1. Scegliere la pianificazione per i criteri di conservazione. Per altre informazioni sul funzionamento dei criteri di conservazione, vedere Usare Backup di [Azure per sostituire l'infrastruttura nastro.](backup-azure-backup-cloud-as-tape.md)

    ![Scegliere un criterio di conservazione](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Esempio:

    * I backup vengono eseguiti tutti i giorni alle 12:00 e alle 20:00. Sono tenuti per 180 giorni.
    * Il backup il sabato alle 12:00 pm viene conservato per 104 settimane.
    * Il backup dell'ultimo sabato del mese alle 12:00 viene conservato per 60 mesi.
    * Il backup dell'ultimo sabato di marzo alle 12:00 è conservato per 10 anni.
    
    Dopo aver scelto un criterio di conservazione, selezionare **Avanti**.

1. Scegliere come trasferire la copia di backup iniziale in Azure.Choose how to transfer the initial backup copy to Azure.

    * L'opzione Automaticamente in rete segue la pianificazione del backup per trasferire i dati in Azure.The **Automatically over the network** option follows your backup schedule to transfer the data to Azure.
    * Per ulteriori informazioni sul **backup offline**, vedere [Panoramica del backup non in](offline-backup-overview.md)linea .

    Dopo aver scelto un meccanismo di trasferimento, selezionare **Avanti**.

1. Nella pagina **Riepilogo** esaminare i dettagli dei criteri. Selezionare quindi **Crea gruppo**. È possibile selezionare **Chiudi** e controllare l'avanzamento del processo nell'area di lavoro **Monitoraggio.**

    ![Avanzamento della creazione del gruppo protezione dati](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Creare copie di backup su richiesta di un database di SQL ServerCreate on-demand backup copies of a SQL Server database

Un punto di ripristino viene creato quando si verifica il primo backup. Anziché attendere l'esecuzione della pianificazione, è possibile attivare manualmente la creazione di un punto di ripristino:Anzi, you can manually trigger the creation of a recovery point:

1. Nel gruppo protezione dati verificare che lo stato del database sia **OK**.

    ![Un gruppo protezione dati con lo stato del database](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Fare clic con il pulsante destro del mouse sul database e quindi **scegliere Crea punto di ripristino**.

    ![Scegliere di creare un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Nel menu a discesa selezionare **Protezione online**. Selezionare **quindi OK** per avviare la creazione di un punto di ripristino in Azure.Then select OK to start the creation of a recovery point in Azure.

    ![Iniziare a creare un punto di ripristino in AzureStart creating a recovery point in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. È possibile visualizzare lo stato di avanzamento del processo nell'area di lavoro **Monitoraggio.**

    ![Visualizzare lo stato di avanzamento del processo nella console di monitoraggioView job progress in the Monitoring console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Ripristinare un database SQL Server da Azure

Per ripristinare un'entità protetta, ad esempio un database di SQL Server, da Azure:To recover a protected entity, such as a SQL Server database, from Azure:

1. Aprire la console di gestione del server DPM. Passare all'area di lavoro **Ripristino** per visualizzare i server di cui DPM esegue il backup. Selezionare il database (in questo esempio, ReportServer/MSDPM2012). Selezionare un tempo di **recupero** che termina con **Online**.

    ![selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Fare clic con il pulsante destro del mouse sul nome del database e **scegliere Ripristina**.

    ![Ripristinare un database da AzureRecover a database from Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM mostra i dettagli del punto di ripristino. Fare clic su **Avanti**. Per sovrascrivere il database, selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Quindi selezionare **Avanti**.

    ![Ripristinare un database nella posizione originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio, DPM consente di ripristinare il database in un'altra istanza di SQL Server SQL Server o in una cartella di rete autonoma.
1. Nella pagina **Specifica opzioni di ripristino** è possibile selezionare le opzioni di ripristino. Ad esempio, è possibile scegliere **La limitazione dell'utilizzo** della larghezza di banda di rete per limitare la larghezza di banda utilizzata dal ripristino. Quindi selezionare **Avanti**.
1. Nella pagina **Riepilogo** viene visualizzata la configurazione di ripristino corrente. Selezionare **Recover (Recupera).**

    Lo stato di ripristino mostra il database in fase di ripristino. È possibile selezionare **Chiudi** per chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area di lavoro **Monitoraggio.**

    ![Avviare il processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Al termine del ripristino, il database ripristinato è coerente con l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere Domande frequenti su Backup di Azure .For more [information,](backup-azure-backup-faq.md)see Azure Backup FAQ .
