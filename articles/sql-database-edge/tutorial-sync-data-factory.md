---
title: Sincronizzare i dati dal database SQL Edge di Azure usando Azure Data Factory | Microsoft Docs
description: Informazioni sulla sincronizzazione dei dati tra il database SQL Edge di Azure e l'archivio BLOB di Azure
keywords: database sql edge, sincronizzare i dati dal database sql edge, data factory del database sql edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 2bfa65117bf31ad9cb9917fd8a643a0358e02be0
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384216"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Esercitazione: Sincronizzare i dati dal database SQL Edge all'archivio BLOB di Azure usando Azure Data Factory

In questa esercitazione si userà Azure Data Factory per sincronizzare in modo incrementale i dati nell'archivio BLOB di Azure da una tabella in un'istanza del database SQL Edge di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Se non sono già stati creati un database o una tabella nella distribuzione del database SQL Edge di Azure, usare uno dei metodi seguenti per crearli:

* Usare [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) o [Azure Data Studio](/sql/azure-data-studio/download/) per connettersi a al database SQL Edge. Eseguire uno script SQL per creare il database e la tabella.
* Creare una tabella e un database SQL tramite [SQLCMD](/sql/tools/sqlcmd-utility/) connettendosi direttamente al modulo del database SQL Edge. Per altre informazioni, vedere [Connettersi al motore di database usando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Usare SqlPackage.exe per distribuire un file del pacchetto di applicazione livello dati nel contenitore del database SQL Edge. È possibile automatizzare questo processo specificando l'URI del file SqlPackage come parte della configurazione delle proprietà desiderate del modulo. È anche possibile usare direttamente lo strumento client SqlPackage.exe per distribuire un pacchetto di applicazione livello dati nel database SQL Edge.

    Per informazioni su come scaricare SqlPackage.exe, vedere [Scaricare e installare sqlpackage](/sql/tools/sqlpackage-download/). Di seguito sono riportati alcuni comandi di esempio per SqlPackage.exe. Per altre informazioni, vedere la documentazione di SqlPackage.exe.

    **Creare un pacchetto di applicazione livello dati**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Applicare un pacchetto di applicazione livello dati**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Creare una tabella e una procedura SQL per archiviare e aggiornare i livelli dei limiti

La tabella dei limiti viene usata per archiviare l'ultimo timestamp fino al quale i dati sono già stati sincronizzati con la risorsa di archiviazione di Azure. La stored procedure Transact-SQL (T-SQL) viene usata per aggiornare la tabella dei limiti dopo ogni sincronizzazione.

Eseguire questi comandi sull'istanza del database SQL Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Creare una pipeline di Data Factory

In questa sezione si creerà una pipeline di Azure Data Factory per sincronizzare i dati nell'archivio BLOB di Azure da una tabella di un database SQL Edge di Azure.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Creare una data factory usando l'interfaccia utente di Data Factory

Creare una data factory seguendo le istruzioni di [questa esercitazione](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Creare una pipeline di Data Factory

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Data Factory selezionare **Crea pipeline**.

    ![Creare una pipeline di Data Factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Nella pagina **Generale** della finestra **Proprietà** della pipeline immettere il nome **PeriodicSync**.

3. Aggiungere l'attività Ricerca per ottenere il valore limite precedente. Nel riquadro **Attività** espandere **Generale** e trascinare l'attività **Ricerca** nell'area di progettazione della pipeline. Cambiare il nome dell'attività in **OldWatermark**.

    ![Aggiungere la ricerca limite precedente](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Passare alla scheda **Impostazioni** e selezionare **Nuovo** per **Source Dataset** (Set di dati di origine). Verrà quindi creato un set di dati per rappresentare i dati nella tabella dei limiti. Questa tabella contiene il limite precedente che è stato usato nella precedente operazione di copia.

5. Nella finestra **Nuovo set di dati** selezionare **Server di Azure SQL**  e quindi **Continua**.  

6. Nella finestra **Imposta proprietà** per il set di dati immettere **WatermarkDataset** per in **Nome**.

7. Per **Servizio collegato**, selezionare **Nuovo** e quindi seguire questa procedura:

    1. In **Nome** immettere **SQLDBEdgeLinkedService**.

    2. In **Nome server** immettere i dettagli del server di database SQL Edge.

    3. Selezionare il **Nome del database** dall'elenco.

    4. Immettere il **Nome utente** e la **Password**.

    5. Per testare la connessione all'istanza del database SQL Edge, selezionare **Test connessione**.

    6. Selezionare **Create** (Crea).

    ![Creare un servizio collegato](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selezionare **OK**.

8. Nella scheda **Impostazioni** selezionare **Modifica**.

9. Nella scheda **Connessione** selezionare **[dbo].[watermarktable]** per **Tabella**. Se si vuole visualizzare un'anteprima dei dati nella tabella, selezionare **Anteprima dati**.

10. Passare all'editor di pipeline selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività Ricerca verificare che sia selezionata la voce **WatermarkDataset** nell'elenco **Source Dataset** (Set di dati di origine).

11. Nel riquadro **Attività** espandere **Generale** e trascinare un'altra attività **Ricerca** nell'area di progettazione della pipeline. Nella scheda **Generale** della finestra delle proprietà impostare il nome su **NewWatermark**. Questa attività di ricerca recupera il nuovo valore del limite dalla tabella contenente i dati di origine da copiare nella destinazione.

12. Nella finestra delle proprietà per la seconda attività Ricerca passare alla scheda **Impostazioni** e selezionare **Nuovo** per creare un set di dati che punti alla tabella di origine che contiene il nuovo valore del limite.

13. Nella finestra **Nuovo set di dati** selezionare l'**istanza del database SQL Edge** e quindi fare clic su **Continua**.

    1. Nella finestra **Imposta proprietà** immettere **SourceDataset** in **Nome**. In **Servizio collegato** selezionare **SQLDBEdgeLinkedService**.

    2. In **Tabella** selezionare la tabella che si vuole sincronizzare. È anche possibile specificare una query per questo set di dati, come illustrato più avanti nell'esercitazione. La query avrà la precedenza rispetto alla tabella specificata in questo passaggio.

    3. Selezionare **OK**.

14. Passare all'editor di pipeline selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività Ricerca verificare che sia selezionata la voce **SourceDataset** nell'elenco **Source dataset** (Set di dati di origine).

15. In **Use query** (Usa query) selezionare **Query**. Aggiornare il nome della tabella nella query seguente e quindi immettere la query. Si sta selezionando solo il valore massimo di `timestamp` dalla tabella. Assicurarsi di selezionare **Solo prima riga**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Selezionare la query](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Nel riquadro **Attività** espandere **Move & Transform** (Sposta e trasforma) e trascinare l'attività **Copia** dal riquadro **Attività** nell'area di progettazione. Impostare il nome dell'attività su **IncrementalCopy**.

17. Connettere entrambe le attività Ricerca all'attività Copia trascinando il pulsante verde associato alle attività Ricerca sull'attività Copia. Rilasciare il pulsante del mouse quando il bordo dell'attività Copia diventa blu.

18. Selezionare l'attività Copia e verificare che le relative proprietà vengano visualizzate nella finestra **Proprietà**.

19. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** nella casella **Source Dataset** (Set di dati di origine).

    2. In **Use query** (Usa query) selezionare **Query**.

    3. Immettere la query SQL nella casella **Query**. Ecco una query di esempio:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Nella scheda **Sink** selezionare **Nuovo** in **Sink Dataset** (Set di dati sink).

21. In questa esercitazione l'archivio di dati sink è un archivio dati di archiviazione BLOB di Azure. Selezionare **Archiviazione BLOB di Azure** e quindi **Continua** nella finestra **Nuovo set di dati**.

22. Nella pagina **Select Format** (Seleziona formato) selezionare il formato dei dati e quindi **Continua**.

23. Nella finestra **Imposta proprietà** immettere **SinkDataset** in **Nome**. In **Servizio collegato** selezionare **Nuovo**. Verrà creata una connessione all'archivio BLOB di Azure, ossia un servizio collegato.

24. Nella finestra **New Linked Service (Azure Blog Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure) procedere come segue:

    1. Nella casella **Nome** immettere **AzureStorageLinkedService**.

    2. In **Nome account di archiviazione** selezionare l'account di archiviazione di Azure per la sottoscrizione di Azure in uso.

    3. Testare la connessione e quindi selezionare **Fine**.

25. Nella finestra **Imposta proprietà** verificare che sia selezionata la voce **AzureStorageLinkedService** in **Servizio collegato**. Selezionare **Crea** e fare clic su **OK**.

26. Nella scheda **Sink** selezionare **Modifica**.

27. Passare alla scheda **Connessione** di SinkDataset e seguire questa procedura:

    1. In **Percorso file** immettere *asdedatasync/incrementalcopy*, dove *asdedatasync* è il nome del contenitore BLOB e *incrementalcopy* è il nome della cartella. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. Se non esiste, la cartella di output *incrementalcopy* viene creata automaticamente da Azure Data Factory. È anche possibile usare il pulsante **Sfoglia** per **Percorso file** per passare a una cartella in un contenitore BLOB.

    2. Per la parte **File** di **Percorso file** selezionare **Aggiungi contenuto dinamico [ALT+P]** e quindi immettere **@CONCAT('Incremental-', pipeline().RunId, '.txt')** nella finestra che viene aperta. Selezionare **Fine**. Il nome file viene generato in modo dinamico dall'espressione. Ogni esecuzione della pipeline ha un ID univoco, che viene usato dall'attività di copia per generare il nome file.

28. Passare all'editor di pipeline selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra.

29. Ne riquadro **Attività** espandere **Generale** e trascinare l'attività **Stored procedure** dal riquadro **Attività** all'area di progettazione della pipeline. Connettere l'output contrassegnato in verde (come operazione riuscita) dell'attività Copia all'attività Stored procedure.

30. Selezionare l'**attività Stored procedure** nell'area di progettazione della pipeline e sostituire il nome con **SPtoUpdateWatermarkActivity**.

31. Passare alla scheda **Account SQL** e selezionare ***QLDBEdgeLinkedService** in **Servizio collegato**.

32. Passare alla scheda **Stored procedure** e seguire questa procedura:

    1. In **Nome stored procedure** selezionare **[dbo].[usp_write_watermark]** .

    2. Per specificare i valori dei parametri della stored procedure, selezionare **Import parameter** (Importa parametro) e immettere i valori seguenti per i parametri:

    |NOME|Type|Valore|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|string|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Per convalidare le impostazioni della pipeline, selezionare **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere la finestra del **report di convalida della pipeline**, selezionare **>>** .

34. Pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Azure Data Factory selezionando il pulsante **Pubblica tutti**. Attendere finché non viene visualizzato un messaggio che conferma che l'operazione di pubblicazione è stata completata.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Attivare una pipeline in base a una pianificazione

1. Sulla barra degli strumenti della pipeline selezionare **Aggiungi trigger**, quindi selezionare **New/Edit** (Nuovo/Modifica) e infine **Nuovo**.

2. Assegnare al trigger il nome **HourlySync**. In **Tipo** selezionare **Pianificazione**. Impostare la **Ricorrenza** su ogni ora.

3. Selezionare **OK**.

4. Selezionare **Pubblica tutti**.

5. Selezionare **Trigger Now** (Attiva adesso).

6. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Selezionare **Aggiorna** per aggiornare l'elenco.

## <a name="next-steps"></a>Passaggi successivi

La pipeline di Azure Data Factory descritta in questa esercitazione copia i dati da una tabella di un'istanza del database SQL Edge in una posizione nell'archivio BLOB di Azure una volta ogni ora. Per informazioni sull'uso di Data Factory in altri scenari, vedere queste [esercitazioni](../data-factory/tutorial-copy-data-portal.md).
