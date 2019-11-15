---
title: Eseguire la migrazione di utenti e gruppi di SQL ServerWindows a un'istanza gestita tramite T-SQL
description: Informazioni su come eseguire la migrazione di utenti e gruppi locali di Windows in SQL Server a un'istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 3ed4e4b1d37a9705378281ca74b53a6b60713d97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807166"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Esercitazione: Eseguire la migrazione di utenti e gruppi locali di Windows in SQL Server a un'istanza gestita di database SQL di Azure con la sintassi DDL T-SQL

> [!NOTE]
> La sintassi usata in questo articolo per eseguire la migrazione di utenti e gruppi a un'istanza gestita è in fase di **anteprima pubblica**.

Questo articolo illustra il processo di migrazione di utenti e gruppi locali di Windows in SQL Server a un'istanza gestita esistente di database SQL di Azure con la sintassi T-SQL.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare account di accesso per SQL Server
> - Creare un database di test per la migrazione
> - Creare account di accesso, utenti e ruoli
> - Eseguire il backup e il ripristino del database in un'istanza gestita
> - Eseguire manualmente la migrazione degli utenti a un'istanza gestita con la sintassi ALTER USER
> - Testare l'autenticazione con i nuovi utenti mappati

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario soddisfare i prerequisiti seguenti:

- Il dominio di Windows è federato con Azure Active Directory (Azure AD).
- Accesso ad Active Directory per la creazione di utenti/gruppi.
- Un'istanza di SQL Server esistente nell'ambiente locale.
- Un'istanza gestita esistente. Vedere [Avvio rapido: Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
  - Per creare account di accesso Azure AD, è necessario usare `sysadmin` nell'istanza gestita.
- [Creare un amministratore di Azure AD per l'istanza gestita](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- È possibile connettersi all'istanza gestita all'interno della rete. Per altre informazioni, vedere gli articoli seguenti: 
    - [Connettere un'applicazione a un'istanza gestita di database SQL di Azure](sql-database-managed-instance-connect-app.md)
    - [Guida introduttiva: Configurare una connessione da punto a sito a un'Istanza gestita di database SQL di Azure da un computer locale](sql-database-managed-instance-configure-p2s.md)
    - [Configurare endpoint pubblici in Istanza gestita di database SQL di Azure](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Sintassi DDL T-SQL

Di seguito è riportata la sintassi DDL T-SQL usata per supportare la migrazione di utenti e gruppi locali di Windows in SQL Server a un'istanza gestita con l'autenticazione di Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argomenti

_domainName_</br>
Specifica il nome del dominio dell'utente.

_userName_</br>
Specifica il nome dell'utente identificato all'interno del database.

_= loginName\@domainName.com_</br>
Esegue di nuovo il mapping di un utente all'account di accesso di Azure AD

_groupName_</br>
Specifica il nome del gruppo identificato all'interno del database.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Parte 1: Creare account di accesso per gli utenti e i gruppi locali di SQL Server

> [!IMPORTANT]
> La sintassi seguente crea un account di accesso per un utente e un gruppo in SQL Server. Prima di eseguire questa sintassi, è necessario assicurarsi che l'utente e il gruppo esistano all'interno di Active Directory (AD). </br> </br>
> Utenti: testUser1, testGroupUser </br>
> Gruppo: migration - testGroupUser deve appartenere al gruppo di migrazione in AD

L'esempio seguente crea un account di accesso in SQL Server per un account denominato _testUser1_ nel dominio _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Crea un database per questo test.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Parte 2: Creare utenti e gruppi di Windows, quindi aggiungere ruoli e autorizzazioni

Usare la sintassi seguente per creare l'utente di test.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Controllare le autorizzazioni dell'utente:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Creare un ruolo e assegnarlo all'utente di test:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Usare la query seguente per visualizzare i nomi utente assegnati a un ruolo specifico:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Usare la sintassi seguente per creare un gruppo. Quindi aggiungere il gruppo al ruolo `db_owner`.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Creare una tabella di test e aggiungere alcuni dati usando la sintassi seguente:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Parte 3: Eseguire il backup e il ripristino del singolo database utente in un'istanza gestita

Creare un backup del database di migrazione usando l'articolo [Copiare i database con backup e ripristino](/sql/relational-databases/databases/copy-databases-with-backup-and-restore) oppure usare la sintassi seguente:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Seguire l'argomento [Avvio rapido: Ripristinare un database a un'istanza gestita](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Parte 4: Eseguire la migrazione degli utenti all'istanza gestita

> [!NOTE]
> L'amministratore di Azure AD per la funzionalità dell'istanza gestita dopo la creazione è stato modificato. Per altre informazioni, vedere [Nuove funzionalità di amministrazione di Azure AD per l'istanza gestita](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Eseguire il comando ALTER USER per completare il processo di migrazione nell'istanza gestita.

1. Accedere all'istanza gestita usando l'account amministratore Azure AD per l'istanza gestita. Quindi creare l'account di accesso di Azure AD nell'istanza gestita usando la sintassi seguente. Per altre informazioni, vedere [Esercitazione: Garantire la sicurezza dell'istanza gestita del database SQL di Azure con le entità server (account di accesso) di Azure AD](sql-database-managed-instance-aad-security-tutorial.md).

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Controllare la migrazione per verificare se il database, la tabella e le entità sono corretti.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Usare la sintassi ALTER USER per eseguire il mapping dell'utente locale all'account di accesso di Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Usare la sintassi ALTER USER per eseguire il mapping del gruppo locale all'account di accesso di Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Parte 5: Test dell'autenticazione di utenti o gruppi di Azure AD

Testare l'autenticazione nell'istanza gestita usando l'utente di cui è stato in precedenza eseguito il mapping all'account di accesso di Azure AD usando la sintassi ALTER USER.
 
1. Accedere alla macchina virtuale federata con la sottoscrizione dell'istanza gestita come `aadsqlmi\testUser1`
1. Usando SQL Server Management Studio (SSMS), accedere all'istanza gestita usando l'**autenticazione integrata di Active Directory**, connettendosi al database `migration`.
    1. È anche possibile accedere usando le credenziali testUser1@aadsqlmi.net con l'opzione di SSMS **Active Directory - Universale con supporto MFA**. Tuttavia, in questo caso, non è possibile usare il meccanismo Single Sign-On ed è necessario digitare una password. Non sarà necessario usare una macchina virtuale federata per accedere all'istanza gestita.
1. Come parte del membro del ruolo **SELECT**, è possibile selezionare dalla tabella `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Testare l'autenticazione in un'istanza gestita usando un membro di un gruppo `migration` di Windows. È necessario che l'utente `aadsqlmi\testGroupUser` sia stato aggiunto al gruppo `migration` prima della migrazione.

1. Accedere alla macchina virtuale federata con la sottoscrizione dell'istanza gestita come `aadsqlmi\testGroupUser` 
1. Usando SSMS con l'**autenticazione integrata di Active Directory**, connettersi al server dell'istanza gestita e al database `migration`
    1. È anche possibile accedere usando le credenziali testGroupUser@aadsqlmi.net con l'opzione di SSMS **Active Directory - Universale con supporto MFA**. Tuttavia, in questo caso, non è possibile usare il meccanismo Single Sign-On ed è necessario digitare una password. Non sarà necessario usare una macchina virtuale federata per accedere all'istanza gestita. 
1. Come parte del ruolo `db_owner`, è possibile creare una nuova tabella.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> A causa di un problema di progettazione noto per il database SQL di Azure, un'istruzione di creazione tabella eseguita come membro di un gruppo non riuscirà con l'errore seguente: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> La soluzione alternativa consiste attualmente nel creare una tabella con uno schema esistente, nel caso sopra <dbo.new>

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Eseguire la migrazione offline di SQL Server a Istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)