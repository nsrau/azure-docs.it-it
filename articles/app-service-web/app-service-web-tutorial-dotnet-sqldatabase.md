---
title: Creare un'app ASP.NET in Azure con un database SQL | Microsoft Docs
description: "Informazioni su come ottenere un'app ASP.NET che è possibile usare in Azure con connessione a un database SQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: article
ms.date: 06/09/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 593a355dd29371c321b2e939677e28637f74b291
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Creare un'app ASP.NET in Azure con un database SQL

Le [app Web di Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) forniscono un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione. Questa esercitazione illustra come distribuire un'app Web ASP.NET basata sui dati in Azure e connetterla al [database SQL di Azure](../sql-database/sql-database-technical-overview.md). Al termine sarà disponibile un'applicazione ASP.NET in esecuzione nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) e connessa al database SQL.

![Applicazione ASP.NET pubblicata nell'app Web di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database SQL in Azure
> * Connettere un'app ASP.NET al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Installare [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) con i carichi di lavoro seguenti:
  - **Sviluppo Web e ASP.NET**
  - **Sviluppo di Azure**

  ![Sviluppo Web e ASP.NET e sviluppo di Azure (in Web e Cloud)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Estrarre (decomprimere) il file *dotnet-sqldb-tutorial-master.zip*.

Il progetto di esempio contiene un'app CRUD (create-read-update-delete) [ASP.NET MVC](https://www.asp.net/mvc) di base che usa [Code First di Entity Framework](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Esecuzione dell'app

Aprire il file *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* in Visual Studio. 

Digitare `Ctrl+F5` per eseguire l'app senza debug. L'app viene visualizzata nel browser predefinito. Selezionare il collegamento **Crea nuovo** e creare due elementi *Attività*. 

![Finestra di dialogo Nuovo progetto ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.

L'app usa un contesto di database per connettersi al database. Nell'esempio il contesto di database usa una stringa di connessione denominata `MyDbConnection`. La stringa di connessione è impostata nel file *Web.config* e il file *Models\MyDatabaseContext.cs* include un riferimento alla stringa. Il nome della stringa di connessione è usato più avanti nell'esercitazione per connettere l'app Web di Azure a un database SQL di Azure. 

## <a name="publish-to-azure-with-sql-database"></a>Pubblicare in Azure con il database SQL

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **DotNetAppSqlDb** e selezionare **Pubblica**.

![Pubblicare da Esplora soluzioni](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Verificare che **Servizio app di Microsoft Azure** sia selezionato e fare clic su **Pubblica**.

![Pubblicare dalla pagina di panoramica progetto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

La pubblicazione apre la finestra di dialogo **Crea servizio app** che consente di creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Nella finestra di dialogo **Crea servizio App** fare clic su **Aggiungi un account** e quindi accedere alla sottoscrizione di Azure. Se si è già connessi a un account Microsoft, verificare che l'account contenga la sottoscrizione di Azure. Se l'account Microsoft a cui si è connessi non include la sottoscrizione di Azure, fare clic su di esso per aggiungere l'account corretto.
   
![Accedere ad Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Dopo avere eseguito l'accesso, è possibile creare tutte le risorse necessarie per l'app Web di Azure in questa finestra di dialogo.

### <a name="configure-the-web-app-name"></a>Configurare il nome dell'app Web

È possibile mantenere il nome dell'app Web generato o modificarlo in un altro nome univoco (i caratteri validi sono `a-z`, `0-9` e `-`). Il nome dell'app Web viene usato come parte dell'URL predefinito per l'app (`<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app Web). Il nome dell'app Web deve essere univoco in tutte le app di Azure. 

![Finestra di dialogo Crea servizio app](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Accanto a **Gruppo di risorse** fare clic su **Nuovo**.

![Accanto a Gruppo di risorse fare clic su Nuovo.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Assegnare al gruppo di risorse il nome **myResourceGroup**.

> [!NOTE]
> Non fare clic su **Crea**. È prima necessario configurare un database SQL in un passaggio successivo.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Accanto a **Piano di servizio app** fare clic su **Nuovo**. 

Nella finestra di dialogo **Configura piano di servizio app** configurare il nuovo piano di servizio app con le impostazioni seguenti:

![Creare un piano di servizio app](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Impostazione  | Valore consigliato | Per altre informazioni |
| ----------------- | ------------ | ----|
|**Piano di servizio app**| myAppServicePlan | [Piani del servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |
|**Posizione**| Europa occidentale | [Aree di Azure](https://azure.microsoft.com/regions/) |
|**Dimensione**| Free | [Piani tariffari](https://azure.microsoft.com/pricing/details/app-service/)|

### <a name="create-a-sql-server-instance"></a>Creare un'istanza di SQL Server

Prima di creare un database è necessario un [server logico di database SQL di Azure](../sql-database/sql-database-features.md). Un server logico contiene un gruppo di database gestiti come gruppo.

Selezionare **Esplora altri servizi di Azure**.

![Configurare il nome dell'app Web](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

Nella scheda **Servizi** fare clic sull'icona **+** accanto a **Database SQL**. 

![Nella scheda Servizi fare clic sull'icona + accanto a Database SQL.](media/app-service-web-tutorial-dotnet-sqldatabase/sql.png)

nella finestra di dialogo **Configura database SQL** fare clic su **Nuovo** accanto a **SQL Server**. 

Viene generato un nome di server univoco. Questo nome viene usato come parte dell'URL predefinito per il server logico `<server_name>.database.windows.net`. Deve essere univoco in tutte le istanze di server logico in Azure. Sebbene sia possibile modificare il nome del server, per questa esercitazione mantenere il valore generato.

Aggiungere un nome utente e una password di amministratore e quindi selezionare **OK**. Per i requisiti di complessità delle password, vedere [Criteri password](/sql/relational-databases/security/password-policy).

Prendere nota del nome utente e della password. Saranno necessari per gestire l'istanza del server logico in seguito.

![Creare un'istanza di SQL Server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="create-a-sql-database"></a>Creare un database SQL

Nella finestra di dialogo **Configura database SQL**: 

* Mantenere il **Nome database** generato predefinito.
* In **Nome stringa di connessione** digitare *MyDbConnection*. Questo nome deve corrispondere alla stringa di connessione cui viene fatto riferimento in *Models\MyDatabaseContext.cs*.
* Selezionare **OK**.

![Configurare il database SQL](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

La finestra di dialogo **Crea servizio app** mostra le risorse create. Fare clic su **Crea**. 

![Risorse create](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Dopo aver creato le risorse di Azure, la procedura guidata pubblica l'app ASP.NET in Azure. Il browser predefinito viene avviato con l'URL dell'app distribuita. 

Aggiungere alcune attività.

![Applicazione ASP.NET pubblicata nell'app Web di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Congratulazioni. L'applicazione ASP.NET basata sui dati è in esecuzione nel Servizio app di Azure.

## <a name="access-the-sql-database-locally"></a>Accedere al database SQL locale

Visual Studio consente di esplorare e gestire facilmente il nuovo database SQL in **Esplora oggetti di SQL Server**.

### <a name="create-a-database-connection"></a>Creare una connessione al database

Dal menu **Visualizza** selezionare **Esplora oggetti di SQL Server**.

Nella parte superiore di **Esplora oggetti di SQL Server** fare clic sul pulsante **Aggiungi istanza di SQL Server**.

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nella finestra di dialogo **Connetti** espandere il nodo **Azure**. Vengono visualizzate tutte le istanze del database SQL di Azure.

Selezionare il database SQL `DotNetAppSqlDb`. La connessione creata in precedenza viene inserita automaticamente nella parte inferiore.

Digitare la password di amministratore di database creata in precedenza e fare clic su **Connetti**.

![Configurare la connessione al database da Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Consentire la connessione client dal computer

Viene visualizzata la finestra di dialogo **Crea nuova regola del firewall**. Per impostazione predefinita, l'istanza del database SQL consente solo le connessioni da servizi di Azure, ad esempio dall'app Web di Azure. Per connettersi al database, creare una regola del firewall nell'istanza del database SQL. La regola del firewall autorizza l'indirizzo IP pubblico del computer locale.

Nella finestra di dialogo è già specificato l'indirizzo IP pubblico del computer.

Assicurarsi che l'opzione **Aggiungi IP client** sia selezionata e fare clic su **OK**. 

![Impostare il firewall per l'istanza del database SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Dopo che Visual Studio ha completato la creazione dell'impostazione del firewall per l'istanza del database SQL, la connessione viene visualizzata in **Esplora oggetti di SQL Server**.

In questa posizione è possibile eseguire le operazioni di database più comuni, ad esempio eseguire query, creare visualizzazioni e stored procedure e così via. 

Fare clic con il pulsante destro del mouse sulla tabella `Todoes` e selezionare **Visualizza dati**. 

![Esplorare gli oggetti del database SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aggiornare l'app con Migrazioni Code First

Per aggiornare il database e l'app Web in Azure è possibile usare gli strumenti noti di Visual Studio. In questo passaggio si usa Migrazioni Code First di Entity Framework per apportare una modifica allo schema del database e pubblicarlo in Azure.

Per altre informazioni sull'uso di Migrazioni Code First di Entity Framework, vedere [Getting Started with Entity Framework 6 Code First using MVC 5](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Introduzione a Code First di Entity Framework 6 con MVC 5).

### <a name="update-your-data-model"></a>Aggiornare il modello di dati

Aprire _Models\Todo.cs_ nell'editor di codice. Aggiungere la proprietà seguente alla classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Eseguire Migrazioni Code First in locale

Eseguire alcuni comandi per eseguire gli aggiornamenti del database locale. 

Nel menu **Strumenti** fare clic su **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

Nella finestra Console di Gestione pacchetti abilitare le migrazioni Code First:

```PowerShell
Enable-Migrations
```

Aggiungere una migrazione:

```PowerShell
Add-Migration AddProperty
```

Aggiornare il database locale:

```PowerShell
Update-Database
```

Digitare `Ctrl+F5` per eseguire l'app. Testare i collegamenti Modifica, Dettagli e Crea.

Se l'applicazione viene caricata senza errori, l'esecuzione di Migrazioni Code First è stata completata. La pagina tuttavia può apparire sempre uguale perché la logica dell'applicazione non usa ancora la nuova proprietà. 

### <a name="use-the-new-property"></a>Usare la nuova proprietà

Apportare alcune modifiche al codice per usare la proprietà `Done`. Per motivi di semplicità in questa esercitazione vengono modificate solo le visualizzazioni `Index` e `Create` per visualizzare la proprietà.

Aprire _Controllers\TodosController.cs_.

Trovare il metodo `Create()` e aggiungere `Done` all'elenco delle proprietà nell'attributo `Bind`. Al termine, la firma del metodo `Create()` è simile al codice seguente:

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

Aprire _Views\Todos\Create.cshtml_.

Nel codice Razor viene visualizzato un elemento `<div class="form-group">` che usa `model.Description` e quindi un altro elemento `<div class="form-group">` che usa `model.CreatedDate`. Immediatamente dopo questi due elementi, aggiungere un altro elemento `<div class="form-group">` che usa `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Aprire _Views\Todos\Index.cshtml_.

cercare l'elemento `<th></th>` vuoto. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Trovare l'elemento `<td>` che contiene i metodi helper `Html.ActionLink()`. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Le modifiche verranno visualizzate nelle visualizzazioni `Index` e `Create`. 

Digitare `Ctrl+F5` per eseguire l'app.

È ora possibile aggiungere un'attività e selezionare **Fine**. L'attività viene visualizzata come completata nella home page. Tenere presente che la visualizzazione `Edit` non mostra il campo `Done` poiché non è stata modificata la visualizzazione `Edit`.

### <a name="enable-code-first-migrations-in-azure"></a>Abilitare Migrazioni Code First in Azure

Dopo aver completato la modifica al codice, inclusa la migrazione del database, la modifica viene pubblicata nell'app Web di Azure e viene eseguito l'aggiornamento del database SQL con Migrazioni Code First.

Fare di nuovo clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.

Fare clic su **Impostazioni** per aprire la procedura guidata di pubblicazione.

![Aprire le impostazioni di pubblicazione](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

Nella procedura guidata fare clic su **Avanti**.

Assicurarsi che la stringa di connessione per il database SQL sia popolata in **MyDatabaseContext (MyDbConnection)**. Potrebbe essere necessario selezionare il database **myToDoAppDb** dall'elenco a discesa. 

Selezionare **Esegui migrazione primo codice (inizia all'avvio dell'applicazione)**, quindi fare clic su **Salva**.

![Abilitare Migrazioni Code First nell'app Web di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Pubblicare le modifiche

Dopo aver abilitato Migrazioni Code First nell'app Web di Azure pubblicare le modifiche al codice.

Nella pagina di pubblicazione fare clic su **Pubblica**.

Provare di nuovo ad aggiungere attività e selezionare **Fine**. Le attività verranno visualizzate nella home page come completate.

![App Web di Azure dopo la migrazione Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Tutte le attività esistenti rimangono visualizzate. Quando si pubblica nuovamente l'applicazione ASP.NET, i dati esistenti nel database SQL non vengono persi. Inoltre, solo Migrazioni Code First modifica lo schema dei dati lasciando intatti i dati esistenti.


## <a name="stream-application-logs"></a>Eseguire lo streaming dei log delle applicazioni

È possibile eseguire lo streaming dei messaggi di traccia direttamente dall'app Web di Azure in Visual Studio.

Aprire _Controllers\TodosController.cs_.

Ogni azione inizia con un metodo `Trace.WriteLine()`. Questo codice viene aggiunto per illustrare come aggiungere messaggi di traccia all'app Web di Azure.

### <a name="open-server-explorer"></a>Aprire Esplora server

Dal menu **Visualizza** selezionare **Esplora server**. È possibile configurare la registrazione per l'app Web di Azure in **Esplora server**. 

### <a name="enable-log-streaming"></a>Abilitare lo streaming dei log

In **Esplora server** espandere **Azure** > **Servizio app**.

Espandere il gruppo di risorse **myResourceGroup** creato al momento della creazione dell'app Web di Azure.

Fare clic con il pulsante destro del mouse sull'app Web di Azure e scegliere **Visualizza log in streaming**.

![Abilitare lo streaming dei log](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Lo streaming dei log viene eseguito nella finestra **Output**. 

![Streaming dei log nella finestra Output](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

I messaggi di traccia tuttavia non sono ancora visibili. Ciò avviene perché la prima volta che si seleziona **Visualizza log in streaming** l'app Web di Azure imposta il livello di traccia su `Error` che comporta soltanto la registrazione degli eventi di errore (con il metodo `Trace.TraceError()`).

### <a name="change-trace-levels"></a>Modificare i livelli di traccia

Per modificare i livelli di traccia per generare altri messaggi di traccia, tornare a **Esplora server**.

Fare di nuovo clic con il pulsante destro del mouse sull'app Web di Azure e selezionare **Impostazioni**.

Nell'elenco a discesa **Registrazione applicazioni (file system)** selezionare **Dettagli**. Fare clic su **Salva**.

![Modificare il livello di traccia in Dettagli](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> È possibile provare i diversi livelli di traccia per verificare i tipi di messaggi visualizzati per ogni livello. Ad esempio, il livello **Informazioni** include tutti i log creati da `Trace.TraceInformation()`, `Trace.TraceWarning()` e `Trace.TraceError()`, ma non i log creati da `Trace.WriteLine()`.
>
>

Nel browser provare a fare clic all'interno dell'applicazione dell'elenco attività in Azure. Viene eseguito lo streaming dei messaggi di traccia nella finestra **Output** in Visual Studio.

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Arrestare lo streaming dei log

Per arrestare il servizio di streaming dei log, fare clic sul pulsante **Interrompi monitoraggio** nella finestra **Output**.

![Arrestare lo streaming dei log](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata. 



Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Viene visualizzata la pagina dell'app Web. 

Per impostazione predefinita, il portale visualizza la pagina **Panoramica**. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire. 

![Pagina del servizio app nel portale di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un database SQL in Azure
> * Connettere un'app ASP.NET al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)

