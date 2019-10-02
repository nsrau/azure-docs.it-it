---
title: Usare Funzioni di Azure per eseguire un'attività di pulizia del database | Microsoft Docs
description: Usare Funzioni di Azure per pianificare un'attività che si connette al database SQL di Azure per eseguire una pulizia periodica delle righe.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: glenga
ms.openlocfilehash: 469e0149a3b9dce22f0590240a053ee3b183c7b9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815986"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Usare Funzioni di Azure per connettersi al database SQL di Azure

Questo articolo illustra come usare funzioni di Azure per creare un processo pianificato che si connette a un database SQL di Azure o a un Istanza gestita SQL di Azure. Il codice della funzione pulisce le righe in una tabella nel database. La nuova C# funzione viene creata in base a un modello predefinito di attivazione del timer in Visual Studio 2019. Per supportare questo scenario, è necessario anche impostare una stringa di connessione di database come impostazione app nell'app per le funzioni. Per il Istanza gestita SQL di Azure è necessario [abilitare l'endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) per potersi connettere da funzioni di Azure. Questo scenario esegue un'operazione in blocco sul database. 

Se si tratta della prima esperienza di utilizzo di funzioni C#, è consigliabile leggere la [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Prerequisiti

+ Completare i passaggi nell'articolo [Creare la prima funzione con Visual Studio](functions-create-your-first-function-visual-studio.md) per creare un'app per le funzioni locale destinata alla versione 2.x del runtime. È anche necessario aver pubblicato il progetto in un'app per le funzioni in Azure.

+ Questo articolo illustra un comando Transact-SQL che esegue un'operazione di pulizia in blocco nella tabella **SalesOrderHeader** nel database di esempio AdventureWorksLT. Per creare il database di esempio AdventureWorksLT, completare la procedura nell'articolo [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started-portal.md).

+ È necessario aggiungere una [regola del firewall a livello di server](../sql-database/sql-database-get-started-portal-firewall.md) per l'indirizzo IP pubblico del computer usato per questa guida introduttiva. Questa regola è necessaria per essere in grado di accedere all'istanza del database SQL dal computer locale.  

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

È necessario ottenere la stringa di connessione per il database creato dopo avere completato [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Scegliere **Database SQL** dal menu a sinistra, quindi scegliere il database nella pagina **Database SQL**.

1. Selezionare **Stringhe di connessione** in **Impostazioni** e copiare la stringa di connessione **ADO.NET** completa. Per Azure SQL Istanza gestita Copiare la stringa di connessione per l'endpoint pubblico.

    ![Copiare la stringa di connessione ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Impostare la stringa di connessione

Un'app per le funzioni ospita l'esecuzione delle funzioni in Azure. Come procedura consigliata per la sicurezza è opportuno archiviare le stringhe di connessione e altre informazioni riservate nelle impostazioni dell'app per le funzioni. L'uso delle impostazioni dell'applicazione impedisce la diffusione accidentale della stringa di connessione con il codice. È possibile accedere alle impostazioni dell'app per l'app per le funzioni direttamente da Visual Studio.

È necessario aver precedentemente pubblicato l'app in Azure. Se non è già stato fatto, [pubblicare l'app per le funzioni in Azure](functions-develop-vs.md#publish-to-azure).

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto dell'app per le funzioni e scegliere **Pubblica** > **Gestisci impostazioni applicazione**. Selezionare **Aggiungi impostazione**, in **Nome nuova impostazione app** digitare `sqldb_connection` e selezionare **OK**.

    ![Impostazioni applicazioni per l'app per le funzioni.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Nella nuova impostazione **sqldb_connection** incollare la stringa di connessione copiata nella sezione precedente nel campo **Locale** e sostituire i segnaposto `{your_username}` e `{your_password}` con valori reali. Selezionare **Inserisci valore da locale** per copiare il valore aggiornato nel campo **Remoto** e quindi selezionare **OK**.

    ![Aggiungere l'impostazione della stringa di connessione SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Le stringhe di connessione vengono archiviate crittografate in Azure (**remoto**). Per evitare perdite di segreti, è consigliabile escludere il file di progetto local.settings.json (**locale**) dal controllo del codice sorgente, ad esempio usando un file con estensione gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Aggiungere il pacchetto SqlClient al progetto

È necessario aggiungere il pacchetto NuGet che contiene la libreria SqlClient. Questa libreria di accesso ai dati è necessaria per connettersi a un database SQL.

1. Aprire il progetto di app per le funzioni locali in Visual Studio 2019.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app per le funzioni e scegliere **Gestisci pacchetti NuGet**.

1. Nella scheda **Sfoglia** cercare ```System.Data.SqlClient``` e, dopo averlo trovato, selezionarlo.

1. Nella pagina **System.Data.SqlClient** selezionare la versione `4.5.1` e quindi fare clic su **Installa**.

1. Al termine dell'installazione, rivedere le modifiche e quindi fare clic su **OK** per chiudere la finestra **Anteprima**.

1. Se viene visualizzata una finestra **Accettazione della licenza** fare clic su **Accetto**.

A questo punto, è possibile aggiungere il codice della funzione C# che si connette al database SQL.

## <a name="add-a-timer-triggered-function"></a>Aggiungere una funzione attivata da un timer

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app per le funzioni e scegliere **Aggiungi** > **Nuova funzione di Azure**.

1. Con il modello **Funzioni di Azure** selezionato, assegnare al nuovo elemento un nome simile a `DatabaseCleanup.cs` e selezionare **Aggiungi**.

1. Nella finestra di dialogo **Nuova funzione di Azure** scegliere **Trigger timer** e quindi **OK**. Questa finestra di dialogo crea un file di codice per la funzione attivata da timer.

1. Aprire il nuovo file di codice e aggiungere le istruzioni using seguenti all'inizio del file:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Sostituire la funzione `Run` esistente con il codice seguente:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Questa funzione viene eseguita ogni 15 secondi per aggiornare la colonna `Status` in base alla data di spedizione. Per altre informazioni relative al trigger timer, vedere [Trigger timer per Funzioni di Azure](functions-bindings-timer.md).

1. Premere **F5** per avviare l'app per le funzioni. Verrà visualizzata la finestra di esecuzione [Strumenti di base di Funzioni di Azure](functions-develop-local.md) in secondo piano rispetto a Visual Studio.

1. 15 secondi dopo l'avvio, la funzione viene eseguita. Osservare l'output e notare il numero di righe aggiornate nella tabella **SalesOrderHeader**.

    ![Visualizzare i log di funzione.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Alla prima esecuzione dovrebbero essere aggiornate 32 righe di dati. Le esecuzioni successive non aggiornano alcuna riga di dati, a meno che non si apportino modifiche ai dati della tabella SalesOrderHeader in modo che vengano selezionate altre righe dall'istruzione `UPDATE`.

Se si intende [pubblicare questa funzione](functions-develop-vs.md#publish-to-azure), ricordarsi di modificare l'attributo `TimerTrigger` specificando una [pianificazione CRON](functions-bindings-timer.md#ncrontab-expressions) più ragionevole rispetto a ogni 15 secondi.

## <a name="next-steps"></a>Passaggi successivi

Si vedrà ora come usare Funzioni con app per la logica per l'integrazione con altri servizi.

> [!div class="nextstepaction"]
> [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md)

Per altre informazioni su Funzioni, vedere gli articoli seguenti:

+ [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)  
  Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
+ [Test di Funzioni di Azure](functions-test-a-function.md)  
  Descrive diversi strumenti e tecniche per il test delle funzioni.  
