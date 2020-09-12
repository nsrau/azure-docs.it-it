---
title: Eseguire la copia bulk da un database con una tabella di controllo
description: Informazioni su come usare un modello di soluzione per la copia bulk dei dati da un database tramite una tabella di controllo esterna per archiviare un elenco di partizioni di tabelle di origine con Azure Data Factory.
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
ms.openlocfilehash: be3b82765f2f5268a75147e8e1ef6de34aeb8ff2
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441069"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Eseguire la copia bulk da un database con una tabella di controllo

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Per copiare dati da un data warehouse in Oracle Server, Netezza, Teradata o SQL Server ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse), è necessario caricare grandi quantità di dati da più tabelle. I dati in genere devono essere partizionati in ogni tabella, in modo che sia possibile caricare righe con più thread in parallelo da una singola tabella. Questo articolo descrive un modello da usare in questi scenari.

 >! Si noti che se si vogliono copiare dati da un numero ridotto di tabelle con un volume di dati relativamente piccolo ad Azure sinapsi Analytics, è più efficiente usare lo [strumento Azure Data Factory copia dati](copy-data-tool.md). Il modello descritto in questo articolo è più avanzato di quello effettivamente necessario per questo scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera un elenco di partizioni del database di origine da copiare da una tabella di controllo esterna. Esegue quindi l'iterazione su ogni partizione nel database di origine e copia i dati nella destinazione.

Il modello contiene tre attività:
- **Ricerca** recupera l'elenco delle partizioni del database di origine da una tabella di controllo esterna.
- **ForEach** ottiene l'elenco di partizioni dall'attività di ricerca ed esegue l'iterazione di ogni partizione nell'attività di copia.
- **Copia** esegue la copia di ciascuna partizione dall'archivio del database di origine all'archivio di destinazione.

Il modello definisce i parametri seguenti:
- *Control_Table_Name* è la tabella di controllo esterna, in cui è archiviato l'elenco delle partizioni per il database di origine.
- *Control_Table_Schema_PartitionID* è il nome della colonna nella tabella di controllo esterna in cui è archiviato ogni ID partizione. Assicurarsi che l'ID partizione sia univoco per ogni partizione nel database di origine.
- *Control_Table_Schema_SourceTableName* è il nome della tabella di controllo esterna in cui è archiviato ogni nome di tabella del database di origine.
- *Control_Table_Schema_FilterQuery* è il nome della colonna nella tabella di controllo esterna in cui è archiviata la query di filtro con cui ottenere i dati da ogni partizione nel database di origine. Se ad esempio i dati sono stati partizionati per anno, la query archiviata in ogni riga potrebbe essere simile a "select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* è il percorso in cui i dati vengono copiati nell'archivio di destinazione (applicabile quando la destinazione scelta è "File system" o "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* è il percorso della cartella radice in cui i dati vengono copiati nell'archivio di destinazione. 
- *Data_Destination_Directory* è il percorso di directory sotto la radice in cui i dati vengono copiati nell'archivio di destinazione. 

Gli ultimi tre parametri, che definiscono il percorso nell'archivio di destinazione, sono visibili solo se la destinazione scelta è l'archiviazione basata su file. Se si sceglie "Azure Synapse Analytics (in precedenza SQL DW)" come archivio di destinazione, questi parametri non sono necessari. Tuttavia, i nomi delle tabelle e dello schema in Azure sinapsi Analytics devono corrispondere a quelli del database di origine.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Creare una tabella di controllo in SQL Server o database SQL di Azure per archiviare l'elenco di partizioni del database di origine per la copia bulk. Nell'esempio seguente sono presenti cinque partizioni nel database di origine. Tre partizioni sono per la tabella *datasource_table* e due per *project_table*. La colonna *LastModifytime* viene usata per partizionare i dati nella tabella *datasource_table* dal database di origine. La query usata per leggere la prima partizione è "select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00" and LastModifytime <= ''2015-12-31 23:59:59.999'''. È possibile usare una query simile per la lettura dei dati da altre partizioni.

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

2. Passare al modello **Copia bulk dal database**. Creare una **nuova** connessione alla tabella di controllo esterna creata nel passaggio 1.

    ![Creare una nuova connessione alla tabella di controllo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Creare una **nuova** connessione al database di origine da cui si stanno copiando i dati.

    ![Creare una nuova connessione al database di origine](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Creare una **nuova** connessione all'archivio dati di destinazione in cui si stanno copiando i dati.

    ![Creare una nuova connessione all'archivio di destinazione](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selezionare **Usa questo modello**.

6. Verrà visualizzata la pipeline, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selezionare **Debug**, immettere i valori in **Parametri**, quindi selezionare **Fine**.

    ![Fare clic su **Debug **](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. I risultati visualizzati sono simili all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Opzionale Se si sceglie "Azure sinapsi Analytics (in precedenza SQL DW)" come destinazione dei dati, è necessario immettere una connessione all'archivio BLOB di Azure per la gestione temporanea, come richiesto dalla polibase di analisi delle sinapsi di Azure. Il modello genererà automaticamente un percorso del contenitore per l'archiviazione BLOB. Controllare che il contenitore sia stato creato dopo l'esecuzione della pipeline.
    
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)
