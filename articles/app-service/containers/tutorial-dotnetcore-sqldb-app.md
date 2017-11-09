---
title: Creare un'app Web di database SQL e .NET Core in Servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come ottenere un'app .NET Core da usare in Servizio app di Azure in Linux, con connessione a un database SQL.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ef68f64437935f08f76c29ecf15d574279cca7f1
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>Creare un'app Web di database SQL e .NET Core in Servizio app di Azure in Linux

Un [servizio app in Linux](app-service-linux-intro.md) fornisce un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa esercitazione illustra come creare un'app Web .NET Core e connetterla a un database SQL. Al termine, si avrà un'app .NET Core MVC in esecuzione nel servizio app in Linux.

![App in esecuzione nel servizio app in Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Si apprenderà come:

> [!div class="checklist"]
> * Creare un database SQL in Azure
> * Connettere un'app .NET Core al database SQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>Creare l'app .NET Core locale

In questo passaggio si configura il progetto .NET Core locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.

Eseguire i comandi seguenti per clonare il repository di esempio e passare alla radice.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Il progetto di esempio contiene un'app CRUD (create-read-update-delete) di base che usa [Entity Framework](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Eseguire l'applicazione

Usare i comandi seguenti per installare i pacchetti necessari, eseguire le migrazioni dei database e avviare l'applicazione.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Andare a `http://localhost:5000` in un browser. Selezionare il collegamento **Crea nuovo** e creare due elementi _Attività_.

![Connessione al database SQL riuscita](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Per arrestare .NET Core in qualsiasi momento, premere `Ctrl+C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Creare il database SQL di produzione

In questo passaggio si crea un database SQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

Come database SQL questa esercitazione usa [Database SQL di Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Creare un server logico per il database SQL

In Cloud Shell creare un server logico di database SQL con il comando [az sql server create](/cli/azure/sql/server#create).

Sostituire il segnaposto *\<server_name>* con un nome di database SQL univoco. Poiché questo nome è incluso nell'endpoint del database SQL, `<server_name>.database.windows.net`, è necessario che sia univoco in tutti i server logici in Azure. Il nome deve contenere solo lettere minuscole, numeri e il carattere (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri. Sostituire anche *\<db_username>* e *\<db_password>* con un nome utente e una password di propria scelta. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Al termine della creazione del server logico di database SQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurare una regola del firewall del server

Creare una [regola del firewall del server del database SQL di Azure](../../sql-database/sql-database-firewall-configure.md) con il comando [az sql server firewall create](/cli/azure/sql/server#create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Creare un database

Creare nel server un database con [livello di prestazioni S0](../../sql-database/sql-database-service-tiers.md) con il comando [az sql db create](/cli/azure/sql/db#create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Creare la stringa di connessione

Sostituire la stringa seguente con gli elementi *\<server_name>*, *\<db_username>* e *\<db_password>* usati prima.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Questa è la stringa di connessione per l'app .NET Core. Copiarla per usarla in seguito.

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio si distribuisce l'applicazione .NET Core connessa al database SQL nel servizio app in Linux.

### <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurare una variabile di ambiente

Per impostare le stringhe di connessione per l'app Azure, usare il comando [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update) in Cloud Shell. Nel comando seguente sostituire *\<app name>* e il parametro *\<connection_string>* con la stringa di connessione creata prima.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Configurare quindi l'impostazione dell'app `ASPNETCORE_ENVIRONMENT` su _Production_. Questa impostazione consente di determinare se l'esecuzione è in corso in Azure, perché si usa SQLLite per l'ambiente di sviluppo locale e il database SQL per l'ambiente Azure.

L'esempio seguente configura l'impostazione dell'app `ASPNETCORE_ENVIRONMENT` nell'app Web di Azure. Sostituire il segnaposto *\<app_name>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Connettersi al database SQL nell'ambiente di produzione

Nel repository locale aprire Startup.cs e trovare il codice seguente:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Sostituirlo con il codice seguente, che usa le variabili di ambiente configurate prima.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

Se questo codice rileva di essere in esecuzione nell'ambiente di produzione (che indica l'ambiente Azure), usa la stringa di connessione configurata per connettersi al database SQL.

La chiamata a `Database.Migrate()` è utile quando viene eseguita in Azure, perché crea automaticamente i database necessari per l'app .NET Core, in base alla configurazione della migrazione. 

Salvare le modifiche.

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare all'app Web distribuita usando il Web browser.

```bash
http://<app_name>.azurewebsites.net
```

Aggiungere alcune attività.

![App in esecuzione nel servizio app in Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Congratulazioni.** Si sta eseguendo un'app .NET Core basata sui dati nel servizio app in Linux.

## <a name="update-locally-and-redeploy"></a>Eseguire l'aggiornamento e la ridistribuzione in locale

In questo passaggio si apporta una modifica allo schema del database e la si pubblica in Azure.

### <a name="update-your-data-model"></a>Aggiornare il modello di dati

Aprire _Models\Todo.cs_ nell'editor di codice. Aggiungere la proprietà seguente alla classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Eseguire Migrazioni Code First in locale

Eseguire alcuni comandi per eseguire gli aggiornamenti del database locale.

```bash
dotnet ef migrations add AddProperty
```

Aggiornare il database locale:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Usare la nuova proprietà

Apportare alcune modifiche al codice per usare la proprietà `Done`. Per motivi di semplicità in questa esercitazione vengono modificate solo le visualizzazioni `Index` e `Create` per visualizzare la proprietà.

Aprire _Controllers\TodosController.cs_.

Trovare il metodo `Create()` e aggiungere `Done` all'elenco delle proprietà nell'attributo `Bind`. Al termine, la firma del metodo `Create()` è simile al codice seguente:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Aprire _Views\Todos\Create.cshtml_.

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

Aprire _Views\Todos\Index.cshtml_.

cercare l'elemento `<th></th>` vuoto. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Trovare l'elemento `<td>` che contiene i gli helper per i tag `asp-action`. Immediatamente sopra l'elemento, aggiungere il codice Razor seguente:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

Le modifiche verranno visualizzate nelle visualizzazioni `Index` e `Create`.

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Eseguire l'app in locale.

```bash
dotnet run
```

Nel browser passare a `http://localhost:5000/`. È ora possibile aggiungere un'attività e selezionare **Fine**. L'attività viene visualizzata come completata nella home page. Tenere presente che la visualizzazione `Edit` non mostra il campo `Done` poiché non è stata modificata la visualizzazione `Edit`.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

```bash

git commit -am "added done field"
git push azure master
```

Dopo aver completato `git push`, passare all'app Web di Azure e provare la nuova funzionalità.

![App Web di Azure dopo la migrazione Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Tutte le attività esistenti rimangono visualizzate. Quando si pubblica nuovamente l'app .NET Core, i dati esistenti nel database SQL non vengono persi. Inoltre le migrazioni di Entity Framework Core modificano solo lo schema dei dati lasciando intatti i dati esistenti.

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)