---
title: Copia delta da un database utilizzando una tabella di controllo
description: Informazioni su come usare un modello di soluzione per la copia incrementale solo delle righe nuove o aggiornate da un database con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414778"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Copia delta da un database con una tabella di controllo
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive un modello disponibile per caricare in modo incrementale righe nuove o aggiornate da una tabella di database ad Azure usando una tabella di controlli esterno che archivia un valore di filigrana elevato.

Questo modello richiede che lo schema del database di origine contenga una colonna timestamp o una chiave di incremento per identificare le righe nuove o aggiornate.

>[!NOTE]
> Se nel database di origine è presente una colonna timestamp per identificare righe nuove o aggiornate ma non si vuole creare una tabella di controllo esterno da usare per la copia delta, è invece possibile usare [lo strumento Copia dati](copy-data-tool.md) di Azure Data Factory per ottenere una pipeline. Questo strumento utilizza un'ora pianificata dal trigger come variabile per leggere le nuove righe dal database di origine.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera innanzitutto il valore della filigrana precedente e lo confronta con il valore della filigrana corrente. Successivamente, copia solo le modifiche dal database di origine, in base a un confronto tra i due valori di filigrana. Infine, archivia il nuovo valore high-watermark in una tabella di controllo esterna per il caricamento dei dati delta la volta successiva.

Il modello contiene quattro attività:
- **La ricerca** recupera il vecchio valore high-watermark, archiviato in una tabella di controllo esterno.
- Un'altra attività **di ricerca** recupera il valore di limite massimo corrente dal database di origine.
- **Copia** copie solo le modifiche dal database di origine all'archivio di destinazione. La query che identifica le modifiche nel database di origine è simile a 'SELECT - Data_Source_Table FROM WHERE TIMESTAMP_Column > "ultima filigrana alta" e TIMESTAMP_Column <" "high-watermark"" corrente.
- **SqlServerStoredProcedure** scrive il valore high-watermark corrente in una tabella di controllo esterna per la copia delta la volta successiva.

Il modello definisce i seguenti parametri:
- *Data_Source_Table_Name* è la tabella nel database di origine da cui si desidera caricare i dati.
- *Data_Source_WaterMarkColumn* è il nome della colonna nella tabella di origine utilizzata per identificare le righe nuove o aggiornate. Il tipo di questa colonna è in genere *datetime*, *INT*o simili.
- *Data_Destination_Container* è il percorso radice della posizione in cui vengono copiati i dati nell'archivio di destinazione.
- *Data_Destination_Directory* è il percorso della directory nella directory principale della posizione in cui vengono copiati i dati nell'archivio di destinazione.
- *Data_Destination_Table_Name* è la posizione in cui i dati vengono copiati nell'archivio di destinazione (applicabile quando "Azure Synapse Analytics (in precedenza SQL DW)" è selezionato come Destinazione dati).
- *Data_Destination_Folder_Path* è la posizione in cui i dati vengono copiati nell'archivio di destinazione (applicabile quando "File System" o "Azure Data Lake Storage Gen1" è selezionato come Destinazione dati).
- *Control_Table_Table_Name* è la tabella di controllo esterna in cui è archiviato il valore di filigrana alta.
- *Control_Table_Column_Name* è la colonna nella tabella dei controlli esterni in cui è archiviato il valore di filigrana alta.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Esplorare la tabella di origine che si desidera caricare e definire la colonna con filigrana alta che può essere utilizzata per identificare le righe nuove o aggiornate. Il tipo di questa colonna potrebbe essere *datetime*, *INT*o simili. Il valore di questa colonna aumenta con l'aggiunta di nuove righe. Dalla tabella di origine di esempio seguente (data_source_table) è possibile usare la colonna *LastModifytime* come colonna con filigrana alta.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Creare una tabella di controllo in SQL ServerSQL Server o nel database SQL di Azure per archiviare il valore di limite massimo per il caricamento dei dati delta. Nell'esempio seguente, il nome della tabella di controllo è *watermarktable*. In questa *tabella, WatermarkValue* è la colonna in cui è archiviato il valore high-watermark e il relativo tipo è *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Creare una stored procedure nella stessa istanza di SQL Server o database SQL di Azure usata per creare la tabella di controllo. La stored procedure viene utilizzata per scrivere il nuovo valore di filigrana alta nella tabella di controllo esterna per il caricamento dei dati differenziali la volta successiva.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Passare alla copia Delta dal modello **Database.** Creare una **nuova** connessione al database di origine da cui si desidera copiare i dati.

    ![Creare una nuova connessione alla tabella di origine](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Creare una **nuova** connessione all'archivio dati di destinazione in cui si desidera copiare i dati.

    ![Creare una nuova connessione alla tabella di destinazione](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Creare una **nuova** connessione alla tabella di controllo esterno e alla stored procedure creata nei passaggi 2 e 3.

    ![Creare una nuova connessione all'archivio dati della tabella di controllo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selezionare **Usa questo modello**.
    
8. Viene visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:You see the available pipeline, as shown in the following example:
  
    ![Esaminare la pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selezionare **Stored procedure**. Per **Nome stored procedure**scegliere **[dbo].[ update_watermark]**. Selezionare **Importa parametro**, quindi **Aggiungi contenuto dinamico**.  

    ![Impostare l'attività della stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Scrivere il contenuto ** \@, Activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue**, quindi selezionare **Finish**.  

    ![Scrivere il contenuto per i parametri della stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Selezionare il valore di debug](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Vengono visualizzati risultati simili all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. È possibile creare nuove righe nella tabella di origine. Di seguito è riportato un linguaggio SQL di esempio per creare nuove righe:Here is sample SQL language to create new rows:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Per eseguire nuovamente la pipeline, selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    Si noterà che solo le nuove righe sono state copiate nella destinazione.

15. (Facoltativo:) Se si seleziona Analisi synapse di Azure (in precedenza SQL DW) come destinazione dei dati, è inoltre necessario fornire una connessione all'archiviazione BLOB di Azure per la gestione temporanea, richiesta da Polybase del data warehouse SQL. Il modello genererà automaticamente un percorso del contenitore. Dopo l'esecuzione della pipeline, verificare se il contenitore è stato creato nell'archivio BLOB.
    
    ![Configurare PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Copia di massa da un database usando una tabella di controllo con Azure Data FactoryBulk copy from a database by using a control table with Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
