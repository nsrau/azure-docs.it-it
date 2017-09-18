---
title: 'Esercitazione su ASP.NET MVC per Azure Cosmos DB: sviluppo di un''applicazione Web | Microsoft Docs'
description: "Esercitazione su MVC ASP.NET per creare un'applicazione Web MVC con Azure Cosmos DB. Si archivieranno documenti JSON e si accederà ai dati da un'app todo ospitata in siti Web di Azure - Esercitazione dettagliata su MVC ASP.NET."
keywords: esercitazione su mvc asp.net, sviluppo di applicazioni web, applicazione web mvc, esercitazione dettagliata su mvc asp net
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 985962c4f2db21526291981ad5180fc0a16e2ab8
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="_Toc395809351"></a>Esercitazione su ASP.NET MVC: Sviluppo di applicazioni Web con Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.JS](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Per illustrare come sfruttare in modo efficiente Azure Cosmos DB per archiviare ed eseguire query su documenti JSON, questo articolo include una procedura dettagliata end-to-end che illustra come creare un'app todo con Azure Cosmos DB. Le attività verranno memorizzate come documenti JSON in Azure Cosmos DB.

![Schermata dell'applicazione Web MVC per un elenco di azioni creata in questa esercitazione - Esercitazione dettagliata su MVC ASP NET](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image01.png)

La procedura guidata illustra come usare il servizio Azure Cosmos DB per archiviare i dati e accedervi da un'applicazione Web MVC ASP.NET ospitata in Azure. Se si preferisce un'esercitazione che illustra solo Azure Cosmos DB e non i componenti ASP.NET MVC, vedere [Compilare un'applicazione console C# di Azure Cosmos DB](documentdb-get-started.md).

> [!TIP]
> Questa esercitazione presuppone già una certa esperienza nell'uso di MCV ASP.NET e di Siti Web di Azure. Se non si ha alcuna esperienza con ASP.NET o gli [strumenti richiesti come prerequisiti](#_Toc395637760), è consigliabile scaricare il progetto di esempio completo da [GitHub][GitHub] e seguire le relative istruzioni. Una volta creata la soluzione, è possibile leggere questo articolo per approfondire il codice nel contesto del progetto.
> 
> 

## <a name="_Toc395637760"></a>Prerequisiti per questa esercitazione sul database
Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili gli elementi seguenti:

* Un account Azure attivo.  Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK per .NET per Visual Studio 2017, disponibile tramite il programma di installazione di Visual Studio.

Tutte le schermate in questo articolo sono state ottenute usando Microsoft Visual Studio Community 2017. Se il sistema in uso è configurato con versioni diverse, è probabile che le schermate e le opzioni non siano interamente corrispondenti. Se si soddisfano i prerequisiti precedenti, la soluzione dovrebbe funzionare comunque.

## <a name="_Toc395637761"></a>Passaggio 1: Creare un account del database di Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account SQL (DocumentDB) per Azure Cosmos DB o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile proseguire con il passaggio [Creare una nuova applicazione MVC ASP.NET](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Verrà ora illustrata in modo dettagliato la procedura per creare un'applicazione MVC ASP.NET completamente nuova. 

## <a name="_Toc395637762"></a>Passaggio 2: Creare una nuova applicazione MVC ASP.NET

1. Scegliere **Nuovo** dal menu **File** di Visual Studio e quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto** .

2. Nel riquadro **Tipi progetto** espandere **Modelli**, **Visual C#**, **Web** e quindi selezionare **Applicazione Web ASP.NET**.

      ![Schermata della finestra di dialogo Nuovo progetto con il tipo di progetto applicazione Web ASP.NET evidenziato](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Nella casella **Nome** digitare il nome del progetto. Questa esercitazione usa il nome "todo". Se si sceglie di usare un valore diverso da questo, quindi ogni volta che in questa esercitazione viene descritto lo spazio dei nomi todo, è necessario modificare gli esempi di codice forniti per usare il nome assegnato all'applicazione. 
4. Fare clic su **Sfoglia** per passare alla cartella in cui si vuole creare il progetto e quindi fare clic su **OK**.
   
      Viene visualizzata la finestra di dialogo **Nuova applicazione Web ASP.NET**.
   
    ![Schermata della finestra della nuova applicazione Web ASP.NET con il modello di applicazione MVC evidenziato](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. Nel riquadro dei modelli selezionare **MVC**.

6. Fare clic su **OK** e lasciare che Visual Studio esegua le operazioni necessarie per lo scaffolding del modello MVC ASP.NET vuoto. 

          
7. Al termine della creazione dell'applicazione MVC standard da parte di Visual Studio, sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.
   
    L'esecuzione locale del progetto verrà ignorata perché è già stata mostrata per l'applicazione ASP.NET "Hello World". Si passerà direttamente all'aggiunta di Azure Cosmos DB al progetto e alla compilazione dell'applicazione.

## <a name="_Toc395637767"></a>Passaggio 3: Aggiungere Azure Cosmos DB al progetto di applicazione Web MVC
Ora che è disponibile la maggior parte del plumbing MVC ASP.NET necessario per questa soluzione, è possibile passare all'aggiunta di Azure Cosmos DB all'applicazione Web MVC.

1. Azure Cosmos DB .NET SDK è distribuito come pacchetto NuGet. Per ottenere il pacchetto NuGet in Visual Studio, usare Gestione pacchetti NuGet in Visual Studio facendo clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliendo **Gestisci pacchetti NuGet**.
   
    ![Screenshot delle opzioni del menu di scelta rapida per il progetto applicazione Web in Esplora soluzioni, con Gestisci pacchetti NuGet evidenziato.](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet** .
2. Nella casella **Sfoglia** di NuGet digitare ***Azure DocumentDB***. Il nome del pacchetto non è stato aggiornato in Azure Cosmos DB.
   
    Dai risultati installare il pacchetto **Microsoft.Azure.DocumentDB by Microsoft**. Il pacchetto Azure Cosmos DB verrà scaricato e installato, con tutte le dipendenze, ad esempio Newtonsoft.Json. Fare clic su **OK** nella finestra **Anteprima** e su **Accetto** nella finestra **Accettazione della licenza** per completare l'installazione.
   
    ![Schermata della finestra Gestisci pacchetti NuGet, con la libreria del client di Microsoft Azure DocumentDB evidenziata](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      In alternativa, è possibile usare la console di Gestione pacchetti per installare il pacchetto. A tale scopo, scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Al prompt dei comandi, immettere quanto segue.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Una volta installato il pacchetto, la soluzione di Visual Studio dovrebbe assomigliare alla seguente, con due nuovi riferimenti aggiunti, Microsoft.Azure.Documents.Client e Newtonsoft.Json.
   
    ![Schermata dei due riferimenti aggiunti al progetto di dati JSON in Esplora soluzioni](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Passaggio 4: Configurare l'applicazione MVC ASP.NET
Vengono ora aggiunti i modelli, le visualizzazioni e i controller all'applicazione MVC:

* [Aggiungere un modello](#_Toc395637764).
* [Aggiungere un controller](#_Toc395637765).
* [Aggiungere visualizzazioni](#_Toc395637766).

### <a name="_Toc395637764"></a>Aggiungere un modello di dati JSON
Creare prima di tutto l'elemento che corrisponde alla lettera **M** in MVC, ovvero il modello. 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi fare clic su **Classe**.
   
      Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento** .
2. Denominare la nuova classe **Item.cs** e fare clic su **Aggiungi**. 
3. In questo nuovo file **Item.cs** , aggiungere quanto segue dopo l'ultima *istruzione using*.
   
        using Newtonsoft.Json;
4. A questo punto sostituire il codice 
   
        public class Item
        {
        }
   
    con il codice seguente:
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Tutti i dati presenti in Azure Cosmos DB vengono passati in rete e archiviati come JSON. Per controllare il modo in cui gli oggetti vengono serializzati/deserializzati da JSON.NET, è possibile usare l'attributo **JsonProperty**, come mostrato nella classe **Item** appena creata. Questa operazione **non è necessaria** , ma è utile per verificare che le proprietà seguano le convenzioni di denominazione maiuscole-minuscole JSON. 
   
    Non solo è possibile controllare il formato del nome della proprietà quando viene usata in JSON, ma anche rinominare completamente le proprietà .NET, come è stato fatto con la proprietà **Description** . 

### <a name="_Toc395637765"></a>Aggiungere un controller
Una volta creato il modello **M**, si passa alla creazione di una classe controller **C** in MVC.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi fare clic su **Controller**.
   
    Verrà visualizzata la finestra di dialogo **Aggiungi scaffolding** .
2. Selezionare **Controller MVC 5 - Vuoto** e quindi fare clic su **Aggiungi**.
   
    ![Schermata della finestra di dialogo Aggiungi scaffolding con l'opzione Controller MVC 5 - Vuoto evidenziata](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Assegnare al controller il nome **ItemController.**
   
    ![Schermata della finestra di dialogo Aggiungi controller](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Una volta creato il file, la soluzione di Visual Studio deve essere simile alla seguente, con il nuovo file ItemController.cs in **Esplora soluzioni**. Viene anche visualizzato il nuovo file Item.cs creato in precedenza.
   
    ![Schermata della soluzione Visual Studio - Esplora soluzioni con il nuovo file ItemController.cs e il file Item.cs evidenziati](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    È possibile chiudere il file ItemController.cs. Si tornerà a questo file in un secondo momento. 

### <a name="_Toc395637766"></a>Aggiungere visualizzazioni
A questo punto è necessario creare le visualizzazioni **V** in MVC:

* [Aggiungere una visualizzazione Indice elemento](#AddItemIndexView).
* [Aggiungere una visualizzazione Nuovo elemento](#AddNewIndexView).
* [Aggiungere una visualizzazione Modifica elemento](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Aggiungere una visualizzazione Indice elemento
1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, fare clic con il pulsante destro del mouse sulla cartella **Item** vuota creata prima da Visual Studio quando è stato aggiunto **ItemController**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.
   
    ![Schermata di Esplora soluzioni con la cartella Item creata da Visual Studio con i comandi Aggiungi visualizzazione evidenziati](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Index***.
   * Nella casella **Modello** selezionare ***Elenco***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
     
   ![Schermata con la finestra di dialogo Aggiungi visualizzazione](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Una volta impostati tutti questi valori, fare clic su **Aggiungi** , in modo che venga creata una nuova visualizzazione modello in Visual Studio. Al termine, verrà aperto il file con estensione cshtml creato. È ora possibile chiudere questo file in Visual Studio, perché vi si tornerà di nuovo più avanti.

#### <a name="AddNewIndexView"></a>Aggiungere una visualizzazione Nuovo elemento
Viene ora creata una nuova visualizzazione per la creazione di nuovi oggetti **Item** in modo simile a come è stata creata una visualizzazione **Indice elemento**.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Item**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.
2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Create***.
   * Nella casella **Modello** selezionare ***Create***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
   * Fare clic su **Aggiungi**.
   
#### <a name="_Toc395888515"></a>Aggiungere una visualizzazione Modifica elemento
È infine possibile aggiungere un'ultima visualizzazione per la modifica di un oggetto **Item** , allo stesso modo in cui sono state create le altre.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Item**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.
2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
   
   * Nella casella **Nome visualizzazione** digitare ***Edit***.
   * Nella casella **Modello** selezionare ***Edit***.
   * Nella casella **Classe modello** selezionare ***Item (todo.Models)***.
   * Nella casella della pagina di layout digitare ***~/Views/Shared/_Layout.cshtml***.
   * Fare clic su **Aggiungi**.

Al termine, chiudere tutti i documenti con estensione cshtml in Visual Studio. Si tornerà a queste visualizzazioni in un secondo momento.

## <a name="_Toc395637769"></a>Passaggio 5: Completamento dell'aggiunta di Azure Cosmos DB
Ora che le caratteristiche standard di MVC vengono prese in considerazione, è possibile aggiungere il codice per Azure Cosmos DB. 

In questa sezione viene aggiunto il codice per gestire le operazioni seguenti:

* [Creare un elenco di elementi incompleti](#_Toc395637770).
* [Aggiungere elementi](#_Toc395637771).
* [Modificare elementi](#_Toc395637772).

### <a name="_Toc395637770"></a>Elenco di elementi incompleti in un'applicazione Web MVC
La prima cosa da fare è aggiungere una classe che contenga tutta la logica per connettersi e usare Azure Cosmos DB. Per questa esercitazione verrà inclusa tutta questa logica in una classe di tipo archivio denominata DocumentDBRepository. 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi** e quindi fare clic su **Classe**. Assegnare alla nuova classe il nome **DocumentDBRepository** e fare clic su **Aggiungi**.
2. Nella nuova classe **DocumentDBRepository** creata aggiungere le istruzioni *using* seguenti sopra la dichiarazione *namespace*.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net
        
    A questo punto sostituire il codice 
   
        public class DocumentDBRepository
        {
        }
   
    con il codice seguente:
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Poiché alcuni valori vengono letti dalla configurazione, aprire il file **Web.config** dell'applicazione e aggiungere le righe seguenti dopo la sezione `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. A questo punto, aggiornare i valori per *endpoint* e *authKey* usando il pannello Chiavi del portale di Azure. Usare il valore di **URI** dal pannello Chiavi come valore dell'impostazione dell'endpoint e usare **CHIAVE PRIMARIA** oppure **CHIAVE SECONDARIA** dal pannello Chiavi come valore dell'impostazione authKey.

    Ciò consente di collegare il repository Azure Cosmos DB, ora è possibile aggiungere la logica dell'applicazione.

1. Un'applicazione per un elenco di azioni deve innanzitutto consentire la visualizzazione degli elementi incompleti.  Copiare e incollare il seguente frammento di codice in un punto qualsiasi all'interno della classe **DocumentDBRepository** .
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Aprire il file **ItemController** aggiunto in precedenza e aggiungere le seguenti *istruzioni using* sopra la dichiarazione dello spazio dei nomi.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Se il progetto non è denominato "todo", è quindi necessario eseguire l'aggiornamento usando "todo.Models" per inserire il nome del progetto.
   
    A questo punto sostituire il codice
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    con il codice seguente:
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Aprire **Global.asax.cs** e aggiungere la riga seguente al metodo **Application_Start**. 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

A questo punto dovrebbe essere possibile compilare la soluzione senza errori.

Se si eseguisse l'applicazione a questo punto, si passerebbe a **HomeController** e alla visualizzazione **Index** di tale controller. Questo è il comportamento predefinito per il progetto di modello MVC scelto inizialmente, ma è possibile cambiarlo. Per modificare questo comportamento, è possibile cambiare il routing nell'applicazione MVC.

Aprire ***App\_Start\RouteConfig.cs***, trovare la riga che inizia con "defaults:" e modificarla in modo che sia analoga a quanto riportato di seguito.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Questo codice indica ad ASP.NET MVC che, se non è stato specificato alcun valore nell'URL per controllare il comportamento di routing, sarà necessario usare **Item** invece di **Home** come controller e **Index** come visualizzazione.

A questo punto se si esegue l'applicazione, verrà eseguita una chiamata in **ItemController** che eseguirà una chiamata alla classe di tipo repository e userà il metodo GetItems per restituire tutti gli elementi incompleti alla visualizzazione **Views**\\**Item**\\**Index**. 

Se compilato ed eseguito ora, il progetto avrà un aspetto simile al seguente.    

![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione del database](./media/documentdb-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Aggiungere elementi
Vengono ora inseriti alcuni elementi nel database, in modo che la griglia non sia più vuota.

Sarà ora effettuata l'aggiunta di codice ad Azure Cosmos DBRepository e ItemController per rendere persistente il record in Azure Cosmos DB.

1. Aggiungere il metodo seguente alla classe **DocumentDBRepository** .
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Questo metodo accetta semplicemente un oggetto passato al metodo stesso e lo rende permanente in Azure Cosmos DB.
2. Aprire il file ItemController.cs e aggiungere il seguente frammento di codice all'interno della classe. In tal modo, ASP.NET MVC individua le operazioni da eseguire per l'azione **Create** . In questo caso, eseguire semplicemente il rendering della visualizzazione Create.cshtml associata creata in precedenza.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Nel controller è necessario avere più codici, ed il controller stesso accetterà la presentazione dal codice **Crea** visualizzazione.
3. Aggiungere il blocco di codice successivo alla classe ItemController.cs, che indica a MVC ASP.NET le operazioni da eseguire con un POST per i moduli per questo controller.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Tale codice chiama DocumentDBRepository e utilizza il metodo CreateItemAsync per rendere persistente la nuova voce al database. 
   
    **Nota sulla sicurezza**: l'attributo **ValidateAntiForgeryToken** viene usato qui per proteggere l'applicazione da attacchi di richiesta intersito falsa. Oltre ad aggiungere questo attributo, è necessario che le visualizzazioni usino questo token antifalsificazione. Per altre informazioni sull'oggetto e sugli esempi di come implementarla correttamente, vedere [Preventing Cross-Site Request Forgery][Preventing Cross-Site Request Forgery] (Impedire la falsificazione della richiesta tra siti). Il codice sorgente fornito in [GitHub][GitHub] consente l'implementazione completa.
   
    **Nota sulla sicurezza**: è possibile usare l'attributo **Bind** nel metodo del parametro per proteggersi da attacchi di overposting. Per altre informazioni dettagliate, vedere [Basic CRUD Operations in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC] (Operazioni CRUD di base in MVC ASP.NET).

Questo è tutto il codice necessario per aggiungere nuovi elementi al database.

### <a name="_Toc395637772"></a>Modificare elementi
Vi è un'ultima operazione da eseguire ovvero l'aggiunta della possibilità di modificare oggetti **Item** nel database e di contrassegnarli come completi. Poiché questa visualizzazione per la modifica è già stata aggiunta al progetto, è sufficiente aggiungere il codice al controller e di nuovo alla classe **DocumentDBRepository** .

1. Aggiungere il codice seguente alla classe **DocumentDBRepository** .
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Il primo di questi metodi, **GetItem**, recupera un elemento da Azure Cosmos DB, che viene passato a **ItemController** e quindi alla visualizzazione **Edit**.
   
    Il secondo metodo appena aggiunto sostituisce il **documento** in Azure Cosmos DB con la versione del **documento** passato da **ItemController**.
2. Aggiungere il codice seguente alla classe **ItemController** .
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Il primo metodo gestisce Http GET che si verifica quando l'utente fa clic sul collegamento **Edit** dalla visualizzazione **Index**. Questo metodo recupera un [**documento**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) da Azure Cosmos DB e lo passa alla visualizzazione **Edit**.
   
    La visualizzazione **Edit** esegue quindi un'operazione Http POST in **IndexController**. 
   
    Il secondo metodo aggiunto gestisce il passaggio dell'oggetto aggiornato ad Azure Cosmos DB in modo da renderlo permanente nel database.

A questo punto, queste sono le procedure necessarie per eseguire l'applicazione, elencare oggetti **Item incompleti**, aggiungere nuovi oggetti **Item** e modificare oggetti **Item**.

## <a name="_Toc395637773"></a>Passaggio 6: Eseguire l'applicazione in locale
Per testare l'applicazione nel computer locale, eseguire le operazioni seguenti:

1. Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug. L'applicazione verrà compilata e verrà avviato un browser con la pagina di griglia vuota osservata sopra:
   
    ![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione del database](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Fare clic sul collegamento **Crea nuovo** e specificare i valori dei campi **Nome** e **Descrizione**. Lasciare deselezionata la casella di controllo **Completed**. In caso contrario, il nuovo oggetto **Item** verrà aggiunto con uno stato completato e non sarà visualizzato nell'elenco iniziale.
   
    ![Schermata della visualizzazione Create](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Fare clic su **Create** per tornare di nuovo alla visualizzazione **Index** e visualizzare **Item** nell'elenco.
   
    ![Schermata della visualizzazione Index](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    È possibile aggiungere altri oggetti **Item** all'elenco di azioni.
    
4. Fare clic su **Edit** accanto a un oggetto **Item** nell'elenco per tornare alla visualizzazione **Edit**, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag **Completed**. Se si seleziona il flag **Completed** e si fa clic su **Salva**, l'oggetto **Item** viene rimosso dall'elenco di attività incomplete.
   
    ![Schermata della visualizzazione Index con la casella Completed selezionata](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Una volta testata l'app, premere CTRL+F5 per arrestarne il debug. È ora possibile distribuire l'app.

## <a name="_Toc395637774"></a>Passaggio 7: Distribuire l'applicazione nel Servizio app di Azure 
Poiché ora è completa e funziona correttamente con Azure Cosmos DB, è possibile distribuire questa app Web nel Servizio app di Azure.  

1. Per pubblicare l'applicazione, è sufficiente fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.
   
    ![Schermata dell'opzione Pubblica in Esplora soluzioni](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. Nella finestra di dialogo **Pubblica** fare clic su **Servizio app di Microsoft Azure**, quindi selezionare **Crea nuovo** per creare un profilo di servizio app o fare clic su **Seleziona esistente** per usare un profilo esistente.

    ![Finestra di dialogo Pubblica in Visual Studio](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Se si dispone di un profilo del Servizio app di Azure esistente, immettere il nome della sottoscrizione. Usare il filtro di **visualizzazione** per eseguire l'ordinamento in base al gruppo d risorse o al tipo di risorsa, quindi selezionare il Servizio app di Azure in uso. 
   
    ![Finestra di dialogo del servizio app in Visual Studio](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Per creare un nuovo profilo del Servizio app di Azure, fare clic su **Crea nuovo** nella finestra di dialogo **Pubblica**. Nella finestra di dialogo **Crea servizio app** immettere il nome dell'app Web e la sottoscrizione appropriata, il gruppo di risorse e il piano di servizio app, quindi fare clic su **Crea**.

    ![Finestra di dialogo Crea servizio app in Visual Studio](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro in esecuzione in Azure.



## <a name="_Toc395637775"></a>Passaggi successivi
Congratulazioni. È stata creata la prima applicazione Web MVC ASP.NET con Azure Cosmos DB che è stata quindi pubblicata in Azure. Il codice sorgente per l'applicazione completa, insieme alle funzionalità di eliminazione e relative ai dettagli non incluse in questa esercitazione, può essere scaricato o clonato da [GitHub][GitHub]. Per aggiungere queste funzionalità all'app, recuperare il codice e aggiungerlo all'app.

Per aggiungere altre funzionalità all'applicazione, esaminare le API disponibili nella raccolta [Azure Cosmos DB .NET Library](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) (Libreria .NET per Azure Cosmos DB), in cui è anche possibile aggiungere il proprio contributo in [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app

