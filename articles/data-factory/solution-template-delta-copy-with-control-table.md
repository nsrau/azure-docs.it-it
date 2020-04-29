---
title: Copia Delta da un database tramite una tabella di controllo
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414778"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Copia Delta da un database con una tabella di controllo
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive un modello disponibile per caricare in modo incrementale righe nuove o aggiornate da una tabella di database in Azure usando una tabella di controllo esterna che archivia un valore limite massimo.

Questo modello richiede che lo schema del database di origine contenga una colonna timestamp o una chiave di incremento per identificare le righe nuove o aggiornate.

>[!NOTE]
> Se nel database di origine è presente una colonna timestamp per identificare le righe nuove o aggiornate, ma non si vuole creare una tabella di controllo esterna da usare per la copia Delta, è invece possibile usare lo [strumento Azure Data Factory copia dati](copy-data-tool.md) per ottenere una pipeline. Tale strumento utilizza un'ora pianificata del trigger come variabile per leggere le nuove righe dal database di origine.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera innanzitutto il valore limite precedente e lo confronta con il valore limite corrente. In seguito, vengono copiate solo le modifiche dal database di origine, in base a un confronto tra i due valori di filigrana. Infine, archivia il nuovo valore limite massimo in una tabella di controllo esterna per il caricamento dei dati Delta alla prossima esecuzione.

Il modello contiene quattro attività:
- **Lookup** Recupera il vecchio valore limite massimo, che viene archiviato in una tabella di controllo esterna.
- Un'altra attività di **ricerca** Recupera il valore del limite massimo corrente dal database di origine.
- **Copia** solo le modifiche apportate al database di origine nell'archivio di destinazione. La query che identifica le modifiche nel database di origine è simile a' SELECT * FROM Data_Source_Table dove TIMESTAMP_Column > "ultimo limite massimo" e TIMESTAMP_Column <= "limite massimo corrente".
- **SqlServerStoredProcedure** scrive il valore di limite massimo corrente in una tabella di controllo esterna per la copia Delta la volta successiva.

Il modello definisce i parametri seguenti:
- *Data_Source_Table_Name* è la tabella nel database di origine da cui si desidera caricare i dati.
- *Data_Source_WaterMarkColumn* è il nome della colonna nella tabella di origine utilizzata per identificare le righe nuove o aggiornate. Il tipo di questa colonna è in genere *DateTime*, *int*o similar.
- *Data_Destination_Container* è il percorso radice della posizione in cui vengono copiati i dati nell'archivio di destinazione.
- *Data_Destination_Directory* è il percorso della directory sotto la radice della posizione in cui vengono copiati i dati nell'archivio di destinazione.
- *Data_Destination_Table_Name* è la posizione in cui vengono copiati i dati nell'archivio di destinazione (applicabile quando si seleziona "Azure sinapsi Analytics (in precedenza SQL DW)" come destinazione dati.
- *Data_Destination_Folder_Path* è la posizione in cui vengono copiati i dati nell'archivio di destinazione (applicabile quando "file System" o "Azure Data Lake storage Gen1" è selezionato come destinazione dei dati).
- *Control_Table_Table_Name* è la tabella di controllo esterna in cui è archiviato il valore limite massimo.
- *Control_Table_Column_Name* è la colonna della tabella di controllo esterno che archivia il valore limite massimo.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Esplorare la tabella di origine che si desidera caricare e definire la colonna con limite massimo che può essere utilizzata per identificare le righe nuove o aggiornate. Il tipo di questa colonna potrebbe essere *DateTime*, *int*o simile. Il valore di questa colonna aumenta man mano che vengono aggiunte nuove righe. Dalla seguente tabella di origine di esempio (data_source_table), è possibile usare la colonna *LastModifytime* come colonna con limite massimo.

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
    
2. Creare una tabella di controllo in SQL Server o nel database SQL di Azure per archiviare il valore limite massimo per il caricamento di dati Delta. Nell'esempio seguente il nome della tabella dei controlli è *watermarktable*. In questa tabella, *WatermarkValue* è la colonna in cui è archiviato il valore limite massimo e il tipo è *DateTime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Creare una stored procedure nella stessa SQL Server o nell'istanza del database SQL di Azure usata per creare la tabella di controllo. Il stored procedure viene usato per scrivere il nuovo valore limite massimo nella tabella del controllo esterno per il caricamento dei dati Delta alla prossima esecuzione.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Passare alla **copia Delta dal modello di database** . Consente di creare una **nuova** connessione al database di origine da cui si desidera copiare i dati.

    ![Creare una nuova connessione alla tabella di origine](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Creare una **nuova** connessione all'archivio dati di destinazione in cui si desidera copiare i dati.

    ![Creare una nuova connessione alla tabella di destinazione](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Creare una **nuova** connessione alla tabella di controllo esterno e stored procedure creata nei passaggi 2 e 3.

    ![Creare una nuova connessione all'archivio dati della tabella di controllo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selezionare **Usa questo modello**.
    
8. Viene visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:
  
    ![Esaminare la pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selezionare **stored procedure**. Per **Nome stored procedure**scegliere **[dbo]. [ update_watermark]**. Selezionare **Importa parametro**, quindi selezionare **Aggiungi contenuto dinamico**.  

    ![Impostare l'attività stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Scrivere il contenuto ** \@{Activity (' LookupCurrentWaterMark '). output. FirstRow. NewWatermarkValue}** e quindi selezionare **Finish (fine**).  

    ![Scrivere il contenuto per i parametri dell'stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Selezionare * * debug * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Vengono visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. È possibile creare nuove righe nella tabella di origine. Ecco il linguaggio SQL di esempio per creare nuove righe:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Per eseguire di nuovo la pipeline, selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    Si noterà che nella destinazione sono state copiate solo le nuove righe.

15. Opzionale Se si seleziona Azure sinapsi Analytics (in precedenza SQL DW) come destinazione dei dati, è necessario fornire anche una connessione all'archivio BLOB di Azure per la gestione temporanea, necessaria per SQL Data Warehouse polibase. Il modello genererà automaticamente un percorso del contenitore. Dopo l'esecuzione della pipeline, controllare se il contenitore è stato creato nell'archivio BLOB.
    
    ![Configurare PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una copia bulk da un database tramite una tabella di controllo con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
