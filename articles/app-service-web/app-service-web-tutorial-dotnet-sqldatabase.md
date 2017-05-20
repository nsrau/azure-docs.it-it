---
title: Creare un&quot;app ASP.NET in Azure con un database SQL | Microsoft Docs
description: "Informazioni su come ottenere un&quot;app ASP.NET che è possibile usare in Azure con connessione a un database SQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 6563d1520149ae5ced7e2de80686ef1624ebb651
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Creare un'app ASP.NET in Azure con un database SQL

Questa esercitazione illustra come sviluppare un'app Web ASP.NET basata sui dati in Azure, connetterla al database SQL di Azure e abilitare la funzionalità collegata ai dati. Al termine sarà disponibile un'applicazione ASP.NET in esecuzione nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) e connessa al database SQL.

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

Prima di eseguire questo esempio, [scaricare e installare la versione gratuita di Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio
In questo passaggio viene scaricata un'applicazione ASP.NET di esempio.

### <a name="get-the-sample-project"></a>Ottenere il progetto di esempio

Scaricare il progetto di esempio facendo clic [qui](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Estrarre il file _dotnet-sqldb-tutorial-master.zip_ scaricato in una directory di lavoro.

> [!TIP]
> È possibile ottenere il progetto di esempio tramite la clonazione del repository GitHub:
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

Questo progetto di esempio contiene l'applicazione CRUD (create-read-update-delete) [ASP.NET MVC](https://www.asp.net/mvc) basata su [Code First di Entity Framework](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-application"></a>Eseguire l'applicazione

Dalla directory estratta aprire _dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln_ in Visual Studio 2017.

Dopo aver aperto la soluzione di esempio, digitare `F5` per eseguirla nel browser.

Nella home page viene visualizzato un elenco attività semplice. Provare ad aggiungere alcune attività all'elenco vuoto.

![Finestra di dialogo Nuovo progetto ASP.NET](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Il contesto di database usa una stringa di connessione denominata `MyDbConnection`. A questa stringa di connessione, definita in _Web.config_, viene fatto riferimento in _Models\MyDatabaseContext.cs_. In seguito, al momento della connessione dell'app Web di Azure al database SQL di Azure, sarà sufficiente specificare il nome della stringa di connessione. 

## <a name="publish-to-azure-with-sql-database"></a>Pubblicare in Azure con il database SQL

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **DotNetAppSqlDb** e selezionare **Pubblica**.

![Pubblicare da Esplora soluzioni](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Verificare che **Servizio app di Microsoft Azure** sia selezionato e fare clic su **Pubblica**.

![Pubblicare dalla pagina di panoramica progetto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Si apre la finestra di dialogo **Crea servizio app**, che consente di creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Nella finestra di dialogo **Crea servizio App** fare clic su **Aggiungi un account** e quindi accedere alla sottoscrizione di Azure. Se si è già connessi a un account Microsoft, verificare che l'account contenga la sottoscrizione di Azure. Se l'account Microsoft a cui si è connessi non include la sottoscrizione di Azure, fare clic su di esso per aggiungere l'account corretto.
   
![Accedere ad Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Dopo avere eseguito l'accesso, è possibile creare tutte le risorse necessarie per l'app Web di Azure in questa finestra di dialogo.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per prima cosa, è necessario un _gruppo di risorse_. 

> [!NOTE] 
> Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione.
>
>

Accanto a **Gruppo di risorse** fare clic su **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e fare clic su **OK**.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Per l'app Web di Azure è necessario anche un _piano di servizio app_. 

> [!NOTE]
> Un piano di servizio app rappresenta la raccolta delle risorse fisiche usate per ospitare le app. Tutte le app assegnate a un piano di servizio app condividono le risorse definite dal piano, che consente di risparmiare sui costi quando si ospitano più app. 
>
> I piani di servizio app definiscono:
>
> - Area (Europa settentrionale, Stati Uniti orientali, Asia sud-orientale)
> - Dimensioni delle istanze (Small, Medium, Large)
> - Numero di scala (una, due o tre istanze e così via) 
> - SKU (Gratuito, Condiviso, Basic, Standard, Premium)
>
>

Accanto a **Piano di servizio app** fare clic su **Nuovo**. 

Nella finestra di dialogo **Configura piano di servizio app** configurare il nuovo piano di servizio app con le impostazioni seguenti:

- **Piano di servizio app**: digitare **myAppServicePlan**. 
- **Località**: scegliere **Europa occidentale** o un'altra area.
- **Dimensioni**: scegliere **Gratuito** o un altro [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/).

Fare clic su **OK**.

![Creare un piano di servizio app](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>Configurare il nome dell'app Web

In **Nome app Web** digitare un nome univoco per l'app. Questo nome viene usato come parte del nome DNS predefinito per l'app (`<app_name>.azurewebsites.net`) e deve quindi essere univoco tra tutte le app in Azure. In un secondo momento è possibile eseguire il mapping di un nome di dominio personalizzato all'app prima di esporla agli utenti.

È anche possibile accettare il nome generato automaticamente, che è già univoco.

Per preparare il passaggio successivo, fare clic su **Esplora altri servizi di Azure**.

> [!NOTE]
> Non fare ancora clic su **Crea** in questa finestra di dialogo. È prima necessario configurare un database SQL nel passaggio successivo.

![Configurare il nome dell'app Web](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>Creare un'istanza di SQL Server

Nella scheda **Servizi** fare clic sull'icona **+** accanto a **Database SQL**. 

nella finestra di dialogo **Configura database SQL** fare clic su **Nuovo** accanto a **SQL Server**. 

In **Nome server** digitare un nome univoco. Questo nome viene usato come parte del nome DNS predefinito per il server di database (`<server_name>.database.windows.net`) e deve quindi essere univoco tra tutte le istanze di SQL Server in Azure. 

Configurare i campi rimanenti come desiderato e fare clic su **OK**.

![Creare un'istanza di SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>Configurare il database SQL

In **Nome database** digitare _myToDoAppDb_ o qualsiasi nome si preferisca.

In **Nome stringa di connessione** digitare _MyDbConnection_. Questo nome deve corrispondere alla stringa di connessione a cui viene fatto riferimento in _Models\MyDatabaseContext.cs_.

![Configurare il database SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>Creare e pubblicare l'app Web

Fare clic su **Crea**. 

Dopo aver completato la creazione delle risorse di Azure, la procedura guidata pubblica automaticamente l'applicazione ASP.NET in Azure per la prima volta e quindi avvia l'app Web di Azure pubblicata nel browser predefinito.

Provare ad aggiungere alcune attività all'elenco vuoto.

![Applicazione ASP.NET pubblicata nell'app Web di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Congratulazioni. L'applicazione ASP.NET basata sui dati è in esecuzione nel Servizio app di Azure.

## <a name="access-the-sql-database-locally"></a>Accedere al database SQL locale

Visual Studio consente di esplorare e gestire facilmente il nuovo database SQL in **Esplora oggetti di SQL Server**.

### <a name="create-a-database-connection"></a>Creare una connessione al database

Aprire **Esplora oggetti di SQL Server** digitando `Ctrl`+`` ` ``, `Ctrl`+`S`.

Nella parte superiore di **Esplora oggetti di SQL Server** fare clic sul pulsante **Aggiungi istanza di SQL Server**.

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nella finestra di dialogo **Connetti** espandere il nodo **Azure**. Vengono visualizzati tutti i database SQL di Azure.

Selezionare il database SQL creato in precedenza. La connessione usata in precedenza viene inserita automaticamente nella parte inferiore.

Digitare la password di amministratore di database usata in precedenza e fare clic su **Connetti**.

![Configurare la connessione al database da Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Consentire la connessione client dal computer

Viene visualizzata la finestra di dialogo **Crea nuova regola del firewall**. Per impostazione predefinita, l'istanza di SQL Server consente solo le connessioni da servizi di Azure, ad esempio dall'app Web di Azure. Per connettersi al database direttamente da Visual Studio, è necessario creare una regola del firewall nell'istanza di SQL Server per consentire l'indirizzo IP pubblico del computer locale.

In Visual Studio, si tratta di un'operazione semplice. Nella finestra di dialogo è già specificato l'indirizzo IP pubblico del computer.

Assicurarsi che l'opzione **Aggiungi IP client** sia selezionata e fare clic su **OK**. 

![Impostare il firewall per l'istanza di SQL Server](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Dopo che Visual Studio ha completato la creazione dell'impostazione del firewall per l'istanza di SQL Server, la connessione viene visualizzata in **Esplora oggetti di SQL Server**.

In questa posizione è possibile eseguire le operazioni di database più comuni, ad esempio eseguire query, creare visualizzazioni e stored procedure e così via. L'esempio seguente illustra come visualizzare i dati del database. 

![Esplorare gli oggetti del database SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Aggiornare l'app con Migrazioni Code First

In questo passaggio si usa Migrazioni Code First di Entity Framework per apportare una modifica allo schema del database e pubblicarlo in Azure.

### <a name="update-your-data-model"></a>Aggiornare il modello di dati

Aprire _Models\Todo.cs_ nell'editor di codice. Aggiungere la proprietà seguente alla classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Eseguire Migrazioni Code First in locale

Eseguire quindi alcuni comandi per eseguire gli aggiornamenti del database LocalDB. 

Nel menu **Strumenti** fare clic su **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**. La console viene in genere aperta nella finestra inferiore.

Abilitare Migrazioni Code First come segue:

```PowerShell
Enable-Migrations
```

Aggiungere una migrazione come segue:

```PowerShell
Add-Migration AddProperty
```

Aggiornare il database LocalDB come segue:

```PowerShell
Update-Database
```

Testare le modifiche eseguendo l'applicazione con `F5`.

Se l'applicazione viene caricata senza errori, l'esecuzione di Migrazioni Code First è stata completata. La pagina tuttavia può apparire sempre uguale perché la logica dell'applicazione non usa ancora la nuova proprietà. 

### <a name="use-the-new-property"></a>Usare la nuova proprietà

Apportare alcune modifiche al codice per usare la proprietà `Done`. Per motivi di semplicità in questa esercitazione vengono modificate solo le visualizzazioni `Index` e `Create` per visualizzare la proprietà.

Aprire _Controllers\TodosController.cs_.

Trovare il metodo `Create()` e aggiungere `Done` all'elenco delle proprietà nell'attributo `Bind`. Al termine, la firma del metodo `Create()` dovrebbe apparire come segue:

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

Aprire _Views\Todos\Create.cshtml_.

Nel codice Razor viene visualizzato un tag `<div class="form-group">` che usa `model.Description` e quindi un altro tag `<div class="form-group">` che usa `model.CreatedDate`. Immediatamente dopo questi due tag, aggiungere un altro tag `<div class="form-group">` che usa `model.Done` come segue:

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

Cercare il tag `<th></th>` vuoto. Immediatamente sopra il tag, aggiungere il codice Razor seguente:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Trovare il `<td>` tag che contiene il `Html.ActionLink()` metodi helper. Immediatamente sopra il tag, aggiungere il codice Razor seguente:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Le modifiche verranno visualizzate nelle visualizzazioni `Index` e `Create`. 

Digitare nuovamente `F5` per eseguire l'applicazione.

È ora possibile aggiungere un'attività e selezionare **Fine**. L'attività viene visualizzata come completata nella home page. Si noti che queste sono le uniche operazioni possibili al momento poiché non è stata modificata la visualizzazione `Edit`.

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

Dopo aver abilitato Migrazioni Code First nell'app Web di Azure è possibile pubblicare le modifiche.

Nella pagina di pubblicazione fare clic su **Pubblica**.

Provare di nuovo ad aggiungere attività e selezionare **Fine**. Le attività verranno visualizzate nella home page come completate.

![App Web di Azure dopo la migrazione Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> Si noti che tutte le attività esistenti rimangono visualizzate. Quando si pubblica nuovamente l'applicazione ASP.NET, i dati esistenti nel database SQL non vengono persi. Inoltre, solo Migrazioni Code First modifica lo schema dei dati lasciando intatti i dati esistenti.
>
>

## <a name="stream-application-logs"></a>Eseguire lo streaming dei log delle applicazioni

È possibile eseguire lo streaming dei messaggi di traccia direttamente dall'app Web di Azure in Visual Studio.

Aprire _Controllers\TodosController.cs_.

Si noti che ogni azione inizia con un metodo `Trace.WriteLine()`. Questo codice è stato aggiunto per illustrare com'è facile aggiungere messaggi di traccia all'app Web di Azure.

### <a name="open-server-explorer"></a>Aprire Esplora server

È possibile configurare la registrazione per l'app Web di Azure in **Esplora server**. 

Per aprire Esplora server, digitare `Ctrl`+`Alt`+`S`.

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

Accedere al portale di Azure per visualizzare l'app Web creata. 

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app Web. 

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire. 

![Pannello del servizio app nel portale di Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:

- Eseguire il mapping di un nome DNS personalizzato
- Associare un certificato SSL personalizzato
- Configurare la distribuzione continua
- Aumentare le prestazioni e il numero di istanze
- Aggiungere l'autenticazione utente

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse
 
Se queste risorse non sono necessarie per un'altra esercitazione (vedere [Passaggi successivi](#next)), è possibile eliminarle eseguendo questo comando: 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

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

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)

