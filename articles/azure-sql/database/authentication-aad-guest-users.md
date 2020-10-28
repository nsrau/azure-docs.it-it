---
title: Creare Azure AD utenti Guest
description: Come creare Azure AD utenti guest e impostarli come Azure AD amministratore senza usare gruppi di Azure AD nel database SQL di Azure, in Azure SQL Istanza gestita e in Azure sinapsi Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675062"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Creare utenti guest di Azure AD e impostarli come amministratore di Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Questo articolo è disponibile in **anteprima pubblica** .

Gli utenti guest in Azure Active Directory (Azure AD) sono utenti che sono stati importati nella Azure AD corrente da altre directory di Azure Active Directory o all'esterno di esso. Ad esempio, gli utenti guest possono includere utenti di altre directory di Azure Active Directory o da account come *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* o *\@ gmail.com* . In questo articolo viene illustrato come creare un Azure AD utente Guest e impostare tale utente come amministratore Azure AD per il server logico SQL di Azure, senza che sia necessario che l'utente Guest faccia parte di un gruppo all'interno Azure AD.

## <a name="feature-description"></a>Descrizione delle caratteristiche

Questa funzionalità eleva la limitazione corrente che consente solo agli utenti guest di connettersi a database SQL di Azure, SQL Istanza gestita o Azure sinapsi Analytics quando sono membri di un gruppo creato in Azure AD. Il gruppo deve essere mappato a un utente manualmente usando l'istruzione [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) in un database specifico. Una volta creato un utente del database per il gruppo di Azure AD contenente l'utente Guest, l'utente Guest può accedere al database usando Azure Active Directory con autenticazione a più fattori. Come parte di questa versione di **anteprima pubblica** , gli utenti guest possono essere creati e connettersi direttamente al database SQL, a SQL istanza gestita o a una sinapsi di Azure senza che sia necessario aggiungerli a un gruppo di Azure ad e quindi creare un utente del database per tale gruppo di Azure ad.

Come parte di questa funzionalità, è anche possibile impostare l'utente guest di Azure AD direttamente come amministratore di Active Directory per il server logico SQL di Azure. La funzionalità esistente in cui l'utente Guest può far parte di un gruppo di Azure AD e tale gruppo può quindi essere impostato come amministratore Azure AD per il server logico SQL di Azure non ha alcun effetto. Questa modifica non ha alcun effetto anche sugli utenti guest nel database che fanno parte di un gruppo di Azure AD.

Per altre informazioni sul supporto esistente per gli utenti guest che usano gruppi di Azure AD, vedere uso dell'autenticazione a più [fattori Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Prerequisito

- Il modulo [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versione successiva è necessario quando si usa PowerShell per impostare un utente guest come amministratore Azure ad per il server logico SQL di Azure.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Creare un utente del database per Azure AD utente Guest 

Attenersi alla seguente procedura per creare un utente del database utilizzando un Azure AD utente Guest.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Creare un utente Guest nel database SQL e nella sinapsi di Azure

1. Verificare che l'utente Guest, ad esempio, `user1@gmail.com` sia già stato aggiunto all'Azure ad e che sia stato impostato un amministratore Azure ad per il server di database. Per l'autenticazione Azure Active Directory, è necessario disporre di un amministratore Azure AD.

1. Connettersi al database SQL come amministratore Azure AD o un utente Azure AD con autorizzazioni SQL sufficienti per creare gli utenti ed eseguire il comando seguente nel database in cui è necessario aggiungere l'utente Guest:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. A questo punto dovrebbe essere creato un utente del database per l'utente Guest `user1@gmail.com` .

1. Eseguire il comando seguente per verificare che l'utente del database sia stato creato correttamente:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Disconnettersi e accedere al database come utente Guest `user1@gmail.com` usando [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) usando il metodo di autenticazione **Azure Active Directory universale con** l'autenticazione a più fattori. Per altre informazioni, vedere [uso dell'autenticazione a più fattori Azure Active Directory](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Crea utente guest in SQL Istanza gestita

> [!NOTE]
> SQL Istanza gestita supporta gli account di accesso per gli utenti Azure AD e Azure AD gli utenti del database indipendente. Nei passaggi seguenti viene illustrato come creare un account di accesso e un utente per un Azure AD utente guest in SQL Istanza gestita. È anche possibile scegliere di creare un [utente di database indipendente](/sql/relational-databases/security/contained-database-users-making-your-database-portable) in SQL istanza gestita usando il metodo nella sezione [creare un utente Guest nel database SQL e](#create-guest-user-in-sql-database-and-azure-synapse) in una sinapsi di Azure.

1. Verificare che l'utente Guest, ad esempio, `user1@gmail.com` sia già stato aggiunto all'Azure ad e che sia stato impostato un amministratore Azure ad per il server SQL istanza gestita. Per l'autenticazione Azure Active Directory, è necessario disporre di un amministratore Azure AD.

1. Connettersi al server SQL Istanza gestita come amministratore Azure AD o un utente Azure AD con autorizzazioni SQL sufficienti per creare gli utenti ed eseguire il comando seguente nel `master` database per creare un account di accesso per l'utente Guest:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. A questo punto dovrebbe essere creato un account di accesso per l'utente Guest `user1@gmail.com` nel `master` database.

1. Eseguire il comando seguente per verificare che l'account di accesso sia stato creato correttamente:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Eseguire il comando seguente nel database in cui è necessario aggiungere l'utente Guest: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. A questo punto dovrebbe essere creato un utente del database per l'utente Guest `user1@gmail.com` .

1. Disconnettersi e accedere al database come utente Guest `user1@gmail.com` usando [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) usando il metodo di autenticazione **Azure Active Directory universale con** l'autenticazione a più fattori. Per altre informazioni, vedere [uso dell'autenticazione a più fattori Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Impostazione di un utente guest come amministratore Azure AD

Per impostare un utente Guest Azure AD come amministratore Azure AD per il server logico SQL, seguire questa procedura.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Impostare Azure AD amministratore per il database SQL e la sinapsi di Azure

1. Verificare che l'utente Guest, ad esempio, `user1@gmail.com` sia già stato aggiunto all'Azure ad.

1. Eseguire il comando di PowerShell seguente per aggiungere l'utente guest come amministratore Azure AD per il server logico SQL di Azure:

    - Sostituire `<ResourceGroupName>` con il nome del gruppo di risorse di Azure che contiene il server logico SQL di Azure.
    - Sostituire `<ServerName>` con il nome del server logico SQL di Azure. Se il nome del server è `myserver.database.windows.net`, sostituire `<Server Name>` con `myserver`.
    - Sostituire `<DisplayNameOfGuestUser>` con il nome utente Guest.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    È anche possibile usare l'interfaccia della riga di comando di Azure [AZ SQL Server ad-admin](/cli/azure/sql/server/ad-admin) per impostare l'utente guest come amministratore Azure ad per il server logico SQL di Azure.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Imposta Azure AD amministratore per SQL Istanza gestita

1. Verificare che l'utente Guest, ad esempio, `user1@gmail.com` sia già stato aggiunto all'Azure ad.

1. Passare alla [portale di Azure](https://portal.azure.com)e passare alla risorsa **Azure Active Directory** . In **Gestisci** andare al riquadro **utenti** . Selezionare l'utente Guest e registrare il `Object ID` . 

1. Eseguire il comando di PowerShell seguente per aggiungere l'utente guest come amministratore Azure AD per il Istanza gestita SQL:

    - Sostituire `<ResourceGroupName>` con il nome del gruppo di risorse di Azure che contiene il istanza gestita SQL.
    - Sostituire `<ManagedInstanceName>` con il nome del istanza gestita SQL.
    - Sostituire `<DisplayNameOfGuestUser>` con il nome utente Guest.
    - Sostituire `<AADObjectIDOfGuestUser>` con l'oggetto `Object ID` raccolto in precedenza.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    È anche possibile usare l'interfaccia della riga di comando di Azure [AZ SQL mi ad-admin](/cli/azure/sql/mi/ad-admin) per impostare l'utente guest come amministratore Azure ad per il istanza gestita SQL.

## <a name="limitations"></a>Limitazioni

Esiste una limitazione per la portale di Azure che impedisce la selezione di un utente Guest Azure AD come amministratore Azure AD per Istanza gestita SQL. Per gli account Guest esterni all'Azure ad come *\@ Outlook.com* , *\@ hotmail.com* , *\@ Live.com* o *\@ gmail.com* , il selettore di amministrazione di Active Directory Mostra questi account, ma sono disabilitati e non possono essere selezionati. Usare i comandi di [PowerShell o dell'interfaccia](#setting-a-guest-user-as-an-azure-ad-admin) della riga di comando elencati sopra per impostare l'amministratore Azure ad. In alternativa, è possibile impostare un gruppo di Azure AD contenente l'utente guest come amministratore Azure AD per il Istanza gestita SQL.

Questa funzionalità verrà abilitata per SQL Istanza gestita prima della disponibilità generale di questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare e gestire l'autenticazione di Azure AD con Azure SQL](authentication-aad-configure.md)
- [Uso dell'autenticazione a più fattori Azure Active Directory](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)