---
title: 'Esercitazione su MVC ASP.NET Core per Azure Cosmos DB: Sviluppo di applicazioni Web'
description: Esercitazione su MVC ASP.NET Core per creare un'applicazione Web MVC con Azure Cosmos DB. Si archivieranno documenti JSON e si accederà ai dati da un'app todo ospitata in Servizio app di Azure - Esercitazione dettagliata su MVC ASP.NET Core.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 9824e1468604763834e37abe94290d68d81077ab
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020118"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Esercitazione: Sviluppare un'applicazione Web MVC ASP.NET Core con Azure Cosmos DB usando .NET SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Questa esercitazione illustra come usare Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web MVC ASP.NET ospitata in Azure. In questa esercitazione si userà .NET SDK V3. L'immagine seguente mostra la pagina Web che verrà compilata usando l'esempio di questo articolo:
 
![Screenshot dell'applicazione Web MVC di elenco attività creata in questa esercitazione - Esercitazione dettagliata su ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se non si ha il tempo necessario per completare l'esercitazione, è possibile scaricare il progetto di esempio completo da [GitHub][GitHub].

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creazione di un account di Azure Cosmos
> * Creazione di un'app MVC ASP.NET Core
> * Connessione dell'app ad Azure Cosmos DB 
> * Esecuzione di operazioni CRUD sui dati

> [!TIP]
> Questa esercitazione presuppone già una certa esperienza nell'uso di MVC ASP.NET Core e di Servizio app di Azure. Se non si ha alcuna esperienza con ASP.NET Core o con gli [strumenti richiesti come prerequisiti](#prerequisites), è consigliabile scaricare il progetto di esempio completo da [GitHub][GitHub], aggiungere i pacchetti NuGet necessari ed eseguirlo. Una volta compilato il progetto, è possibile leggere questo articolo per approfondire il codice nel contesto del progetto.

## <a name="prerequisites"></a>Prerequisiti 

Prima di seguire le istruzioni di questo articolo, verificare di disporre delle risorse seguenti:

* **Un account Azure attivo:** Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Tutti gli screenshot in questo articolo sono stati ottenuti usando Microsoft Visual Studio Community 2019. Se il sistema in uso è configurato con una versione diversa, è probabile che le schermate e le opzioni non siano interamente corrispondenti, ma se si soddisfano i prerequisiti precedenti la soluzione dovrebbe funzionare comunque.

## <a name="create-an-azure-cosmos-account"></a>Passaggio 1: Creare un account Azure Cosmos

La prima cosa da fare è creare un account di Azure Cosmos. Se si ha già un account API SQL di Azure Cosmos DB o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile passare direttamente alla sezione [Creare una nuova applicazione MVC ASP.NET](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Nella sezione successiva si creerà una nuova applicazione MVC ASP.NET Core. 

## <a name="create-a-new-mvc-application"></a>Passaggio 2: Creare una nuova applicazione MVC ASP.NET Core

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto** .

2. Nella finestra **Nuovo progetto** usare la casella di input **Cerca modelli** per cercare "Web" e quindi selezionare **Applicazione Web ASP.NET Core**. 

   ![Creare un nuovo progetto di applicazione Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Nella casella **Nome** digitare il nome del progetto. Questa esercitazione usa il nome "todo". Se si sceglie di usare un nome diverso, ogni volta che in questa esercitazione viene menzionato lo spazio dei nomi todo occorre modificare gli esempi di codice forniti sostituendo todo con il nome scelto per l'applicazione. 

4. Selezionare **Sfoglia** per passare alla cartella in cui si vuole creare il progetto. Selezionare **Create** (Crea). 

5. Viene visualizzata la finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**. Nell'elenco di modelli selezionare **Applicazione Web (MVC)** .

6. Selezionare **Crea** e lasciare che Visual Studio prepari lo scaffolding attorno al modello MVC ASP.NET Core vuoto. 

7. Al termine della creazione dell'applicazione MVC boilerplate in Visual Studio, sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.

## <a name="add-nuget-packages"></a>Passaggio 3: Aggiungere il pacchetto NuGet di Azure Cosmos DB al progetto

A questo punto la maggior parte del codice del framework MVC ASP.NET Core necessario per la soluzione è disponibile, quindi è il momento di aggiungere i pacchetti NuGet richiesti per la connessione ad Azure Cosmos DB.

1. Azure Cosmos DB .NET SDK è distribuito come pacchetto NuGet. Per ottenere il pacchetto NuGet di Visual Studio, usare Gestione pacchetti NuGet in Visual Studio facendo clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliendo **Gestisci pacchetti NuGet**.
   
   ![Screenshot delle opzioni del menu di scelta rapida per il progetto applicazione Web in Esplora soluzioni, con Gestisci pacchetti NuGet evidenziato.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet** . Nella casella **Sfoglia** di NuGet digitare **Microsoft.Azure.Cosmos**. Dai risultati installare la libreria **Microsoft.Azure.Cosmos**. Il pacchetto Azure Cosmos DB verrà scaricato e installato insieme alle relative dipendenze. Selezionare **Accetto** nella finestra **Accettazione della licenza** per completare l'installazione.
   
   In alternativa, è possibile usare la console di Gestione pacchetti per installare il pacchetto NuGet. A questo scopo, scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi selezionare **Console di Gestione pacchetti**. Al prompt dei comandi, digitare il seguente comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Dopo l'installazione del pacchetto, il progetto di Visual Studio dovrebbe contenere il riferimento della libreria a Microsoft.Azure.Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Passaggio 4: Configurare l'applicazione MVC ASP.NET Core

A questo punto occorre aggiungere i modelli, le visualizzazioni e i controller all'applicazione MVC:

* [Aggiungere un modello](#add-a-model).
* [Aggiungere un controller](#add-a-controller).
* [Aggiungere visualizzazioni](#add-views).

### <a name="add-a-model"></a> Aggiungere un modello

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi selezionare **Classe**. Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento** .

1. Assegnare alla nuova classe il nome **Item.cs** e scegliere **Aggiungi**. 

1. Sostituire quindi il codice nella classe "Item.cs" con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   I dati archiviati in Azure Cosmos DB vengono passati sulla rete cablata e archiviati come JSON. Per controllare il modo in cui gli oggetti vengono serializzati/deserializzati da JSON.NET, è possibile usare l'attributo **JsonProperty**, come mostrato nella classe **Item** creata in precedenza. Non solo è possibile controllare il formato del nome della proprietà usata in JSON, ma anche rinominare le proprietà .NET come è stato fatto con la proprietà **Completed**. 

### <a name="add-views"></a>Aggiungere visualizzazioni

A questo punto occorre creare le tre visualizzazioni seguenti: 

* [Aggiungere una visualizzazione elemento elenco](#AddItemIndexView).
* [Aggiungere una visualizzazione Nuovo elemento](#AddNewIndexView).
* [Aggiungere una visualizzazione Modifica elemento](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Aggiungere una visualizzazione elemento elenco

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, fare clic con il pulsante destro del mouse sulla cartella **Item** vuota creata da Visual Studio quando è stato aggiunto **ItemController**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.
   
   ![Screenshot di Esplora soluzioni con la cartella Item creata da Visual Studio con i comandi Aggiungi visualizzazione evidenziati](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Nella finestra di dialogo **Aggiungi visualizzazione** aggiornare i valori seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Index***.
   * Nella casella **Modello** selezionare ***Elenco***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
     
   ![Screenshot con la finestra di dialogo Aggiungi visualizzazione](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Dopo aver aggiunto questi valori, scegliere **Aggiungi** per creare automaticamente una nuova visualizzazione modello. Al termine si aprirà il file cshtml creato. È possibile chiudere questo file in Visual Studio, in quanto servirà più avanti.

#### <a name="AddNewIndexView"></a>Aggiungere una visualizzazione Nuovo elemento

In modo analogo alla creazione della visualizzazione per gli elementi elenco, creare una nuova visualizzazione per la creazione di elementi seguendo questa procedura:

1. In **Esplora soluzioni** fare di nuovo clic con il pulsante destro del mouse sulla cartella **Item**, scegliere **Aggiungi** e quindi selezionare **Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** aggiornare i valori seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Create***.
   * Nella casella **Modello** selezionare ***Create***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
   * Selezionare **Aggiungi**.
   
#### <a name="AddEditIndexView"></a>Aggiungere una visualizzazione Modifica elemento

Infine, aggiungere una visualizzazione per la modifica di elementi seguendo questa procedura:

1. In **Esplora soluzioni** fare di nuovo clic con il pulsante destro del mouse sulla cartella **Item**, scegliere **Aggiungi** e quindi selezionare **Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Edit***.
   * Nella casella **Modello** selezionare ***Edit***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
   * Selezionare **Aggiungi**.

Al termine, chiudere tutti i documenti con estensione cshtml in Visual Studio. Si tornerà a queste visualizzazioni in un secondo momento.

### <a name="add-a-controller"></a>Aggiungere un controller

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi selezionare **Controller**. Verrà visualizzata la finestra di dialogo **Aggiungi scaffolding** .

1. Selezionare **Controller MVC - Vuoto** e quindi scegliere **Aggiungi**.

   ![Screenshot della finestra di dialogo Aggiungi scaffolding con l'opzione Controller MVC - Vuoto evidenziata](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Assegnare al nuovo controller il nome **ItemController** e sostituire il codice incluso nel file con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   L'attributo **ValidateAntiForgeryToken** viene usato qui per proteggere l'applicazione da attacchi di falsificazione (CSRF) richiesta tra siti. Oltre ad aggiungere questo attributo, è necessario che le visualizzazioni usino questo token antifalsificazione. Per altre informazioni in proposito e per alcuni esempi sulla corretta implementazione, vedere [Prevenzione degli attacchi di falsificazione (CSRF) richiesta tra siti nell'applicazione ASP.NET MVC][Preventing Cross-Site Request Forgery]. Il codice sorgente fornito in [GitHub][GitHub] permette l'implementazione completa.

   Viene usato anche l'attributo **Bind** nel parametro del metodo come protezione dagli attacchi di overposting. Per informazioni dettagliate, vedere [Implementazione della funzionalità CRUD di base con Entity Framework nell'applicazione ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Passaggio 5: Connettersi ad Azure Cosmos DB 

Sistemate le caratteristiche standard di MVC, è il momento di aggiungere il codice per connettersi ad Azure Cosmos DB ed eseguire le operazioni CRUD. 

### <a name="perform-crud-operations"></a> Eseguire operazioni CRUD sui dati

La prima cosa da fare è aggiungere una classe che contenga la logica per connettersi e usare Azure Cosmos DB. Per questa esercitazione questa logica verrà incapsulata in una classe denominata `CosmosDBService` e in un'interfaccia denominata `ICosmosDBService`. Questo servizio esegue le operazioni CRUD e di lettura di feed, come elencare gli elementi incompleti e creare, modificare ed eliminare gli elementi. 

1. In **Esplora soluzioni** creare una nuova cartella denominata **Services** nel progetto.

1. Fare clic con il pulsante destro del mouse sulla cartella **Services**, scegliere **Aggiungi** e quindi selezionare **Classe**. Assegnare alla nuova classe il nome **CosmosDBService** e scegliere **Aggiungi**.

1. Aggiungere il codice seguente alla classe **CosmosDBService** e sostituire il codice incluso nel file con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Ripetere i passaggi 2 e 3, ma questa volta per una classe denominata **ICosmosDBService** e aggiungere il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Il codice precedente riceve `CosmosClient` come parte del costruttore. Seguendo la pipeline di ASP.NET Core, è necessario passare al file **Startup.cs** del progetto e inizializzare il client in base alla configurazione come istanza singleton da inserire tramite l'[inserimento delle dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Nel gestore **ConfigureServices** si definisce:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. Nello stesso file viene definito il metodo helper **InitializeCosmosClientInstanceAsync**, che leggerà la configurazione e inizializzerà il client.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. La configurazione viene definita nel file **appsettings.json** del progetto. Aprirlo e aggiungere una sezione denominata **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Se ora si esegue l'applicazione, la pipeline di ASP.NET Core, creerà un'istanza di **CosmosDbService** e manterrà una sola istanza come singleton. Quando **ItemController** viene usato per elaborare le richieste lato client, riceverà questa istanza singola e potrà usarla per eseguire operazioni CRUD.

Se compilato ed eseguito ora, il progetto avrà un aspetto simile al seguente:

![Screenshot dell'applicazione Web di elenco attività creata in questa esercitazione sul database](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Passaggio 6: Eseguire l'applicazione in locale

Per testare l'applicazione nel computer locale, eseguire la procedura seguente:

1. Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug. L'applicazione verrà compilata e verrà avviato un browser con la pagina di griglia vuota osservata sopra:
   
   ![Screenshot dell'applicazione Web di elenco attività creata in questa esercitazione](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Fare clic sul collegamento **Crea nuovo** e specificare i valori dei campi **Nome** e **Descrizione**. Lasciare deselezionata la casella di controllo **Completato**. In caso contrario, il nuovo elemento viene aggiunto con lo stato completato e non viene visualizzato nell'elenco iniziale.
   
3. Fare clic su **Crea** per tornare di nuovo alla visualizzazione **Index** e visualizzare l'elemento nell'elenco. È possibile aggiungere alcuni altri elementi all'elenco di azioni.

    ![Screenshot della visualizzazione Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Fare clic su **Edit** accanto a un oggetto **Item** nell'elenco per tornare alla visualizzazione **Edit**, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag **Completed**. Se si seleziona il flag **Completed** e si fa clic su **Salva**, l'oggetto **Item** verrà visualizzato come completato nell'elenco.
   
   ![Screenshot della visualizzazione Index con la casella Completed selezionata](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. È possibile verificare in qualsiasi momento lo stato dei dati nel servizio Azure Cosmos DB usando [Cosmos Explorer](https://cosmos.azure.com) o Esplora dati dell'emulatore di Azure Cosmos DB.

6. Una volta testata l'app, premere CTRL+F5 per arrestarne il debug. È ora possibile distribuire l'app.

## <a name="deploy-the-application-to-azure"></a>Passaggio 7: Distribuire l'applicazione 
Poiché ora è completa e funziona correttamente con Azure Cosmos DB, è possibile distribuire questa app Web nel Servizio app di Azure.  

1. Per pubblicare l'applicazione, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.
   
2. Nella finestra di dialogo **Pubblica** selezionare **Servizio app**, quindi selezionare **Crea nuovo** per creare un profilo di Servizio app oppure **Seleziona esistente** per usare un profilo esistente.

3. Se si dispone già di un profilo del Servizio app di Azure, selezionare una **sottoscrizione** dall'elenco a discesa. Usare il filtro di **visualizzazione** per eseguire l'ordinamento in base al gruppo di risorse o al tipo di risorsa. Cercare quindi il Servizio app di Azure richiesto e scegliere **OK**.
   
   ![Finestra di dialogo del servizio app in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Per creare un nuovo profilo del Servizio app di Azure, fare clic su **Crea nuovo** nella finestra di dialogo **Pubblica**. Nella finestra di dialogo **Crea servizio app** immettere il nome dell'app Web e la sottoscrizione appropriata, il gruppo di risorse e il piano di servizio app, quindi scegliere **Crea**.

   ![Finestra di dialogo Crea servizio app in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Dopo alcuni secondi Visual Studio pubblica l'applicazione Web e avvia un browser in cui è possibile vedere il progetto in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come compilare un'applicazione Web MVC ASP.NET Core che può accedere ai dati archiviati in Azure Cosmos DB. È possibile passare all'articolo successivo:

* [Informazioni sul partizionamento dei dati in Azure Cosmos DB](./partitioning-overview.md)
* [Informazioni su come eseguire query più avanzate in Azure Cosmos DB](./how-to-sql-query.md)
* [Informazioni su come modellare i dati in uno scenario più avanzato](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
