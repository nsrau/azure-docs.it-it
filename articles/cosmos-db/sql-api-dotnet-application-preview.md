---
title: Esercitazione sullo sviluppo di un'applicazione Web MVC ASP.NET con Azure Cosmos DB tramite .Net Preview SDK.
description: Questa esercitazione illustra come creare un'applicazione Web MVC ASP.NET con Azure Cosmos DB. A questo scopo si archivieranno e si accederà ai dati JSON da un'app Todo ospitata in Azure.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: e3ad852246b4b78d5ed7ac938348e59e9b7e6ce0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037124"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Esercitazione: Sviluppare un'applicazione Web MVC ASP.NET con Azure Cosmos DB tramite .Net Preview SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET Preview](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Questa esercitazione illustra come usare Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web MVC ASP.NET ospitata in Azure. Nel corso dell'esercitazione si userà .NET SDK V3, che è attualmente in versione di anteprima. L'immagine seguente mostra la pagina Web che verrà compilata usando l'esempio di questo articolo:
 
![Schermata dell'applicazione Web MVC per un elenco di azioni creata in questa esercitazione - Esercitazione dettagliata su MVC ASP NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Se non si ha il tempo necessario per completare l'esercitazione, è possibile scaricare il progetto di esempio completo da [GitHub][GitHub]. 

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creazione di un account di Azure Cosmos
> * Creazione di un'app MVC ASP.NET
> * Connessione dell'app ad Azure Cosmos DB 
> * Esecuzione di operazioni CRUD sui dati

> [!TIP]
> Questa esercitazione presuppone già una certa esperienza nell'uso di MCV ASP.NET e di Siti Web di Azure. Se non si ha alcuna esperienza con ASP.NET o con gli [strumenti richiesti come prerequisiti](#prerequisites), è consigliabile scaricare il progetto di esempio completo da [GitHub][GitHub], aggiungere i pacchetti NuGet necessari ed eseguirlo. Una volta compilato il progetto, è possibile leggere questo articolo per approfondire il codice nel contesto del progetto.

## <a name="prerequisites"></a>Prerequisiti 

Prima di seguire le istruzioni di questo articolo, verificare di disporre delle risorse seguenti:

* **Un account Azure attivo:** Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK per .NET per Visual Studio 2017, disponibile tramite il programma di installazione di Visual Studio.

Tutte le schermate in questo articolo sono state ottenute usando Microsoft Visual Studio Community 2017. Se il sistema in uso è configurato con una versione diversa, è probabile che le schermate e le opzioni non siano interamente corrispondenti, ma se si soddisfano i prerequisiti precedenti la soluzione dovrebbe funzionare comunque.

## <a name="create-an-azure-cosmos-account"></a>Passaggio 1: Creare un account Azure Cosmos

La prima cosa da fare è creare un account di Azure Cosmos. Se si ha già un account API SQL di Azure Cosmos DB o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile passare direttamente alla sezione [Creare una nuova applicazione MVC ASP.NET](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Nella sezione successiva si creerà una nuova applicazione MVC ASP.NET. 

## <a name="create-a-new-mvc-application"></a>Passaggio 2: Creare una nuova applicazione MVC ASP.NET

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto** .

2. Nella finestra **Nuovo progetto** espandere **Modelli installati**, **Visual C#**, **Web** e quindi selezionare **Applicazione Web ASP.NET**. 

   ![Creare un nuovo progetto di applicazione Web ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. Nella casella **Nome** digitare il nome del progetto. Questa esercitazione usa il nome "todo". Se si sceglie di usare un nome diverso, ogni volta che in questa esercitazione viene menzionato lo spazio dei nomi todo occorre modificare gli esempi di codice forniti sostituendo todo con il nome scelto per l'applicazione. 

4. Selezionare **Sfoglia** per passare alla cartella in cui si vuole creare il progetto, quindi scegliere **.NET Framework 4.6.1** o una versione successiva. Selezionare **OK**. 

5. Viene visualizzata la finestra di dialogo **Nuova applicazione Web ASP.NET**. Nel riquadro dei modelli selezionare **MVC**.

6. Scegliere **OK** e lasciare che Visual Studio prepari lo scaffolding attorno al modello MVC ASP.NET vuoto. 

7. Al termine della creazione dell'applicazione MVC boilerplate in Visual Studio, sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.

## <a name="add-nuget-packages"></a>Passaggio 3: Aggiungere il pacchetto NuGet di Azure Cosmos DB al progetto

A questo punto la maggior parte del codice del framework MVC ASP.NET necessario per la soluzione è disponibile, quindi è il momento di aggiungere i pacchetti NuGet richiesti per la connessione ad Azure Cosmos DB.

1. Azure Cosmos DB .NET SDK è distribuito come pacchetto NuGet. Per ottenere il pacchetto NuGet di Visual Studio, usare Gestione pacchetti NuGet in Visual Studio facendo clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliendo **Gestisci pacchetti NuGet**.
   
   ![Screenshot delle opzioni del menu di scelta rapida per il progetto applicazione Web in Esplora soluzioni, con Gestisci pacchetti NuGet evidenziato.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet** . Nella casella **Sfoglia** di NuGet digitare **Microsoft.Azure.Cosmos**. Dai risultati installare **Microsoft.Azure.Cosmos** 3.0.0.1, versione di anteprima. Il pacchetto Azure Cosmos DB verrà scaricato e installato insieme alle relative dipendenze, ad esempio Newtonsoft.Json. Scegliere **OK** nella finestra **Anteprima** e selezionare **Accetto** nella finestra **Accettazione della licenza** per completare l'installazione.
   
   In alternativa, è possibile usare la console di Gestione pacchetti per installare il pacchetto NuGet. A questo scopo, scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi selezionare **Console di Gestione pacchetti**. Al prompt dei comandi, digitare il seguente comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Una volta installato il pacchetto, la soluzione di Visual Studio dovrebbe contenere i due nuovi riferimenti alle librerie Microsoft.Azure.Cosmos.Client e Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Passaggio 4: Configurare l'applicazione MVC ASP.NET

A questo punto occorre aggiungere i modelli, le visualizzazioni e i controller all'applicazione MVC:

* [Aggiungere un modello](#add-a-model).
* [Aggiungere un controller](#add-a-controller).
* [Aggiungere visualizzazioni](#add-views).

### <a name="add-a-model"></a> Aggiungere un modello

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi selezionare **Classe**. Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento** .

1. Assegnare alla nuova classe il nome **TodoItem.cs** e scegliere **Aggiungi**. 

1. Sostituire quindi il codice nella classe "Todoitem" con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   I dati archiviati in Azure Cosmos DB vengono passati sulla rete cablata e archiviati come JSON. Per controllare il modo in cui gli oggetti vengono serializzati/deserializzati da JSON.NET, è possibile usare l'attributo **JsonProperty**, come mostrato nella classe **TodoItem** creata in precedenza. Non solo è possibile controllare il formato del nome della proprietà usata in JSON, ma anche rinominare le proprietà .NET come è stato fatto con la proprietà **Description**. 

### <a name="add-a-controller"></a>Aggiungere un controller

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi selezionare **Controller**. Verrà visualizzata la finestra di dialogo **Aggiungi scaffolding** .

1. Selezionare **Controller MVC 5 - Vuoto** e quindi scegliere **Aggiungi**.

   ![Schermata della finestra di dialogo Aggiungi scaffolding con l'opzione Controller MVC 5 - Vuoto evidenziata](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Assegnare al nuovo controller il nome **ItemController e sostituire il codice incluso nel file con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   L'attributo **ValidateAntiForgeryToken** viene usato qui per proteggere l'applicazione da attacchi di falsificazione (CSRF) richiesta tra siti. Oltre ad aggiungere questo attributo, è necessario che le visualizzazioni usino questo token antifalsificazione. Per altre informazioni in proposito e per esempi di corretta implementazione, vedere [Prevenzione degli attacchi di falsificazione (CSRF) richiesta tra siti nell'applicazione ASP.NET MVC][Preventing Cross-Site Request Forgery]. Il codice sorgente fornito in [GitHub][GitHub] consente l'implementazione completa.
   
   Viene usato anche l'attributo **Bind** nel parametro del metodo come protezione dagli attacchi di overposting. Per informazioni dettagliate, vedere [Implementazione della funzionalità CRUD di base con Entity Framework nell'applicazione ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Aggiungere visualizzazioni

A questo punto occorre creare le tre visualizzazioni seguenti: 

* [Aggiungere una visualizzazione elemento elenco](#AddItemIndexView).
* [Aggiungere una visualizzazione Nuovo elemento](#AddNewIndexView).
* [Aggiungere una visualizzazione Modifica elemento](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Aggiungere una visualizzazione elemento elenco

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, fare clic con il pulsante destro del mouse sulla cartella **Item** vuota creata da Visual Studio quando è stato aggiunto **ItemController**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.
   
   ![Schermata di Esplora soluzioni con la cartella Item creata da Visual Studio con i comandi Aggiungi visualizzazione evidenziati](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. Nella finestra di dialogo **Aggiungi visualizzazione** aggiornare i valori seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Index***.
   * Nella casella **Modello** selezionare ***Elenco***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
     
   ![Schermata con la finestra di dialogo Aggiungi visualizzazione](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

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

## <a name="connect-to-cosmosdb"></a>Passaggio 5: Connettersi ad Azure Cosmos DB 

Sistemate le caratteristiche standard di MVC, è il momento di aggiungere il codice per connettersi ad Azure Cosmos DB ed eseguire le operazioni CRUD. 

### <a name="perform-crud-operations"></a> Eseguire operazioni CRUD sui dati

La prima cosa da fare è aggiungere una classe che contenga la logica per connettersi e usare Azure Cosmos DB. Per questa esercitazione questa logica verrà incapsulata in una classe denominata TodoItemService.cs. Questo codice legge i valori dell'endpoint di Azure Cosmos DB dal file di configurazione ed esegue operazioni CRUD come elencare gli elementi incompleti e creare, modificare ed eliminare elementi. 

1. In **Esplora soluzioni** creare una nuova cartella denominata **Services** nel progetto.

1. Fare clic con il pulsante destro del mouse sulla cartella **Services**, scegliere **Aggiungi** e quindi selezionare **Classe**. Assegnare alla nuova classe il nome **TodoItemService** e scegliere **Aggiungi**.

1. Aggiungere il codice seguente alla classe **TodoItemService** e sostituire il codice incluso nel file con il codice seguente:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Il codice precedente legge i valori della stringa di connessione dal file di configurazione. Per aggiornare i valori della stringa di connessione dell'account di Azure Cosmos aprire il file **Web.config** nel progetto e aggiungere le righe seguenti sotto la sezione `<AppSettings>`:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Aggiornare i valori endpoint e primarykey con i valori recuperati dal pannello **Chiavi** nel portale di Azure. Usare il valore di **URI** dal pannello Chiavi come valore dell'impostazione dell'endpoint e usare **CHIAVE PRIMARIA** oppure **CHIAVE SECONDARIA** dal pannello Chiavi per l'impostazione delle chiavi. Ciò consente di collegare il repository Azure Cosmos DB all'applicazione. Ora è possibile aggiungere la logica dell'applicazione.

1. Aprire **Global.asax.cs** e aggiungere la riga seguente al metodo **Application_Start**. 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   A questo punto dovrebbe essere possibile compilare il progetto senza errori. Se si eseguisse l'applicazione adesso, si aprirebbero **HomeController** e la visualizzazione **Index** di tale controller. Questo è il comportamento predefinito del progetto di modello MVC scelto inizialmente. Per modificare questo comportamento, è possibile cambiare il routing nell'applicazione MVC.

1. Aprire ***App\_Start\RouteConfig.cs***, trovare la riga che inizia con "defaults:" e aggiornarla con il codice seguente:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  Questo codice indica a MVC ASP.NET, se non è stato specificato alcun valore nell'URL per controllare il comportamento di routing, di usare **Item** invece di **Home** come controller e **Index** come visualizzazione.

Se ora si esegue l'applicazione, questa effettua una chiamata in **ItemController** che chiama i metodi GetItems dalla classe TodoItemService definita nella sezione successiva. 

Se compilato ed eseguito ora, il progetto avrà un aspetto simile al seguente.    

![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione del database](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Passaggio 6: Eseguire l'applicazione in locale

Per testare l'applicazione nel computer locale, eseguire la procedura seguente:

1. Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug. L'applicazione verrà compilata e verrà avviato un browser con la pagina di griglia vuota osservata sopra:
   
   ![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione del database](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Fare clic sul collegamento **Crea nuovo** e specificare i valori dei campi **Nome** e **Descrizione**. Lasciare deselezionata la casella di controllo **Completato**. In caso contrario, il nuovo elemento viene aggiunto con lo stato completato e non viene visualizzato nell'elenco iniziale.
   
3. Fare clic su **Crea** per tornare di nuovo alla visualizzazione **Index** e visualizzare l'elemento nell'elenco. È possibile aggiungere alcuni altri elementi all'elenco di azioni.

    ![Schermata della visualizzazione Index](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Fare clic su **Edit** accanto a un oggetto **Item** nell'elenco per tornare alla visualizzazione **Edit**, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag **Completed**. Se si seleziona il flag **Completed** e si fa clic su **Salva**, l'oggetto **Item** viene rimosso dall'elenco di attività incomplete.
   
   ![Schermata della visualizzazione Index con la casella Completed selezionata](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Una volta testata l'app, premere CTRL+F5 per arrestarne il debug. È ora possibile distribuire l'app.

## <a name="deploy-the-application-to-azure"></a>Passaggio 7: Distribuire l'applicazione 
Poiché ora è completa e funziona correttamente con Azure Cosmos DB, è possibile distribuire questa app Web nel Servizio app di Azure.  

1. Per pubblicare l'applicazione, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.
   
2. Nella finestra di dialogo **Pubblica** selezionare **Servizio app di Microsoft Azure**, quindi selezionare **Crea nuovo** per creare un profilo di Servizio app oppure **Seleziona esistente** per usare un profilo esistente.

3. Se si dispone già di un profilo del Servizio app di Azure, selezionare una **sottoscrizione** dall'elenco a discesa. Usare il filtro di **visualizzazione** per eseguire l'ordinamento in base al gruppo di risorse o al tipo di risorsa. Cercare quindi il Servizio app di Azure richiesto e scegliere **OK**. 
   
   ![Finestra di dialogo del servizio app in Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Per creare un nuovo profilo del Servizio app di Azure, fare clic su **Crea nuovo** nella finestra di dialogo **Pubblica**. Nella finestra di dialogo **Crea servizio app** immettere il nome dell'app Web e la sottoscrizione appropriata, il gruppo di risorse e il piano di servizio app, quindi scegliere **Crea**.

   ![Finestra di dialogo Crea servizio app in Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Dopo alcuni secondi Visual Studio pubblica l'applicazione Web e avvia un browser in cui è possibile vedere il progetto in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come compilare un'applicazione Web MVC ASP.NET che può accedere ai dati archiviati in Azure Cosmos DB. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Creare un'applicazione Web Java con Azure Cosmos DB e l'API di SQL]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
