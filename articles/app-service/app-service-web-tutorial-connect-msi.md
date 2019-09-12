---
title: Proteggere la connessione al database SQL con un'identità gestita - Servizio app di Azure | Microsoft Docs
description: Informazioni su come rendere più sicura la connettività del database con un'identità gestita e su come applicarla ad altri servizi di Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 09e9a89fc79763eee5d154ba589b599fe8a180b2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743390"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Esercitazione: Proteggere la connessione al database SQL di Azure dal servizio app con un'identità gestita

Il [Servizio app](overview.md) fornisce un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione in Azure. Offre anche un'[identità gestita](overview-managed-identity.md) per l'app, una soluzione chiavi in mano per proteggere l'accesso al [database SQL di Azure](/azure/sql-database/) e ad altri servizi di Azure. Le identità gestite nel servizio app rendono l'app più sicura eliminando i segreti dall'app, ad esempio le credenziali nelle stringhe di connessione. In questa esercitazione si aggiungerà un'identità gestita all'app Web di esempio creata in una delle esercitazioni seguenti: 

- [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Esercitazione: Compilare un'app ASP.NET Core e database SQL in Servizio app di Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

Al termine, l'app di esempio si connetterà al database SQL in modo sicuro senza che siano necessari nome utente e password.

> [!NOTE]
> Le procedure descritte in questa esercitazione supportano le versioni seguenti:
> 
> - .NET Framework 4.7.2 e versioni successive.
> - .NET Core 2.2 e versioni successive.
>

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Abilitare le identità gestite
> * Concedere all'identità gestita l'accesso al database SQL
> * Configurare Entity Framework per usare l'autenticazione di Azure AD con il database SQL
> * Connettersi al database SQL da Visual Studio usando l'autenticazione di Azure AD

> [!NOTE]
>L'autenticazione di Azure AD è _diversa_ dall'[autenticazione integrata di Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) nell'ambiente Active Directory locale (Active Directory Domain Services). Active Directory Domain Services e Azure AD usano protocolli di autenticazione completamente diversi. Per altre informazioni, vedere la [Documentazione di Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questo articolo continua da dove è stato interrotto in [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md) o [Esercitazione: Compilare un'app ASP.NET Core e database SQL in Servizio app di Azure](app-service-web-tutorial-dotnetcore-sqldb.md). Se non è già stato fatto, seguire prima una delle due esercitazioni. In alternativa, è possibile adattare le procedure alla propria app .NET con un database SQL.

Per eseguire il debug dell'app usando il database SQL come back-end, assicurarsi di aver consentito la connessione client dal computer. In caso contrario, aggiungere l'indirizzo IP del client seguendo la procedura descritta in [Gestire regole del firewall IP a livello di server tramite il portale di Azure](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Concedere l'accesso al database all'utente di Azure AD

Abilitare prima di tutto l'autenticazione di Azure AD nel database SQL assegnando un utente di Azure AD come amministratore di Active Directory del server di database SQL. Questo utente è diverso dall'account Microsoft usato per effettuare l'iscrizione alla sottoscrizione di Azure. Deve essere un utente creato, importato, sincronizzato o invitato in Azure AD. Per altre informazioni sugli utenti di Azure AD consentiti, vedere [Funzionalità e limitazioni di Azure AD nel database SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Se il tenant di Azure AD non contiene ancora utenti, crearne uno seguendo la procedura descritta in [Aggiungere o eliminare utenti con Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Trovare l'ID oggetto dell'utente di Azure AD tramite [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) e sostituire *\<user-principal-name>* . Il risultato viene salvato in una variabile.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Per visualizzare l'elenco di tutti i nomi delle entità utente in Azure AD, eseguire `az ad user list --query [].userPrincipalName`.
>

Aggiungere questo utente di Azure AD come amministratore di Active Directory usando il comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) in Cloud Shell. Nel comando seguente sostituire *\<server-name>* con il nome del server di database SQL (senza il suffisso `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Per altre informazioni sull'aggiunta di un amministratore di Active Directory, vedere [Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurare Visual Studio

Per abilitare lo sviluppo e il debug in Visual Studio, aggiungere l'utente di Azure AD in Visual Studio scegliendo **File** > **Impostazioni account** dal menu e facendo clic su **Aggiungi un account**.

Per impostare l'utente di Azure AD per l'autenticazione dei servizi di Azure, scegliere **Strumenti** > **Opzioni** dal menu, quindi selezionare **Azure Service Authentication (Autenticazione servizi di Azure)**  > **Selezione account**. Selezionare l'utente di Azure AD aggiunto e fare clic su **OK**.

A questo punto è possibile iniziare a sviluppare l'app ed eseguirne il debug con il database SQL come back-end, usando l'autenticazione di Azure AD.

## <a name="modify-your-project"></a>Modificare il progetto

La procedura da seguire per il progetto varia a seconda che si tratti di un progetto ASP.NET o di un progetto ASP.NET Core.

- [Modificare ASP.NET](#modify-aspnet)
- [Modificare ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificare ASP.NET

In Visual Studio, aprire la Console di Gestione pacchetti e aggiungere il pacchetto NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

In *Web.config* apportare le modifiche seguenti partendo dall'inizio del file:

- In `<configSections>` aggiungere la dichiarazione di sezione seguente:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Sotto il tag `</configSections>` di chiusura aggiungere il codice XML seguente per `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Trovare la stringa di connessione denominata `MyDbConnection` e sostituire il relativo valore `connectionString` con `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Sostituire _\<server-name>_ e _\<db-name>_ con i nomi del server e del database in uso.

Per connettersi al database SQL non sono necessarie altre modifiche. Quando si esegue il debug in Visual Studio, il codice usa l'utente di Azure AD configurato in [Configurare Visual Studio](#set-up-visual-studio). Il server di database SQL verrà configurato in un secondo momento per consentire la connessione dall'identità gestita dell'app del servizio app.

Digitare `Ctrl+F5` per eseguire di nuovo l'app. La stessa app CRUD nel browser si connette ora al database SQL di Azure direttamente, usando l'autenticazione di Azure AD. Questa configurazione consente di eseguire migrazioni del database da Visual Studio.

### <a name="modify-aspnet-core"></a>Modificare ASP.NET Core

In Visual Studio, aprire la Console di Gestione pacchetti e aggiungere il pacchetto NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

Nell'[esercitazione su ASP.NET Core e database SQL](app-service-web-tutorial-dotnetcore-sqldb.md), la stringa di connessione `MyDbConnection` non viene usata perché l'ambiente locale usa un file di database SQLite e l'ambiente di produzione Azure usa una stringa di connessione del servizio app. Con l'autenticazione di Active Directory si vuole che entrambi gli ambienti usino la stessa stringa di connessione. In *appsettings.json* sostituire il valore della stringa di connessione `MyDbConnection` con:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

In *Startup.cs* rimuovere la sezione di codice aggiunta prima:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Sostituirla quindi con il codice seguente:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Specificare quindi il contesto di database Entity Framework con il token di accesso per il database SQL. In *Data\MyDatabaseContext.cs* aggiungere il codice seguente all'interno delle parentesi graffe del costruttore vuoto `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)`:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Questo codice dimostrativo è sincrono per maggiore chiarezza. Per altre informazioni, vedere la [guida asincrona per i costruttori](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Per connettersi al database SQL non sono necessarie altre modifiche. Quando si esegue il debug in Visual Studio, il codice usa l'utente di Azure AD configurato in [Configurare Visual Studio](#set-up-visual-studio). Il server di database SQL verrà configurato in un secondo momento per consentire la connessione dall'identità gestita dell'app del servizio app. La classe `AzureServiceTokenProvider` memorizza nella cache il token e lo recupera da Azure AD appena prima della scadenza. Per aggiornare il token, non è necessario specificare codice personalizzato.

Digitare `Ctrl+F5` per eseguire di nuovo l'app. La stessa app CRUD nel browser si connette ora al database SQL di Azure direttamente, usando l'autenticazione di Azure AD. Questa configurazione consente di eseguire migrazioni del database da Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Usare la connettività dell'identità gestita

Configurare quindi l'app del servizio app per la connessione al database SQL con un'identità gestita assegnata dal sistema.

### <a name="enable-managed-identity-on-app"></a>Abilitare l'identità gestita nell'app

Per abilitare un'identità gestita per l'app Azure, usare il comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) in Cloud Shell. Nel comando seguente sostituire *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Ecco un esempio di output:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Aggiungere l'identità gestita a un gruppo di Azure AD

Per concedere a questa identità l'accesso al database SQL, è necessario aggiungerla a un [gruppo di Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). In Cloud Shell aggiungere l'identità gestita a un nuovo gruppo denominato _myAzureSQLDBAccessGroup_, come illustrato nello script seguente:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Per visualizzare l'output JSON completo per ogni comando, rimuovere i parametri `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Concedere le autorizzazioni al gruppo di Azure AD

In Cloud Shell accedere al database SQL con il comando SQLCMD. Sostituire _\<server-name>_ con il nome del server di database SQL, _\<db-name>_ con il nome del database usato dall'app e _\<aad-user-name>_ e _\<aad-password>_ con le credenziali dell'utente di Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Al prompt di SQL per il database eseguire i comandi seguenti per aggiungere il gruppo di Azure AD e concedere le autorizzazioni necessarie per l'app. Ad esempio, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Digitare `EXIT` per tornare al prompt di Cloud Shell.

### <a name="modify-connection-string"></a>Modificare la stringa di connessione

Tenere presente che le stesse modifiche apportate in *Web.config* o *appsettings.json* funzionano con l'identità gestita, quindi è sufficiente rimuovere la stringa di connessione esistente nel servizio app che è stata creata da Visual Studio quando l'app è stata distribuita per la prima volta. Usare il comando seguente, ma sostituire *\<app-name>* con il nome dell'app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Pubblicare le modifiche

A questo punto è sufficiente pubblicare le modifiche in Azure.

**Se l'esercitazione completata in precedenza è [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md)** , pubblicare le modifiche in Visual Studio. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **DotNetAppSqlDb** e selezionare **Pubblica**.

![Pubblicare da Esplora soluzioni](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Nella pagina di pubblicazione fare clic su **Pubblica**. 

**Se l'esercitazione completata in precedenza è [Esercitazione: Compilare un'app ASP.NET Core e database SQL in Servizio app di Azure](app-service-web-tutorial-dotnetcore-sqldb.md)** , pubblicare le modifiche usando Git, con i comandi seguenti:

```bash
git commit -am "configure managed identity"
git push azure master
```

Quando nella nuova pagina Web viene visualizzato l'elenco attività, l'app si connette al database con l'identità gestita.

![App Azure dopo la migrazione Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Dovrebbe ora essere possibile modificare l'elenco attività come prima.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Abilitare le identità gestite
> * Concedere all'identità gestita l'accesso al database SQL
> * Configurare Entity Framework per usare l'autenticazione di Azure AD con il database SQL
> * Connettersi al database SQL da Visual Studio usando l'autenticazione di Azure AD

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](app-service-web-tutorial-custom-domain.md)
