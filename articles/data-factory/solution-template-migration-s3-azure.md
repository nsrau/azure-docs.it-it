---
title: Migrare i dati da Amazon S3 a Azure Data Lake Storage Gen2
description: Informazioni su come usare un modello di soluzione per migrare i dati da Amazon S3 usando una tabella di controllo esterna per archiviare un elenco di partizioni in AWS S3 con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/07/2019
ms.openlocfilehash: b1e7d15f1c747644c755b1e0bbe3351c626f7c28
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890811"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrare i dati da Amazon S3 a Azure Data Lake Storage Gen2

Usare i modelli per eseguire la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 a Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Se si vuole copiare un volume di dati di piccole dimensioni da AWS S3 ad Azure (ad esempio, meno di 10 TB), è più efficiente e facile utilizzare lo [strumento di Copia dati Azure Data Factory](copy-data-tool.md). Il modello descritto in questo articolo è più di quello necessario.

## <a name="about-the-solution-templates"></a>Informazioni sui modelli di soluzione

La partizione dei dati è consigliata soprattutto quando si esegue la migrazione di più di 10 TB di dati. Per partizionare i dati, usare l'impostazione "prefix" per filtrare le cartelle e i file in Amazon S3 in base al nome e quindi ogni processo di copia di ADF può copiare una partizione alla volta. È possibile eseguire più processi di copia ADF simultaneamente per una migliore velocità effettiva.

La migrazione dei dati in genere richiede la migrazione dei dati cronologici una sola volta e la sincronizzazione periodica delle modifiche da AWS S3 ad Azure. Di seguito sono riportati due modelli, in cui un modello riguarda la migrazione dei dati cronologici una sola volta e un altro modello descrive la sincronizzazione delle modifiche da AWS S3 ad Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per il modello per eseguire la migrazione dei dati cronologici da Amazon S3 a Azure Data Lake Storage Gen2

Questo modello (*nome modello: esegue la migrazione dei dati cronologici da AWS S3 a Azure Data Lake storage Gen2*) presuppone che sia stato scritto un elenco di partizioni in una tabella di controllo esterna nel database SQL di Azure. Utilizzerà quindi un'attività di *ricerca* per recuperare l'elenco delle partizioni dalla tabella di controllo esterno, eseguire l'iterazione su ogni partizione e fare in modo che ogni processo di copia ADF copi una partizione alla volta. Al termine di un processo di copia, viene utilizzata l'attività *stored procedure* per aggiornare lo stato della copia di ogni partizione nella tabella del controllo.

Il modello contiene cinque attività:
- **Lookup** recupera le partizioni che non sono state copiate in Azure Data Lake storage Gen2 da una tabella di controllo esterna. Il nome della tabella è *s3_partition_control_table* e la query per caricare i dati dalla tabella è *"SELECT PARTITIONPREFIX from s3_partition_control_table where SuccessOrFailure = 0"* .
- **Foreach** Ottiene l'elenco di partizioni dall'attività *Lookup* e scorre ogni partizione nell'attività *TriggerCopy* . È possibile impostare *batchCount* per l'esecuzione simultanea di più processi di copia di ADF. In questo modello è stato impostato 2.
- **ExecutePipeline** esegue la pipeline *CopyFolderPartitionFromS3* . Il motivo per cui si crea un'altra pipeline per eseguire la copia di ogni processo di copia una partizione è che consente di rieseguire facilmente il processo di copia non riuscito per ricaricare nuovamente la partizione specifica da AWS s3. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.
- **Copy copia** ogni partizione da AWS S3 a Azure Data Lake storage Gen2.
- **SqlServerStoredProcedure** aggiornare lo stato della copia di ogni partizione nella tabella di controllo.

Il modello contiene due parametri:
- **AWS_S3_bucketName** è il nome del bucket in AWS S3 da cui si desidera eseguire la migrazione dei dati. Se si vuole eseguire la migrazione di dati da più bucket in AWS S3, è possibile aggiungere una colonna nella tabella del controllo esterno per archiviare il nome del bucket per ogni partizione e aggiornare anche la pipeline per recuperare i dati da tale colonna di conseguenza.
- **Azure_Storage_fileSystem** è il nome del file System Azure Data Lake storage Gen2 in cui si desidera eseguire la migrazione dei dati.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Affinché il modello copi i file modificati solo da Amazon S3 a Azure Data Lake Storage Gen2

Questo modello (*nome modello: copia dati Delta da AWS S3 a Azure Data Lake storage Gen2*) USA LastModifiedTime di ogni file per copiare i file nuovi o aggiornati solo da AWS S3 ad Azure. Tenere presente che se i file o le cartelle sono già stati partizionati con le informazioni TimeSlice come parte del nome del file o della cartella in AWS s3 (ad esempio,/yyyy/mm/DD/file.csv), è possibile passare a questa [esercitazione](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) per ottenere un approccio più efficiente per il caricamento incrementale di nuovi file. Questo modello presuppone che sia stato scritto un elenco di partizioni in una tabella di controllo esterna nel database SQL di Azure. Utilizzerà quindi un'attività di *ricerca* per recuperare l'elenco delle partizioni dalla tabella di controllo esterno, eseguire l'iterazione su ogni partizione e fare in modo che ogni processo di copia ADF copi una partizione alla volta. Quando ogni processo di copia inizia a copiare i file da AWS S3, si basa sulla proprietà LastModifiedTime per identificare e copiare solo i file nuovi o aggiornati. Al termine di un processo di copia, viene utilizzata l'attività *stored procedure* per aggiornare lo stato della copia di ogni partizione nella tabella del controllo.

Il modello contiene sette attività:
- La **ricerca** recupera le partizioni da una tabella di controllo esterna. Il nome della tabella è *s3_partition_delta_control_table* e la query per caricare i dati dalla tabella è *"SELECT distinct PartitionPrefix from s3_partition_delta_control_table"* .
- **Foreach** Ottiene l'elenco di partizioni dall'attività *Lookup* e scorre ogni partizione nell'attività *TriggerDeltaCopy* . È possibile impostare *batchCount* per l'esecuzione simultanea di più processi di copia di ADF. In questo modello è stato impostato 2.
- **ExecutePipeline** esegue la pipeline *DeltaCopyFolderPartitionFromS3* . Il motivo per cui si crea un'altra pipeline per eseguire la copia di ogni processo di copia una partizione è che consente di rieseguire facilmente il processo di copia non riuscito per ricaricare nuovamente la partizione specifica da AWS s3. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.
- La **ricerca** recupera l'ultima fase di esecuzione del processo di copia dalla tabella del controllo esterno, in modo che i file nuovi o aggiornati possano essere identificati tramite LastModifiedTime. Il nome della tabella è *s3_partition_delta_control_table* e la query per caricare i dati dalla tabella è *"Select Max (JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix =' @ {pipeline (). Parameters. prefixStr}' and SuccessOrFailure = 1"* .
- **Copiare** copia i file nuovi o modificati solo per ogni partizione da AWS S3 a Azure Data Lake storage Gen2. La proprietà di *modifiedDatetimeStart* è impostata sull'ultima fase di esecuzione del processo di copia. La proprietà di *modifiedDatetimeEnd* è impostata sul tempo di esecuzione del processo di copia corrente. Tenere presente che l'ora viene applicata al fuso orario UTC.
- **SqlServerStoredProcedure** aggiorna lo stato di copia di ogni partizione e di esecuzione della copia nella tabella di controllo in caso di esito positivo. La colonna di SuccessOrFailure è impostata su 1.
- **SqlServerStoredProcedure** aggiorna lo stato di copia di ogni partizione e di esecuzione della copia nella tabella di controllo in caso di errore. La colonna di SuccessOrFailure è impostata su 0.

Il modello contiene due parametri:
- **AWS_S3_bucketName** è il nome del bucket in AWS S3 da cui si desidera eseguire la migrazione dei dati. Se si vuole eseguire la migrazione di dati da più bucket in AWS S3, è possibile aggiungere una colonna nella tabella del controllo esterno per archiviare il nome del bucket per ogni partizione e aggiornare anche la pipeline per recuperare i dati da tale colonna di conseguenza.
- **Azure_Storage_fileSystem** è il nome del file System Azure Data Lake storage Gen2 in cui si desidera eseguire la migrazione dei dati.

## <a name="how-to-use-these-two-solution-templates"></a>Come usare questi due modelli di soluzione

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per il modello per eseguire la migrazione dei dati cronologici da Amazon S3 a Azure Data Lake Storage Gen2 

1. Creare una tabella di controllo nel database SQL di Azure per archiviare l'elenco di partizioni di AWS s3. 

    > [!NOTE]
    > Il nome della tabella è s3_partition_control_table.
    > Lo schema della tabella dei controlli è PartitionPrefix e SuccessOrFailure, dove PartitionPrefix è l'impostazione del prefisso in S3 per filtrare le cartelle e i file in Amazon S3 per nome e SuccessOrFailure è lo stato della copia di ogni partizione: 0 indica che questa partizione ha non è stato copiato in Azure e 1 indica che questa partizione è stata copiata correttamente in Azure.
    > Nella tabella di controllo sono definite 5 partizioni e lo stato predefinito di copia di ogni partizione è 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Creare una stored procedure nello stesso database SQL di Azure per la tabella di controllo. 

    > [!NOTE]
    > Il nome della stored procedure è sp_update_partition_success. Verrà richiamato dall'attività SqlServerStoredProcedure nella pipeline di ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Passare a **Esegui la migrazione dei dati cronologici da AWS S3 a Azure Data Lake storage Gen2** modello. Immettere le connessioni alla tabella di controllo esterno, AWS S3 come archivio dell'origine dati e Azure Data Lake Storage Gen2 come archivio di destinazione. Tenere presente che la tabella di controllo esterno e la stored procedure fanno riferimento alla stessa connessione.

    ![Crea una nuova connessione](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Vengono visualizzate le 2 pipeline e 3 set di impostazioni sono stati creati, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Fare clic su * * debug * *](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Verranno visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Affinché il modello copi i file modificati solo da Amazon S3 a Azure Data Lake Storage Gen2

1. Creare una tabella di controllo nel database SQL di Azure per archiviare l'elenco di partizioni di AWS s3. 

    > [!NOTE]
    > Il nome della tabella è s3_partition_delta_control_table.
    > Lo schema della tabella dei controlli è PartitionPrefix, JobRunTime e SuccessOrFailure, dove PartitionPrefix è l'impostazione del prefisso in S3 per filtrare le cartelle e i file in Amazon S3 per nome, JobRunTime è il valore DateTime quando vengono eseguiti i processi di copia e SuccessOrFailure è lo stato della copia di ogni partizione: 0 indica che la partizione non è stata copiata in Azure e 1 indica che la partizione è stata copiata in Azure.
    > Sono presenti 5 partizioni definite nella tabella del controllo. Il valore predefinito di JobRunTime può essere l'ora in cui viene avviata la migrazione dei dati cronologici una volta. L'attività di copia ADF copia i file in AWS S3 che sono stati modificati per ultimi dopo tale periodo di tempo. Lo stato predefinito di copia di ogni partizione è 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Creare una stored procedure nello stesso database SQL di Azure per la tabella di controllo. 

    > [!NOTE]
    > Il nome della stored procedure è sp_insert_partition_JobRunTime_success. Verrà richiamato dall'attività SqlServerStoredProcedure nella pipeline di ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Passare a **copia dati Delta da AWS S3 a Azure Data Lake storage Gen2** modello. Immettere le connessioni alla tabella di controllo esterno, AWS S3 come archivio dell'origine dati e Azure Data Lake Storage Gen2 come archivio di destinazione. Tenere presente che la tabella di controllo esterno e la stored procedure fanno riferimento alla stessa connessione.

    ![Crea una nuova connessione](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Vengono visualizzate le 2 pipeline e 3 set di impostazioni sono stati creati, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Fare clic su * * debug * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Verranno visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. È anche possibile controllare i risultati della tabella di controllo tramite una query *"Select * from s3_partition_delta_control_table"* . l'output sarà simile all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Copia i file da più contenitori](solution-template-copy-files-multiple-containers.md)
- [Sposta file](solution-template-move-files.md)