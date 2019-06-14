---
title: Copia bulk da un database usando una tabella di controllo con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per copiare i dati per operazioni bulk da un database tramite una tabella di controllo del codice esterno per archiviare un elenco di partizione delle tabelle di origine usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635389"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Copia bulk da un database con una tabella di controllo

Per copiare dati da un data warehouse in Server Oracle, Netezza, Teradata o SQL Server in Azure SQL Data Warehouse, è necessario caricare grandi quantità di dati da più tabelle. In genere, i dati deve essere partizionati in ogni tabella in modo che è possibile caricare le righe con più thread in parallelo da una singola tabella. Questo articolo descrive un modello da usare in questi scenari.

 >! Nota Se vuoi copiare dati da un numero limitato di tabelle con volume di dati relativamente piccolo a SQL Data Warehouse, è più efficiente usare la [dello strumento Copia dati di Azure Data Factory](copy-data-tool.md). Il modello descritto in questo articolo è più bisogno per tale scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello consente di recuperare un elenco di partizioni di database di origine da copiare da una tabella di controllo del codice esterno. Quindi esegue l'iterazione in ogni partizione nel database di origine e copia i dati nella destinazione.

Il modello contiene tre attività:
- **Ricerca** recupera l'elenco delle partizioni di database che da una tabella di controllo del codice esterno.
- **ForEach** Ottiene l'elenco di partizioni dall'attività Lookup ed esegue l'iterazione di ogni partizione per l'attività di copia.
- **Copia** copia ciascuna partizione dall'archivio di database di origine nell'archivio di destinazione.

Il modello definisce cinque parametri:
- *Control_Table_Name* è la tabella di controllo del codice esterno, che memorizza l'elenco di partizione per il database di origine.
- *Control_Table_Schema_PartitionID* è il nome del nome della colonna della tabella di controllo del codice esterno che archivia ogni ID di partizione. Assicurarsi che l'ID di partizione è univoca per ogni partizione nel database di origine.
- *Control_Table_Schema_SourceTableName* è la tabella di controllo del codice esterno che archivia ogni nome di tabella dal database di origine.
- *Control_Table_Schema_FilterQuery* è il nome della colonna della tabella di controllo del codice esterno che contiene la query di filtro per ottenere i dati da ogni partizione nel database di origine. Ad esempio, se il partizionamento dei dati per anno, la query che viene archiviata in ogni riga potrebbe essere simile a ' selezionare * dall'origine dati in cui LastModifytime > = ' 2015-01-01 00:00:00 "e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* è il percorso in cui i dati vengono copiati nell'archivio di destinazione. Questo parametro è visibile solo se la destinazione scelta è l'archiviazione basata su file. Se si sceglie SQL Data Warehouse come archivio di destinazione, questo parametro non è obbligatorio. Ma i nomi di tabella e lo schema in SQL Data Warehouse devono essere uguali a quelli nel database di origine.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Creare una tabella di controllo in SQL Server o Database SQL di Azure per archiviare l'elenco di partizioni del database di origine per la copia bulk. Nell'esempio seguente, sono presenti cinque partizioni nel database di origine. Sono tre partizioni per la *datasource_table*, e due per il *project_table*. La colonna *LastModifytime* viene usato per partizionare i dati nella tabella *datasource_table* dal database di origine. La query che viene usata per leggere la prima partizione è ' selezionare * da datasource_table dove LastModifytime > = ' 2015-01-01 00:00:00 "e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '. È possibile usare una query simile a leggere i dati da altre partizioni.

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

2. Andare alla **copia Bulk dal Database** modello. Creare un **New** connessione alla tabella di controllo del codice esterno che è stato creato nel passaggio 1.

    ![Creare una nuova connessione alla tabella di controllo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Creare un **New** connessione al database di origine che si copiano dati da.

     ![Creare una nuova connessione al database di origine](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Creare un **New** archiviare connessione ai dati di destinazione che si sta copiando i dati.

    ![Creare una nuova connessione all'archivio di destinazione](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selezionare **usare questo modello**.

    ![Usa questo modello](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Verrà visualizzata la pipeline, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selezionare **eseguire il Debug**, immettere il **parametri**, quindi selezionare **fine**.

    ![Fare clic su * * Debug * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Vengono visualizzati risultati simili all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facoltativo) Se si sceglie SQL Data Warehouse come destinazione dei dati, è necessario immettere una connessione all'archivio Blob di Azure per la gestione temporanea, come richiesto da Polybase di SQL Data Warehouse. Assicurarsi che il contenitore nell'archiviazione Blob è già stato creato.
    
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)
