---
title: "Usare Funzioni di Azure per eseguire un'attività di pulizia del database | Microsoft Docs"
description: "Usare Funzioni di Azure per pianificare un'attività che si connette al database SQL di Azure per eseguire una pulizia periodica delle righe."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 9d8261a22f5ea9ce61bcdc79d24a6c054597039b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Usare Funzioni di Azure per connettersi al database SQL di Azure
In questo argomento viene illustrato come usare Funzioni di Azure per creare un processo pianificato al fine di eseguire la pulizia delle righe in una tabella del database SQL di Azure. La nuova funzione C# viene creata in base a un modello predefinito di attivazione del timer nel portale di Azure. Per supportare questo scenario, è necessario anche impostare una stringa di connessione di database come impostazione app nell'app per le funzioni. Questo scenario esegue un'operazione in blocco sul database. 

Affinché la funzione elabori le singole operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) in una tabella di un'app per dispositivi mobili, è necessario usare invece le [associazioni di app per dispositivi mobili](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Prerequisiti

+ Questo argomento usa una funzione attivata da un timer. Completare i passaggi nell'argomento [Creare una funzione in Azure attivata da un timer](functions-create-scheduled-function.md) per creare una versione C# di questa funzione.   

+ Questo argomento illustra un comando Transact-SQL che esegue un'operazione di pulizia in blocco nella tabella **SalesOrderHeader** nel database di esempio AdventureWorksLT. Per creare il database di esempio AdventureWorksLT, completare la procedura nell'argomento [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

È necessario ottenere la stringa di connessione per il database creato dopo avere completato [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started-portal.md).

1. Accedere al [Portale di Azure](https://portal.azure.com/).
 
3. Scegliere **Database SQL** dal menu a sinistra, quindi scegliere il database nella pagina **Database SQL**.

4. Selezionare **Mostra stringhe di connessione del database** e copiare tutta la stringa di connessione **ADO.NET**.

    ![Copiare la stringa di connessione ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Impostare la stringa di connessione 

Un'app per le funzioni ospita l'esecuzione delle funzioni in Azure. È consigliabile archiviare le stringhe di connessione e altre informazioni riservate nelle impostazioni dell'app per le funzioni. L'uso delle impostazioni dell'applicazione impedisce la diffusione accidentale della stringa di connessione con il codice. 

1. Passare all'app per le funzioni creata in [Creare una funzione in Azure attivata da un timer](functions-create-scheduled-function.md).

2. Selezionare **Funzionalità della piattaforma** > **Impostazioni applicazione**.
   
    ![Impostazioni applicazioni per l'app per le funzioni.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Scorrere verso il basso fino a **Stringhe di connessione** e aggiungere una stringa di connessione usando le impostazioni come indicato nella tabella.
   
    ![Aggiungere una stringa di connessione alle impostazioni dell'app per le funzioni.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Impostazione       | Valore consigliato | Descrizione             | 
    | ------------ | ------------------ | --------------------- | 
    | **Nome**  |  sqldb_connection  | Usato per accedere alla stringa di connessione memorizzata nel codice della funzione.    |
    | **Valore** | Stringa copiata  | Incollare la stringa di connessione copiata nella sezione precedente e quindi sostituire i segnaposto `{your_username}` e `{your_password}` con valori reali. |
    | **Tipo** | Database SQL | Usare la connessione al database SQL predefinita. |   

3. Fare clic su **Salva**.

A questo punto, è possibile aggiungere il codice della funzione C# che si connette al database SQL.

## <a name="update-your-function-code"></a>Aggiornare il codice di funzione

1. Nell'app per le funzioni, selezionare la funzione attivata dal timer.
 
3. Aggiungere i riferimenti assembly seguenti all'inizio del codice della funzione esistente:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. Aggiungere le istruzioni `using` seguenti alla funzione:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Sostituire la funzione `Run` esistente con il codice seguente:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Questo comando di esempio aggiorna la colonna `Status` in base alla data di spedizione. Aggiorna 32 righe di dati.

5. Fare clic su **Salva**, osservare le finestre **Log** per l'esecuzione della funzione successiva e quindi prendere nota del numero di righe aggiornate nella tabella **SalesOrderHeader**.

    ![Visualizzare i log di funzione.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Passaggi successivi

Ora si apprenderà come usare le funzioni con l'app per la logica per l'integrazione con altri servizi.

> [!div class="nextstepaction"] 
> [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md)

Per altre informazioni sulle funzioni, vedere gli argomenti seguenti:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)  
  Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
* [Test di Funzioni di Azure](functions-test-a-function.md)  
  Descrive diversi strumenti e tecniche per il test delle funzioni.  
