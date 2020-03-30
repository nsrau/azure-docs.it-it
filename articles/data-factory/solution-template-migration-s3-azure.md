---
title: Eseguire la migrazione dei dati da Amazon S3 ad Azure Data Lake Storage Gen2Migrate data from Amazon S3 to Azure Data Lake Storage Gen2
description: Scopri come usare un modello di soluzione per eseguire la migrazione dei dati da Amazon S3 usando una tabella di controllo esterna per archiviare un elenco di partizioni in AWS S3 con Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e918fe01426202746f0225d25304b9c1b26cb74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927317"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Eseguire la migrazione dei dati da Amazon S3 ad Azure Data Lake Storage Gen2Migrate data from Amazon S3 to Azure Data Lake Storage Gen2

Usa i modelli per eseguire la migrazione di petabyte di dati composti da centinaia di milioni di file da Amazon S3 ad Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Se si desidera copiare un volume di dati di piccole dimensioni da AWS S3 in Azure (ad esempio, meno di 10 TB), è più efficiente e facile da usare lo strumento Copia dati di [Azure Data Factory.](copy-data-tool.md) Il modello descritto in questo articolo è più di quello necessario.

## <a name="about-the-solution-templates"></a>Informazioni sui modelli di soluzione

La partizione dati è consigliata soprattutto quando si esegue la migrazione di più di 10 TB di dati. Per partizionare i dati, utilizza l'impostazione 'prefisso' per filtrare le cartelle e i file su Amazon S3 in base al nome, quindi ogni processo di copia ADF può copiare una partizione alla volta. È possibile eseguire più processi di copia ADF contemporaneamente per una migliore velocità effettiva.

La migrazione dei dati richiede in genere una migrazione dei dati cronologici una tantera e la sincronizzazione periodica delle modifiche da AWS S3 ad Azure. Di seguito sono riportati due modelli, in cui un modello copre la migrazione dei dati cronologici una tantera e un altro modello riguarda la sincronizzazione delle modifiche da AWS S3 ad Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per il modello per eseguire la migrazione dei dati cronologici da Amazon S3 ad Azure Data Lake Storage Gen2

Questo modello ( nome modello: eseguire la migrazione dei*dati cronologici da AWS S3 ad Azure Data Lake Storage Gen2*) presuppone che sia stato scritto un elenco di partizioni in una tabella di controllo esterno nel database SQL di Azure.This template ( template name: migrate historical data from AWS S3 to Azure Data Lake Storage Gen2 ) assume that you have written a partition list in an external control table in Azure SQL Database. Pertanto verrà utilizzata un'attività *di ricerca* per recuperare l'elenco delle partizioni dalla tabella dei controlli esterni, scorrere ogni partizione e fare in modo che ogni processo di copia ADF copi una partizione alla volta. Una volta completato qualsiasi processo di copia, viene utilizzata l'attività *Stored procedure* per aggiornare lo stato della copia di ogni partizione nella tabella di controllo.

Il modello contiene cinque attività:
- **La ricerca** recupera le partizioni che non sono state copiate in Azure Data Lake Storage Gen2 da una tabella di controllo esterna. Il nome della tabella è *s3_partition_control_table* e la query per caricare i dati dalla tabella è *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure - 0"*.
- **ForEach** ottiene l'elenco delle partizioni dall'attività *Lookup* e scorre ogni partizione all'attività *TriggerCopy.* È possibile impostare *batchCount* per eseguire più processi di copia ADF contemporaneamente. Abbiamo impostato 2 in questo modello.
- **ExecutePipeline** esegue *CopyFolderPartitionFromS3* pipeline. Il motivo per cui creiamo un'altra pipeline per fare in modo che ogni processo di copia copi una partizione sia perché ti renderà facile rieseguire il processo di copia non riuscita per ricaricare nuovamente quella partizione specifica da AWS S3. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.
- **Copia** ogni partizione da AWS S3 ad Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** aggiornare lo stato della copia di ogni partizione nella tabella di controllo.

Il modello contiene due parametri:
- **AWS_S3_bucketName** è il nome del bucket in AWS S3 da cui vuoi eseguire la migrazione dei dati. Se vuoi eseguire la migrazione dei dati da più bucket in AWS S3, puoi aggiungere un'altra colonna nella tabella di controllo esterna per archiviare il nome del bucket per ogni partizione e anche aggiornare la pipeline per recuperare di conseguenza i dati da tale colonna.
- **Azure_Storage_fileSystem** è il nome del fileSystem in Azure Data Lake Storage Gen2 in cui si vuole eseguire la migrazione dei dati.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per copiare i file modificati solo da Amazon S3 ad Azure Data Lake Storage Gen2

Questo modello ( nome modello: copia i*dati delta da AWS S3 ad Azure Data Lake Storage Gen2*) usa LastModifiedTime di ogni file per copiare i file nuovi o aggiornati solo da AWS S3 ad Azure. Tieni presente che se i file o le cartelle sono già stati partizionati nel tempo con informazioni sull'intervallo di tempo come parte del nome del file o della cartella in AWS S3 (ad esempio, /aaaa/mm/gg/file.csv), puoi accedere a questa [esercitazione](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) per ottenere l'approccio più performante per il caricamento incrementale di nuovi file. Questo modello presuppone che sia stato scritto un elenco di partizioni in una tabella di controllo esterno nel database SQL di Azure.This template assumes that you have written a partition list in an external control table in Azure SQL Database. Pertanto verrà utilizzata un'attività *di ricerca* per recuperare l'elenco delle partizioni dalla tabella dei controlli esterni, scorrere ogni partizione e fare in modo che ogni processo di copia ADF copi una partizione alla volta. Quando ogni processo di copia inizia a copiare i file da AWS S3, si basa sulla proprietà LastModifiedTime per identificare e copiare solo i file nuovi o aggiornati. Una volta completato qualsiasi processo di copia, viene utilizzata l'attività *Stored procedure* per aggiornare lo stato della copia di ogni partizione nella tabella di controllo.

Il modello contiene sette attività:
- **La ricerca** recupera le partizioni da una tabella di controllo esterna. Il nome della tabella è *s3_partition_delta_control_table* e la query per caricare i dati dalla tabella è *"select distinct PartitionPrefix from s3_partition_delta_control_table"*.
- **ForEach** ottiene l'elenco delle partizioni dall'attività *Lookup* e scorre ogni partizione all'attività *TriggerDeltaCopy.* È possibile impostare *batchCount* per eseguire più processi di copia ADF contemporaneamente. Abbiamo impostato 2 in questo modello.
- **ExecutePipeline** esegue *DeltaCopyFolderPartitionFromS3* pipeline. Il motivo per cui creiamo un'altra pipeline per fare in modo che ogni processo di copia copi una partizione sia perché ti renderà facile rieseguire il processo di copia non riuscita per ricaricare nuovamente quella partizione specifica da AWS S3. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.
- **Lookup** recupera l'ora di esecuzione dell'ultimo processo di copia dalla tabella dei controlli esterni in modo che i file nuovi o aggiornati possano essere identificati tramite LastModifiedTime. Il nome della tabella è *s3_partition_delta_control_table* e la query per caricare i dati dalla tabella è *"select max(JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix ''pipeline().parameters.prefixStr'' and SuccessOrFailure s 1"*(
- **Copia** i file nuovi o modificati solo per ogni partizione da AWS S3 ad Azure Data Lake Storage Gen2. La proprietà di *modifiedDatetimeStart* è impostata sull'ora dell'ultima copia del processo di esecuzione. La proprietà di *modifiedDatetimeEnd* è impostata sul runtime del processo di copia corrente. Tenere presente che l'ora viene applicata al fuso orario UTC.
- **SqlServerStoredProcedure** aggiornare lo stato della copia di ogni partizione e copy run time nella tabella di controllo quando ha esito positivo. La colonna successOrFailure è impostata su 1.The column of SuccessOrFailure is set to 1.
- **SqlServerStoredProcedure** aggiornare lo stato della copia di ogni partizione e copy run time nella tabella di controllo quando non riesce. La colonna successOrFailure è impostata su 0.The column of SuccessOrFailure is set to 0.

Il modello contiene due parametri:
- **AWS_S3_bucketName** è il nome del bucket in AWS S3 da cui vuoi eseguire la migrazione dei dati. Se vuoi eseguire la migrazione dei dati da più bucket in AWS S3, puoi aggiungere un'altra colonna nella tabella di controllo esterna per archiviare il nome del bucket per ogni partizione e anche aggiornare la pipeline per recuperare di conseguenza i dati da tale colonna.
- **Azure_Storage_fileSystem** è il nome del fileSystem in Azure Data Lake Storage Gen2 in cui si vuole eseguire la migrazione dei dati.

## <a name="how-to-use-these-two-solution-templates"></a>Come usare questi due modelli di soluzioneHow to use these two solution templates

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per il modello per eseguire la migrazione dei dati cronologici da Amazon S3 ad Azure Data Lake Storage Gen2 

1. Creare una tabella di controllo nel database SQL di Azure per archiviare l'elenco delle partizioni di AWS S3. 

    > [!NOTE]
    > Il nome della tabella è s3_partition_control_table.
    > Lo schema della tabella di controllo è PartitionPrefix e SuccessOrFailure, dove PartitionPrefix è l'impostazione del prefisso in S3 per filtrare le cartelle e i file in Amazon S3 in base al nome e SuccessOrFailure è lo stato della copia di ogni partizione: 0 indica che questa partizione ha non è stato copiato in Azure e 1 indica che la partizione è stata copiata correttamente in Azure.Not been copied to Azure and 1 means this partition has been copied to Azure successfully.
    > Nella tabella di controllo sono definite 5 partizioni e lo stato predefinito della copia di ogni partizione è 0.There are 5 partitions defined in control table and the default status of copying each partition is 0.

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
    > Viene sp_update_partition_success il nome della stored procedure. Verrà richiamato dall'attività SqlServerStoredProcedure nella pipeline ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Vai al modello **Migrare i dati cronologici da AWS S3 ad Azure Data Lake Storage Gen2.** Immettere le connessioni alla tabella di controllo esterna, AWS S3 come archivio origine dati e Azure Data Lake Storage Gen2 come archivio di destinazione. Tenere presente che la tabella di controllo esterno e la stored procedure fanno riferimento alla stessa connessione.

    ![Crea una nuova connessione](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Vengono create le due pipeline e 3 set di dati, come illustrato nell'esempio seguente:You see the 2 pipelines and 3 datasets were created, as shown in the following example:

    ![Esaminare la pipeline](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Fare clic su Debug](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Vengono visualizzati risultati simili all'esempio seguente:You see results that are similar to the following example:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Per copiare i file modificati solo da Amazon S3 ad Azure Data Lake Storage Gen2

1. Creare una tabella di controllo nel database SQL di Azure per archiviare l'elenco delle partizioni di AWS S3. 

    > [!NOTE]
    > Il nome della tabella è s3_partition_delta_control_table.
    > Lo schema della tabella di controllo è PartitionPrefix, JobRunTime e SuccessOrFailure, dove PartitionPrefix è l'impostazione del prefisso in S3 per filtrare le cartelle e i file in Amazon S3 in base al nome, JobRunTime è il valore datetime quando vengono eseguiti i processi di copia e SuccessOrFailure è lo stato della copia di ogni partizione: 0 indica che la partizione non è stata copiata in Azure e 1 indica che la partizione è stata copiata correttamente in Azure.The status of copying each partition: 0 means this partition has not been copied to Azure and 1 means this partition has been copied to Azure successfully.
    > Nella tabella di controllo sono definite 5 partizioni. Il valore predefinito per JobRunTime può essere il momento in cui viene avviata la migrazione dei dati cronologici una tantera. L'attività di copia ADF copierà i file su AWS S3 che sono stati modificati l'ultima volta dopo tale orario. Lo stato predefinito della copia di ogni partizione è 1.The default status of copying each partition is 1.

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
    > Viene sp_insert_partition_JobRunTime_success il nome della stored procedure. Verrà richiamato dall'attività SqlServerStoredProcedure nella pipeline ADF.

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


3. Passare al modello **Copia dati delta da AWS S3 ad Azure Data Lake Storage Gen2.** Immettere le connessioni alla tabella di controllo esterna, AWS S3 come archivio origine dati e Azure Data Lake Storage Gen2 come archivio di destinazione. Tenere presente che la tabella di controllo esterno e la stored procedure fanno riferimento alla stessa connessione.

    ![Crea una nuova connessione](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Vengono create le due pipeline e 3 set di dati, come illustrato nell'esempio seguente:You see the 2 pipelines and 3 datasets were created, as shown in the following example:

    ![Esaminare la pipeline](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Fare clic su Debug](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Vengono visualizzati risultati simili all'esempio seguente:You see results that are similar to the following example:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. È inoltre possibile controllare i risultati dalla tabella di controllo da una query *"selezionare da s3_partition_delta_control_table"*, verrà visualizzato l'output simile all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Copiare file da più contenitori](solution-template-copy-files-multiple-containers.md)
- [Spostare file](solution-template-move-files.md)