---
title: Eseguire la copia bulk da un database con una tabella di controllo con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per la copia bulk dei dati da un database usando una tabella di controllo esterna per archiviare l'elenco di partizioni delle tabelle di origine con Azure Data Factory.
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
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966704"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Eseguire la copia bulk da un database con una tabella di controllo

Quando si vuole copiare i dati da un data warehouse come Oracle Server, Netezza Server, Teradata Server o SQL Server in Azure, è necessario caricare grandi quantità di dati da più tabelle nelle origini dati. Nella maggior parte dei casi, i dati devono essere ulteriormente partizionati in ogni tabella, in modo che sia possibile caricare righe con più thread in parallelo da una singola tabella. Questo modello è progettato a tale scopo. 

Se si vuole copiare i dati da un numero limitato di tabelle con dimensioni ridotte dei dati, è preferibile passare allo strumento di copia dei dati per avere un'unica attività Copy o un'attività ForEach più un'attività Copy nella pipeline. Per questo semplice caso d'uso, questo modello non è necessario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera l'elenco delle partizioni del database di origine da una tabella di controllo esterna che deve essere copiata nell'archivio di destinazione, quindi scorre ogni partizione nel database di origine ed esegue l'operazione di copia dei dati.

Il modello contiene tre attività:
-   Attività **Lookup** per recuperare l'elenco di partizioni del database di origine da una tabella di controllo esterna.
-   Attività **ForEach** per ottenere l'elenco di partizioni dall'attività Lookup e quindi scorrere ognuna di esse per l'attività Copy.
-   Attività **Copy** per copiare ciascuna partizione dall'archivio del database di origine all'archivio di destinazione.

Il modello definisce cinque parametri:
-   Il parametro *Control_Table_Name* è il nome di tabella per la tabella di controllo esterna. La tabella di controllo viene usata per archiviare l'elenco di partizioni per il database di origine.
-   Il parametro *Control_Table_Schema_PartitionID* è il nome della colonna nella tabella di controllo esterna per l'archiviazione di ogni ID partizione. Assicurarsi che l'ID partizione sia univoco per ogni partizione nel database di origine.
-   Il parametro *Control_Table_Schema_SourceTableName* è il nome della colonna nella tabella di controllo esterna per l'archiviazione di ogni nome di tabella del database di origine.
-   Il parametro *Control_Table_Schema_FilterQuery* è il nome della colonna nella tabella di controllo esterna per l'archiviazione della query di filtro con cui ottenere i dati da ogni partizione nel database di origine. Se, ad esempio, i dati sono stati partizionati per anno, la query archiviata in ogni riga può essere simile a "select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''
-   Il parametro *Data_Destination_Folder_Path* è il percorso della cartella in cui i dati vengono copiati nell'archivio di destinazione.  Questo parametro è visibile solo quando la destinazione scelta è un archivio basato su file.  Se si sceglie SQL Data Warehouse come archivio di destinazione, questo parametro non è necessario. I nomi di tabella e lo schema in SQL Data Warehouse devono tuttavia corrispondere a quelli nel database di origine.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Creare una tabella di controllo in SQL Server o SQL Azure per archiviare l'elenco di partizioni del database di origine per la copia bulk.  Nell'esempio riportato di seguito è possibile vedere che ci sono cinque partizioni nel database di origine, di cui tre partizioni per una tabella (*datasource_table*) e due partizioni relative a un'altra tabella (*project_table*). La colonna *LastModifytime* viene usata per partizionare i dati nella tabella *datasource_table* dal database di origine. La query usata per leggere la prima partizione è "select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00" and LastModifytime <= ''2015-12-31 23:59:59.999'''.  È anche possibile vedere una query simile per la lettura dei dati da altre partizioni. 

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

2. Passare al modello **Bulk Copy from Database** (Copia bulk da database) e creare una **nuova connessione** alla tabella di controllo esterna.  Questa connessione viene stabilita con il database in cui è stata creata la tabella di controllo nel passaggio 1.

    ![Creare una nuova connessione alla tabella di controllo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Creare una **nuova connessione** al database di origine da cui copiare i dati.

     ![Creare una nuova connessione al database di origine](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Creare una **nuova connessione** all'archivio dati di destinazione in cui copiare i dati.

    ![Creare una nuova connessione all'archivio di destinazione](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Fare clic su **Usa questo modello**.

    ![Usa questo modello](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Nel pannello verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Fare clic su **Debug**, immettere i parametri e quindi fare clic su **Fine**

    ![Fare clic su Debug](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Nel pannello verrà visualizzato il risultato, come illustrato nell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facoltativo) Se si seleziona SQL Data Warehouse come destinazione dei dati, è necessario immettere anche la connessione di un'istanza di Archiviazione BLOB di Azure come ambiente di gestione temporanea, come richiesto da SQL Data Warehouse PolyBase.  Assicurarsi che il contenitore nell'archivio BLOB sia già stato creato.  
    
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)