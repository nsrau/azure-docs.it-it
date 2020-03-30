---
title: Copia di massa da un database tramite tabella di controllo
description: Informazioni su come usare un modello di soluzione per copiare dati in blocco da un database usando una tabella di controllo esterno per archiviare un elenco di partizioni di tabelle di origine tramite Azure Data Factory.Learn how to use a solution template to copy bulk data from a database by using an external control table to store a partition list of source tables by using Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439970"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Copia di massa da un database con una tabella di controllo

Per copiare dati da un data warehouse in Oracle Server, Netezza, Teradata o SQL Server in Azure SQL Data Warehouse, è necessario caricare enormi quantità di dati da più tabelle. In genere, i dati devono essere partizionati in ogni tabella in modo che sia possibile caricare righe con più thread in parallelo da una singola tabella. In questo articolo viene descritto un modello da utilizzare in questi scenari.

 >! NOTA Se si desidera copiare i dati da un numero ridotto di tabelle con un volume di dati relativamente ridotto in SQL Data Warehouse, è più efficiente usare [lo strumento Copia dati](copy-data-tool.md)di Azure Data Factory . Il modello descritto in questo articolo è superiore a quello necessario per tale scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera un elenco di partizioni del database di origine da copiare da una tabella di controllo esterno. Quindi scorre ogni partizione nel database di origine e copia i dati nella destinazione.

Il modello contiene tre attività:
- **Lookup** recupera l'elenco delle partizioni di database sicura da una tabella di controllo esterno.
- **ForEach** ottiene l'elenco delle partizioni dall'attività Lookup e scorre ogni partizione all'attività Copy.
- **Copia** ogni partizione dall'archivio del database di origine all'archivio di destinazione.

Il modello definisce i seguenti parametri:
- *Control_Table_Name* è la tabella di controllo esterno, che archivia l'elenco delle partizioni per il database di origine.
- *Control_Table_Schema_PartitionID* è il nome della colonna nella tabella dei controlli esterni in cui è archiviato ogni ID partizione. Assicurarsi che l'ID partizione sia univoco per ogni partizione nel database di origine.
- *Control_Table_Schema_SourceTableName* è la tabella dei controlli esterni in cui viene archiviata ogni nome di tabella dal database di origine.
- *Control_Table_Schema_FilterQuery* è il nome della colonna nella tabella dei controlli esterni in cui viene archiviata la query di filtro per ottenere i dati da ogni partizione nel database di origine. Ad esempio, se i dati sono stati partizionati per anno, la query archiviata in ogni riga potrebbe essere simile a 'selezionare ' dall'origine dati in cui LastModifytime >''2015-01-010:00:00:00'' e LastModifytime <' ''2015-12-31 23:59:59.999''.
- *Data_Destination_Folder_Path* è il percorso in cui i dati vengono copiati nell'archivio di destinazione (applicabile quando la destinazione scelta è "File System" o "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* è il percorso della cartella radice in cui vengono copiati i dati nell'archivio di destinazione. 
- *Data_Destination_Directory* è il percorso della directory nella radice in cui i dati vengono copiati nell'archivio di destinazione. 

Gli ultimi tre parametri, che definiscono il percorso nell'archivio di destinazione, sono visibili solo se la destinazione scelta è l'archiviazione basata su file. Se si sceglie "Azure Synapse Analytics (in precedenza SQL DW)" come archivio di destinazione, questi parametri non sono necessari. Tuttavia, i nomi delle tabelle e lo schema in SQL Data Warehouse devono essere gli stessi del database di origine.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Creare una tabella di controllo in SQL ServerSQL Server o nel database SQL di Azure per archiviare l'elenco delle partizioni del database di origine per la copia bulk. Nell'esempio seguente sono presenti cinque partizioni nel database di origine. Tre partizioni sono per il *datasource_table*e due per la *project_table*. La colonna *LastModifytime* viene utilizzata per partizionare i dati nella tabella *datasource_table* dal database di origine. La query utilizzata per leggere la prima partizione è 'selezionare da datasource_table in cui LastModifytime >' ''2015-01-01 00:00:00'' e LastModifytime <' ''2015-12-31 23:59:59.999''. È possibile utilizzare una query simile per leggere i dati da altre partizioni.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Passare al modello **Copia globale da database.** Creare una nuova connessione alla tabella di controllo esterno creata nel passaggio 1.Create a **New** connection to the external control table that you created in step 1.

    ![Creare una nuova connessione alla tabella di controllo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Creare una **nuova** connessione al database di origine da cui si stanno copiando i dati.

    ![Creare una nuova connessione al database di origine](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Creare una **nuova** connessione all'archivio dati di destinazione in cui si stanno copiando i dati.

    ![Creare una nuova connessione all'archivio di destinazione](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selezionare **Usa questo modello**.

6. Viene visualizzata la pipeline, come illustrato nell'esempio seguente:You see the pipeline, as shown in the following example:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Fare clic su Debug](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Vengono visualizzati risultati simili all'esempio seguente:You see results that are similar to the following example:

    ![Esaminare il risultato](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facoltativo) Se si sceglie "Azure Synapse Analytics (in precedenza SQL DW)" come destinazione dati, è necessario immettere una connessione all'archiviazione BLOB di Azure per la gestione temporanea, come richiesto da POLYbase del data warehouse SQL. Il modello genererà automaticamente un percorso del contenitore per l'archiviazione BLOB. Verificare se il contenitore è stato creato dopo l'esecuzione della pipeline.
    
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)
