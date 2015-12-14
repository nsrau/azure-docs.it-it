<properties
	pageTitle="Backup di Azure per carichi di lavoro SQL Server tramite DPM | Microsoft Azure"
	description="Introduzione al backup dei database SQL Server tramite il servizio Backup di Azure"
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/26/2015" ms.author="aashishr", "giridham"; "jimpark"/>


# Backup di Azure per carichi di lavoro di SQL server tramite DPM

In questo articolo verrà avviata la procedura di configurazione per il backup dei database SQL Server mediante il Backup di Azure.

Per eseguire il backup dei database di SQL server in Azure è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

La gestione delle operazioni di backup del database SQL server in Azure e di ripristino da Azure prevede tre passaggi:

1. Creare un criterio di backup per proteggere i database SQL Server in Azure.
2. Creare copie di backup su richiesta in Azure.
3. Ripristinare il database da Azure.

## Prima di iniziare
Prima di procedere, assicurarsi che tutti i [prerequisiti ](../backup-azure-dpm-introduction/#prerequisites) per l'uso di Backup di Microsoft Azure per proteggere i carichi di lavoro siano stati soddisfatti. I prerequisiti si applicano ad attività quali, ad esempio, la creazione di un archivio di backup, il download delle credenziali dell'insieme di credenziali, l'installazione dell'agente di Backup di Azure e la registrazione del server con l'insieme di credenziali.

## Creare un criterio di backup per proteggere i database SQL Server in Azure.

1. Nel server DPM, configurare un nuovo criterio di backup per i database SQL server creando un nuovo **Gruppo di protezione**. Fare clic sull'area di lavoro **Protezione**.

2. Fare clic su **Nuovo** per creare un nuovo gruppo di protezione.

    ![Creazione di un gruppo di protezione](./media/backup-azure-backup-sql/protection-group.png)

3. DPM mostra la schermata iniziale con le linee guida per creare un **Gruppo di protezione**. Fare clic su **Avanti**.

4. Selezione dei **Server**.

    ![Selezione del tipo di gruppo di protezione "Server"](./media/backup-azure-backup-sql/pg-servers.png)

5. Espandere la macchina del server SQL in cui sono presenti i database da includere nel backup. DPM mostra diverse origini dati di cui è possibile eseguire il backup in quel server. Espandere **Tutti i server SQL** e selezionare i database (in questo caso sono stati selezionati ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB) di cui eseguire il backup. Fare clic su **Avanti**.

    Verrà visualizzata una schermata simile alla seguente.

    ![Selezione del database SQL](./media/backup-azure-backup-sql/pg-databases.png)

6. Specificare il nome del gruppo di protezione che si sta per creare. Assicurarsi di selezionare l'opzione "**Protezione dati online**".

    ![Metodo di protezione dei dati - disco a breve termine e online in Azure](./media/backup-azure-backup-sql/pg-name.png)

7. Nella schermata **Specifica obiettivi a breve termine** includere gli input necessari per creare punti di backup sul disco.

    Come si può vedere nell'immagine, il **Periodo di mantenimento dati** è impostato su *5 giorni* e la **Frequenza di sincronizzazione** è impostata su una volta ogni *15 minuti*, che corrisponde alla frequenza con cui viene eseguito il backup. **Backup completo rapido** è impostato su *8.00 PM*.

    ![Obiettivi a breve termine](./media/backup-azure-backup-sql/pg-shortterm.png)

    >[AZURE.NOTE]Alle 8.00 PM (secondo la schermata di input) viene creato un punto di backup ogni giorno con il trasferimento dei dati modificati rispetto al punto di backup delle 8.00 PM del giorno precedente. Questo processo è detto **Backup completo rapido**. Mentre i log delle transazioni vengono sincronizzati ogni 15 minuti, se è necessario ripristinare il database alle 9.00 PM, il punto viene creato riproducendo i log dall'ultimo backup completo rapido (in questo caso le 8.00 PM).

8. Fare clic su **Avanti**

    DPM mostra lo spazio di archiviazione complessivo e il potenziale utilizzo dello spazio su disco.

    ![Allocazione dei dischi](./media/backup-azure-backup-sql/pg-storage.png)

    DPM crea un volume per ogni origine dati (database SQL Server) per la creazione della copia di backup iniziale. Con questo approccio, il Gestore dischi logici limita DPM alla protezione solo di un massimo di 300 origini dati (database SQL Server). Per evitare questo limite, DPM ha implementato un altro approccio che usa un singolo volume per più origini dati. Ciò è reso possibile dall'uso della funzionalità **Condividi percorso dati nel pool di archiviazione DPM**. Grazie a questo approccio, DPM può proteggere fino a 2000 database SQL.

    Se l’opzione **Aumenta automaticamente i volumi** è selezionata, DPM può adeguare l'aumento del volume di backup all'aumento dei dati di produzione. Deselezionando l’opzione **Aumenta automaticamente i volumi**, verrà limitato lo spazio di archiviazione del backup usato per eseguire il backup delle origini dati nel gruppo di protezione.

9. Gli amministratori possono scegliere di trasferire manualmente il backup iniziale (fuori rete) per evitare la congestione della larghezza di banda oppure di trasferirlo in rete. Possono anche configurare la data e l'ora di inizio del trasferimento. Fare clic su **Avanti**.

    ![Metodo di replica iniziale](./media/backup-azure-backup-sql/pg-manual.png)

    La copia di backup iniziale richiede il trasferimento dell'intera origine dati (database SQL Server) dal server di produzione (macchina SQL Server) al server DPM. In alcuni casi, questi dati possono essere di dimensioni molto grandi e il loro trasferimento sulla rete potrebbe eccedere la larghezza di banda. Gli amministratori hanno quindi la possibilità di scegliere se trasferire questo backup iniziale **Manualmente** per evitare di esaurire la larghezza di banda oppure di farlo **Automaticamente** tramite le rete. Inoltre, quando gli amministratori scelgono **Rete** hanno la possibilità di scegliere se creare la copia di backup iniziale **Adesso** o **In seguito** a un'ora specificata.

    Una volta completato il backup iniziale, quelli successivi saranno backup incrementali della copia di backup iniziale, in genere molto piccoli e trasferiti in rete.

10. Scegliere quando si vuole eseguire la verifica della coerenza e fare clic su **Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM può eseguire una verifica coerenza per controllare l'integrità del punto di backup. Calcola il checksum del file di backup nel server di produzione (macchina SQL Server in questo scenario) e i dati di cui è stato eseguito il backup per quel file in DPM. In caso di conflitto, si presuppone che il file di backup in DPM sia danneggiato. Data Protection Manager corregge i dati di backup inviando i blocchi che equivalgono alla mancata corrispondenza del checksum. Poiché la verifica coerenza è un'operazione con esigenze di prestazioni elevate, gli amministratori hanno la possibilità di scegliere se pianificarla o eseguirla automaticamente.

11. Per specificare la protezione online delle origini dati, selezionare i database da proteggere in Azure e fare clic su **Avanti**.

    ![Selezione delle origini dati](./media/backup-azure-backup-sql/pg-sqldatabases.png)

12. Gli amministratori possono scegliere le pianificazioni di backup e i criteri di conservazione adatti a soddisfare i criteri dell'organizzazione.

    ![Pianificazione e conservazione](./media/backup-azure-backup-sql/pg-schedule.png)

    In questo esempio i backup vengono eseguiti una volta al giorno alle 12.00 PM e alle 8.00 PM (parte in basso della schermata)

    >[AZURE.NOTE]È consigliabile avere sul disco alcuni punti di ripristino a breve termine per un ripristino rapido. Questa operazione è detta "ripristino operativo". Azure è una posizione esterna ottimale con contratti di servizio più elevati e disponibilità garantita.

    **Procedura consigliata**: verificare che i backup di Azure siano pianificati dopo il completamento dei backup su disco locali con DPM. Ciò consente di copiare in Azure il backup su disco più recente.

13. Scegliere la pianificazione per i criteri di conservazione. Per informazioni dettagliate sul funzionamento dei criteri di conservazione, vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).

    ![Criteri di conservazione](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In questo esempio:

    - I backup vengono eseguiti una volta al giorno alle 12.00 PM e alle 8.00 PM (parte in basso della schermata) e vengono conservati per 180 giorni.
    - Il backup di sabato alle 12.00 PM viene conservato per 104 settimane.
    - Il backup dell'ultimo sabato alle 12.00 PM viene conservato per 60 mesi.
    - Il backup dell'ultimo sabato di marzo alle 12.00 PM viene conservato per 10 anni.

14. Fare clic su **Avanti** e selezionare l'opzione appropriata per il trasferimento in Azure della copia di backup iniziale. È possibile scegliere **Automaticamente tramite la rete** o **Backup offline**.

    - Con **Automaticamente tramite la rete** i dati di backup verranno trasferiti in Azure in base alla pianificazione scelta per il backup.
    - Il funzionamento di **Backup offline** è descritto in [Flusso di lavoro di Backup offline in Backup di Azure](backup-azure-backup-import-export.md).

    Scegliere il meccanismo di trasferimento pertinente per l'invio ad Azure della copia di backup iniziale e fare clic su **Avanti**.

15. Dopo aver esaminato i dettagli dei criteri nella schermata **Riepilogo**, fare clic sul pulsante **Crea gruppo** per completare il flusso di lavoro. È possibile fare clic sul pulsante **Chiudi** e monitorare l'avanzamento del processo nell'area di lavoro Monitoraggio.

    ![Creazione di un gruppo di protezione in corso](./media/backup-azure-backup-sql/pg-summary.png)

## Backup su richiesta di un database SQL Server
Mentre nei passaggi precedenti sono stati creati i criteri di backup, un "punto di ripristino" viene creato solo quando si verifica il primo backup. Anziché attendere l'avvio dell'Utilità di pianificazione, i passaggi seguenti attivano la creazione manuale di un punto di ripristino.

1. Prima di creare il punto di ripristino, attendere finché nello stato del gruppo di protezione non viene visualizzato **OK** per il database.

    ![Membri del gruppo di protezione](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)

2. Fare clic con il pulsante destro del mouse sul database e scegliere **Crea punto di ripristino**.

    ![Creazione di un punto di ripristino online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)

3. Nell'elenco a discesa scegliere **Protezione dati online** e fare clic su **OK**. Verrà avviata la creazione di un punto di ripristino in Azure.

    ![Creazione di un piano di ripristino](./media/backup-azure-backup-sql/sqlbackup-azure.png)

4. È possibile visualizzare l'avanzamento del processo nell'area di lavoro **Monitoraggio**, dove si vedrà un processo in corso come quello illustrato nella figura seguente.

    ![Console di monitoraggio](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## Ripristinare un database SQL Server da Azure
I passaggi seguenti sono necessari per ripristinare un'entità protetta (database SQL Server) da Azure.

1. Aprire la console di gestione del server DPM. Passare all'area di lavoro **Ripristino** dove si potranno vedere i server di cui DPM ha eseguito il backup. Passare al database necessario (in questo caso ReportServer$MSDPM2012). In **Ripristino da** selezionare un orario che termina con **Online**.

    ![Selezione di un punto di ripristino](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)

2. Fare clic con il pulsante destro del mouse sul nome del database e scegliere **Ripristina**.

    ![Ripristino da Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)

3. DPM mostra i dettagli del punto di ripristino. Fare clic su **Avanti**. Selezionare il tipo di ripristino **Ripristina nell'istanza originale di SQL Server**. Il database verrà sovrascritto. Fare clic su **Avanti**.

    ![Ripristino nel percorso originale](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In questo esempio DPM consente il ripristino del database in un'altra istanza di SQL server o in una cartella di rete autonoma.

4. Nella schermata **Specifica opzioni di ripristino** è possibile selezionare le opzioni di ripristino, ad esempio Limitazione all'utilizzo della larghezza di banda per controllare la larghezza di banda usata dal processo di ripristino. Fare clic su **Avanti**.

5. Nella schermata **Riepilogo**, verranno visualizzate le configurazioni di ripristino impostate finora. Fare clic su **Ripristina**.

    In Stato ripristino è visualizzato il database in corso di ripristino. È possibile fare clic **Chiudi** per chiudere la procedura guidata e visualizzare lo stato di avanzamento nell'area di lavoro **Monitoraggio**.

    ![Avvio del processo di ripristino](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Una volta completato il ripristino, la copia del database ripristinata sarà coerente con l'applicazione.

### Passaggi successivi:

• [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_1203_2015-->