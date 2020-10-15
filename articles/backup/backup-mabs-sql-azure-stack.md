---
title: Eseguire il backup di carichi di lavoro di SQL Server in Azure Stack
description: Questo articolo illustra come configurare Backup di Microsoft Azure Server (MAB) per proteggere i database di SQL Server in Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 80de7913b010fca69c3703e423109f2ede653590
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332815"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Eseguire il backup di SQL Server in Azure Stack

Usare questo articolo per configurare il server di Backup di Microsoft Azure (MABS) per proteggere i database di SQL Server in Azure Stack.

La gestione delle operazioni di backup del database SQL server in Azure e di ripristino da Azure prevede tre passaggi:

1. Creare un criterio di backup per proteggere i database SQL Server
2. Creare copie di backup su richiesta
3. Ripristinare il database dai dischi e da Azure

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

[Installare e preparare il server di Backup di Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Creare un criterio di backup per proteggere i database SQL Server in Azure.

1. Nell'interfaccia utente di server di Backup di Azure selezionare l'area di lavoro **protezione** .

2. Nella barra multifunzione dello strumento selezionare **nuovo** per creare un nuovo gruppo protezione dati.

    ![Creazione di un gruppo di protezione](./media/backup-azure-backup-sql/protection-group.png)

    Il server di Backup di Azure avvia la procedura guidata Crea nuovo gruppo protezione dati, che guida l'utente nei passaggi necessari per creare un **gruppo protezione dati**. Selezionare **Avanti**.

3. Nella schermata **Selezione tipo di gruppo protezione dati** selezionare **Server**.

    ![Selezione del tipo di gruppo di protezione "Server"](./media/backup-azure-backup-sql/pg-servers.png)

4. Nell'elenco Membri disponibili della schermata **Selezione membri del gruppo** sono visualizzate le varie origini dati. Selezionare **+** questa finestra per espandere una cartella e visualizzare le sottocartelle. Selezionare la casella di controllo per selezionare un elemento.

    ![Selezione del database SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Tutti gli elementi selezionati vengono visualizzati nell'elenco Membri selezionati. Dopo aver selezionato i server o i database che si desidera proteggere, selezionare **Avanti**.

5. Nella schermata **Seleziona metodo protezione dati** specificare un nome per il gruppo di protezione e selezionare la casella di controllo **Desidero la protezione dati online**.

    ![Metodo di protezione dei dati - disco a breve termine e online in Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Nella schermata **Specifica obiettivi Short-Term** includere gli input necessari per creare punti di backup su disco e fare clic su **Avanti**.

    Nell'esempio l'**intervallo di conservazione** è **5 giorni**, la **frequenza di sincronizzazione** è una volta ogni **15 minuti**, che è la frequenza di backup. **Backup completo rapido** è impostato su **8.00 PM**.

    ![Obiettivi a breve termine](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Nell'esempio illustrato alle 20.00 di ogni giorno viene creato un punto di backup trasferendo i dati modificati dal punto di backup delle ore 20.00 del giorno precedente. Questo processo è detto **Backup completo rapido**. I log delle transazioni vengono sincronizzati ogni 15 minuti. Se è necessario recuperare il database alle 21.00, il punto viene creato in base ai log dall'ultimo punto di backup completo rapido, in questo caso le 20.00.
   >
   >

7. Nella schermata **Verifica allocazione dischi** verificare lo spazio di archiviazione complessivo disponibile e il potenziale spazio su disco. Selezionare **Avanti**.

8. In **Scelta del metodo per la creazione della replica** scegliere la modalità di creazione del primo punto di recupero. Il backup iniziale (fuori rete) può essere trasferito manualmente per evitare la congestione della larghezza di banda o attraverso la rete. Se si sceglie di attendere prima di trasferire il primo backup, è possibile specificare l'orario del trasferimento iniziale. Selezionare **Avanti**.

    ![Metodo di replica iniziale](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (SQL Server database) dal server di produzione (SQL Server computer) a server di Backup di Azure. Tali dati potrebbero essere di grandi dimensioni e trasferimento dei dati sulla rete potrebbe superare la larghezza di banda. Per questo motivo, l'utente può scegliere di trasferire il backup iniziale: **manualmente** (usando supporti rimovibili) per evitare la congestione della larghezza di banda o **automaticamente attraverso la rete** (ad un orario specificato).

    Una volta completato il backup iniziale, quelli successivi saranno backup incrementali della copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.

9. Scegliere quando si desidera eseguire la verifica della coerenza e selezionare **Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    Il server di Backup di Azure esegue una verifica di coerenza per controllare l'integrità del punto di backup. Server di Backup di Azure calcola il checksum del file di backup nel server di produzione (SQL Server computer in questo scenario) e i dati di cui è stato eseguito il backup per tale file. Se si verifica un conflitto, si presuppone che il file di cui è stato eseguito il backup in server di Backup di Azure sia danneggiato. Il server di Backup di Azure corregge i dati di backup inviando i blocchi che equivalgono alla mancata corrispondenza del checksum. Poiché le verifiche della coerenza sono esigenti in termini di prestazioni, è possibile pianificarle o eseguirle automaticamente.

10. Per specificare la protezione online delle origini dati, selezionare i database da proteggere in Azure e fare clic su **Avanti**.

    ![Selezione delle origini dati](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Scegliere le pianificazioni di backup e i criteri di conservazione adatti a soddisfare i criteri dell'organizzazione.

    ![Pianificazione e conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio i backup vengono eseguiti una volta al giorno alle 12.00 PM e alle 8.00 PM (parte in basso della schermata)

    > [!NOTE]
    > È consigliabile avere sul disco alcuni punti di ripristino a breve termine per un ripristino rapido. Questi punti di recupero vengono usati per il recupero operativo. Azure è una posizione esterna ottimale con contratti di servizio più elevati e disponibilità garantita.
    >
    >

    **Procedura consigliata**: se si pianificano backup in Azure da avviare al termine dell'esecuzione dei backup del disco locale, i backup del disco più recenti vengono sempre copiati in Azure.

12. Scegliere la pianificazione per i criteri di conservazione. Per informazioni dettagliate sul funzionamento dei criteri di conservazione, vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).

    ![Criteri di conservazione](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Esempio:

    * I backup vengono eseguiti una volta al giorno alle 12.00 PM e alle 8.00 PM (parte in basso della schermata) e vengono conservati per 180 giorni.
    * Il backup di sabato alle ore 12:00 P.M. viene conservato per 104 settimane
    * Il backup dell'ultimo sabato alle 12.00 P.M. viene conservato per 60 mesi
    * Il backup dell'ultimo sabato di marzo alle 12.00 P.M. viene conservato per 10 anni
13. Selezionare **Avanti** e selezionare l'opzione appropriata per trasferire la copia di backup iniziale in Azure. È possibile scegliere **Automaticamente tramite la rete**

14. Dopo aver esaminato i dettagli dei criteri nella schermata **Riepilogo** , selezionare **Crea gruppo** per completare il flusso di lavoro. È possibile selezionare **Chiudi** e monitora lo stato di avanzamento del processo nell'area di lavoro monitoraggio.

    ![Creazione di un gruppo di protezione in corso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup su richiesta di un database SQL Server

Mentre nei passaggi precedenti sono stati creati i criteri di backup, un "punto di ripristino" viene creato solo quando si verifica il primo backup. Anziché attendere l'avvio dell'Utilità di pianificazione, i passaggi seguenti attivano la creazione manuale di un punto di ripristino.

1. Prima di creare il punto di ripristino, attendere finché nello stato del gruppo di protezione non viene visualizzato **OK** per il database.

    ![Membri del gruppo di protezione](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Fare clic con il pulsante destro del mouse sul database e scegliere **Crea punto di ripristino**.

    ![Creazione di un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Scegliere **protezione dati online** dal menu a discesa e selezionare **OK** per avviare la creazione di un punto di ripristino in Azure.

    ![Crea punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visualizzare lo stato del processo nell'area di lavoro **Monitoraggio**.

    ![Console di monitoraggio](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Ripristinare un database SQL Server da Azure

I passaggi seguenti sono necessari per ripristinare un'entità protetta (database SQL Server) da Azure.

1. Aprire la console di gestione del server di Backup di Azure. Passare all'area di lavoro **Recupero** dove appaiono i server protetti. Passare al database necessario (in questo caso ReportServer$MSDPM2012). Selezionare un **ripristino dal** momento specificato come punto **online** .

    ![Selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Fare clic con il pulsante destro del mouse sul nome del database e scegliere **Ripristina**.

    ![Ripristino da Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS mostra i dettagli del punto di ripristino. Selezionare **Avanti**. Per sovrascrivere il database, selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Selezionare **Avanti**.

    ![Ripristino nel percorso originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio MABS recupera il database in un'altra istanza di SQL server o in una cartella di rete autonoma.

4. Nella schermata **Specifica opzioni di ripristino** è possibile selezionare le opzioni di ripristino, ad esempio Limitazione all'utilizzo della larghezza di banda per controllare la larghezza di banda usata dal processo di ripristino. Selezionare **Avanti**.

5. Nella schermata **Riepilogo** vengono visualizzate le configurazioni di ripristino impostate finora. Selezionare **Ripristina**.

    In Stato ripristino è visualizzato il database in corso di ripristino. È possibile selezionare **Chiudi** per chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area di lavoro **monitoraggio** .

    ![Avvio del processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Al termine del ripristino, il database ripristinato sarà coerente con l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo [Eseguire il backup di file e applicazioni in Azure Stack](backup-mabs-files-applications-azure-stack.md).
Vedere l'articolo [Backup SharePoint on Azure Stack](backup-mabs-sharepoint-azure-stack.md) (Backup di SharePoint in Azure Stack).
