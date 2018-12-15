---
title: Introduzione a WebJobs SDK - Azure
description: Introduzione a WebJobs SDK per l'elaborazione in background guidata dagli eventi. Informazioni su come accedere ai dati nei servizi di Azure e di terze parti.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 034f182cc282f50eb3a4a1de05331f42957f49fe
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339808"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introduzione ad Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo illustra come creare, eseguire in locale e distribuire nel Servizio app di Azure un progetto Azure WebJobs SDK.

Le istruzioni riguardano [Visual Studio 2017](https://www.visualstudio.com/vs/), ma le stesse attività possono essere eseguite con altri strumenti, ad esempio [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Cos'è Azure WebJobs SDK

Azure WebJobs SDK è un framework che semplifica l'attività di scrittura del codice di elaborazione in background che accede ai dati nei servizi di Azure. L'SDK usa una sintassi dichiarativa per specificare gli eventi che attiveranno una funzione, ad esempio l'aggiunta di un nuovo messaggio a una coda. Una sintassi dichiarativa simile controlla la lettura e la scrittura di dati dopo l'attivazione di una funzione. Questo sistema di trigger e associazioni gestisce la maggior parte delle attività di scrittura di codice di basso livello associate all'accesso ai servizi di Azure e di terze parti.

### <a name="functions-triggers-and-bindings"></a>Funzioni, trigger e associazioni

Un progetto WebJobs SDK definisce una o più *funzioni*. Una funzione è un metodo che contiene un attributo trigger nella firma del metodo. I trigger specificano le condizioni per la chiamata di una funzione, mentre le associazioni specificano gli elementi da leggere e da scrivere. L'attributo trigger nella funzione seguente, ad esempio, indica al runtime di chiamare la funzione ogni volta che viene inserito un messaggio nella coda `items`. L'attributo `Blob` indica al runtime di usare il messaggio nella coda per leggere un BLOB nel contenitore *workitems*. Il contenuto del messaggio nella coda, specificato nel parametro `queueTrigger`, è il nome del BLOB.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Versioni 2.x e 3.x

Le istruzioni indicano come creare un progetto WebJobs SDK versione 2.x. La versione più recente di WebJobs SDK è la 3.x, ma è attualmente in anteprima e questo articolo non contiene ancora le istruzioni per tale versione. La modifica principale introdotta dalla versione 3.x è l'uso di .NET Core anziché di .NET Framework.

### <a name="azure-functions"></a>Funzioni di Azure

Il servizio [Funzioni di Azure](../azure-functions/functions-overview.md) è basato su WebJobs SDK ed è un'opzione valida quando non è necessario usare direttamente WebJobs SDK. Funzioni di Azure 1.x usa WebJobs SDK 2.x. Per altre informazioni, vedere il [confronto tra Funzioni di Azure e WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia un [account Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ed esperienza con le [app nel servizio app di Azure](app-service-web-overview.md). Per seguire la procedura descritta in questo articolo:

* [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) con il carico di lavoro **Sviluppo di Azure**. Se si ha già Visual Studio ma non tale carico di lavoro, aggiungere il carico di lavoro selezionando **Strumenti > Ottieni strumenti e funzionalità**.
* [Creare un'app del servizio app](app-service-web-get-started-dotnet-framework.md). Se se ne ha già una in cui può essere distribuito un processo Web, si può usare tale app anziché crearne una nuova.

## <a name="create-a-project"></a>Creare un progetto

1. In Visual Studio selezionare **File -> Nuovo progetto**.

2. Selezionare **Desktop classico di Windows > App console (.NET Framework)**.

3. Assegnare al progetto il nome *WebJobsSDKSample* e quindi selezionare **OK**.

   ![Finestra di dialogo Nuovo progetto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Aggiungere il pacchetto NuGet di processi Web

1. Installare l'ultima versione 2.x stabile del pacchetto NuGet `Microsoft.Azure.WebJobs`.
 
   Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Creare JobHost

L'oggetto `JobHost` è il contenitore di runtime per le funzioni, che resta in ascolto dei trigger e chiama le funzioni. 

1. In *Program.cs* aggiungere un'istruzione `using`:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Sostituire il metodo `Main` con il codice seguente:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Abilitare la registrazione nella console

Nel progetto WebJobs SDK sono disponibili diverse opzioni per la registrazione. L'opzione consigliata è il [framework di registrazione sviluppato per ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging). Questo framework offre prestazioni superiori e maggiore flessibilità a livello di supporti di archiviazione e di filtri. 

In questa sezione si configura la registrazione nella console con il nuovo framework.

1. Installare l'ultima versione stabile dei pacchetti NuGet seguenti.

   * `Microsoft.Extensions.Logging`: framework di registrazione.
   * `Microsoft.Extensions.Logging.Console`: *provider* della console. Un provider invia i log a una destinazione specifica, in questo caso la console. 
 
   Di seguito sono riportati i comandi di **Console di Gestione pacchetti** per la versione 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. In *Program.cs* aggiungere un'istruzione `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Nel metodo `Main` aggiungere il codice per aggiornare `JobHostConfiguration` prima della creazione di `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Questo codice apporta le modifiche seguenti:

   * Disabilita la [registrazione nel dashboard](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Il dashboard è uno strumento di monitoraggio legacy e la registrazione nel dashboard non è consigliata per scenari di produzione con velocità effettiva elevata.
   * Aggiunge il provider della console con i [filtri](webjobs-sdk-how-to.md#log-filtering) predefiniti. 

   Il metodo `Main` si presenta ora come segue:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Creare una funzione

1. Creare *Functions.cs* nella cartella del progetto e sostituire il codice del modello con il seguente:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   L'attributo `QueueTrigger` indica al runtime di chiamare questa funzione quando viene scritto un nuovo messaggio in una coda di archiviazione di Azure denominata `queue`. Il contenuto del messaggio nella coda viene passato al codice del metodo nel parametro `message`. Nel corpo del metodo vengono elaborati i dati del trigger. In questo esempio, il codice registra semplicemente il messaggio.

   Il parametro `message` non deve necessariamente essere una stringa. È possibile eseguire l'associazione anche a un oggetto JSON, una matrice di byte o un oggetto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). [Vedere l'utilizzo del trigger della coda](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Ogni tipo di associazione (come code, BLOB o tabelle) presenta un diverso set di tipi di parametri a cui è possibile eseguire l'associazione.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

L'emulatore di archiviazione di Azure eseguito in locale non include tutte le funzionalità necessarie a WebJobs SDK. In questa sezione si crea quindi un account di archiviazione in Azure e si configura il progetto per l'uso di tale account.

1. Aprire **Esplora server** in Visual Studio e accedere ad Azure. Fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Connessione alla sottoscrizione di Microsoft Azure**.

   ![Accedere ad Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Nel nodo **Azure** in **Esplora server** fare clic con il pulsante destro del mouse su **Archiviazione** e quindi scegliere **Crea account di archiviazione**.

   ![Menu per creare un account di archiviazione](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Nella finestra di dialogo **Crea account di archiviazione** immettere un nome univoco per l'account di archiviazione.

1. Scegliere la stessa **area** in cui è stata creata l'app del servizio app o un'area vicina all'utente.

1. Selezionare **Create**.

   ![Creare l'account di archiviazione](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Nel nodo **Archiviazione** in **Esplora server** selezionare il nuovo account di archiviazione. Nella finestra **Proprietà** selezionare i puntini di sospensione (**...**) a destra del campo del valore di **Stringa di connessione**.

   ![Puntini di sospensione di Stringa di connessione](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copiare la stringa di connessione e salvare questo valore in una posizione da cui possa essere copiato di nuovo facilmente.

   ![Copiare la stringa di connessione](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Configurare la risorsa di archiviazione per l'esecuzione in locale

WebJobs SDK cerca la stringa di connessione di archiviazione nella raccolta delle impostazioni dell'app. In caso di esecuzione in locale, questo valore viene cercato nel file *App.config* o nelle variabili di ambiente.

1. Aggiungere il codice XML seguente al file *App.config*, subito dopo il tag `<configuration>` di apertura.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Sostituire *{storage connection string}* con la stringa di connessione copiata in precedenza.

   La stringa di connessione verrà usata di nuovo in seguito durante la configurazione dell'app del servizio app in Azure.

## <a name="test-locally"></a>Test in locale

In questa sezione si compila e si esegue il progetto in locale e si attiva la funzione creando un messaggio nella coda.

1. Premere CTRL+F5 per eseguire il progetto.

   La console mostra che il runtime ha trovato la funzione ed è in attesa che venga attivata da messaggi nella coda.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Potrebbe essere visualizzato un messaggio di avviso relativo a un'impostazione `ServicePointManager`. Per il test eseguito con questo progetto, si può ignorare l'avviso. Per altre informazioni in merito, vedere l'articolo su [come usare WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. Chiudere la finestra della console.

1. In **Esplora server** in Visual Studio, espandere il nodo del nuovo account di archiviazione e quindi fare clic con il pulsante destro del mouse su **Code**. 

1. Scegliere **Crea coda**. 

1. Immettere *queue* come nome della coda e quindi selezionare **OK**.

   ![Crea coda](./media/webjobs-sdk-get-started/create-queue.png)

1. Fare clic con il pulsante destro del mouse sul nodo della nuova coda e quindi scegliere **Visualizza coda**.

1. Selezionare l'icona **Aggiungi messaggio**.

   ![Crea coda](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Nella finestra di dialogo **Aggiungi messaggio** immettere *Hello World!* come **Testo messaggio** e quindi selezionare **OK**.

   ![Crea coda](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Eseguire di nuovo il progetto.

   Dato che è stato usato l'attributo `QueueTrigger` nella funzione `ProcessQueueMessage`, il runtime di WeJobs SDK avviato è in ascolto di messaggi nella coda, trova un nuovo messaggio nella coda denominata *queue* e chiama la funzione.

   A causa del [backoff esponenziale del polling delle code](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), prima che il runtime trovi il messaggio e richiami la funzione possono trascorrere fino a 2 minuti. Questo tempo di attesa può essere ridotto con l'esecuzione in [modalità di sviluppo](webjobs-sdk-how-to.md#jobhost-development-settings).

  L'output della console è simile al seguente:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. Chiudere la finestra della console.

## <a name="add-application-insights-logging"></a>Aggiungere la registrazione in Application Insights

Quando il progetto viene eseguito in Azure, non è possibile monitorare l'esecuzione della funzione visualizzando l'output della console. La soluzione di monitoraggio consigliata è [Application Insights](../application-insights/app-insights-overview.md). Per altre informazioni, vedere [Monitorare Funzioni di Azure](../azure-functions/functions-monitoring.md).

In questa sezione si configura la registrazione in Application Insights prima della distribuzione in Azure eseguendo queste attività:

* Assicurarsi che siano disponibili un'app del servizio app e un'istanza di Application Insights da usare.
* Configurare l'app del servizio app per l'uso dell'istanza di Application Insights e dell'account di archiviazione creato in precedenza.
* Configurare il progetto per la registrazione in Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Creare un'app del servizio app e un'istanza di Application Insights

1. Se non si ha già un'app del servizio app utilizzabile, [crearne una](app-service-web-get-started-dotnet-framework.md).

1. Se non si ha già una risorsa di Application Insights utilizzabile, [crearne una](../application-insights/app-insights-create-new-resource.md). Impostare **Tipo di applicazione** su **Generale** e ignorare le sezioni successive a **Eseguire una copia della chiave di strumentazione**.

1. Se si ha già una risorsa di Application Insights da usare, [copiare la chiave di strumentazione](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione 

1. In **Esplora server** in Visual Studio, espandere il nodo **Servizio app** sotto **Azure**.

1. Espandere il gruppo di risorse in cui si trova l'app del servizio app e quindi fare clic con il pulsante destro del mouse sull'app del servizio app.

1. Scegliere **Visualizza impostazioni**.

1. Nella casella **Stringhe di connessione** aggiungere la voce seguente.

   |NOME  |Stringa di connessione  |Tipo di database|
   |---------|---------|------|
   |AzureWebJobsStorage | {stringa di connessione di archiviazione copiata in precedenza}|Personalizzate|
   
1. Se la casella **Impostazioni applicazione** non contiene una chiave di strumentazione di Application Insights, aggiungere quella copiata in precedenza. La presenza o meno della chiave di strumentazione dipende da come è stata creata l'app del servizio app.

   |NOME  |Valore  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

1. Sostituire *{instrumentation key}* con la chiave di strumentazione della risorsa di Application Insights usata.

1. Selezionare **Salva**.

1. Aggiungere il codice XML seguente al file *App.config*, subito dopo la raccolta delle stringhe di connessione.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Sostituire *{instrumentation key}* con la chiave di strumentazione della risorsa di Application Insights usata.

   L'aggiunta di questi dati al file *App.config* consente di testare la connessione di Application Insights quando si esegue il progetto in locale. 

1. Salvare le modifiche.

### <a name="add-application-insights-logging-provider"></a>Aggiungere il provider di registrazione di Application Insights

1. Installare l'ultima versione 2.x stabile del pacchetto NuGet per il provider di registrazione di Application Insights, `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Installare l'ultima versione 4.x stabile del pacchetto NuGet per lo strumento di gestione configurazione .NET, `System.Configuration.ConfigurationManager`.

   Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Aprire *Program.cs* e aggiungere un'istruzione `using` per lo strumento di gestione configurazione:

   ```csharp
   using System.Configuration;
   ```

1. Sostituire il codice nel metodo `Main` con il codice seguente:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Questo codice apporta le modifiche seguenti:

   * Aggiunge il provider di registrazione di Application Insights con i [filtri](webjobs-sdk-how-to.md#log-filtering) predefiniti. In caso di esecuzione in locale, tutte le informazioni e i log di livello superiore verranno ora inviati sia alla console che ad Application Insights. 
   * Inserisce l'oggetto `LoggerFactory` in un blocco `using` affinché l'output di log venga scaricato alla chiusura dell'host. 

## <a name="test-application-insights-logging"></a>Testare la registrazione in Application Insights

In questa sezione si ripete l'esecuzione in locale per verificare che i dati di registrazione vengano ora inviati sia ad Application Insights che alla console.

1. Usare **Esplora server** in Visual Studio per creare un messaggio nella coda così come è stato fatto [in precedenza](#trigger-the-function-in-azure), immettendo però *Hello App Insights!* come testo del messaggio.

1. Eseguire il progetto.

   WebJobs SDK elabora il messaggio nella coda e nella finestra della console vengono visualizzati i log.

1. Chiudere la finestra della console.

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare alla risorsa di Application Insights.

1. Selezionare **Cerca**.

   ![Selezionare Cerca](./media/webjobs-sdk-get-started/select-search.png)

1. Se il messaggio *Hello App Insights!* non viene visualizzato, selezionare periodicamente **Aggiorna** per alcuni minuti. I log non vengono visualizzati immediatamente perché il client di Application Insights impiega tempo per scaricare i log che elabora.

   ![Log in Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Chiudere la finestra della console.

## <a name="deploy-as-a-webjob"></a>Eseguire la distribuzione come processo Web

In questa sezione si distribuisce il progetto come processo Web in un'app del servizio app [creata in precedenza](#create-app-service-app-and-application-insights-instance). Per testare il codice mentre viene eseguito in Azure, si attiverà una chiamata di funzione creando un messaggio nella coda.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Pubblica come processo Web di Azure**.

1. Nella finestra di dialogo **Aggiungi processo Web di Azure** selezionare **OK**.

   ![Aggiungi processo Web di Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio installa automaticamente un pacchetto NuGet per la pubblicazione del processo Web.

1. Nel passaggio **Profilo** della procedura guidata **Pubblica** selezionare **Servizio app di Microsoft Azure**.

   ![Finestra di dialogo Pubblica](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Nella finestra di dialogo **Servizio app** selezionare **gruppo di risorse > app del servizio app** e quindi **OK**.

   ![Finestra di dialogo Servizio app](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Nel passaggio **Connessione** della procedura guidata selezionare **Pubblica**.

## <a name="trigger-the-function-in-azure"></a>Attivare la funzione in Azure

1. Assicurarsi che l'esecuzione non sia in locale. Se è ancora aperta, chiudere la finestra della console. In caso contrario, l'istanza locale potrebbe elaborare per prima i messaggi creati nella coda.


1. Aggiornare la pagina **Coda** in Visual Studio. Il nuovo messaggio non sarà presente perché è stato elaborato dalla funzione eseguita nel servizio app di Azure.

   > [!TIP]
   > Quando si esegue il test in Azure, usare la [modalità di sviluppo](webjobs-sdk-how-to.md#jobhost-development-settings) in modo da richiamare immediatamente la funzione trigger della coda ed evitare i ritardi causati dal [backoff esponenziale del polling delle code](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visualizzare i log in Application Insights

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare alla risorsa di Application Insights.

1. Selezionare **Cerca**.

1. Se il messaggio *Hello Azure!* non viene visualizzato, selezionare periodicamente **Aggiorna** per alcuni minuti.

   Verranno visualizzati i log della funzione eseguita in un processo Web, incluso il testo *Hello Azure!* immesso nella sezione precedente.

## <a name="add-an-input-binding"></a>Aggiungere un'associazione di input

Le associazioni di input semplificano il codice per la lettura di dati. Per questo esempio, il messaggio nella coda sarà un nome di BLOB che verrà usato per trovare e leggere un BLOB in Archiviazione di Azure.

1. In *Functions.cs* sostituire il metodo `ProcessQueueMessage` con il codice seguente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   In questo codice, `queueTrigger` è un'[espressione di associazione](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) che viene risolta in un diverso valore in fase di esecuzione.  In fase di esecuzione corrisponde al contenuto del messaggio nella coda.

1. Aggiungere un'istruzione `using`:

   ```cs
   using System.IO;
   ```

1. Creare un contenitore BLOB nell'account di archiviazione.

   a. In **Esplora server** in Visual Studio, espandere il nodo dell'account di archiviazione, fare clic con il pulsante destro del mouse su **BLOB** e quindi scegliere **Crea contenitore BLOB**.

   b. Nella finestra di dialogo **Crea contenitore BLOB** immettere *container* come nome del contenitore e quindi fare clic su **OK**.

1. Caricare il file *Program.cs* nel contenitore BLOB. Questo file viene qui usato come esempio. È possibile caricare qualsiasi file di testo e creare un messaggio nella coda con il nome del file.

   a. In **Esplora server** fare doppio clic sul nodo del contenitore appena creato.

   b. Nella finestra **Contenitore** selezionare il pulsante **Carica**.

   ![Pulsante per il caricamento di BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Trovare e selezionare *Program.cs* e quindi selezionare **OK**.

1. Creare un messaggio nella coda creata in precedenza, con *Program.cs* come testo del messaggio.

   ![Messaggio nella coda per Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Eseguire il progetto.

   Il messaggio nella coda attiva la funzione, che legge quindi il BLOB e ne registra la lunghezza. L'output della console è simile al seguente:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

Le associazioni di output semplificano il codice per la scrittura di dati. Questo esempio modifica il precedente scrivendo una copia del BLOB anziché registrarne le dimensioni.

1. Sostituire il metodo `ProcessQueueMessage` con il codice seguente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Creare un altro messaggio nella coda con *Program.cs* come testo del messaggio.

1. Eseguire il progetto.

   Il messaggio nella coda attiva la funzione, che legge quindi il BLOB, ne registra la lunghezza e crea un nuovo BLOB. L'output della console è uguale, ma passando alla finestra del contenitore BLOB e selezionando **Aggiorna** verrà visualizzato un nuovo BLOB denominato *copy-Program.cs*.

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha illustrato come creare, eseguire e distribuire un progetto WebJobs SDK.

Per mostrare tutti gli elementi inclusi in un progetto WebJobs SDK, le istruzioni hanno previsto la creazione di un progetto da zero. Nella creazione del progetto successivo, tuttavia, valutare la possibilità di usare il modello **Processo Web di Azure** della categoria **Cloud**. Questo modello crea un progetto con i pacchetti NuGet e il codice di esempio già configurati. Si noti che potrebbe essere necessario modificare il codice di esempio per usare il nuovo framework di registrazione.

Per altre informazioni, vedere l'articolo su [come usare WebJobs SDK](webjobs-sdk-how-to.md).
