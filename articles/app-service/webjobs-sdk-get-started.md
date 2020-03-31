---
title: Introduzione a WebJobs SDK
description: Introduzione a WebJobs SDK per l'elaborazione in background guidata dagli eventi. Informazioni su come accedere ai dati nei servizi di Azure e di terze parti.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: bfbae282f9c383c19aae84a70dfc53f754bd9367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77592612"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introduzione ad Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo illustra come usare Visual Studio 2019 per creare un progetto Azure WebJobs SDK, eseguirlo in locale e quindi distribuirlo nel servizio [app di Azure.](overview.md) La versione 3.x di WebJobs SDK supporta sia le applicazioni console .NET Core che .NET Framework. Per altre informazioni sull'uso di WebJobs SDK, vedere [Come usare Azure WebJobs SDK per l'elaborazione in background basata](webjobs-sdk-how-to.md)su eventi.

In questo articolo viene illustrato come distribuire WebJobs come app console .NET Core.This article shows you how to deploy WebJobs as a .NET Core console app. Per distribuire WebJobs come app console di .NET Framework, vedere [WebJobs as .NET Framework console apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Se si è interessati a WebJobs SDK versione 2.x, che supporta solo .NET Framework, vedere [Sviluppare e distribuire WebJobs con Visual Studio - Servizio app di Azure](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Prerequisiti

* [Installare Visual Studio 2019](/visualstudio/install/) con il carico di lavoro di sviluppo di **Azure.Install** Visual Studio 2019 with the Azure development workload. Se si ha già Visual Studio ma non tale carico di lavoro, aggiungere il carico di lavoro selezionando **Strumenti > Ottieni strumenti e funzionalità**.

* È necessario disporre di un account Azure per pubblicare il progetto WebJobs SDK in Azure.You must have [an Azure account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) to publish your WebJobs SDK project to Azure.

## <a name="create-a-project"></a>Creare un progetto

1. In Visual Studio selezionare **Crea un nuovo progetto**.

2. Selezionare **App console (.NET Core)**.

3. Assegnare al progetto il nome *WebJobsSDKSample*, quindi scegliere **Crea**.

   ![Finestra di dialogo Nuovo progetto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pacchetti NuGet di processi Web

1. Installare l'ultima versione stabile `Microsoft.Azure.WebJobs.Extensions` 3.x del `Microsoft.Azure.WebJobs`pacchetto NuGet, che include .

     Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 3.0.2:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.2
     ```

## <a name="create-the-host"></a>Creare l'host

L'host è il contenitore di runtime per le funzioni che ascolta i trigger e chiama le funzioni. I passaggi seguenti creano [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)un host che implementa , ovvero l'host generico in ASP.NET Core.

1. In *Program.cs* aggiungere un'istruzione `using`:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Sostituire il metodo `Main` con il codice seguente:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

In ASP.NET Core, le configurazioni host [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) vengono impostate chiamando i metodi sull'istanza. Per altre informazioni, vedere [Host generico .NET](/aspnet/core/fundamentals/host/generic-host). Il metodo di estensione `ConfigureWebJobs` inizializza l'host WebJobs. In `ConfigureWebJobs`, inizializzare estensioni WebJobs specifiche e impostare le proprietà di tali estensioni.  

## <a name="enable-console-logging"></a>Abilitare la registrazione nella console

In questa sezione viene impostata la registrazione della console che utilizza il framework di [registrazione ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Installare l'ultima versione `Microsoft.Extensions.Logging.Console` stabile del pacchetto `Microsoft.Extensions.Logging`NuGet, che include .

   Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. In *Program.cs* aggiungere un'istruzione `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Chiamare [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) il [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)metodo su . Il [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) metodo aggiunge la registrazione della console alla configurazione.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Il metodo `Main` si presenta ora come segue:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Questo aggiornamento esegue le operazioni seguenti:

    * Disabilita la [registrazione nel dashboard](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Il dashboard è uno strumento di monitoraggio legacy e la registrazione nel dashboard non è consigliata per scenari di produzione con velocità effettiva elevata.
    * Aggiunge il provider della console con i [filtri](webjobs-sdk-how-to.md#log-filtering) predefiniti.

A questo punto è possibile aggiungere una funzione attivata dai messaggi in arrivo in una [coda di Archiviazione di Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Installare l'estensione dei binding di Archiviazione

A partire dalla versione 3.x, è necessario installare in modo esplicito l'estensione di associazione di archiviazione richiesta da WebJobs SDK. Nelle versioni precedenti, i binding di archiviazione erano inclusi nell'SDK.

1. Installare l'ultima versione stabile del pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) versione 3.x. 

    Ecco il comando **Console di gestione pacchetti** per la versione 3.0.4:Here's the Package Manager Console command for version 3.0.4:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.4
    ```

2. Nel `ConfigureWebJobs` metodo di estensione, chiamare il `AddAzureStorage` metodo sull'istanza [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) per inizializzare l'estensione di archiviazione. A questo punto il metodo `ConfigureWebJobs` avrà un aspetto simile all'esempio seguente:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Creare una funzione

1. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** > **nuovo elemento...**, scegliere **Classe**, assegnare al nuovo file di classe *C, Functions.cs*, quindi scegliere **Aggiungi**.

1. In Functions.cs sostituire il modello generato con il codice seguente:

   ```cs
   using Microsoft.Azure.WebJobs;
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

   Il parametro `message` non deve necessariamente essere una stringa. È possibile eseguire l'associazione anche a un oggetto JSON, una matrice di byte o un oggetto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage). [Vedere l'utilizzo del trigger della coda](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Ogni tipo di associazione (come code, BLOB o tabelle) presenta un diverso set di tipi di parametri a cui è possibile eseguire l'associazione.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

L'emulatore di archiviazione di Azure eseguito in locale non include tutte le funzionalità necessarie a WebJobs SDK. In questa sezione si crea un account di archiviazione in Azure e si configura il progetto per usarlo. Se si dispone già di un account di archiviazione, andare al passaggio 6.If you already have a storage account, skip down to step 6.

1. Aprire **Esplora server** in Visual Studio e accedere ad Azure. Fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Connessione alla sottoscrizione di Microsoft Azure**.

   ![Accedere ad Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Nel nodo **Azure** in **Esplora server** fare clic con il pulsante destro del mouse su **Archiviazione** e quindi scegliere **Crea account di archiviazione**.

   ![Menu per creare un account di archiviazione](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Nella finestra di dialogo **Crea account di archiviazione** immettere un nome univoco per l'account di archiviazione.

1. Scegliere la stessa **area** in cui è stata creata l'app del servizio app o un'area vicina all'utente.

1. Selezionare **Crea**.

   ![Creare l'account di archiviazione](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Nel nodo **Archiviazione** in **Esplora server** selezionare il nuovo account di archiviazione. Nella finestra **Proprietà** selezionare i puntini di sospensione (**...**) a destra del campo del valore di **Stringa di connessione**.

   ![Puntini di sospensione di Stringa di connessione](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copiare la stringa di connessione e salvare questo valore in una posizione da cui possa essere copiato di nuovo facilmente.

   ![Copiare la stringa di connessione](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configurare l'archiviazione per l'esecuzione in locale

WebJobs SDK cerca la stringa di connessione di archiviazione nelle impostazioni applicazione in Azure. In caso di esecuzione in locale, questo valore viene cercato nel file di configurazione locale o nelle variabili di ambiente.

1. Fare clic con il pulsante destro del mouse sul progetto, selezionare **Aggiungi** > **nuovo elemento...**, scegliere File di **configurazione JSON JavaScript**, assegnare al nuovo file il nome *appsettings.json* il nome e selezionare **Aggiungi**. 

1. Nel nuovo file aggiungere `AzureWebJobsStorage` un campo, come nell'esempio seguente:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Sostituire *{storage connection string}* con la stringa di connessione copiata in precedenza.

1. Selezionare il file *appsettings.json* in Esplora soluzioni e nella finestra **Proprietà** impostare **Copia nella directory** di output su Copia se **più recente**.

La stessa impostazione app della stringa di connessione verrà aggiunta in seguito nell'app in Servizio app di Azure.

## <a name="test-locally"></a>Test in locale

In questa sezione si compila e si esegue il progetto in locale e si attiva la funzione creando un messaggio nella coda.

1. Premete **Ctrl e F5** per eseguire il progetto.

   La console mostra che il runtime ha trovato la funzione ed è in attesa che venga attivata da messaggi nella coda. L'output seguente viene generato dall'host v3.x:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Chiudere la finestra della console.

1. In **Esplora server** in Visual Studio, espandere il nodo del nuovo account di archiviazione e quindi fare clic con il pulsante destro del mouse su **Code**.

1. Scegliere **Crea coda**.

1. Immettere *queue* come nome della coda e quindi selezionare **OK**.

   ![Crea coda](./media/webjobs-sdk-get-started/create-queue.png)

1. Fare clic con il pulsante destro del mouse sul nodo della nuova coda e quindi scegliere **Visualizza coda**.

1. Selezionare l'icona **Aggiungi messaggio**.

   ![Crea coda](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Nella finestra di dialogo **Aggiungi messaggio** immettere *Hello World!* come **Testo messaggio** e quindi selezionare **OK**. Ora è presente un messaggio nella coda.

   ![Crea coda](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Eseguire di nuovo il progetto.

   Dato che è stato usato l'attributo `QueueTrigger` nella funzione `ProcessQueueMessage`, il runtime di WeJobs SDK avviato è in ascolto di messaggi nella coda, trova un nuovo messaggio nella coda denominata *queue* e chiama la funzione.

   A causa del [backoff esponenziale del polling delle code](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm), prima che il runtime trovi il messaggio e richiami la funzione possono trascorrere fino a 2 minuti. Questo tempo di attesa può essere ridotto con l'esecuzione in [modalità di sviluppo](webjobs-sdk-how-to.md#host-development-settings).

   L'output della console è simile al seguente:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Chiudere la finestra della console. 

1. Tornare alla finestra Coda e aggiornarla. Il messaggio non è più presente, poiché è stato elaborato dalla funzione in esecuzione in locale. 

## <a name="add-application-insights-logging"></a>Aggiungere la registrazione in Application Insights

Quando il progetto viene eseguito in Azure, non è possibile monitorare l'esecuzione della funzione visualizzando l'output della console. La soluzione di monitoraggio consigliata è [Application Insights](../azure-monitor/app/app-insights-overview.md). Per altre informazioni, vedere [Monitorare Funzioni di Azure](../azure-functions/functions-monitoring.md).

In questa sezione si configura la registrazione in Application Insights prima della distribuzione in Azure eseguendo queste attività:

* Assicurarsi che siano disponibili un'app del servizio app e un'istanza di Application Insights da usare.
* Configurare l'app del servizio app per l'uso dell'istanza di Application Insights e dell'account di archiviazione creato in precedenza.
* Configurare il progetto per la registrazione in Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Creare un'app del servizio app e un'istanza di Application Insights

1. Se non si ha già un'app del servizio app utilizzabile, [crearne una](app-service-web-get-started-dotnet-framework.md). Quando si crea l'app, è anche possibile creare una risorsa di Application Insights connessa. Quando si esegue questa operazione, `APPINSIGHTS_INSTRUMENTATIONKEY` viene impostato automaticamente nell'app.

1. Se non si ha già una risorsa di Application Insights utilizzabile, [crearne una](../azure-monitor/app/create-new-resource.md ). Impostare **Tipo di applicazione** su **Generale** e ignorare le sezioni successive a **Eseguire una copia della chiave di strumentazione**.

1. Se si ha già una risorsa di Application Insights da usare, [copiare la chiave di strumentazione](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione 

1. In **Esplora server** in Visual Studio, espandere il nodo **Servizio app** sotto **Azure**.

1. Espandere il gruppo di risorse in cui si trova l'app del servizio app e quindi fare clic con il pulsante destro del mouse sull'app del servizio app.

1. Scegliere **Visualizza impostazioni**.

1. Nella casella **Stringhe di connessione** aggiungere la voce seguente.

   |Nome  |Stringa di connessione  |Tipo di database|
   |---------|---------|------|
   |AzureWebJobsStorage | {stringa di connessione di archiviazione copiata in precedenza}|Personalizzato|

1. Se la casella **Impostazioni applicazione** non contiene una chiave di strumentazione di Application Insights, aggiungere quella copiata in precedenza. La presenza o meno della chiave di strumentazione dipende da come è stata creata l'app del servizio app.

   |Nome  |valore  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

1. Sostituire *{instrumentation key}* con la chiave di strumentazione della risorsa di Application Insights usata.

1. Selezionare **Salva**.

1. Aggiungere la connessione di Application Insights al progetto in modo da poterla eseguire in locale. Nel file *appsettings.json* aggiungere un campo `APPINSIGHTS_INSTRUMENTATIONKEY`, come nell'esempio seguente:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Sostituire *{instrumentation key}* con la chiave di strumentazione della risorsa di Application Insights usata.

1. Salvare le modifiche.

### <a name="add-application-insights-logging-provider"></a>Aggiungere il provider di registrazione di Application Insights

Per poter sfruttare la registrazione di [Application Insights](../azure-monitor/app/app-insights-overview.md), aggiornare il codice di registrazione per eseguire le operazioni seguenti:

* Aggiungere un provider di registrazione di Application Insights con i [filtri](webjobs-sdk-how-to.md#log-filtering) predefiniti. In caso di esecuzione in locale, tutte le informazioni e i log di livello superiore vengono inviati sia alla console che ad Application Insights.
* Inserire l'oggetto [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) in un `using` blocco per garantire che l'output del log venga scaricato alla chiusura dell'host.

1. Installare l'ultima versione 3.x stabile del pacchetto NuGet per il provider di registrazione di Application Insights, `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Di seguito è riportato il comando di **Console di Gestione pacchetti** per la versione 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Aprire *Program.cs* e sostituire il codice nel metodo `Main` con il codice seguente:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Questo aggiunge il provider di Application Insights alla registrazione, usando la chiave aggiunta in precedenza alle impostazioni dell'app.

## <a name="test-application-insights-logging"></a>Testare la registrazione in Application Insights

In questa sezione si ripete l'esecuzione in locale per verificare che i dati di registrazione vengano ora inviati sia ad Application Insights che alla console.

1. Utilizzare **Esplora server** in Visual Studio per creare un messaggio della coda come in [precedenza,](#test-locally)ad eccezione di immettere *Hello App Insights!* come testo del messaggio.

1. Eseguire il progetto.

   WebJobs SDK elabora il messaggio della coda e i registri vengono visualizzati nella finestra della console.

1. Chiudere la finestra della console.

1. Passare al portale di Azure per visualizzare la risorsa Application Insights.Go to the [Azure portal](https://portal.azure.com/) to view your Application Insights resource. Cercare e selezionare **Application Insights**.

1. Scegliere l'istanza di Application Insights.Choose your Application Insights instance.

1. Selezionare **Cerca**.

   ![Selezionare Cerca](./media/webjobs-sdk-get-started/select-search.png)

1. Se il messaggio *Hello App Insights!* non viene visualizzato, selezionare periodicamente **Aggiorna** per alcuni minuti. I log non vengono visualizzati immediatamente perché il client Application Insights impiega un po' di tempo per scaricare i log che elabora.

   ![Log in Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Chiudere la finestra della console.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Distribuire in AzureDeploy to Azure

Durante la distribuzione, si crea un'istanza del servizio app in cui eseguire le funzioni. Quando si pubblica un'app console .NET Core nel servizio app in Azure, questa viene eseguita automaticamente come WebJob.When you publish a .NET Core console app to App Service in Azure, it automatically gets run as a WebJob. Per ulteriori informazioni sulla pubblicazione, vedere [Sviluppare e distribuire WebJobs con Visual Studio.](webjobs-dotnet-deploy-vs.md)

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Attivare la funzione in Azure

1. Assicurarsi che l'esecuzione non sia in locale. Se è ancora aperta, chiudere la finestra della console. In caso contrario, l'istanza locale potrebbe elaborare per prima i messaggi creati nella coda.

1. Nella pagina **Coda** in Visual Studio aggiungere un messaggio alla coda come indicato in precedenza.

1. Aggiornare la pagina **Coda**. Il nuovo messaggio non sarà presente perché è stato elaborato dalla funzione eseguita in Azure.

   > [!TIP]
   > Quando si esegue il test in Azure, usare la [modalità di sviluppo](webjobs-sdk-how-to.md#host-development-settings) in modo da richiamare immediatamente la funzione trigger della coda ed evitare i ritardi causati dal [backoff esponenziale del polling delle code](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm).

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

   In questo codice, `queueTrigger` è un'[espressione di associazione](../azure-functions/functions-bindings-expressions-patterns.md) che viene risolta in un diverso valore in fase di esecuzione.  In fase di esecuzione corrisponde al contenuto del messaggio nella coda.

1. Aggiungere un'istruzione `using`:

   ```cs
   using System.IO;
   ```

1. Creare un contenitore BLOB nell'account di archiviazione.

   a. In **Esplora server** in Visual Studio, espandere il nodo dell'account di archiviazione, fare clic con il pulsante destro del mouse su **BLOB** e quindi scegliere **Crea contenitore BLOB**.

   b. Nella finestra di dialogo **Crea contenitore BLOB** immettere *container* come nome del contenitore e quindi fare clic su **OK**.

1. Caricare il file *Program.cs* nel contenitore BLOB. Questo file viene qui usato come esempio. È possibile caricare qualsiasi file di testo e creare un messaggio nella coda con il nome del file.

   a. In **Esplora server** fare doppio clic sul nodo del contenitore creato.

   b. Nella finestra **Contenitore** selezionare il pulsante **Carica**.

   ![Pulsante per il caricamento di BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Trovare e selezionare *Program.cs* e quindi selezionare **OK**.

1. Creare un messaggio nella coda creata in precedenza, con *Program.cs* come testo del messaggio.

   ![Messaggio nella coda per Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Eseguire il progetto in locale.

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

Le associazioni di output semplificano il codice per la scrittura di dati. Questo esempio modifica il precedente scrivendo una copia del BLOB anziché registrarne le dimensioni. I binding di archiviazione BLOB sono inclusi nel pacchetto di estensioni di Archiviazione di Azure installato in precedenza.

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

1. Eseguire il progetto in locale.

   Il messaggio nella coda attiva la funzione, che legge quindi il BLOB, ne registra la lunghezza e crea un nuovo BLOB. L'output della console è uguale, ma passando alla finestra del contenitore BLOB e selezionando **Aggiorna** verrà visualizzato un nuovo BLOB denominato *copy-Program.cs*.

## <a name="republish-the-updates-to-azure"></a>Ripubblicare gli aggiornamenti in AzureRepublish the updates to Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella finestra di dialogo **Pubblica,** accertatevi che il profilo corrente sia selezionato, quindi scegliete **Pubblica**. I risultati della pubblicazione sono descritti in dettaglio nella finestra **Output.**
 
1. Verificare la funzione in Azure caricando nuovamente un file nel contenitore BLOB e aggiungendo un messaggio alla coda che è il nome del file caricato. Viene visualizzato il messaggio viene rimosso dalla coda e una copia del file creato nel contenitore BLOB. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare, eseguire e distribuire un progetto WebJobs SDK 3.x.

> [!div class="nextstepaction"]
> [Altre informazioni su WebJobs SDK](webjobs-sdk-how-to.md)
