---
title: Proteggere la connessione al database SQL con un'identità gestita - Servizio app di Azure | Microsoft Docs
description: Informazioni su come rendere più sicura la connettività del database usando un'identità gestita e su come applicarla ad altri servizi di Azure.
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
ms.date: 11/30/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: dd84f9b3b68d7a34903241caed7f1f93e685fb57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138977"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Esercitazione: Proteggere la connessione al database SQL di Azure dal servizio app con un'identità gestita

Il [Servizio app](overview.md) fornisce un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione in Azure. Offre anche un'[identità gestita](overview-managed-identity.md) per l'app, una soluzione chiavi in mano per proteggere l'accesso al [database SQL di Azure](/azure/sql-database/) e ad altri servizi di Azure. Le identità gestite nel servizio app rendono l'app più sicura eliminando i segreti dall'app, ad esempio le credenziali nelle stringhe di connessione. In questa esercitazione si aggiungerà un'identità gestita all'app Web ASP.NET di esempio creata in [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Al termine, l'app di esempio si connetterà al database SQL in modo sicuro senza che siano necessari nome utente e password.

> [!NOTE]
> Questo scenario è attualmente supportato da .NET Framework 4.6 e versioni successive, ma non da [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows). [.NET core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) supporta lo scenario, ma non è stato ancora incluso nelle immagini predefinite nel servizio app. 
>

Si apprenderà come:

> [!div class="checklist"]
> * Abilitare le identità gestite
> * Concedere all'identità gestita l'accesso al database SQL
> * Configurare il codice dell'applicazione per eseguire l'autenticazione al database SQL con l'autenticazione di Azure Active Directory
> * Concedere privilegi minimi all'identità gestita nel database SQL

> [!NOTE]
>L'autenticazione di Azure Active Directory è _diversa_ dall'[autenticazione integrata di Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) in Active Directory locale (AD DS). AD DS e Azure Active Directory usano protocolli di autenticazione completamente diversi. Per altre informazioni, vedere la [Documentazione di Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questo articolo continua da dove è stato interrotto in [Esercitazione: Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Se non si è ancora provveduto, seguire prima tale esercitazione. In alternativa, è possibile adattare le procedure alla propria app ASP.NET con un database SQL.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Abilitare le identità gestite

Per abilitare un'identità gestita per l'app Azure, usare il comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) in Cloud Shell. Nel comando seguente sostituire *\<app name>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Di seguito è riportato un esempio dell'output dopo la creazione dell'identità in Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Il valore di `principalId` verrà usato nel passaggio successivo. Per visualizzare i dettagli della nuova identità in Azure Active Directory, eseguire questo comando facoltativo con il valore di `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Concedere all'identità l'accesso al database

A questo punto si concede all'identità gestita dell'app l'accesso al database usando il comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest) in Cloud Shell. Nel comando seguente sostituire *\<server_name>* e <principalid_from_last_step>. Digitare il nome di un amministratore al posto di *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

L'identità gestita ha ora accesso al server di database SQL di Azure.

## <a name="modify-connection-string"></a>Modificare la stringa di connessione

Modificare la connessione impostata in precedenza per l'app usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Nel comando seguente sostituire *\<app name>* con il nome dell'app e *\<server_name>* e *\<db_name>* con i nomi per il database SQL.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modificare il codice ASP.NET

In Visual Studio, aprire la Console di Gestione pacchetti e aggiungere il pacchetto NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.1.0-preview
```

Aprire _Models\MyDatabaseContext.cs_ e aggiungere le istruzioni `using` seguenti all'inizio del file:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Nella classe `MyDatabaseContext` aggiungere il costruttore seguente:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Questo costruttore configura un oggetto SqlConnection per l'uso di un token di accesso per il database SQL di Azure dal servizio app. Con il token di accesso, l'app del servizio app esegue l'autenticazione al database SQL di Azure con l'identità gestita. Per altre informazioni, vedere [Ottenimento di token per le risorse di Azure](overview-managed-identity.md#obtaining-tokens-for-azure-resources). L'istruzione `if` consente di continuare a testare l'app in locale con Local DB.

> [!NOTE]
> `SqlConnection.AccessToken` è attualmente supportato solo in .NET Framework 4.6 e versioni successive e in [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2), non in [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows).
>

Per usare questo nuovo costruttore, aprire `Controllers\TodosController.cs` e trovare la riga `private MyDatabaseContext db = new MyDatabaseContext();`. Il codice esistente usa il controller `MyDatabaseContext` predefinito per creare un database con la stringa di connessione standard, che prima della [modifica apportata](#modify-connection-string) conteneva nome utente e password in testo non crittografato.

Sostituire l'intera riga con il codice seguente:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new System.Data.SqlClient.SqlConnection());
```

### <a name="publish-your-changes"></a>Pubblicare le modifiche

A questo punto è sufficiente pubblicare le modifiche in Azure.

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **DotNetAppSqlDb** e selezionare **Pubblica**.

![Pubblicare da Esplora soluzioni](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Nella pagina di pubblicazione fare clic su **Pubblica**. Quando nella nuova pagina Web viene visualizzato l'elenco attività, l'app si connette al database con l'identità gestita.

![App Azure dopo la migrazione Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Dovrebbe ora essere possibile modificare l'elenco attività come prima.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Concedere privilegi minimi all'identità

Durante i passaggi precedenti si sarà probabilmente notato che l'identità gestita è connessa a SQL Server come amministratore di Azure AD. Per concedere privilegi minimi all'identità gestita, è necessario accedere al server di database SQL di Azure come amministratore di Azure AD e quindi aggiungere un gruppo di Azure Active Directory contenente l'identità gestita. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Aggiungere l'identità gestita a un gruppo di Azure Active Directory

In Cloud Shell aggiungere l'identità gestita per l'app a un nuovo gruppo di Azure Active Directory denominato _myAzureSQLDBAccessGroup_, come illustrato nello script seguente:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Per visualizzare l'output JSON completo per ogni comando, rimuovere i parametri `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Riconfigurare l'amministratore di Azure AD

In precedenza, l'identità gestita è stata assegnata come amministratore di Azure AD per il database SQL. Questa identità non può essere usata per l'accesso interattivo (per aggiungere utenti del database), quindi è necessario usare l'utente di Azure AD reale. Per aggiungere l'utente di Azure AD, seguire la procedura descritta in [Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

> [!IMPORTANT]
> Una volta aggiunto, non rimuovere questo amministratore di Azure AD per il database SQL, a meno che non si voglia disabilitare completamente (da tutti gli account Azure AD) l'accesso di Azure AD al database SQL.
> 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Concedere autorizzazioni al gruppo di Azure Active Directory

In Cloud Shell accedere al database SQL con il comando SQLCMD. Sostituire _\<server\_name>_ con il nome del server di database SQL, _\<db\_name>_ con il nome di database usato dall'app e _\<AADuser\_name>_ e _\<AADpassword>_ con le credenziali utente di Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Al prompt di SQL per il database interessato eseguire i comandi seguenti per aggiungere il gruppo di Azure Active Directory creato in precedenza e concedere le autorizzazioni necessarie per l'app. Ad esempio, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Digitare `EXIT` per tornare al prompt di Cloud Shell. 

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Abilitare le identità gestite
> * Concedere all'identità gestita l'accesso al database SQL
> * Configurare il codice dell'applicazione per eseguire l'autenticazione al database SQL con l'autenticazione di Azure Active Directory
> * Concedere privilegi minimi all'identità gestita nel database SQL

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](app-service-web-tutorial-custom-domain.md)
