---
title: Configurare Azure Active Directory Authentication Library - SQL | Documentazione Microsoft
description: Informazioni su come eseguire la connessione al database SQL, a Istanza gestita e a SQL Data Warehouse con l'autenticazione di Azure Active Directory, dopo aver configurato Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: b6414ac41b1bb43e3fe1470a7ae2b1358126003a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569673"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurare e gestire l'autenticazione di Azure Active Directory con SQL

Questo articolo illustra come creare e popolare Azure Active Directory e quindi usare Azure Active Directory con il [database SQL](sql-database-technical-overview.md) di Azure, [Istanza gestita](sql-database-managed-instance.md) o con [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Per una panoramica vedere [Autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.
> [!IMPORTANT]  
> La connessione a SQL Server in esecuzione in una VM di Azure non è supportata con un account Azure Active Directory. Usare un account Active Directory di dominio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

## <a name="create-and-populate-an-azure-ad"></a>Creare e popolare un'istanza di Azure AD

Creare un'istanza di Azure AD e popolarla con utenti e gruppi. Azure AD può essere il dominio gestito di Azure AD iniziale. Azure AD può anche essere un set di servizi di dominio Active Directory locali federato con Azure AD.

Per altre informazioni, consultare [Integrazione delle identità locali con Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Windows Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Windows Azure), [Amministrazione della directory di Azure AD](../active-directory/fundamentals/active-directory-administer.md) e [Gestire Azure AD con Windows PowerShell](/powershell/azure/overview) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory

1. Associare la sottoscrizione di Azure ad Azure Active Directory impostando la directory come directory attendibile per la sottoscrizione di Azure che ospita il database. Per altri dettagli, vedere [Associare le sottoscrizioni di Azure ad Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Usare il controllo per cambiare directory nel portale di Azure per passare alla sottoscrizione associata al dominio.

   **Informazioni aggiuntive:** Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory. Per altre informazioni sulle risorse, vedere [Informazioni sull'accesso alle risorse di Azure](../active-directory/active-directory-b2b-admin-add-users.md). Per altre informazioni su questa relazione attendibile, vedere [Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Creare un amministratore di Azure Active Directory per il server SQL di Azure

Ogni server di Azure SQL, che ospita un database SQL o SQL Data Warehouse, viene avviato con un singolo account amministratore del server, che è l'amministratore dell'intera istanza del server di Azure SQL. È necessario creare un secondo amministratore del server SQL, cioè un account Azure AD. Questa entità viene creata come un utente di database indipendente nel database master. Per quando riguarda gli amministratori, gli account amministratore del server sono membri del ruolo **db_owner** in ogni database utente e ogni database utente viene immesso come utente **dbo**. Per altre informazioni sugli account amministratore del server, vedere l'articolo relativo alla [gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).

Quando si usa Azure Active Directory con la replica geografica, l'amministratore di Azure Active Directory deve essere configurato sia per i server primari che per quelli secondari. Se un server non ha un amministratore di Azure Active Directory, gli utenti e gli account di accesso di Azure Active Directory ricevono un messaggio di errore "Impossibile connettersi al server".

> [!NOTE]
> Gli utenti che non hanno un account Azure Active Directory, incluso l'account amministratore di Azure SQL Server, non possono creare utenti di Azure Active Directory, perché non hanno l'autorizzazione per convalidare con Azure Active Directory gli utenti di database proposti.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per l'istanza gestita

> [!IMPORTANT]
> Seguire questi passaggi solo se si effettua il provisioning di un'istanza gestita. Questa operazione può essere eseguita solo dall'amministratore globale/aziendale in Azure AD. Di seguito viene descritta la procedura da seguire per concedere le autorizzazioni agli utenti con privilegi diversi nella directory.

Per poter completare attività quali l'autenticazione degli utenti tramite l'appartenenza al gruppo di sicurezza o la creazione di nuovi utenti, l'istanza gestita deve disporre di autorizzazioni per la lettura di Azure AD. È quindi necessario concedere tali autorizzazioni all'istanza gestita. Per eseguire questa operazione sono disponibili due metodi. È possibile usare il portale e PowerShell. I passaggi seguenti illustrano entrambi i metodi.

1. Nell'angolo in alto a destra del portale di Azure scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory.
2. Scegliere la directory corretta come directory predefinita di Azure AD.

   Questo passaggio collega la sottoscrizione associata ad Active Directory con Istanza gestita, verificando che venga usata la stessa sottoscrizione per Azure AD e per l'istanza gestita.
3. Passare a Istanza gestita e selezionare l'istanza da usare per l'integrazione con Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selezionare il banner nella parte superiore della pagina di amministrazione di Active Directory e concedere l'autorizzazione all'utente corrente. Se si è connessi come amministratore globale/aziendale in Azure AD, è possibile eseguire questa operazione dal portale di Azure o usando PowerShell tramite lo script seguente.

    ![concessione di autorizzazioni-portale](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Al termine dell'operazione, nell'angolo in alto a destra verrà visualizzata la notifica seguente:

    ![success](./media/sql-database-aad-authentication/success.png)

6. È ora possibile scegliere l'amministratore di Azure AD per l'istanza gestita. Nella pagina Amministratore di Active Directory selezionare il comando **Imposta amministratore**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Nella pagina di amministrazione di Azure Active Directory, cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi scegliere **Seleziona**.

   La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati in [Funzionalità e limitazioni di Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Il controllo degli accessi in base al ruolo (RBAC) si applica solo al portale di Azure e non viene propagato a SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Nella parte superiore della pagina Amministratore di Active Directory scegliere **Salva**.

    ![salva](./media/sql-database-aad-authentication/save.png)

    Il processo di modifica dell'amministratore può richiedere alcuni minuti. Al termine del processo, nella casella Amministratore di Active Directory verrà visualizzato il nome del nuovo amministratore.

Dopo aver effettuato il provisioning di un amministratore di Azure AD per l'istanza gestita, è possibile iniziare a creare le entità server (accessi) di Azure AD (**versione di anteprima pubblica**) con la sintassi <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>. Per altre informazioni, vedere [Panoramica di Istanza gestita](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina Amministratore di Active Directory scegliere **Rimuovi amministratore** e quindi **Salva**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure

> [!IMPORTANT]
> Seguire questi passaggi solo se si effettua il provisioning di un server di database SQL di Azure o di un data warehouse.

Le due procedure seguenti illustrano come eseguire il provisioning di un amministratore di Azure Active Directory per il server SQL Azure usando il portale di Azure e PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/) scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio collega la directory di Active Directory associata alla sottoscrizione con il server SQL di Azure, assicurando che la stessa sottoscrizione venga usata per Azure AD e per SQL Server. Il server SQL di Azure può ospitare il database SQL di Azure o Azure SQL Data Warehouse. ![choose-ad][8]

2. Nel banner a sinistra selezionare **All services** (Tutti i servizi) e il tipo di filtro in **SQL server**. Selezionare **SQL server**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > In questa pagina, prima di selezionare **SQL Server** è possibile selezionare la **stella** accanto al nome per *aggiungere ai preferiti* la categoria e aggiungere **SQL Server**alla barra di navigazione sinistra.

3. Nella pagina **SQL Server**, selezionare **Amministratore di Active Directory**.
4. Nella pagina **Amministratore di Active Directory** selezionare il comando **Imposta amministratore**. ![selezionare active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Nella pagina **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi scegliere **Seleziona**. La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Per l'elenco degli amministratori supportati, vedere la sezione **Funzionalità e limitazioni di Azure AD** in [Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di SQL Data Warehouse](sql-database-aad-authentication.md). Il controllo di accesso basata sui ruoli (RBAC) si applica solo al portale e non viene propagato a SQL Server.
    ![Selezionare l'amministratore](./media/sql-database-aad-authentication/select-admin.png)  

6. Nella parte superiore della pagina **Amministratore di Active Directory** scegliere **SALVA**.
    ![Salvare l'impostazione dell'amministratore](./media/sql-database-aad-authentication/save-admin.png)

Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore è quindi visualizzato nella casella **Amministratore di Active Directory** .

   > [!NOTE]
   > Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore, utente o gruppo, non può essere già presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD ha esito negativo.

Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina **Amministratore di Active Directory** scegliere **Rimuovi amministratore** e quindi **Salva**.

### <a name="powershell"></a>PowerShell

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

- Connect-AzAccount
- Select-AzSubscription

Cmdlet usati per il provisioning e la gestione dell'amministratore di Azure AD:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Esegue il provisioning di un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. Deve far parte della sottoscrizione corrente. |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Rimuove un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Restituisce informazioni sull'amministratore di Azure Active Directory attualmente configurato per il server di Azure SQL o Azure SQL Data Warehouse. |

Per visualizzare altre informazioni su ognuno di questi comandi, usare il comando get-help di PowerShell, ad esempio ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

Lo script seguente effettua il provisioning di un gruppo di amministratori di Azure AD denominato **DBA_Group** (ID oggetto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) per il server **demo_server** in un gruppo di risorse denominato **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure AD o il nome dell'entità utente. Ad esempio, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure AD è supportato solo il nome visualizzato di Azure AD.

> [!NOTE]
> Il comando di Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` non impedisce di eseguire il provisioning degli amministratori di Azure AD per gli utenti non supportati. È possibile eseguire il provisioning di un utente non supportato il quale non può tuttavia connettersi a un database.

L'esempio seguente usa il valore **ObjectID**opzionale:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Il valore **ObjectID** di Azure AD è obbligatorio quando **DisplayName** non è univoco. Per recuperare i valori **ObjectID** e **DisplayName**, usare la sezione Active Directory del portale di Azure classico e visualizzare le proprietà di un utente o di un gruppo.

L'esempio seguente restituisce informazioni sull'amministratore di Azure AD corrente per Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

L'esempio seguente illustra come rimuovere un amministratore di Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

È anche possibile effettuare il provisioning di un amministratore di Azure Active Directory usando le API REST. Per altre informazioni, vedere [Service Management REST API Reference and Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) (Informazioni di riferimento e operative sull'API REST di gestione dei servizi per le operazioni del database SQL di Azure)

### <a name="cli"></a>Interfaccia della riga di comando  

È anche possibile eseguire il provisioning di un admin Azure AD chiamando i comandi dell'interfaccia della riga di comando seguenti:

| Comando | Descrizione |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Esegue il provisioning di un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. Deve far parte della sottoscrizione corrente. |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Rimuove un amministratore di Azure Active Directory per il server di Azure SQL o per Azure SQL Data Warehouse. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Restituisce informazioni sull'amministratore di Azure Active Directory attualmente configurato per il server di Azure SQL o Azure SQL Data Warehouse. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Aggiornare l'amministratore di Azure Active Directory per un server di Azure SQL o per Azure SQL Data Warehouse. |

Per altre informazioni sui comandi dell'interfaccia della riga di comando, vedere [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Configurare i computer client

È necessario installare il software seguente in tutti i computer client da cui le applicazioni o gli utenti si connettono al database SQL di Azure o ad Azure SQL Data Warehouse con le identità di Azure AD:

- .NET Framework 4.6 o versioni successive da [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library per SQL Server (**ADALSQL.DLL**) è disponibile in più lingue (sia per x86 che per amd64) dall'Area download in [Microsoft Active Directory Authentication Library per Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

È possibile soddisfare questi requisiti tramite:

- L'installazione di [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o [SQL Server Data Tools per Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) soddisfa i requisiti di .NET Framework 4.6.
- SSMS installa la versione x86 di **ADALSQL.DLL**.
- SSDT installa la versione amd64 di **ADALSQL.DLL**.
- L'ultima versione di Visual Studio disponibile nella pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa i requisiti di .NET Framework 4.6, ma non installa la versione amd64 di **ADALSQL.DLL**richiesta.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD

>[!IMPORTANT]
>Istanza gestita supporta ora le entità server (account di accesso) di Azure AD (**versione di anteprima pubblica**), per creare gli account di accesso dagli utenti, gruppi o applicazioni di Azure AD. Le entità server (account di accesso) di Azure AD offrono la possibilità di eseguire l'autenticazione all'istanza gestita senza richiedere che gli utenti del database debbano essere creati come un utente di database indipendente. Per altre informazioni, vedere [Panoramica di Istanza gestita](sql-database-managed-instance.md#azure-active-directory-integration). Per la sintassi sulla creazione di entità server (account di accesso) di Azure AD, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

L'autenticazione di Azure Active Directory richiede la creazione di utenti del database come utenti di database indipendente. Un utente di database indipendente basato su un'identità di Azure AD è un utente di database che non può accedere al database master e del quale viene eseguito il mapping a un'identità nella directory di Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Gli utenti del database, tranne gli amministratori, non possono essere creati tramite il portale di Azure. I ruoli RBAC non vengono propagati a SQL Server, al database SQL o a SQL Data Warehouse. I ruoli Controllo degli accessi in base al ruolo di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo **Collaboratore SQL Server** non consente l'accesso per connettersi al database SQL oppure a SQL Data Warehouse. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.
> [!WARNING]
> Caratteri speciali, ad esempio i due punti `:` o la e commerciale `&`, quando vengono inclusi come nomi utente in istruzioni T-SQL CREATE LOGIN e CREATE USER, non sono supportati.

Per creare un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD come utente con almeno l'autorizzazione **ALTER ANY USER** . Usare quindi la sintassi Transact-SQL seguente:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* può essere il nome dell'entità utente di un utente di Azure AD o il nome visualizzato di un gruppo di Azure AD.

**Esempi:** per creare un utente di database indipendente che rappresenta un utente di dominio gestito o federato di Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un gruppo di dominio federato o di Azure AD, specificare il nome visualizzato di un gruppo di sicurezza:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un'applicazione che si connette usando un token di Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> È possibile creare un utente direttamente da Azure Active Directory solo se si usa l'Azure Active Directory associata alla sottoscrizione di Azure. Tuttavia, i membri di altre directory di Active Directory che sono utenti importati nell'Active Directory associata (chiamati utenti esterni) possono essere aggiunti a un gruppo Active Directory nell'Active Directory tenant. Tramite la creazione di un utente del database indipendente per il gruppo AD, gli utenti dell'Active Directory esterna possono ottenere l'accesso al database SQL.

Per altre informazioni sulla creazione di utenti di database indipendente basati su identità di Azure Active Directory, vedere [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> La rimozione dell'amministratore di Azure Active Directory per il server di Azure SQL impedisce agli utenti dell'autenticazione di Azure AD di connettersi al server. Se necessario, un amministratore del database SQL può eliminare manualmente gli utenti di Azure AD inutilizzabili.
> [!NOTE]
> Se si riceve un messaggio di **timeout della connessione scaduto**, potrebbe essere necessario impostare il parametro `TransparentNetworkIPResolution` della stringa di connessione su false. Per altre informazioni, vedere [Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Problema di timeout della connessione con .NET Framework 4.6.1 - TransparentNetworkIPResolution).

Quando si crea un database utente, questo riceve l'autorizzazione **CONNECT** e può connettersi al database come membro del ruolo **PUBLIC**. Inizialmente le sole autorizzazioni disponibili per l'utente sono quelle concesse al ruolo **PUBLIC** o quelle concesse a qualsiasi gruppo di Azure AD di cui è membro. Dopo avere effettuato il provisioning di un utente di database indipendente basato su Azure AD, è possibile concedere all'utente altre autorizzazioni, esattamente come si concede un'autorizzazione a qualsiasi altro tipo di utente. In genere si concedono autorizzazioni ai ruoli del database e si aggiungono gli utenti ai ruoli. Per altre informazioni, vedere l'articolo relativo alle [nozioni di base delle autorizzazioni per il motore di database](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per altre informazioni sui ruoli speciali del database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).
Un account utente di dominio federato importato in un dominio gestito come utente esterno deve usare l'identità del dominio gestito.

> [!NOTE]
> Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL_GROUPS). Per altre informazioni, vedere [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Connettersi al data warehouse o al database utente con SSMS o SSDT  

Per verificare che l'amministratore di Azure AD sia configurato correttamente, connettersi al database **master** con l'account amministratore di Azure AD.
Per effettuare il provisioning di un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD che abbia accesso al database.

> [!IMPORTANT]
> Il supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. La versione di agosto 2016 di SQL Server Management Studio include anche il supporto per l'autenticazione universale di Active Directory, che consente agli amministratori di richiedere la Multi-Factor Authentication mediante chiamata telefonica, SMS, smart card con pin, o notifica dell'app per dispositivi mobili.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Uso di un'identità di Azure AD per la connessione con SSMS o SSDT

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory usando SQL Server Management Studio o SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Usare questo metodo se si è connessi a Windows con le credenziali di Azure Active Directory da un dominio federato.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) e selezionare **Active Directory - Integrata** nella casella **Autenticazione**. La password non è necessaria e non può essere immessa, perché per la connessione vengono presentate le credenziali esistenti.

    ![Selezionare Autenticazione integrata di Active Directory][11]
2. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). L'opzione **ID tenant o nome di dominio AD** è supportata solo per le opzioni di connessione **Universale con supporto MFA**. Negli altri casi non è disponibile.  

    ![Selezionare il nome del database][13]

## <a name="active-directory-password-authentication"></a>Autenticazione della password Active Directory

Usare questo metodo per connettersi con il nome dell'entità di Azure AD tramite il dominio gestito di Azure AD. Si può anche usarlo per account federati senza accesso al dominio, ad esempio quando si lavora in modalità remota.

Usare questo metodo per eseguire l'autenticazione al database/data warehouse SQL con Azure AD per utenti di Azure AD nativi o federati. Un utente nativo è un utente creato in modo esplicito in Azure AD e autenticato tramite nome utente e password, mentre un utente federato è un utente Windows il cui dominio è federato con Azure AD. Quest'ultimo metodo (tramite utente e password) può essere usato quando un utente vuole usare le credenziali di Windows, ma il computer locale non è aggiunto al dominio (ad esempio tramite accesso remoto). In questo caso un utente Windows può specificare il proprio account di dominio e la password ed eseguire l'autenticazione al database/data warehouse SQL tramite credenziali federate.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti al motore di database**) e selezionare **Active Directory - Password** nella casella **Autenticazione**.
2. Nella casella **nome utente** Digitare il nome utente Azure Active Directory nel formato **\@** nome utente Domain.com. I nomi utenti devono essere un account di Azure Active Directory o un account di un dominio federato con Azure Active Directory.
3. Nella casella **Password** digitare la password utente dell'account Azure Active Directory o dell'account di dominio federato.

    ![Selezionare Autenticazione della password di Active Directory][12]
4. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). Vedere il grafico nell'opzione precedente.

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizzo di un'identità di Azure Active Directory per connettersi da un'applicazione client

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory da un'applicazione client.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Per usare l'autenticazione integrata di Windows, l'Active Directory del dominio deve essere federato con Azure Active Directory. L'applicazione client (o un servizio) che si connette al database deve essere in esecuzione in un computer aggiunto al dominio con le credenziali di dominio dell'utente.

Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication nella stringa di connessione al database deve essere impostata su Active Directory Integrated. L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

La parola chiave ``Integrated Security=True`` della stringa di connessione non è supportata per la connessione al database SQL di Azure. Quando si esegue una connessione ODBC, è necessario rimuovere gli spazi e impostare l'autenticazione su 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Per connettersi a un database usando l'autenticazione integrata e un'identità di Azure AD, la parola chiave Authentication deve essere impostata su Active Directory Password. La stringa di connessione deve contenere le parole chiave e i valori User ID/UID e Password/PWD. L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Per altre informazioni sui metodi di autenticazione di Azure AD, usare gli esempi di codice dimostrativo disponibili in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(Dimostrazione GitHub dell'autenticazione di Azure AD).

## <a name="azure-ad-token"></a>Token Azure AD

Questo tipo di autenticazione consente ai servizi di livello intermedio di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse ottenendo un token da Azure Active Directory (AAD). Consente scenari complessi, tra cui l'autenticazione basata su certificato. Per usare l'autenticazione tramite token di Azure AD, è necessario completare quattro passaggi fondamentali:

1. Registrare l'applicazione in Azure Active Directory e ottenere l'ID client per il codice.
2. Creare un utente del database che rappresenta l'applicazione. (Completato in precedenza al passaggio 6).
3. Creare un certificato nel computer client che esegue l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Per altre informazioni, vedere [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)(Blog sulla sicurezza del server SQL). Per informazioni sull'aggiunta di un certificato, vedere [Introduzione all'autenticazione basata su certificati di Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Le istruzioni seguenti eseguono la connessione usando la versione 13.1 di sqlcmd disponibile in [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'accesso e del controllo nel database SQL, vedere l'articolo relativo al [controllo dell'accesso al database SQL](sql-database-control-access.md).
- Per una panoramica degli account di accesso, degli utenti e dei ruoli del database nel database SQL, vedere l'articolo relativo ad [account di accesso, utenti e ruoli del database](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
