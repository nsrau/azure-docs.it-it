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
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509205"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Esercitazione: Sincronizzare i dati dal database SQL Edge all'archivio BLOB di Azure usando Azure Data Factory

In questa esercitazione si userà Azure Data Factory per sincronizzare in modo incrementale i dati di una tabella in un'istanza del database SQL Edge di Azure all'archivio BLOB di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Se non è già stato creato un database o una tabella nella distribuzione del database SQL Edge di Azure, usare uno dei metodi seguenti per crearne uno:

* Usare [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) o [Azure Data Studio](/sql/azure-data-studio/download/) per connettersi al database SQL Edge ed eseguire uno script SQL per creare il database e la tabella.
* Creare una tabella e un database SQL tramite [SQLCMD](/sql/tools/sqlcmd-utility/) connettendosi direttamente al modulo del database SQL Edge. Per altre informazioni, vedere [Connettersi al motore di database usando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Usare SqlPackage.exe per distribuire un file dacpac nel contenitore del database SQL Edge. Questa operazione può essere automatizzata specificando l'URI del file SQLPackage come parte della configurazione delle proprietà desiderate del modulo oppure usando direttamente lo strumento client SqlPackage.exe per distribuire un file dacpac al database SQL Edge.

    Per scaricare sqlpackage, vedere [Scaricare e installare sqlpackage](/sql/tools/sqlpackage-download/). Qui vengono forniti i comandi di esempio seguenti per SqlPackage.exe, ma per altre informazioni controllare la documentazione di sqlpackage.

    **Creare il file dacpac**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Applicare dacpac**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Creare una tabella e una procedura SQL per archiviare e aggiornare i livelli limite

La tabella dei limiti viene usata per archiviare l'ultimo timestamp fino al quale i dati sono già stati sincronizzati con l'archivio di Azure. La stored procedure Transact-SQL (T-SQL) viene usata per aggiornare la tabella dei limiti dopo ogni sincronizzazione. 

Eseguire i comandi seguenti sull'istanza del database SQL Edge:

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

## <a name="create-a-data-factory-workflow"></a>Creare un flusso di lavoro di Data Factory

In questa sezione si creerà una pipeline di Azure Data Factory per sincronizzare i dati di una tabella di un database SQL Edge di Azure con l'archivio BLOB di Azure.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Creare la data factory tramite l'interfaccia utente di Data Factory

Creare una data factory seguendo le istruzioni di questa [esercitazione](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Creare una pipeline di Data Factory

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Data Factory selezionare il riquadro **Crea pipeline**.

    ![Data Factory - Crea pipeline](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Nella pagina **Generale** della finestra **Proprietà** della pipeline immettere il nome **PeriodicSync**.

3. Aggiungere l'attività **Ricerca** per ottenere il valore limite precedente. Nella **casella degli strumenti Attività** espandere **Generale** e trascinare l'attività **Ricerca** nell'area di progettazione della pipeline. Cambiare il nome dell'attività in *OldWatermark*.

    ![ricerca limite precedente](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Passare alla scheda **Impostazioni** e selezionare **+ Nuovo** in corrispondenza di **Source Dataset** (Set di dati di origine). In questo passaggio viene creato un set di dati per rappresentare i dati nella tabella dei limiti. Questa tabella contiene il limite precedente che è stato usato nella precedente operazione di copia.

5. Nella finestra **Nuovo set di dati** selezionare **Server di Azure SQL**  e quindi **Continua**.  

6. Nella finestra **Imposta proprietà** per il set di dati immettere *WatermarkDataset* per il nome.

7. Per **Servizio collegato**, selezionare **Nuovo** e quindi seguire questa procedura:

    1. Immettere *SQLDBEdgeLinkedService* per **Nome**.

    2. Immettere i dettagli del server di database SQL Edge per **Nome server**.

    3. Immettere il **Nome del database** dall'elenco a discesa.

    4. Immettere il **Nome utente** e la **Password**.

    5. Per testare la connessione all'istanza del database SQL Edge, selezionare **Test connessione**.

    6. Selezionare **Create** (Crea).

    ![creare il servizio collegato](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Scegliere **OK**.

8. Nella scheda **Impostazioni** selezionare **Modifica**.

9. Nella scheda **Connessione** selezionare *[dbo].[watermarktable]* per **Tabella**. Se si vuole visualizzare un'anteprima dei dati nella tabella, selezionare **Anteprima dati**.

10. Passare all'editor di pipeline selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'**attività Ricerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **WatermarkDataset**.

11. Nella casella degli strumenti **Attività** espandere **Generale**, trascinare un'altra attività **Ricerca** nell'area di progettazione della pipeline e impostare il nome su **NewWatermark** nella scheda **Generale** della finestra delle proprietà. Questa attività di ricerca recupera il nuovo valore limite dalla tabella con i dati di origine da copiare nella destinazione.

12. Nella finestra delle proprietà per la seconda attività **Ricerca** passare alla scheda **Impostazioni** e selezionare **Nuovo** per creare un set di dati che punti alla tabella di origine che contiene il nuovo valore limite.

13. Nella finestra **Nuovo set di dati** selezionare l'istanza del database SQL Edge e quindi selezionare **Continua**.

    1. Nella finestra **Imposta proprietà** immettere **SourceDataset** per **Nome**. Selezionare *SQLDBEdgeLinkedService* per Servizio collegato.

    2. Selezionare ***la tabella che si vuole sincronizzare*** in Tabella. È anche possibile specificare una query per questo set di dati, come indicato più avanti nell'esercitazione. La query avrà la precedenza rispetto alla tabella specificata in questo passaggio.

    3. Selezionare **OK**.

14. Passare all'editor di pipeline selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività **Cerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **SourceDataset**.

15. Selezionare **Query** per il campo **Use Query** (Usa query) e immettere la query seguente dopo aver aggiornato il nome della tabella nella query: si sta selezionando solo il valore massimo del timestamp dalla tabella. Assicurarsi di aver selezionato anche **Solo prima riga**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Selezionare la query](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Nella casella degli strumenti **Attività** espandere **Move & Transform** (Sposta e trasforma), trascinare l'attività **Copia** dalla casella degli strumenti Attività e impostare il nome su **IncrementalCopy**.

17. Connettere entrambe le attività **Ricerca** all'attività **Copia** trascinando il **pulsante verde** associato alle attività **Ricerca** sull'attività **Copia**. Rilasciare il pulsante del mouse quando il bordo dell'attività Copia diventa blu.

18. Selezionare l'attività **Copia** e verificare che le relative proprietà vengano visualizzate nella finestra **Proprietà**.

19. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** nel campo **Source Dataset** (Set di dati di origine).

    2. Selezionare **Query** per il campo **Use Query** (Usa query).

    3. Immettere la query SQL nel campo **Query**. Query di esempio di seguito

    4. Query SQL:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Passare alla scheda **Sink** e selezionare **+ Nuovo** in corrispondenza del campo **Sink Dataset** (Set di dati sink).

21. In questa esercitazione, l'archivio di dati sink è un **archivio BLOB di Azure**. Selezionare **Archiviazione BLOB di Azure** e quindi **Continua** nella finestra **Nuovo set di dati**.

22. Nella pagina **Select Format** (Seleziona formato) selezionare il tipo di formato dei dati e quindi **Continua**.

23. Nella finestra **Imposta proprietà** immettere **SinkDataset** per Nome. Per Servizio collegato selezionare **+ Nuovo**. In questo passaggio si crea una connessione all'**archivio BLOB di Azure**, ossia un servizio collegato.

24. Nella finestra **New Linked Service (Azure Blog Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure) procedere come segue:

    1. Immettere *AzureStorageLinkedService* per Nome.

    2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione** con la sottoscrizione di Azure in uso.

    3. Testare la **connessione** e selezionare **Fine**.

25. Nella finestra **Imposta proprietà** verificare che per **Servizio collegato** sia selezionato *AzureStorageLinkedService*. Selezionare quindi **Crea** e **OK**.

26. Nella scheda **Sink** selezionare **Modifica**.

27. Passare alla scheda **Connessione** di *SinkDataset* e seguire questa procedura:

    1. Nel campo **Percorso file** immettere *asdedatasync/incrementalcopy*, dove **asdedatasync** è il nome del contenitore BLOB e **incrementalcopy** è il nome della cartella. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. Se non esiste, la cartella di output *incrementalcopy* viene creata automaticamente da Azure Data Factory. È anche possibile usare il pulsante **Sfoglia** per **Percorso file** per passare a una cartella in un contenitore BLOB.

    2. Per la parte **File** del campo **Percorso file** selezionare **Aggiungi contenuto dinamico [ALT+P]** e quindi *immettere @CONCAT('Incremental-', pipeline().RunId, '.txt')* nella finestra che viene aperta. Selezionare quindi **Fine**. Il nome file viene generato in modo dinamico usando l'espressione. Ogni esecuzione della pipeline ha un ID univoco, che viene usato dall'attività di copia per generare il nome file.

28. Passare all'editor di **pipeline** selezionando la scheda della pipeline in alto oppure il nome della pipeline nella visualizzazione albero a sinistra.

29. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Stored procedure** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. **Connettere** l'output contrassegnato in verde (come operazione riuscita) dell'attività **Copia** all'attività **Stored procedure**.

30. Selezionare l'**attività Stored procedure** nella finestra di progettazione della pipeline e cambiarne il nome in *SPtoUpdateWatermarkActivity*.

31. Passare alla scheda **Account SQL** e selezionare *SQLDBEdgeLinkedService* per **Servizio collegato**.

32. Passare alla scheda **Stored procedure** e seguire questa procedura:

    1. In **Nome stored procedure** selezionare *[dbo].[usp_write_watermark]* .

    2. Per specificare i valori dei parametri della stored procedure, selezionare Import parameter (Importa parametro) e immettere i valori seguenti per i parametri:

    |NOME|Type|Valore|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|string|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Per convalidare le impostazioni della pipeline, selezionare **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere la finestra del **report di convalida della pipeline**, selezionare **>>** .

34. Pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Azure Data Factory selezionando il pulsante **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio che informa che la pubblicazione è riuscita.

## <a name="trigger-a-pipeline-on-schedule"></a>Attivare una pipeline in base a una pianificazione

1. Sulla barra degli strumenti della pipeline selezionare **Aggiungi trigger**, quindi selezionare **New/Edit** (Nuovo/Modifica) e infine **+Nuovo**.

2. Assegnare al trigger il nome *HourlySync*, scegliere **Tipo** come pianificazione e impostare **Ricorrenza** su una volta all'ora.

3. Selezionare **OK**.

4. Selezionare **Pubblica tutti**.

5. Selezionare **Trigger Now** (Attiva adesso).

6. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Selezionare il pulsante **Aggiorna** per aggiornare l'elenco.

## <a name="next-steps"></a>Passaggi successivi

La pipeline di Azure Data Factory descritta in questa esercitazione copia i dati da una tabella nell'istanza del database SQL Edge in una posizione nell'archivio BLOB di Azure con frequenza oraria. Per informazioni sull'uso di Data Factory in altri scenari, vedere queste [esercitazioni](../data-factory/tutorial-copy-data-portal.md).
