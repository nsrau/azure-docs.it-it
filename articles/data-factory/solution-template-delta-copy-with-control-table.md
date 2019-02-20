---
title: Eseguire la copia differenziale da un database con una tabella di controllo con Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966638"
---
# <a name="delta-copy-from-database-with-control-table"></a>Eseguire la copia differenziale da un database con una tabella di controllo

È possibile caricare in modo incrementale solo le modifiche (righe nuove o aggiornate) da una tabella in un database ad Azure con una tabella di controllo esterna in cui è archiviato il valore limite massimo.  Questo modello è progettato a tale scopo. 

Per questo modello, lo schema del database di origine DEVE contenere una colonna timestamp o una chiave incrementale per identificare le righe nuove o aggiornate.

Se nel database di origine è presente una colonna timestamp per identificare le righe nuove o aggiornate, ma non si vuole creare una tabella di controllo esterna per abilitare la copia differenziale, è possibile usare lo strumento di copia dei dati per ottenere una pipeline, che usa un orario pianificato in base a un trigger come variabile per leggere solo le nuove righe dal database di origine.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera sempre per prima cosa il valore limite precedente e quindi lo confronta con il valore limite corrente. Successivamente, copia solo le modifiche dal database di origine in base al confronto tra i due valori limite.  Al termine, archivia il nuovo valore limite massimo in una tabella di controllo esterna per il successivo caricamento dei dati differenziali.

Il modello contiene quattro attività:
-   Attività **Lookup** per recuperare il valore limite massimo precedente archiviato in una tabella di controllo esterna.
-   Attività **Lookup** per recuperare il valore limite massimo corrente dal database di origine.
-   Attività **Copy** per copiare solo le modifiche dal database di origine all'archivio di destinazione. La query usata per identificare le modifiche dal database di origine nell'attività Copy è simile a "SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" and TIMESTAMP_Column <= "current high-watermark"".
-   Attività **SqlServerStoredProcedure** per scrivere il valore limite massimo corrente in una tabella di controllo esterna per la successiva copia differenziale.

Il modello definisce cinque parametri:
-   Il parametro *Data_Source_Table_Name* è il nome della tabella del database di origine da cui caricare i dati.
-   Il parametro *Data_Source_WaterMarkColumn* è il nome della colonna nella tabella di origine che è possibile usare per identificare le righe nuove o aggiornate. In genere, il tipo di questa colonna può essere datetime o INT e così via.
-   Il parametro *Data_Destination_Folder_Path* o *Data_Destination_Table_Name* indica la posizione in cui i dati vengono copiati nell'archivio di destinazione.
-   Il parametro *Control_Table_Table_Name* è il nome della tabella di controllo esterna per l'archiviazione del valore limite massimo.
-   Il parametro *Control_Table_Column_Name* è il nome della colonna nella tabella di controllo esterna per l'archiviazione del valore limite massimo.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Esplorare la tabella di origine da caricare e definire la colonna del limite massimo che è possibile usare per suddividere le righe nuove o aggiornate. In genere, il tipo di questa colonna può essere datetime o INT e così via e i relativi dati aumentano quando si aggiungono nuove righe.  Con la tabella di origine di esempio (nome di tabella: data_source_table) seguente, è possibile usare la colonna *LastModifytime* come colonna del limite massimo.

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
    
2. Creare una tabella di controllo in SQL Server o SQL Azure per archiviare il valore limite massimo per il caricamento dei dati differenziali. Nell'esempio seguente è possibile vedere che il nome della tabella di controllo è *watermarktable*. Al suo interno, il nome della colonna per l'archiviazione del valore limite massimo è *WatermarkValue* e il tipo è *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Creare una stored procedure nella stessa istanza di SQL Server o SQL Azure usata per creare la tabella di controllo. La stored procedure viene usata per scrivere il nuovo valore limite massimo in una tabella di controllo esterna per il successivo caricamento dei dati differenziali.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Passare al modello **Delta copy from Database** (Copia differenziale da database) e creare una **nuova connessione** al database di origine da cui copiare i dati.

    ![Creare una nuova connessione alla tabella di origine](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Creare una **nuova connessione** all'archivio dati di destinazione in cui copiare i dati.

    ![Creare una nuova connessione alla tabella di destinazione](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Creare una **nuova connessione** alla tabella di controllo esterna e alla stored procedure.  Questa connessione viene stabilita con il database in cui sono state create la tabella di controllo e la stored procedure nei passaggi 2 e 3.

    ![Creare una nuova connessione all'archivio dati della tabella di controllo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Fare clic su **Usa questo modello**.

     ![Usa questo modello](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Nel pannello verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

     ![Esaminare la pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Fare clic sull'attività Stored Procedure, selezionare **Nome stored procedure**, fare clic su **import parameter** (Importa parametro) e quindi fare clic su **Aggiungi contenuto dinamico**.  

     ![Impostare l'attività Stored Procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Scrivere il contenuto **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e fare clic su **Fine**.  

     ![Scrivere il contenuto per il parametro per la stored procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Fare clic su **Debug**, immettere i parametri e fare clic su **Fine**.

    ![Fare clic su Debug](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Nel pannello verrà visualizzato il risultato, come illustrato nell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. È possibile creare nuove righe nella tabella di origine.  Il codice SQL di esempio per la creazione di nuove righe può essere simile al seguente:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Eseguire di nuovo la pipeline facendo clic su **Debug**, immettere i parametri e fare clic su **Fine**.

    ![Fare clic su Debug](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. È possibile notare che solo le nuove righe sono state copiate nella destinazione.

15. (Facoltativo) Se si seleziona SQL Data Warehouse come destinazione dei dati, è necessario immettere anche la connessione di un'istanza di Archiviazione BLOB di Azure come ambiente di gestione temporanea, come richiesto da SQL Data Warehouse PolyBase.  Assicurarsi che il contenitore nell'archivio BLOB sia già stato creato.  
    
    ![Configurare PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)