---
title: "Eseguire la copia incrementale di più tabelle con Azure Data Factory | Microsoft Docs"
description: "In questa esercitazione si creerà una pipeline di Azure Data Factory che copia dati differenziali in modo incrementale da più tabelle di un database di SQL Server locale a un database SQL di Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/20/2018
ms.author: jingwang
ms.openlocfilehash: c79bce401b0f1d67d7955f4c97a5dfac5008be0d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Caricare dati in modo incrementale da più tabelle in SQL Server a un database SQL di Azure
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati delta da più tabelle di un database di SQL Server locale a un database SQL di Azure.    

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare gli archivi dati di origine e di destinazione.
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Installare il runtime di integrazione. 
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare, eseguire e monitorare una pipeline.
> * Esaminare i risultati.
> * Aggiungere o aggiornare dati nelle tabelle di origine.
> * Rieseguire e monitorare la pipeline.
> * Esaminare i risultati finali.

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione di Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Panoramica
Di seguito sono descritti i passaggi fondamentali per la creazione di questa soluzione: 

1. **Selezionare la colonna del limite**.
    
    Selezionare una colonna per ogni tabella nell'archivio dati di origine, che può essere usata per identificare i record nuovi o aggiornati per ogni esecuzione. I dati della colonna selezionata (ad esempio last_modify_time o ID) continuano in genere ad aumentare quando le righe vengono create o aggiornate. Il valore massimo di questa colonna viene usato come limite.

2. **Preparare un archivio dati per l'archiviazione del valore limite**.   
    
    In questa esercitazione si archivia il valore limite in un database SQL.

3. **Creare una pipeline con le attività seguenti**: 
    
    a. Creare un'attività ForEach che esegue l'iterazione di un elenco di nomi di tabella di origine passato come parametro alla pipeline. Per ogni tabella di origine, l'attività richiama le attività seguenti per eseguire il caricamento differenziale per la tabella.

    b. Creare due attività di ricerca. Usare la prima attività di ricerca per recuperare l'ultimo valore limite. Usare la seconda attività di ricerca per recuperare il nuovo valore limite. Questi valori limite vengono passati all'attività di copia.

    c. Creare un'attività di copia che copi le righe dell'archivio dati di origine con il valore della colonna del limite maggiore del valore limite precedente e minore del nuovo valore limite. L'attività copierà quindi i dati delta dall'archivio dati di origine a un archivio BLOB di Azure come nuovo file.

    d. Creare un'attività stored procedure che aggiorni il valore limite per la pipeline che verrà eseguita la volta successiva. 

    Il diagramma generale della soluzione è il seguente: 

    ![Caricare i dati in modo incrementale](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **SQL Server**. Usare un database di SQL Server locale come archivio dati di origine in questa esercitazione. 
* **Database SQL di Azure**. Usare un database SQL come archivio dati sink. Se non è disponibile un database SQL, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md) per crearne uno. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Creare le tabelle di origine nel database di SQL Server

1. Aprire SQL Server Management Studio e connettersi al database SQL Server locale.

2. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

3. Eseguire il comando SQL seguente sul database per creare le tabelle denominate `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Creare le tabelle di destinazione nel database SQL di Azure
1. Aprire SQL Server Management Studio e connettersi al database SQL di Azure.

2. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

3. Eseguire questo comando SQL sul database SQL per creare le tabelle denominate `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-sql-database-to-store-the-high-watermark-value"></a>Creare un'altra tabella nel database SQL per archiviare il valore del limite massimo
1. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `watermarktable` in cui archiviare il valore limite: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Inserire i valori del limite iniziale per entrambe le tabelle di origine nella tabella dei limiti.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-sql-database"></a>Creare una stored procedure nel database SQL 

Eseguire questo comando per creare una stored procedure nel database SQL. Questa stored procedure aggiorna il valore del limite dopo ogni esecuzione di pipeline. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Creare tipi di dati e stored procedure aggiuntive
Eseguire questa query per creare due stored procedure e due tipi di dati nel database SQL. Questi elementi vengono usati per unire i dati delle tabelle di origine nelle tabelle di destinazione.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFMultiIncCopyTutorialDF** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFMultiIncCopyTutorialDF, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
        Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.      
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.
11. Nella pagina iniziale dell'interfaccia utente di Azure Data Factory fare clic su **Create pipeline** (Crea pipeline) (o) passare alla scheda **Modifica**. 

   ![Pagina delle attività iniziali](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted
Mentre si spostano i dati da un archivio dati di una rete privata (locale) a un archivio dati di Azure, installare un runtime di integrazione self-hosted nell'ambiente locale. Il runtime di integrazione self-hosted sposta i dati tra la rete privata e Azure. 

1. Fare clic su **Connessioni** nella parte inferiore del riquadro sinistro e passare a **Integration Runtimes** (Runtime di integrazione) nella finestra **Connessioni**. 

   ![Scheda Connessioni](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. Nella scheda **Integration Runtimes** (Runtime di integrazione) fare clic su **+ Nuovo**. 

   ![Pulsante per nuovo runtime di integrazione](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. Nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare **Perform data movement and dispatch activities to external computes** (Eseguire attività di invio e spostamento dati in servizi di calcolo esterni) e fare clic su **Avanti**. 

   ![Selezionare il tipo di runtime di integrazione](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. Selezionare ** Private Network** (Rete privata) e fare clic su **Avanti**. 

   ![Selezionare una rete privata](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. Immettere **MySelfHostedIR** per **Nome**, e fare clic su **Avanti**. 

   ![Nome del runtime di integrazione self-hosted](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. Fare clic su **Click here to launch the express setup for this computer** (Fare clic qui per avviare l'installazione rapida per il computer) nella sezione **Option 1: Express setup** (Opzione 1: Installazione rapida). 

   ![Fare clic sul collegamento per l'installazione rapida](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. Nella finestra **Installazione rapida di Integration Runtime (self-hosted)** fare clic su **Chiudi**. 

   ![Installazione del runtime di integrazione completata](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. Nel Web browser fare clic su **Fine** nella finestra **Integration Runtime Setup** (Installazione runtime di integrazione). 

   ![Installazione del runtime di integrazione: fine](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. Verificare che **MySelfHostedIR** sia visualizzato nell'elenco di runtime di integrazione.

       ![Elenco dei runtime di integrazione](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione vengono creati i servizi collegati al database di SQL Server locale e al database SQL. 

### <a name="create-the-sql-server-linked-service"></a>Creare il servizio collegato di SQL Server
In questo passaggio si collega il database di SQL Server locale alla data factory.

1. Nella finestra **Connessioni** passare dalla scheda **Integration Runtimes** (Runtime di integrazione) alla scheda **Servizi collegati** e fare clic su **+ Nuovo**.

    ![Pulsante per il nuovo per il servizio collegato](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **SQL Server** e fare clic su **Continua**. 

    ![Selezionare SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **SqlServerLinkedService** per **Nome**. 
    2. Selezionare **MySelfHostedIR** per **Connect via integration runtime** (Connetti tramite runtime di integrazione). Questo è un passaggio **importante**. Il runtime di integrazione predefinita non può connettersi a un archivio dati locale. Usare il runtime di integrazione self-hosted creato in precedenza. 
    3. Per **Nome server** immettere il nome del computer che ospita il database di SQL Server.
    4. Per **Nome database** immettere il nome del database nell'istanza di SQL Server che ospita l'origine dati. Come parte dei prerequisiti è stata creata una tabella e sono stati inseriti i dati in questo database. 
    5. Per **Tipo di autenticazione** selezionare il **tipo dell'autenticazione** che si vuole usare per connettersi al database. 
    6. Per **Nome utente** immettere il nome dell'utente che ha accesso al database di SQL Server. Se è necessario usare una barra (`\`) nell'account utente o nel nome del server, usare il carattere di escape (`\`). Un esempio è `mydomain\\myuser`.
    7. Per **Password** immettere la **password** dell'utente. 
    8. Per testare se Data Factory può connettersi al database di SQL Server, fare clic su **Connessione di test**. Correggere eventuali errori fino a quando la connessione ha esito positivo. 
    9. Fare clic su **Salva** per salvare il servizio collegato.

        ![Servizio collegato di SQL Server: impostazioni](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Creare il servizio collegato Database SQL di Azure
Nell'ultimo passaggio viene creato un servizio collegato per collegare il database di SQL Server di origine alla data factory. In questo passaggio viene collegato il database SQL di Azure di destinazione/sink alla data factory. 

1. Nella finestra **Connessioni** passare dalla scheda **Integration Runtimes** (Runtime di integrazione) alla scheda **Servizi collegati** e fare clic su **+ Nuovo**.

    ![Pulsante per il nuovo per il servizio collegato](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Database SQL di Azure** e fare clic su **Continua**. 
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **AzureSqlDatabaseLinkedService** per **Nome**. 
    3. Per **Nome server** selezionare il nome del server di Azure SQL dall'elenco a discesa. 
    4. Per **Nome database** selezionare il database SQL di Azure in cui sono stati creati customer_table e project_table come parte dei prerequisiti. 
    6. Per **Nome utente** immettere il nome dell'utente che ha accesso al database SQL di Azure. 
    7. Per **Password** immettere la **password** dell'utente. 
    8. Per testare se Data Factory può connettersi al database di SQL Server, fare clic su **Connessione di test**. Correggere eventuali errori fino a quando la connessione ha esito positivo. 
    9. Fare clic su **Salva** per salvare il servizio collegato.

        ![Impostazioni del servizio collegato SQL di Azure](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. Verificare che nell'elenco siano visualizzati due servizi collegati. 
   
    ![Due servizi collegati](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati per rappresentare l'origine dati, la destinazione dati e la posizione di archiviazione del valore limite.

### <a name="create-a-source-dataset"></a>Creare set di dati di origine

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Set di dati**.

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Nella finestra **Nuovo set di dati** selezionare **SQL Server** e fare clic su **Fine**. 

   ![Selezionare SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. Nel Web browser viene aperta una nuova scheda per la configurazione del set di dati. Un set di dati è riportato anche nella visualizzazione struttura ad albero. Nella scheda **Generale** della finestra Proprietà in basso immettere **SourceDataset** per **Nome**. 

   ![Set di dati di origine: nome](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. Passare alla scheda **Connessione** nella finestra Proprietà e selezionare **SqlServerLinkedService** per **Servizio collegato**. Qui non si seleziona una tabella. L'attività di copia nella pipeline usa una query SQL per caricare i dati invece di caricare l'intera tabella.

   ![Connessione al set di dati di origine](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Creare un set di dati sink
1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Set di dati**.

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. 

   ![Selezionare il database SQL di Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Nel Web browser viene aperta una nuova scheda per la configurazione del set di dati. Un set di dati è riportato anche nella visualizzazione struttura ad albero. Nella scheda **Generale** della finestra Proprietà in basso immettere **SinkDataset** per **Nome**.

   ![Set di dati sink: Generale](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. Passare alla scheda **Connessione** nella finestra Proprietà e selezionare **AzureSqlLinkedService** per **Servizio collegato**. 

   ![Set di dati sink: Connessione](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
5. Passare alla scheda **Parametri** nella finestra Proprietà e seguire questa procedura: 

    1. Fare clic su **Nuovo** nella sezione **Create/update parameters** (Crea/Aggiorna parametri). 
    2. Immettere **SinkTableName** per il **nome** e **Stringa** per il **tipo**. Questo set di dati accetta **SinkTableName** come parametro. Il parametro SinkTableName viene impostato dalla pipeline in modo dinamico in fase di esecuzione. L'attività ForEach nella pipeline esegue l'iterazione di un elenco di nomi di tabella e passa il nome di tabella a questo set di dati in ogni interazione.
    3. Immettere `@{dataset().SinkTableName}` per la proprietà **tableName** nella sezione **Parameterized properties** (Proprietà con parametri). Si usa il valore passato al parametro **SinkTableName** per inizializzare la proprietà **tableName** del set di dati. 

       ![Proprietà del set di dati sink](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)

### <a name="create-a-dataset-for-a-watermark"></a>Creare un set di dati per un limite
In questo passaggio si crea un set di dati per l'archiviazione di un valore limite massimo. 

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Set di dati**.

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. 

   ![Selezionare il database SQL di Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. Nella scheda **Generale** della finestra Proprietà in basso immettere **WatermarkDataset** per **Nome**.
4. Passare alla scheda **Connessione** e seguire questa procedura: 

    1. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.
    2. Selezionare **[dbo].[watermarktable]** per **Tabella**.

       ![Set di dati limite: connessione](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Creare una pipeline
Questa pipeline accetta un elenco di nomi di tabella come parametro. L'attività ForEach esegue l'iterazione dell'elenco dei nomi di tabella ed esegue le operazioni seguenti: 

1. Usa l'attività di ricerca per recuperare il valore del limite precedente (valore iniziale o usato nell'ultima iterazione).

2. Usa l'attività di ricerca per recuperare il nuovo valore del limite (valore massimo della colonna dei limiti nella tabella di origine).

3. Usa l'attività di copia per copiare dati tra i due valori del limite dal database di origine al database di destinazione.

4. Usa l'attività stored procedure per aggiornare il valore del limite precedente da usare nel primo passaggio dell'iterazione successiva. 

### <a name="create-the-pipeline"></a>Creare la pipeline

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Pipeline**.

    ![Menu per nuova pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. Nella scheda **Generale** della finestra **Proprietà** immettere **IncrementalCopyPipeline** per **Nome**. 

    ![Nome della pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. Nella finestra **Proprietà** seguire questa procedura: 

    1. Fare clic su **+ Nuovo**. 
    2. Immettere **tableList** per il **nome** del parametro. 
    3. Selezionare **Oggetto** per il **tipo** di parametro.

    ![Parametri della pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. Trascinare l'attività **ForEach** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. Nella scheda **Generale** della finestra **Proprietà** immettere **IterateSQLTables**. 

    ![Nome dell'attività ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. Passare alla scheda **Impostazioni** nella finestra **Proprietà** e immettere `@pipeline().parameters.tableList` per **Elementi**. L'attività ForEach esegue l'iterazione di un elenco di tabelle ed esegue l'operazione di copia incrementale. 

    ![Impostazioni dell'attività ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. Selezionare l'attività **ForEach** nella pipeline se non è già selezionata. Fare clic sul pulsante **Modifica (icona a forma di matita)**.

    ![Modifica dell'attività ForEach](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. Trascinare l'attività **Ricerca** dalla casella degli strumenti **Attività** e immettere **LookupOldWaterMarkActivity** per **Nome**.

    ![Prima attività di ricerca: nome](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. Passare alla scheda **Impostazioni** della finestra **Proprietà** e seguire questa procedura: 

    1. Selezionare **WatermarkDataset** per **Source Dataset** (Set di dati di origine).
    2. Selezionare **Query** per **Use Query** (Usa query). 
    3. Immettere la query SQL seguente per **Query**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Prima attività di ricerca: impostazioni](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. Trascinare l'attività **Ricerca** dalla casella degli strumenti **Attività** e immettere **LookupNewWaterMarkActivity** per **Nome**.
        
    ![Seconda attività di ricerca: nome](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. Passare alla scheda **Impostazioni** .

    1. Selezionare **SourceDataset** per **Source Dataset** (Set di dati di origine). 
    2. Selezionare **Query** per **Use Query** (Usa query).
    3. Immettere la query SQL seguente per **Query**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Seconda attività di ricerca: impostazioni](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. Trascinare l'attività **Copia** dalla casella degli strumenti **Attività** e immettere **IncrementalCopyActivity** per **Nome**. 

    ![Attività di copia: nome](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. Una alla volta, connettere le attività **Ricerca** all'attività **Copia**. Per connetterle, iniziare il trascinamento dalla casella **verde** dell'attività **Ricerca** e rilasciarla sull'attività **Copia**. Rilasciare il pulsante del mouse quando il bordo dell'attività di copia diventa di colore **blu**.

    ![Connettere le attività di ricerca all'attività di copia](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. Selezionare l'attività **Copia** nella pipeline. Passare alla scheda **Origine** nella finestra **Proprietà**. 

    1. Selezionare **SourceDataset** per **Source Dataset** (Set di dati di origine). 
    2. Selezionare **Query** per **Use Query** (Usa query). 
    3. Immettere la query SQL seguente per **Query**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Attività di copia: impostazioni origine](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. Passare alla scheda **Sink** e selezionare **SinkDataset** in **Sink Dataset** (Set di dati sink). 
        
    ![Attività di copia: impostazioni sink](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. Passare alla scheda **Parametri** e seguire questa procedura:

    1. Per la proprietà **Sink Stored Procedure Name** (Nome stored procedure sink) immettere `@{item().StoredProcedureNameForMergeOperation}`.
    2. Per la proprietà **Sink Table Type** (Tipo di tabella sink) immettere `@{item().TableType}`.
    3. Nella sezione **Sink Dataset** (Set di dati sink), per il parametro **SinkTableName**, immettere `@{item().TABLE_NAME}`.

        ![Attività di copia: parametri](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. Trascinare l'attività **Stored procedure** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. Connettere l'attività **Copia** all'attività **Stored procedure**. 

    ![Attività di copia: parametri](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. Selezionare l'attività **Stored procedure** nella pipeline e immettere **StoredProceduretoWriteWatermarkActivity** per **Nome** nella scheda **Generale** della finestra **Proprietà**. 

    ![Attività stored procedure: nome](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. Passare alla scheda **Account SQL** e selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.

    ![Attività stored procedure: account SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. Passare alla scheda **Stored procedure** e seguire questa procedura:

    1. Immettere `sp_write_watermark` per **Nome stored procedure**. 
    2. Usare il pulsante **Nuovo** per aggiungere i parametri seguenti: 

        | NOME | type | Valore | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | string | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Attività stored procedure: impostazioni della stored procedure](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. Nel riquadro a sinistra fare clic su **Pubblica**. Questa azione pubblica le entità create nel servizio Data Factory. 

    ![Pulsante Publish](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare le notifiche, fare clic sul collegamento **Show Notifications** (Mostra notifiche). Per chiudere la finestra delle notifiche, fare clic su **X**.

    ![Visualizzare le notifiche](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Eseguire la pipeline

1. Sulla barra degli strumenti per la pipeline fare clic su **Trigger** e quindi su **Trigger Now** (Attiva adesso).     

    ![Trigger now (Attiva adesso)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) immettere il valore seguente per il parametro **tableList** e fare clic su **Fine**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![Argomenti di esecuzione della pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline attivata dal **trigger manuale**. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco. I collegamenti nella colonna **Azioni** consentono di visualizzare le esecuzioni attività associate all'esecuzione della pipeline e di eseguire di nuovo la pipeline. 

    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Vengono visualizzate tutte le esecuzioni attività associate all'esecuzione della pipeline. 

    ![Esecuzioni attività](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Esaminare i risultati
In SQL Server Management Studio eseguire queste query sul database SQL di destinazione per verificare che i dati siano stati copiati dalle tabelle di origine alle tabelle di destinazione: 

**Query** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Notare che i valori del limite per entrambe le tabelle sono stati aggiornati. 

## <a name="add-more-data-to-the-source-tables"></a>Aggiungere altri dati alle tabelle di origine

Eseguire questa query sul database di SQL Server di origine per aggiornare una riga esistente in customer_table. Inserire una nuova riga in project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Eseguire di nuovo la pipeline
1. Nella finestra del Web browser passare alla scheda **Modifica** a sinistra. 
2. Sulla barra degli strumenti per la pipeline fare clic su **Trigger** e quindi su **Trigger Now** (Attiva adesso).   

    ![Trigger now (Attiva adesso)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Nella finestra **Pipeline Run** (Esecuzione di pipeline) immettere il valore seguente per il parametro **tableList** e fare clic su **Fine**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline attivata dal **trigger manuale**. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco. I collegamenti nella colonna **Azioni** consentono di visualizzare le esecuzioni attività associate all'esecuzione della pipeline e di eseguire di nuovo la pipeline. 

    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Vengono visualizzate tutte le esecuzioni attività associate all'esecuzione della pipeline. 

    ![Esecuzioni attività](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Esaminare i risultati finali
In SQL Server Management Studio eseguire le query seguenti sul database di destinazione per verificare che i dati nuovi/aggiornati siano stati copiati dalle tabelle di origine alle tabelle di destinazione. 

**Query** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Notare i nuovi valori di **Name** e **LastModifytime** per **PersonID** per il numero 3. 

**Query**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Si noti che è stata aggiunta la voce **NewProject** a project_table. 

**Query**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Notare che i valori del limite per entrambe le tabelle sono stati aggiornati.
     
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Preparare gli archivi dati di origine e di destinazione.
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Installare il runtime di integrazione.
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare, eseguire e monitorare una pipeline.
> * Esaminare i risultati.
> * Aggiungere o aggiornare dati nelle tabelle di origine.
> * Rieseguire e monitorare la pipeline.
> * Esaminare i risultati finali.

Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Caricare dati in modo incrementale dal database SQL di Azure all'archivio BLOB di Azure tramite la tecnologia Rilevamento modifiche](tutorial-incremental-copy-change-tracking-feature-portal.md)


