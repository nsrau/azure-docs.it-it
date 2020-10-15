---
title: 'Esercitazione: ASP.NET Core con database SQL di Azure'
description: Informazioni su come ottenere un'app .NET Core da usare nel servizio app di Azure, con connessione a un database SQL di Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b83dfbd715a6b27491fd5f5efd41213210092325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90982879"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Esercitazione: Creare un'app ASP.NET Core con database SQL di Azure nel servizio app di Azure

::: zone pivot="platform-windows"  

Il [Servizio app di Azure](overview.md) fornisce un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione in Azure. Questa esercitazione mostra come creare un'app .NET Core e connetterla al database SQL. Al termine, si avrà un'app .NET Core MVC in esecuzione nel servizio app in Windows.

::: zone-end

::: zone pivot="platform-linux"

Il [Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e applicazione automatica di patch basato sul sistema operativo Linux. Questa esercitazione mostra come creare un'app .NET Core e connetterla a un database SQL. Al termine, si avrà un'app .NET Core MVC in esecuzione nel servizio app in Linux.

::: zone-end

![App in esecuzione nel servizio app](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database SQL in Azure
> * Connettere un'app .NET Core al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* <a href="https://git-scm.com/" target="_blank">Installare Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installare l'ultima versione di .NET Core 3.1 SDK</a>

## <a name="create-local-net-core-app"></a>Creare l'app .NET Core locale

In questo passaggio si configura il progetto .NET Core locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.

Eseguire i comandi seguenti per clonare il repository di esempio e passare alla radice.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Il progetto di esempio contiene un'app CRUD (create-read-update-delete) di base che usa [Entity Framework](/ef/core/).

### <a name="run-the-application"></a>Eseguire l'applicazione

Usare i comandi seguenti per installare i pacchetti necessari, eseguire le migrazioni dei database e avviare l'applicazione.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Andare a `http://localhost:5000` in un browser. Selezionare il collegamento **Crea nuovo** e creare due elementi _Attività_.

![Connessione al database SQL riuscita](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Per arrestare .NET Core in qualsiasi momento, premere `Ctrl+C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Creare il database SQL di produzione

In questo passaggio si crea un database SQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

Come database SQL questa esercitazione usa [Database SQL di Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Creare un server logico per il database SQL

In Cloud Shell creare un server logico di database SQL con il comando [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Sostituire il segnaposto *\<server-name>* con un nome di database SQL *univoco*. Questo nome viene usato nell'endpoint del database SQL univoco globale `<server-name>.database.windows.net`. I caratteri validi sono `a`-`z`, `0`-`9`, `-`. Sostituire anche *\<db-username>* e *\<db-password>* con un nome utente e una password di propria scelta. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Al termine della creazione del server logico di database SQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una [regola del firewall a livello di server di database SQL di Azure](../azure-sql/database/firewall-configure.md) con il comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

In Cloud Shell eseguire di nuovo il comando per consentire l'accesso dal computer locale sostituendo *\<your-ip-address>* con l'[indirizzo IPv4 locale](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Creazione di un database

Creare un database con [livello di prestazioni S0](../azure-sql/database/service-tiers-dtu.md) nel server con il comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Creare la stringa di connessione

Ottenere la stringa di connessione con il comando [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string).

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

Nell'output del comando sostituire *\<username>* e *\<password>* con le credenziali di amministratore del database usate in precedenza.

Questa è la stringa di connessione per l'app .NET Core. Copiarla per usarla in seguito.

### <a name="configure-app-to-connect-to-production-database"></a>Configurare l'app per connettersi al database di produzione

Nel repository locale aprire Startup.cs e trovare il codice seguente:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Sostituirla con il codice seguente.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> Per le app di produzione per cui è necessario aumentare il numero di istanze, seguire le procedure consigliate illustrate in [Applicazione delle migrazioni nell'ambiente di produzione](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

### <a name="run-database-migrations-to-the-production-database"></a>Eseguire le migrazioni del database al database di produzione

L'app si connette attualmente a un database SQLite locale. Ora che è stato configurato un database SQL di Azure, creare di nuovo la migrazione iniziale per usarlo come destinazione. 

Eseguire i comandi seguenti dalla radice del repository. Sostituire *\<connection-string>* con la stringa di connessione creata in precedenza.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Eseguire l'app con la nuova configurazione

Ora che le migrazioni di database vengono eseguite nel database di produzione, testare l'app eseguendo il comando seguente:

```
dotnet run
```

Andare a `http://localhost:5000` in un browser. Selezionare il collegamento **Crea nuovo** e creare due elementi _Attività_. L'app ora legge e scrive i dati nel database di produzione.

Eseguire il commit delle modifiche locali e quindi eseguire il commit nel repository GIT. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

È ora possibile distribuire il codice.

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio si distribuisce l'applicazione .NET Core connessa al database SQL nel servizio app.

### <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Creare un'app Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Configurare la stringa di connessione

Per impostare le stringhe di connessione per l'app Azure, usare il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Nel comando seguente sostituire *\<app-name>* e il parametro *\<connection-string>* con la stringa di connessione creata in precedenza.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

In ASP.NET Core è possibile usare questa stringa di connessione denominata (`MyDbConnection`) con il modello standard, così come qualsiasi stringa di connessione specificata in *appsettings.json*. In questo caso, la stringa `MyDbConnection` è definita anche in *appsettings.json*. In caso di esecuzione nel servizio app, la stringa di connessione definita nel servizio app ha la precedenza su quella definita in *appsettings.json*. Il codice usa il valore di *appsettings.json* durante lo sviluppo locale e il valore del servizio app quando viene distribuito.

Per osservare come viene fatto riferimento alla stringa di connessione nel codice, vedere [Configurare l'app per connettersi al database di produzione](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare all'app distribuita usando il Web browser.

```bash
http://<app-name>.azurewebsites.net
```

Aggiungere alcune attività.

![App in esecuzione nel servizio app](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Congratulazioni** Si sta eseguendo un'app .NET Core basata sui dati nel servizio app.

## <a name="update-locally-and-redeploy"></a>Eseguire l'aggiornamento e la ridistribuzione in locale

In questo passaggio si apporta una modifica allo schema del database e la si pubblica in Azure.

### <a name="update-your-data-model"></a>Aggiornare il modello di dati

Aprire _Models/Todo.cs_ nell'editor di codice. Aggiungere la proprietà seguente alla classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Ripetere le migrazioni del database

Eseguire alcuni comandi per eseguire gli aggiornamenti del database di produzione.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Se si apre una nuova finestra del terminale, è necessario impostare la stringa di connessione sul database di produzione nel terminale, come illustrato in [Eseguire le migrazioni del database al database di produzione](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Usare la nuova proprietà

Apportare alcune modifiche al codice per usare la proprietà `Done`. Per motivi di semplicità in questa esercitazione vengono modificate solo le visualizzazioni `Index` e `Create` per visualizzare la proprietà.

Aprire _Controllers/TodosController.cs_.

Trovare il metodo `Create([Bind("ID,Description,CreatedDate")] Todo todo)` e aggiungere `Done` all'elenco delle proprietà nell'attributo `Bind`. Al termine, la firma del metodo `Create()` è simile al codice seguente:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Aprire _Views/Todos/Create.cshtml_.

Nel codice Razor viene visualizzato un elemento `<div class="form-group">` per `Description` e quindi un altro elemento `<div class="form-group">` per `CreatedDate`. Immediatamente dopo questi due elementi, aggiungere un altro elemento `<div class="form-group">` per `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Aprire _Views/Todos/Index.cshtml_.

cercare l'elemento `<th></th>` vuoto. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Trovare l'elemento `<td>` che contiene i gli helper per i tag `asp-action`. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Le modifiche verranno visualizzate nelle visualizzazioni `Index` e `Create`.

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Eseguire l'app in locale.

```bash
dotnet run
```

> [!NOTE]
> Se si apre una nuova finestra del terminale, è necessario impostare la stringa di connessione sul database di produzione nel terminale, come illustrato in [Eseguire le migrazioni del database al database di produzione](#run-database-migrations-to-the-production-database).
>

Nel browser passare a `http://localhost:5000/`. È ora possibile aggiungere un'attività e selezionare **Fine**. L'attività viene visualizzata come completata nella home page. Tenere presente che la visualizzazione `Edit` non mostra il campo `Done` poiché non è stata modificata la visualizzazione `Edit`.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Al termine di `git push`, passare all'app del servizio app, provare ad aggiungere un'attività e selezionare **Done** (Fine).

![App Azure dopo la migrazione Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Tutte le attività esistenti rimangono visualizzate. Quando si pubblica nuovamente l'app ASP.NET Core, i dati esistenti nel database SQL non vengono persi. Inoltre le migrazioni di Entity Framework Core modificano solo lo schema dei dati lasciando intatti i dati esistenti.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

Mentre l'app ASP.NET Core è in esecuzione nel servizio app di Azure, è possibile fare in modo che i log di console siano inviati tramite pipe a Cloud Shell. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Il progetto di esempio segue già le indicazioni riportate in [Registrazione in ASP.NET Core in Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) con due modifiche della configurazione:

- Include un riferimento a `Microsoft.Extensions.Logging.AzureAppServices` in *DotNetCoreSqlDb.csproj*.
- Chiama `loggerFactory.AddAzureWebAppDiagnostics()` in *Program.cs*.

Per impostare il [livello di registrazione](/aspnet/core/fundamentals/logging#log-level) di ASP.NET Core nel Servizio app su `Information` dal livello predefinito `Error`, usare il comando [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) in Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Il livello di registrazione del progetto è già impostato su `Information` in *appsettings.json*.
> 

Per avviare lo streaming dei log, usare il comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) in Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Dopo avere avviato lo streaming dei log, aggiornare l'app di Azure nel browser per ricevere traffico Web. I log di console vengono inviati tramite pipe al terminale. Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.

Per altre informazioni sulla personalizzazione dei logo di ASP.NET Core, vedere [Registrazione in ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gestire l'app Azure

Per visualizzare l'app creata, nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Servizi app**.

![Selezionare Servizi app nel portale di Azure](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

Nella pagina **Servizi app** selezionare il nome dell'app di Azure.

![Passaggio all'app di Azure nel portale](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un database SQL in Azure
> * Connettere un'app .NET Core al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare l'app ASP.NET Core](configure-language-dotnetcore.md)
