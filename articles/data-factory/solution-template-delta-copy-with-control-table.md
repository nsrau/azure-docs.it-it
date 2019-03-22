---
title: La copia delta da un database usando una tabella di controllo con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per la copia incrementale solo delle righe nuove o aggiornate da un database con Azure Data Factory.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771158"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Copia delta da un database con una tabella di controllo

Questo articolo descrive un modello che è disponibile per il caricamento incrementale righe nuove o aggiornate da una tabella di database in Azure tramite una tabella di controllo del codice esterno che archivia un valore di limite massimo.

Questo modello richiede che lo schema del database di origine contiene una chiave di colonna o l'incremento di timestamp per identificare le righe nuove o aggiornate.

>[!NOTE]
> Se si dispone di una colonna timestamp nel database di origine per identificare le righe nuove o aggiornate, ma non si desidera creare una tabella di controllo del codice esterno da utilizzare per la copia delta, è possibile usare la [dello strumento Copia dati di Azure Data Factory](copy-data-tool.md) per ottenere una pipeline. Tale strumento Usa un'ora pianificata trigger come una variabile per la lettura di nuove righe dal database di origine.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello prima di tutto recupera il valore del limite precedente e lo confronta con il valore del limite corrente. Successivamente, copia solo le modifiche dal database di origine, in base a un confronto tra i due valori del limite. Infine, archivia il nuovo valore limite massimo per una tabella di controllo del codice esterno per i dati differenziali successivo caricamento.

Il modello contiene quattro attività:
- **Ricerca** recupera il valore limite massimo precedente, che viene archiviato in una tabella di controllo del codice esterno.
- Un'altra **ricerca** attività recupera il valore di limite massimo corrente dal database di origine.
- **Copia** solo le modifiche vengono copiate dal database di origine nell'archivio di destinazione. La query che identifica le modifiche nel database di origine è simile a ' selezionare * da Data_Source_Table dove TIMESTAMP_Column > "limite massimo ultimo" e TIMESTAMP_Column < = "limite massimo corrente" '.
- **SqlServerStoredProcedure** scrive il valore di limite massimo corrente in una tabella di controllo del codice esterno per la copia delta successiva.

Il modello definisce cinque parametri:
- *Data_Source_Table_Name* è la tabella nel database di origine che si desidera caricare i dati.
- *Data_Source_WaterMarkColumn* è il nome della colonna nella tabella di origine che ha usato per identificare nuove o aggiornate le righe. Il tipo di questa colonna è in genere *data/ora*, *INT*, o simile.
- *Data_Destination_Folder_Path* oppure *Data_Destination_Table_Name* è la posizione in cui i dati vengono copiati in archivio di destinazione.
- *Control_Table_Table_Name* è la tabella di controllo del codice esterno che archivia il valore di limite massimo.
- *Control_Table_Column_Name* è la colonna della tabella di controllo del codice esterno che archivia il valore di limite massimo.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Esplorare l'origine di tabella è che si desidera caricare e definire la colonna limite massimo che può essere utilizzata per identificare le righe nuove o aggiornate. Il tipo di questa colonna potrebbe essere *data/ora*, *INT*, o simile. Valore della colonna aumenta man mano che vengono aggiunte nuove righe. Dalla seguente esempio tabella di origine (data_source_table), è possibile usare la *LastModifytime* colonna come colonna limite massimo.

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
    
2. Creare una tabella di controllo in SQL Server o Database SQL di Azure per archiviare il valore di limite massimo di caricamento di dati differenziale. Nell'esempio seguente, il nome della tabella di controllo è *watermarktable*. In questa tabella *WatermarkValue* è la colonna che archivia il valore di limite massimo e il relativo tipo è *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Creare una stored procedure nella stessa istanza di SQL Server o Database SQL di Azure usato per creare la tabella di controllo. La stored procedure viene utilizzata per scrivere il nuovo valore limite massimo per la tabella di controllo del codice esterno per i dati differenziali successivo caricamento.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Andare alla **copia Delta dal Database** modello. Creare un **New** connessione al database di origine che si desidera copiare i dati da.

    ![Creare una nuova connessione alla tabella di origine](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Creare un **New** connessione all'archivio dati di destinazione che si desidera copiare i dati.

    ![Creare una nuova connessione alla tabella di destinazione](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Creare un **New** connessione alla tabella di controllo del codice esterno e stored procedure che è stato creato nei passaggi 2 e 3.

    ![Creare una nuova connessione all'archivio dati della tabella di controllo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selezionare **usare questo modello**.

     ![Usa questo modello](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

     ![Esaminare la pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selezionare **Stored Procedure**. Per la **nome Stored procedure**, scegliere **[update_watermark]**. Selezionare **parametro di importazione**, quindi selezionare **Aggiungi contenuto dinamico**.  

     ![Impostare l'attività stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Scrivere il contenuto  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, quindi selezionare **fine**.  

     ![Scrivere il contenuto per i parametri della stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Selezionare **eseguire il Debug**, immettere il **parametri**, quindi selezionare **fine**.

    ![Selezionare * * Debug * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Vengono visualizzati risultati simili all'esempio seguente:

    ![Esaminare il risultato](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. È possibile creare nuove righe nella tabella di origine. Di seguito è linguaggio SQL di esempio per creare nuove righe:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Per eseguire di nuovo la pipeline, selezionare **eseguire il Debug**, immettere il **parametri**, quindi selezionare **fine**.

    ![Selezionare * * Debug * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Noterete che sono state copiate solo nuove righe alla destinazione.

15. (Facoltativo) Se si seleziona SQL Data Warehouse come destinazione dei dati, è necessario fornire anche una connessione all'archiviazione Blob di Azure per la gestione temporanea, è richiesta da Polybase di SQL Data Warehouse. Assicurarsi che il contenitore è già stato creato nell'archivio Blob.
    
    ![Configurare PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Copia bulk da un database usando una tabella di controllo con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiare i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
