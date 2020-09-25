---
title: Eseguire il backup di SQL Server tramite server di Backup di Azure
description: Questo articolo illustra la configurazione per eseguire il backup dei database SQL Server usando Backup di Microsoft Azure Server (MAB).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 29813741e88ad5f2bc5109be87939abf7cc11502
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316920"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Eseguire il backup di SQL Server in Azure usando server di Backup di Azure

Questo articolo consente di configurare i backup dei database di SQL Server usando Backup di Microsoft Azure Server (MAB).

Per eseguire il backup di un database di SQL Server e ripristinarlo da Azure:

1. Creare un criterio di backup per proteggere i database di SQL Server in Azure.
1. Creare copie di backup su richiesta in Azure.
1. Ripristinare il database in Azure.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

* Se un database con file è ubicato in una condivisione di file remota, la protezione fallirà con Errore ID 104. MAB non supporta la protezione per i dati SQL Server in una condivisione file remota.
* MAB non è in grado di proteggere i database archiviati nelle condivisioni SMB remote.
* Assicurarsi che le [repliche del gruppo di disponibilità siano configurate in sola lettura](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* È necessario aggiungere in modo esplicito l'account di sistema **NTAuthority\System** al gruppo Sysadmin in SQL Server.
* Quando si esegue il ripristino in un percorso alternativo per un database parzialmente indipendente, assicurarsi che per l'istanza di SQL di destinazione sia stata abilitata la funzionalità [Database indipendenti](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable).
* Quando si esegue il ripristino in un percorso alternativo per un database di flusso dei file, assicurarsi che per l'istanza di SQL di destinazione sia stata abilitata la funzionalità del [database FILESTREAM](/sql/relational-databases/blob/enable-and-configure-filestream).
* Protezione per SQL Server AlwaysOn:
  * MAB rileva i gruppi di disponibilità durante l'esecuzione di una richiesta di verifica nella creazione del gruppo protezione
  * MAB rileva un failover e continua la protezione del database.
  * MAB supporta le configurazioni di cluster multisito per un'istanza di SQL Server.
* Quando si proteggono i database che utilizzano la funzionalità AlwaysOn, MAB presenta le limitazioni seguenti:
  * MAB rispetta i criteri di backup per i gruppi di disponibilità impostati in SQL Server in base alle preferenze di backup, come indicato di seguito:
    * Preferisco secondario: i backup devono essere eseguiti in una replica secondaria tranne quando la replica primaria è l'unica replica online. Se sono disponibili più repliche secondarie, verrà selezionato per il backup il nodo con la priorità di backup più elevata. Se è disponibile solo la replica primaria, il backup deve essere eseguito nella replica primaria.
    * Solo secondario: il backup non deve essere eseguito nella replica primaria. Se la replica primaria è l'unica online, il backup non deve essere eseguito.
    * Primario: i backup devono essere sempre eseguiti nella replica primaria.
    * Qualsiasi replica: i backup possono essere eseguiti in qualsiasi replica nel gruppo di disponibilità. Il nodo da cui eseguire il backup sarà basato sulle priorità di backup per ciascuno dei nodi.
  * Tenere presente quanto segue:
    * I backup possono essere eseguiti da qualsiasi replica leggibile, ovvero primario, secondario sincrono, secondario asincrono.
    * Se una replica viene esclusa dal backup, ad esempio se **Escludi replica** è abilitato o è contrassegnato come non leggibile, la replica non verrà selezionata per il backup in alcuna opzione.
    * Se sono disponibili e leggibili più repliche, per il backup verrà selezionato il nodo con la priorità di backup più elevata.
    * Se il backup non riesce nel nodo selezionato, l'operazione di backup avrà esito negativo.
    * Il ripristino nel percorso originale non è supportato.
* Problemi di backup di SQL Server 2014 o versione successiva:
  * In SQL Server 2014 è stata aggiunta una nuova funzionalità per creare un [database di SQL Server locale nell'archivio BLOB di Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Non è possibile usare MAB per proteggere questa configurazione.
  * Esistono alcuni problemi noti relativi alla preferenza di backup "preferisci secondario" per l'opzione SQL AlwaysOn. MAB accetta sempre un backup dal database secondario. Se non viene trovato alcun database secondario, il backup ha esito negativo.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, assicurarsi di aver [installato e preparato server di backup di Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Per proteggere SQL Server database in Azure, creare prima di tutto un criterio di backup:

1. In server di Backup di Azure selezionare l'area di lavoro **protezione** .
1. Selezionare **nuovo** per creare un gruppo protezione dati.

    ![Creazione di un gruppo protezione dati in server di Backup di Azure](./media/backup-azure-backup-sql/protection-group.png)
1. Nella pagina iniziale esaminare le linee guida per la creazione di un gruppo protezione dati. Fare quindi clic su **Avanti**.
1. Per il tipo di gruppo protezione dati selezionare **Server**.

    ![Selezionare il tipo di gruppo protezione dati server](./media/backup-azure-backup-sql/pg-servers.png)
1. Espandere l'istanza di SQL Server in cui si trovano i database di cui si desidera eseguire il backup. Verranno visualizzate le origini dati di cui è possibile eseguire il backup da tale server. Espandere **tutte le condivisioni SQL** , quindi selezionare i database di cui si desidera eseguire il backup. In questo esempio si seleziona ReportServer $ MSDPM2012 e ReportServer $ MSDPM2012TempDB. Selezionare **Avanti**.

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

1. Selezionare **Avanti**. MAB Mostra lo spazio di archiviazione globale disponibile. Mostra anche il potenziale utilizzo dello spazio su disco.

    ![Configurare l'allocazione del disco in MAB](./media/backup-azure-backup-sql/pg-storage.png)

    Per impostazione predefinita, MAB crea un volume per origine dati (SQL Server database). Il volume viene usato per la copia di backup iniziale. In questa configurazione, gestione dischi logici (LDM) limita la protezione da MAB a 300 origini dati (SQL Server database). Per porre rimedio a questa limitazione, selezionare **Condividi percorso dati nel pool di archiviazione DPM**. Se si usa questa opzione, MAB usa un singolo volume per più origini dati. Questa configurazione consente a MAB di proteggere fino a 2.000 database SQL Server.

    Se si seleziona aumenta **automaticamente i volumi**, MAB può considerare l'aumento del volume di backup man mano che i dati di produzione aumentano. Se non si seleziona **aumenta automaticamente i volumi**, MAB limita l'archiviazione di backup alle origini dati nel gruppo protezione dati.
1. Se si è un amministratore, è possibile scegliere di trasferire automaticamente questo backup iniziale **sulla rete** e scegliere l'ora del trasferimento. In alternativa, scegliere di trasferire **manualmente** il backup. Fare quindi clic su **Avanti**.

    ![Scegliere un metodo di creazione della replica in MAB](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (SQL Server database). I dati di backup vengono spostati dal server di produzione (SQL Server computer) a MAB. Se il backup è di grandi dimensioni, il trasferimento dei dati in rete potrebbe causare congestione della larghezza di banda. Per questo motivo, gli amministratori possono scegliere di utilizzare supporti rimovibili per trasferire **manualmente**il backup iniziale. Oppure possono trasferire i dati **automaticamente in rete** a un'ora specificata.

    Al termine del backup iniziale, i backup continuano in modo incrementale sulla copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.
1. Scegliere quando eseguire una verifica di coerenza. Fare quindi clic su **Avanti**.

    ![Scegliere quando eseguire una verifica di coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    MAB può eseguire una verifica di coerenza sull'integrità del punto di backup. Calcola il checksum del file di backup nel server di produzione (il computer SQL Server in questo esempio) e i dati di cui è stato eseguito il backup per il file in MAB. Se il controllo rileva un conflitto, si presuppone che il file di cui è stato eseguito il backup in MAB sia danneggiato. MAB corregge i dati di backup inviando i blocchi che corrispondono alla mancata corrispondenza del checksum. Poiché la verifica della coerenza è un'operazione che richiede un utilizzo intensivo delle prestazioni, gli amministratori possono scegliere di pianificare la verifica della coerenza o di eseguirla automaticamente.
1. Selezionare le origini dati da proteggere in Azure. Fare quindi clic su **Avanti**.

    ![Selezionare le origini dati da proteggere in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se si è un amministratore, è possibile scegliere le pianificazioni di backup e i criteri di conservazione che soddisfano i criteri dell'organizzazione.

    ![Scegliere le pianificazioni e i criteri di conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio, i backup vengono eseguiti ogni giorno alle ore 12:00 e 8:00.

    > [!TIP]
    > Per un ripristino rapido, conserva alcuni punti di ripristino a breve termine sul disco. Questi punti di recupero vengono usati per il recupero operativo. Azure funge da luogo fuori sede, garantendo contratti di servizio più elevati e disponibilità garantita.
    >
    > Usare Data Protection Manager (DPM) per pianificare i backup di Azure al termine dei backup del disco locale. Quando si segue questa procedura, il backup su disco più recente viene copiato in Azure.
    >

1. Scegliere la pianificazione per i criteri di conservazione. Per altre informazioni sul funzionamento dei criteri di conservazione, vedere [usare backup di Azure per sostituire l'infrastruttura](backup-azure-backup-cloud-as-tape.md)basata su nastro.

    ![Scegliere un criterio di conservazione in MAB](./media/backup-azure-backup-sql/pg-retentionschedule.png)

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

### <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [domande frequenti su backup di Azure](backup-azure-backup-faq.md).
