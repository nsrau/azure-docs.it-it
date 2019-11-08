---
title: 'Esercitazione su MVC ASP.NET Core per Azure Cosmos DB: Sviluppo di applicazioni Web'
description: Esercitazione su MVC ASP.NET Core per creare un'applicazione Web MVC con Azure Cosmos DB. Si archivieranno documenti JSON e si accederà ai dati da un'app todo ospitata in Servizio app di Azure - Esercitazione dettagliata su MVC ASP.NET Core.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 1cfb7718c55920d817e1f87407fa1af590e9f006
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720901"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Esercitazione: Sviluppare un'applicazione Web MVC ASP.NET Core con Azure Cosmos DB usando .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Questa esercitazione illustra come usare Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web MVC ASP.NET ospitata in Azure. In questa esercitazione si userà .NET SDK V3. L'immagine seguente mostra la pagina Web che verrà creata usando l'esempio di questo articolo:

![Screenshot dell'applicazione Web MVC di elenco attività creata in questa esercitazione - Esercitazione dettagliata su ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se non si ha il tempo necessario per completare l'esercitazione, è possibile scaricare il progetto di esempio completo da [GitHub][GitHub].

Contenuto dell'esercitazione:

> [!div class="checklist"]
>
> * Creazione di un account di Azure Cosmos
> * Creazione di un'app MVC ASP.NET Core
> * Connessione dell'app ad Azure Cosmos DB
> * Esecuzione di operazioni di creazione, lettura, aggiornamento ed eliminazione dei dati

> [!TIP]
> Questa esercitazione presuppone già una certa esperienza nell'uso di MVC ASP.NET Core e di Servizio app di Azure. Se non si ha familiarità con ASP.NET Core, o con gli [strumenti richiesti come prerequisiti](#prerequisites), è consigliabile scaricare il progetto di esempio completo da [GitHub][GitHub], aggiungere i pacchetti NuGet necessari ed eseguirlo. Una volta compilato il progetto, è possibile leggere questo articolo per approfondire il codice nel contesto del progetto.

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare di avere le risorse seguenti:

* Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Tutti gli screenshot di questo articolo provengono da Microsoft Visual Studio Community 2019. Se si usa una versione diversa, le schermate e le opzioni potrebbero non corrispondere perfettamente. La soluzione dovrebbe funzionare se si soddisfano i prerequisiti.

## <a name="create-an-azure-cosmos-account"></a>Passaggio 1: Creare un account Azure Cosmos

La prima cosa da fare è creare un account di Azure Cosmos. Se si ha già un account dell'API SQL di Azure Cosmos DB o se si usa l'emulatore di Azure Cosmos DB, proseguire con il [Passaggio 2: Creare una nuova applicazione MVC ASP.NET](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Nella sezione successiva si creerà una nuova applicazione MVC ASP.NET Core.

## <a name="create-a-new-mvc-application"></a>Passaggio 2: Creare una nuova applicazione MVC ASP.NET Core

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.

1. In **Crea un nuovo progetto** trovare e selezionare **Applicazione Web ASP.NET Core** per C#. Selezionare **Avanti** per continuare.

   ![Creare un nuovo progetto di applicazione Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. In **Configura il nuovo progetto** assegnare al progetto il nome *todo* e selezionare **Crea**.

1. In **Crea una nuova applicazione Web ASP.NET Core**  scegliere **Applicazione Web (Model-View-Controller)** . Fare clic su **Crea** per continuare.

   Visual Studio crea un'applicazione MVC vota.

1. Selezionare **Debug** > **Avvia debug** o premere F5 per eseguire l'applicazione ASP.NET in locale.

## <a name="add-nuget-packages"></a>Passaggio 3: Aggiungere il pacchetto NuGet di Azure Cosmos DB al progetto

A questo punto la maggior parte del codice del framework MVC ASP.NET Core necessario per la soluzione è disponibile, quindi è il momento di aggiungere i pacchetti NuGet richiesti per la connessione ad Azure Cosmos DB.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.

1. In **Gestione pacchetti NuGet** cercare e selezionare **Microsoft.Azure.Cosmos**. Selezionare **Installa**.

   ![Installare il pacchetto NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio scarica e installa il pacchetto Azure Cosmos DB e le relative dipendenze.

   È anche possibile usare la **console di Gestione pacchetti** per installare il pacchetto NuGet. A questo scopo, selezionare **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**. Al prompt dei comandi, digitare il seguente comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Passaggio 4: Configurare l'applicazione MVC ASP.NET Core

Aggiungere ora i modelli, le visualizzazioni e i controller a questa applicazione MVC.

### <a name="add-a-model"></a> Aggiungere un modello

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli** e scegliere **Aggiungi** > **Classe**.

1. In **Aggiungi nuovo elemento** assegnare alla nuova classe il nome *Item.cs* e selezionare **Aggiungi**.

1. Sostituire il contenuto della classe *Item.cs* con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

Azure Cosmos DB usa JSON per lo spostamento e l'archiviazione di dati. È possibile usare l'attributo `JsonProperty` per controllare il modo in cui JSON serializza e deserializza gli oggetti. La classe `Item` dimostra l'attributo `JsonProperty`. Questo codice controlla il formato del nome della proprietà inserita in JSON. Rinomina inoltre la proprietà .NET `Completed`.

### <a name="add-views"></a>Aggiungere visualizzazioni

Creare ora le tre visualizzazioni seguenti.

* Aggiungere una visualizzazione elemento elenco
* Aggiungere una visualizzazione Nuovo elemento
* Aggiungere una visualizzazione Modifica elemento

#### <a name="AddItemIndexView"></a>Aggiungere una visualizzazione elemento elenco

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Visualizzazioni** e scegliere **Aggiungi** > **Nuova cartella**. Assegnare alla cartella il nome *Item*.

1. Fare clic con il pulsante destro del mouse sulla cartella **Item** vuota, quindi scegliere **Aggiungi** > **Visualizzazione**.

1. In **Add MVC View** (Aggiungi visualizzazione MVC) specificare i valori seguenti:

   * In **Nome visualizzazione** immettere *Indice*.
   * In **Modello** selezionare **Elenco**.
   * In **Classe modello** selezionare **Elemento (todo.Models)** .
   * Selezionare **Usa una pagina layout** e immettere *~/Views/Shared/_Layout.cshtml*.

   ![Screenshot della finestra di dialogo Aggiungi visualizzazione MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Dopo aver aggiunto questi valori, scegliere **Aggiungi** per creare automaticamente una nuova visualizzazione modello.

Al termine, Visual Studio apre il file *cshtml* creato. È possibile chiudere tale file in Visual Studio. Verrà usato più tardi.

#### <a name="AddNewIndexView"></a>Aggiungere una visualizzazione Nuovo elemento

In modo analogo alla creazione della visualizzazione per gli elementi elenco, creare una nuova visualizzazione per la creazione di elementi seguendo questa procedura:

1. In **Esplora soluzioni** fare di nuovo clic con il pulsante destro del mouse sulla cartella **Item** e scegliere **Aggiungi** > **Visualizzazione**.

1. In **Add MVC View** (Aggiungi visualizzazione MVC) apportare le modifiche seguenti:

   * In **Nome visualizzazione** immettere *Crea*.
   * In **Modello** selezionare **Crea**.
   * In **Classe modello** selezionare **Elemento (todo.Models)** .
   * Selezionare **Usa una pagina layout** e immettere *~/Views/Shared/_Layout.cshtml*.
   * Selezionare **Aggiungi**.

#### <a name="AddEditIndexView"></a>Aggiungere una visualizzazione Modifica elemento

Infine, aggiungere una visualizzazione per la modifica di elementi seguendo questa procedura:

1. In **Esplora soluzioni** fare di nuovo clic con il pulsante destro del mouse sulla cartella **Item** e scegliere **Aggiungi** > **Visualizzazione**.

1. In **Add MVC View** (Aggiungi visualizzazione MVC) apportare le modifiche seguenti:

   * Nella casella **Nome visualizzazione** digitare *Edit*.
   * Nella casella **Modello** selezionare **Edit**.
   * Nella casella **Classe modello** selezionare **Item (todo.Models)** .
   * Selezionare **Usa una pagina layout** e immettere *~/Views/Shared/_Layout.cshtml*.
   * Selezionare **Aggiungi**.

Dopo aver completato questi passaggi, chiudere tutti i documenti con estensione *cshtml* in Visual Studio. Si tornerà a queste visualizzazioni in un secondo momento.

### <a name="add-a-controller"></a>Aggiungere un controller

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller** e scegliere **Aggiungi** > **Controller**.

1. In **Aggiungi scaffolding** selezionare **Controller MVC - Vuoto** e quindi **Aggiungi**.

   ![Selezionare Controller MVC - Vuoto in Aggiungi scaffolding](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Assegnare al controller il nome *ItemController*.

1. Sostituire il contenuto di *ItemController.cs* con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

L'attributo **ValidateAntiForgeryToken** viene usato qui per proteggere l'applicazione da attacchi di falsificazione (CSRF) richiesta tra siti. Le visualizzazioni dovrebbero funzionare anche con questo token antifalsificazione. Per altre informazioni ed esempi, vedere l'articolo relativo alla [prevenzione delle richieste intersito false (CSRF) nell'applicazione MVC ASP.NET][Preventing Cross-Site Request Forgery]. Il codice sorgente fornito in [GitHub][GitHub] permette l'implementazione completa.

Viene usato anche l'attributo **Bind** nel parametro del metodo come protezione dagli attacchi di overposting. Per altre informazioni, vedere [Esercitazione: Implementare funzionalità di creazione, lettura, aggiornamento ed eliminazione con Entity Framework in MVC ASP.NET][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Passaggio 5: Connettersi ad Azure Cosmos DB

Dopo aver impostato le funzionalità standard di MVC, aggiungere il codice per connettersi ad Azure Cosmos DB ed eseguire operazioni di creazione, lettura, aggiornamento ed eliminazione.

### <a name="perform-crud-operations"></a>Eseguire operazioni di creazione, lettura, aggiornamento ed eliminazione dei dati

Aggiungere prima di tutto una classe che contiene la logica per connettersi e usare Azure Cosmos DB. Per questa esercitazione questa logica verrà incapsulata in una classe denominata `CosmosDBService` e in un'interfaccia denominata `ICosmosDBService`. Il servizio esegue le operazioni di creazione, lettura, aggiornamento ed eliminazione. Esegue inoltre operazioni di lettura dei feed, ad esempio elenca gli elementi incompleti e crea, modifica ed elimina elementi.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Nuova cartella**. Assegnare alla cartella il nome *Services*.

1. Fare clic con il pulsante destro del mouse sulla cartella **Services** e scegliere **Aggiungi** > **Classe**. Assegnare alla nuova classe il nome *CosmosDBService* e scegliere **Aggiungi**.

1. Sostituire il contenuto del file *CosmosDBService.cs* con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Ripetere i due passaggi precedenti, ma questa volta usare il nome *ICosmosDBService* e il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. Nel gestore **ConfigureServices** aggiungere la riga seguente:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    Il codice del passaggio precedente riceve `CosmosClient` come parte del costruttore. Seguendo la pipeline di ASP.NET Core, è necessario passare al file *Startup.cs* del progetto. Il codice di questo passaggio inizializza il client in base alla configurazione come istanza singleton da inserire tramite [inserimento delle dipendenze in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. All'interno dello stesso file aggiungere il metodo **InitializeCosmosClientInstanceAsync** seguente, che legge la configurazione e inizializza il client.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Definire la configurazione nel file *appsettings.json* del progetto. Aprire il file e aggiungere una sezione denominata **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Se si esegue l'applicazione, la pipeline di ASP.NET Core crea istanze di **CosmosDbService** e mantiene una singola istanza come singleton. Quando **ItemController** elabora le richieste sul lato client, riceve questa singola istanza e la usa per le operazioni di creazione, lettura, aggiornamento ed eliminazione.

Se compilato ed eseguito ora, il progetto avrà un aspetto simile al seguente:

![Screenshot dell'applicazione Web di elenco attività creata in questa esercitazione sul database](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>Passaggio 6: Eseguire l'applicazione in locale

Per testare l'applicazione nel computer locale, eseguire la procedura seguente:

1. Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug. L'applicazione verrà compilata e verrà avviato un browser con la pagina di griglia vuota osservata sopra:

   ![Screenshot dell'applicazione Web di elenco attività creata in questa esercitazione](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Selezionare il collegamento **Crea nuovo** e aggiungere valori nei campi **Nome** e **Descrizione**. Lasciare deselezionata la casella di controllo **Completato**. Se la si seleziona, l'app aggiunge il nuovo elemento con lo stato completato. L'elemento non viene più visualizzato nell'elenco iniziale.

1. Selezionare **Create** (Crea). Si torna nella visualizzazione **Crea** e l'elemento viene visualizzato nell'elenco. È possibile aggiungere alcuni altri elementi all'elenco **To-Do**.

    ![Screenshot della visualizzazione Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Selezionare **Modifica** accanto a un **elemento** nell'elenco. L'app apre la visualizzazione **Modifica** in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag **Completato**. Se si seleziona **Completato** e quindi **Salva**, l'app visualizza l'**elemento** come completato nell'elenco.

   ![Screenshot della visualizzazione Index con la casella Completed selezionata](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Verificare lo stato dei dati nel servizio Azure Cosmos DB usando [Cosmos Explorer](https://cosmos.azure.com) o Esplora dati dell'emulatore di Azure Cosmos DB.

1. Una volta testata l'app, premere CTRL+F5 per arrestarne il debug. È ora possibile distribuire l'app.

## <a name="deploy-the-application-to-azure"></a>Passaggio 7: Distribuire l'applicazione

Poiché ora è completa e funziona correttamente con Azure Cosmos DB, è possibile distribuire questa app Web nel Servizio app di Azure.  

1. Per pubblicare questa applicazione, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.

1. In **Selezionare una destinazione di pubblicazione** selezionare **Servizio app**.

1. Per usare un profilo esistente di Servizio app, scegliere **Seleziona esistente** e quindi **Pubblica**.

1. In **Servizio app** selezionare una **sottoscrizione**. Usare il filtro di **visualizzazione** per eseguire l'ordinamento in base al gruppo di risorse o al tipo di risorsa.

1. Trovare il proprio profilo e quindi selezionare **OK**. Cercare quindi il Servizio app di Azure richiesto e scegliere **OK**.

   ![Finestra di dialogo del servizio app in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

In alternativa, creare un nuovo profilo:

1. Come nella procedura precedente, fare clic con il pulsante destro del mouse in **Esplora soluzioni**, quindi scegliere **Pubblica**.
  
1. In **Selezionare una destinazione di pubblicazione** selezionare **Servizio app**.

1. In **Selezionare una destinazione di pubblicazione** selezionare **Crea nuovo** e quindi **Pubblica**.

1. In **Servizio app** immettere il nome dell'app Web e la sottoscrizione, il gruppo di risorse e il piano di hosting appropriati, quindi selezionare **Crea**.

   ![Finestra di dialogo Crea servizio app in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Dopo alcuni secondi Visual Studio pubblica l'applicazione Web e avvia un browser in cui è possibile vedere il progetto in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come compilare un'applicazione Web MVC ASP.NET Core. L'applicazione può accedere ai dati archiviati in Azure Cosmos DB. È ora possibile continuare con queste risorse:

* [Partitioning in Azure Cosmos DB](./partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Introduzione alle query SQL](./how-to-sql-query.md)
* [Come modellare e partizionare i dati in Azure Cosmos DB usando un esempio reale](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
