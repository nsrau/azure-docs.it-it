---
title: Creare utenti Azure AD con entità servizio
description: Questa esercitazione illustra la creazione di un utente Azure AD con applicazioni di Azure AD (entità servizio) nel database SQL di Azure e in Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 4e7da02f7dd7e8fb19e031b814624b289730b3ee
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367721"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Esercitazione: Creare utenti Azure AD con applicazioni di Azure AD

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Questo articolo è disponibile in **anteprima pubblica**. Per altre informazioni, vedere [Entità servizio di Azure Active Directory con Azure SQL](authentication-aad-service-principal.md). Questo articolo userà il database SQL di Azure per illustrare i passaggi necessari dell'esercitazione, ma è valido anche per [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Questo articolo illustra il processo di creazione di utenti Azure AD nel database SQL di Azure con entità servizio di Azure (applicazioni di Azure AD). Questa funzionalità esiste già in Istanza gestita di SQL di Azure, ma viene ora introdotta nel database SQL di Azure e in Azure Synapse Analytics. Per supportare questo scenario, è necessario generare un'identità Azure AD e assegnarla al server logico SQL di Azure.

Per altre informazioni sull'autenticazione di Azure AD per Azure SQL, vedere l'articolo [Usare l'autenticazione di Azure Active Directory](authentication-aad-overview.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Assegnare un'identità al server logico SQL di Azure
> - Assegnare il ruolo con autorizzazioni di lettura nella directory all'identità del server logico SQL
> - Creare un'entità servizio (un'applicazione di Azure AD) in Azure AD
> - Creare un utente dell'entità servizio nel database SQL di Azure
> - Creare un utente Azure AD diverso nel database SQL usando un utente dell'entità servizio di Azure AD

## <a name="prerequisites"></a>Prerequisiti

- Un [database SQL di Azure](single-database-create-quickstart.md) esistente o la distribuzione di [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Per questa esercitazione si presuppone che si disponga di un database SQL funzionante.
- Accesso a un'istanza di Azure Active Directory già esistente.
- Un modulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versioni successive se si usa PowerShell per configurare una singola applicazione di Azure AD come amministratore Azure AD per Azure SQL. Assicurarsi di aver effettuato l'aggiornamento al modulo più recente.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Assegnare un'identità al server logico SQL di Azure

1. Connettersi ad Azure Active Directory. Sarà necessario trovare l'ID tenant. A tale scopo, passare al [portale di Azure](https://portal.azure.com) e quindi alla risorsa **Azure Active Directory**. Nel riquadro **Panoramica** dovrebbe essere visualizzato il valore di **ID tenant**. Eseguire il comando PowerShell seguente:

    - Sostituire `<TenantId>` con il proprio valore di **ID tenant**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Registrare il valore `TenantId` per un uso futuro in questa esercitazione.

1. Generare un'identità Azure AD e assegnarla al server logico SQL di Azure. Eseguire questo comando PowerShell:

    - Sostituire `<resource group>` e `<server name>` con le proprie risorse. Se il nome del server è `myserver.database.windows.net`, sostituire `<server name>` con `myserver`.

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Per altre informazioni, vedere il comando [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver).

    > [!IMPORTANT]
    > Se per il server logico SQL di Azure è configurata un'identità Azure AD, è necessario concedere all'identità l'autorizzazione [**Ruoli con autorizzazioni di lettura nella directory**](../../active-directory/roles/permissions-reference.md#directory-readers). Questo procedura verrà illustrata nella sezione seguente. **Non** ignorare questa procedura perché l'autenticazione Azure AD smetterà di funzionare.

    - Se è stato usato precedentemente il comando [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) con il parametro `AssignIdentity` per la creazione di un nuovo server SQL, sarà necessario eseguire successivamente [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) come comando separato per abilitare questa proprietà nell'infrastruttura di Azure.

1. Verificare che l'identità del server sia stata assegnata correttamente. Eseguire questo comando PowerShell:

    - Sostituire `<resource group>` e `<server name>` con le proprie risorse. Se il nome del server è `myserver.database.windows.net`, sostituire `<server name>` con `myserver`.
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    L'output dovrebbe mostrare `PrincipalId`, `Type` e `TenantId`. L'identità assegnata è `PrincipalId`.

1. Per controllare l'identità, è anche possibile passare al [portale di Azure](https://portal.azure.com).

    - Nella risorsa **Azure Active Directory** passare ad **Applicazioni aziendali**. Digitare il nome del server logico SQL. Si noterà che presenta un **ID oggetto** associato alla risorsa.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Assegnare il ruolo con autorizzazioni di lettura nella directory all'identità del server logico SQL

Per consentire il corretto funzionamento dell'identità Azure AD assegnata per Azure SQL, è necessario concedere l'autorizzazione `Directory Readers` di Azure AD all'identità del server.

Per concedere questa autorizzazione obbligatoria, eseguire lo script seguente.

> [!NOTE] 
> Questo script deve essere eseguito da un utente `Global Administrator` o `Privileged Roles Administrator` di Azure AD.
>
> Nell'**anteprima pubblica** è possibile assegnare il ruolo `Directory Readers` a un gruppo in Azure AD. I proprietari del gruppo possono quindi aggiungere l'identità gestita come membro di questo gruppo, evitando così la necessità di un `Global Administrator` o `Privileged Roles Administrator` che conceda il ruolo `Directory Readers`. Per altre informazioni su questa funzionalità, vedere [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md).

- Sostituire `<TenantId>` con il valore `TenantId` raccolto in precedenza.
- Sostituire `<server name>` con il nome del server logico SQL. Se il nome del server è `myserver.database.windows.net`, sostituire `<server name>` con `myserver`.

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> L'output dello script precedente indicherà se è stato concesso all'identità il ruolo con autorizzazioni di lettura nella directory. È possibile eseguire di nuovo lo script se non si è certi che sia stata concessa l'autorizzazione.

Per un approccio simile per l'impostazione dell'autorizzazione **Ruoli con autorizzazioni di lettura nella directory** per Istanza gestita di SQL, vedere [Effettuare il provisioning di amministrazione di Azure AD (Istanza gestita di SQL)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Creare un'entità servizio (un'applicazione di Azure AD) in Azure AD

1. Seguire le indicazioni qui per [registrare l'app e impostare le autorizzazioni](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Assicurarsi di aggiungere **Autorizzazioni dell'applicazione** nonché **Autorizzazioni delegate**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="object-id":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="object-id":::

2. Sarà inoltre necessario creare un segreto client per l'accesso. Seguire le indicazioni fornite qui per [caricare un certificato o creare un segreto per l'accesso](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Registrare quanto segue dalla registrazione dell'applicazione. Dovrebbe essere disponibile nel riquadro **Panoramica**:
    - **ID applicazione**
    - **ID tenant**: deve essere come quello precedente

In questa esercitazione verranno usate le applicazioni *AppSP* come entità servizio principale e *myapp* come utente della seconda entità servizio che verrà creato in Azure SQL da *AppSP*. Sarà necessario creare due applicazioni, *AppSP* e *myapp*.

Per altre informazioni su come creare un'applicazione di Azure AD, vedere l'articolo [ Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md).


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Creare l'utente dell'entità servizio nel database SQL di Azure

Una volta creata un'entità servizio in Azure AD, creare l'utente nel database SQL. È necessario connettersi al database SQL con un account di accesso valido con le autorizzazioni per la creazione di utenti nel database.

1. Creare l'utente *AppSP* nel database SQL usando il comando T-SQL seguente:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Concedere l'autorizzazione `db_owner` ad *AppSP* in modo che l'utente possa creare altri utenti Azure AD nel database.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Per altre informazioni, vedere [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    In alternativa, è possibile concedere l'autorizzazione `ALTER ANY USER` anziché assegnare il ruolo `db_owner`. In questo modo l'entità servizio potrà aggiungere altri utenti Azure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > L'impostazione precedente non è obbligatoria se *AppSP* è impostata come amministratore Azure AD per il server. Per impostare l'entità servizio come amministratore AD per il server logico SQL, è possibile usare i comandi del portale di Azure, di PowerShell o dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Effettuare il provisioning di amministrazione di Azure AD (database SQL)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Creare un utente Azure AD nel database SQL usando un'entità servizio di Azure AD

> [!IMPORTANT]
> L'entità servizio usata per accedere al database SQL deve disporre di un segreto client. Se non dispone di un segreto client, seguire il passaggio 2 in [Creare un'entità servizio (un'applicazione di Azure AD) in Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Questo segreto client deve essere aggiunto come parametro di input nello script seguente.

1. Usare lo script seguente per creare un utente entità servizio di Azure AD *MyApp* usando l'entità servizio *AppSP*.

    - Sostituire `<TenantId>` con il valore `TenantId` raccolto in precedenza.
    - Sostituire `<ClientId>` con il valore `ClientId` raccolto in precedenza.
    - Sostituire `<ClientSecret>` con il segreto client creato in precedenza.
    - Sostituire `<server name>` con il nome del server logico SQL. Se il nome del server è `myserver.database.windows.net`, sostituire `<server name>` con `myserver`.
    - Sostituire `<database name>` con il nome del database SQL.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    In alternativa, è possibile usare l'esempio di codice nel blog [Azure AD Service Principal authentication to SQL DB - Code Sample](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) (Autenticazione dell'entità servizio di Azure AD nel database SQL - Esempio di codice). Modificare lo script per eseguire un'istruzione DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER`. Lo stesso script può essere usato per creare un gruppo di utenti Azure AD normale nel database SQL.

    > [!NOTE]
    > Se è necessario installare il modulo AzureRM.profile, sarà necessario aprire PowerShell come amministratore. È possibile usare i comandi seguenti per installare automaticamente la versione di AzureRM.profile più recente e impostare `$adalpath` per lo script precedente:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Controllare se è presente l'utente *myapp* nel database eseguendo questo comando:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Verrà visualizzato un output simile al seguente:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Entità servizio di Azure Active Directory con Azure SQL](authentication-aad-service-principal.md)
- [Informazioni sulle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../../app-service/overview-managed-identity.md)
- [Azure AD Service Principal authentication to SQL DB - Code Sample](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) (Autenticazione dell'entità servizio di Azure AD nel database SQL - Esempio di codice)
- [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Creare un'entità servizio di Azure con Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md)
