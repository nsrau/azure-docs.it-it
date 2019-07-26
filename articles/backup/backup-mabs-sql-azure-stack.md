---
title: Eseguire il backup di carichi di lavoro di SQL Server in Azure Stack
description: Usare il server di Backup di Azure per proteggere il carico di lavoro di SQL Server in Azure Stack.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: 11d03a9c5cc81b915f48bc66f5a0e5ab034662ed
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465150"
---
# <a name="back-up-sql-server-on-stack"></a>Eseguire il backup di SQL Server in Stack
Usare questo articolo per configurare il server di Backup di Microsoft Azure (MABS) per proteggere i database di SQL Server in Azure Stack.

La gestione delle operazioni di backup del database SQL server in Azure e di ripristino da Azure prevede tre passaggi:

1. Creare un criterio di backup per proteggere i database SQL Server
2. Creare copie di backup su richiesta
3. Ripristinare il database dai dischi e da Azure

## <a name="before-you-start"></a>Prima di iniziare

[Installare e preparare il server di Backup di Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Creare un criterio di backup per proteggere i database SQL Server in Azure.
1. Nell'interfaccia utente del server di Backup di Azure fare clic sull'area di lavoro **Protezione**.

2. Nella barra degli strumenti, fare clic su **Nuovo** per creare un nuovo gruppo di protezione.

    ![Creare un gruppo di protezione](./media/backup-azure-backup-sql/protection-group.png)

    Il server di Backup di Azure avvia la procedura guidata Crea nuovo gruppo protezione dati, che guida l'utente nei passaggi necessari per creare un **gruppo protezione dati**. Fare clic su **Avanti**.

3. Nella schermata **Selezione tipo di gruppo protezione dati** selezionare **Server**.

    ![Selezione del tipo di gruppo di protezione "Server"](./media/backup-azure-backup-sql/pg-servers.png)

4. Nell'elenco Membri disponibili della schermata **Selezione membri del gruppo** sono visualizzate le varie origini dati. Fare clic su **+** per espandere una cartella e visualizzare le sottocartelle. Fare clic sulla casella di controllo per selezionare un elemento.

    ![Selezione del database SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Tutti gli elementi selezionati vengono visualizzati nell'elenco Membri selezionati. Dopo aver selezionato i server o i database da proteggere, fare clic su **Avanti**.

5. Nella schermata **Seleziona metodo protezione dati** specificare un nome per il gruppo di protezione e selezionare la casella di controllo **Desidero la protezione dati online**.

    ![Metodo di protezione dei dati - disco a breve termine e online in Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Nella schermata **Specifica obiettivi a breve termine** includere gli input necessari per creare punti di backup sul disco e fare clic su **Avanti**.

    Nell'esempio l'**intervallo di conservazione** è **5 giorni**, la **frequenza di sincronizzazione** è una volta ogni **15 minuti**, che è la frequenza di backup. **Backup completo rapido** è impostato su **8.00 PM**.

    ![Obiettivi a breve termine](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Nell'esempio illustrato alle 20.00 di ogni giorno viene creato un punto di backup trasferendo i dati modificati dal punto di backup delle ore 20.00 del giorno precedente. Questo processo è detto **Backup completo rapido**. I log delle transazioni vengono sincronizzati ogni 15 minuti. Se è necessario recuperare il database alle 21.00, il punto viene creato in base ai log dall'ultimo punto di backup completo rapido, in questo caso le 20.00.
   >
   >

7. Nella schermata **Verifica allocazione dischi** verificare lo spazio di archiviazione complessivo disponibile e il potenziale spazio su disco. Fare clic su **Avanti**.

8. In **Scelta del metodo per la creazione della replica** scegliere la modalità di creazione del primo punto di recupero. Il backup iniziale (fuori rete) può essere trasferito manualmente per evitare la congestione della larghezza di banda o attraverso la rete. Se si sceglie di attendere prima di trasferire il primo backup, è possibile specificare l'orario del trasferimento iniziale. Fare clic su **Avanti**.

    ![Metodo di replica iniziale](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (database SQL Server) dal server di produzione (macchina SQL Server) al server di Backup di Azure. Tali dati potrebbero essere di grandi dimensioni e trasferimento dei dati sulla rete potrebbe superare la larghezza di banda. Per questo motivo, è possibile scegliere di trasferire il backup iniziale: **manualmente** (usando supporti rimovibili) per evitare la congestione della larghezza di banda o **automaticamente attraverso la rete** (a un orario specificato).

    Una volta completato il backup iniziale, quelli successivi saranno backup incrementali della copia di backup iniziale. I backup incrementali tendono a essere di piccole dimensioni e facilmente trasferibili sulla rete.

9. Scegliere quando si vuole eseguire la verifica della coerenza e fare clic su **Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    Il server di Backup di Azure esegue una verifica di coerenza per controllare l'integrità del punto di backup. Il server di Backup di Azure calcola il checksum del file di backup nel server di produzione (computer SQL Server in questo scenario) e i dati di cui è stato eseguito il backup per quel file. Se si verifica un conflitto, si presume che il file di cui è stato eseguito il backup nel server di Backup di Azure sia danneggiato. Il server di Backup di Azure corregge i dati di backup inviando i blocchi che equivalgono alla mancata corrispondenza del checksum. Poiché le verifiche della coerenza sono esigenti in termini di prestazioni, è possibile pianificarle o eseguirle automaticamente.

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

    In questo esempio:

    * I backup vengono eseguiti una volta al giorno alle 12.00 PM e alle 8.00 PM (parte in basso della schermata) e vengono conservati per 180 giorni.
    * Il backup di sabato alle ore 12:00 P.M. viene conservato per 104 settimane
    * Il backup dell'ultimo sabato alle 12.00 P.M. viene conservato per 60 mesi
    * Il backup dell'ultimo sabato di marzo alle 12.00 P.M. viene conservato per 10 anni
13. Fare clic su **Avanti** e selezionare l'opzione appropriata per il trasferimento in Azure della copia di backup iniziale. È possibile scegliere **Automaticamente tramite la rete**

14. Dopo aver esaminato i dettagli dei criteri nella schermata **Riepilogo**, fare clic su **Crea gruppo** per completare il flusso di lavoro. È possibile fare clic su **Chiudi** e monitorare l'avanzamento del processo nell'area di lavoro Monitoraggio.

    ![Creazione di un gruppo di protezione in corso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup su richiesta di un database SQL Server
Mentre nei passaggi precedenti sono stati creati i criteri di backup, un "punto di ripristino" viene creato solo quando si verifica il primo backup. Anziché attendere l'avvio dell'Utilità di pianificazione, i passaggi seguenti attivano la creazione manuale di un punto di ripristino.

1. Prima di creare il punto di ripristino, attendere finché nello stato del gruppo di protezione non viene visualizzato **OK** per il database.

    ![Membri del gruppo di protezione](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Fare clic con il pulsante destro del mouse sul database e scegliere **Crea punto di ripristino**.

    ![Creazione di un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Scegliere **Protezione dati online** nel menu a discesa e fare clic su **OK** per avviare la creazione di un punto di recupero in Azure.

    ![Crea punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visualizzare lo stato del processo nell'area di lavoro **Monitoraggio**.

    ![Console di monitoraggio](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Ripristinare un database SQL Server da Azure
I passaggi seguenti sono necessari per ripristinare un'entità protetta (database SQL Server) da Azure.

1. Aprire la console di gestione del server di Backup di Azure. Passare all'area di lavoro **Recupero** dove appaiono i server protetti. Passare al database necessario (in questo caso ReportServer$MSDPM2012). Selezionare un orario di inizio del **recupero**, specificato come punto **online**.

    ![Selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Fare clic con il pulsante destro del mouse sul nome del database e scegliere **Ripristina**.

    ![Ripristino da Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS mostra i dettagli del punto di ripristino. Fare clic su **Avanti**. Per sovrascrivere il database, selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Fare clic su **Avanti**.

    ![Ripristino nel percorso originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio MABS recupera il database in un'altra istanza di SQL server o in una cartella di rete autonoma.

4. Nella schermata **Specifica opzioni di ripristino** è possibile selezionare le opzioni di ripristino, ad esempio Limitazione all'utilizzo della larghezza di banda per controllare la larghezza di banda usata dal processo di ripristino. Fare clic su **Avanti**.

5. Nella schermata **Riepilogo** vengono visualizzate le configurazioni di ripristino impostate finora. Fare clic su **Ripristina**.

    In Stato ripristino è visualizzato il database in corso di ripristino. È possibile fare clic **Chiudi** per chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area di lavoro **Monitoraggio**.

    ![Avvio del processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Al termine del ripristino, il database ripristinato sarà coerente con l'applicazione.

## <a name="next-steps"></a>Fasi successive

Vedere l'articolo [Eseguire il backup di file e applicazioni in Azure Stack](backup-mabs-files-applications-azure-stack.md).
Vedere l'articolo [Backup SharePoint on Azure Stack](backup-mabs-sharepoint-azure-stack.md) (Backup di SharePoint in Azure Stack).
