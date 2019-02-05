---
title: Garantire la sicurezza di Istanza gestita di database SQL di Azure con gli account di accesso di Azure AD | Microsoft Docs
description: Informazioni sulle tecniche e le funzionalità per proteggere Istanza gestita nel database SQL di Azure e usare gli account di accesso di Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 01/18/2019
ms.openlocfilehash: f96b2853b887836a94091dcba0ceaf6f8dd43d12
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229135"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-logins"></a>Esercitazione: Garantire la sicurezza di Istanza gestita nel database SQL di Azure con gli account di accesso di Azure AD

Istanza gestita di database SQL di Azure offre quasi tutte le funzionalità di sicurezza incluse nella versione più recente del motore di database di SQL Server locale (Enterprise Edition):

- Limitazione dell'accesso in un ambiente isolato
- Uso di meccanismi di autenticazione che verificano l'identità (Azure AD, Autenticazione SQL)
- Concessione di autorizzazioni tramite appartenenze e autorizzazioni basate sui ruoli
- Abilitazione delle funzionalità di sicurezza

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare un account di accesso di Azure Active Directory (AD) per Istanza gestita
> - Concedere le autorizzazioni agli account di accesso di Azure AD in Istanza gestita
> - Creare utenti di Azure AD da account di accesso di Azure AD
> - Assegnare autorizzazioni a utenti di Azure AD e alla sicurezza del database gestito
> - Usare la rappresentazione con utenti di Azure AD
> - Usare query tra database con utenti di Azure AD
> - Informazioni sulle funzionalità di sicurezza come la protezione da minacce, il controllo, il data masking e la crittografia

> [!NOTE]
> Gli account di accesso di Azure AD per Istanza gestita di database SQL sono disponibili in versione **anteprima pubblica**.

Per altre informazioni, vedere gli articoli sulla [panoramica](sql-database-managed-instance-index.yml) e sulle [capacità](sql-database-managed-instance.md) di Istanza gestita di database SQL di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di avere i prerequisiti seguenti:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Istanza gestita di database SQL di Azure
    - Consultare questo articolo: [Guida introduttiva: Creare un'Istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md)
- Possibilità di accedere a Istanza gestita di database SQL di Azure e di effettuare il [provisioning di un amministratore Azure AD per Istanza gestita](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Per altre informazioni, vedere:
    - [Connettere un'applicazione a Istanza gestita di database SQL di Azure](sql-database-managed-instance-connect-app.md) 
    - [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md)
    - [Configurare e gestire l'autenticazione di Azure Active Directory con SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Limitazione dell'accesso a Istanza gestita

Istanza gestita è accessibile solo tramite un indirizzo IP privato. Non sono disponibili endpoint di servizio per la connessione a Istanza gestita dall'esterno della rete di Istanza gestita. Analogamente a quanto avviene con un ambiente locale Server isolato, le applicazioni o gli utenti devono accedere alla rete di Istanza gestita (rete virtuale) prima di poter stabilire una connessione. Per altre informazioni, vedere l'articolo [Connettere un'applicazione a Istanza gestita di database SQL di Azure](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Dal momento che Istanza gestita è accessibile solo all'interno della relativa rete virtuale, le [regole del firewall del database SQL](sql-database-firewall-configure.md) non vengono applicate. Istanza gestita include un [firewall predefinito](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-login-for-a-managed-instance-using-ssms"></a>Creare un account di accesso di Azure AD per Istanza gestita con SQL Server Management Studio

Il primo account di accesso di Azure AD deve essere creato dall'account di SQL Server standard (non Azure AD) che è un `sysadmin`. Per esempi relativi alla connessione a Istanza gestita, vedere gli articoli seguenti:

- [Guida introduttiva: Configurare una macchina virtuale di Azure per la connessione a un'Istanza gestita di database SQL di Azure](sql-database-managed-instance-configure-vm.md)
- [Guida introduttiva: Configurare una connessione da punto a sito a un'Istanza gestita di database SQL di Azure da un computer locale](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> L'amministratore di Azure AD usato per configurare Istanza gestita non può essere usato per creare un account di accesso di Azure AD all'interno di Istanza gestita. È necessario creare il primo account di accesso di Azure AD usando un account di SQL Server che è un `sysadmin`. Si tratta di una limitazione temporanea che verrà rimossa quando gli account di accesso di Azure AD saranno disponibili a livello generale. Se si prova a usare un account amministratore di Azure AD per creare l'account di accesso, viene visualizzato l'errore seguente: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Accedere a Istanza gestita usando un account di SQL Server standard (non Azure AD) che è un `sysadmin`, usando [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.

3. Nella finestra di query usare la sintassi seguente per creare un account di accesso per un account Azure AD locale:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Questo esempio mostra come creare un account di accesso per l'account nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Sulla barra degli strumenti selezionare **Esegui** per creare l'account di accesso.

5. Verificare l'account di accesso appena aggiunto eseguendo il comando T-SQL seguente:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Per altre informazioni, vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Concessione delle autorizzazioni per consentire l a creazione di account di accesso di Istanza gestita

Per creare altri account di accesso di Azure AD, è necessario concedere autorizzazioni o ruoli di SQL Server all'entità di sicurezza (SQL o Azure AD).

### <a name="sql-authentication"></a>Autenticazione SQL

- Se l'account di accesso è un'entità di sicurezza SQL, solo gli account di accesso che fanno parte del ruolo `sysadmin` possono usare il comando per creare gli account di accesso per un account Azure AD.

### <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

- Per consentire all'account di accesso di Azure AD appena creato di creare altri account di accesso per altri utenti, gruppi o applicazioni di Azure AD, concedere all'account di accesso il ruolo del server `sysadmin` o `securityadmin`. 
- Per consentire all'account di accesso di Azure AD di creare altri account di accesso di Azure AD, è necessario concedere almeno l'autorizzazione **ALTER ANY LOGIN**. 
- Per impostazione predefinita, le autorizzazioni standard concesse agli account di accesso di Azure AD appena creati nel database master sono: **CONNECT SQL** e **VIEW ANY DATABASE**.
- Il ruolo del server `sysadmin` può essere concesso a più account di accesso AD Azure all'interno di Istanza gestita.

Per aggiungere l'account di accesso al ruolo del server `sysadmin`:

1. Accedere di nuovo a Istanza gestita oppure usare la connessione esistente con un'entità di sicurezza SQL che è un `sysadmin`.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.

1. Concedere all'account di accesso di Azure AD il ruolo del server `sysadmin` usando la sintassi T-SQL seguente:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    L'esempio seguente mostra come concedere il ruolo del server `sysadmin` all'account di accesso nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-logins-using-ssms"></a>Creare altri account di accesso AD Azure con SQL Server Management Studio

Dopo aver creato l'account di accesso di Azure AD e aver concesso i privilegi di `sysadmin`, tale account può creare altri account di accesso usando la clausola **FROM EXTERNAL PROVIDER** con **CREATE LOGIN**.

1. Connettersi al server di Istanza gestita con l'account di accesso di Azure AD usando SQL Server Management Studio. Immettere il nome del server di Istanza gestita. Per l'autenticazione in SSMS è possibile scegliere tra tre opzioni quando si esegue l'accesso con un account Azure AD:

    - Active Directory - Universale con supporto MFA
    - Active Directory - Password
    - Active Directory - Integrata </br>

    ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

    Per altre informazioni, vedere l'articolo seguente: [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)](sql-database-ssms-mfa-authentication.md)

1. Selezionare **Active Directory - Universale con supporto MFA**. Viene visualizzata una finestra di accesso Multi-Factor Authentication (MFA). Accedere con la password di Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. In **Esplora oggetti** di SSMS fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
1. Nella finestra di query usare la sintassi seguente per creare un account di accesso per un altro account Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Questo esempio mostra come creare un account di accesso per l'utente di Azure AD bob@aadsqlmi.net, il cui dominio aadsqlmi.net è federato con l'istanza aadsqlmi.onmicrosoft.com di Azure AD.

    Eseguire il comando T-SQL seguente. Gli account Azure AD federati sono le sostituzioni di Istanza gestita per account di accesso e utenti Windows locali.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Creare un database in Istanza gestita usando la sintassi [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Questo database verrà usato per testare gli account di accesso nella sezione successiva.
    1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
    1. Nella finestra di query usare la sintassi seguente per creare un database denominato **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Creare un account di accesso di Istanza gestita per un gruppo in Azure AD. Il gruppo deve essere già presente in Azure AD prima di poter aggiungere l'account di accesso a Istanza gestita. Vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Creare un gruppo _mygroup_ e aggiungere membri a tale gruppo.

1. Aprire una nuova finestra di query in SQL Server Management Studio.

    Questo esempio presuppone l'esistenza di un gruppo denominato _mygroup_ in Azure AD. Eseguire il comando seguente:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. A scopo di test, accedere a Istanza gestita con l'account di accesso o il gruppo appena creato. Aprire una nuova connessione a Istanza gestita e usare il nuovo account di accesso per l'autenticazione.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query** per la nuova connessione.
1. Per verificare le autorizzazioni del server per l'account di accesso di Azure AD appena creato, eseguire il comando seguente:

    ```sql
    SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
    GO
    ```

> [!NOTE]
> Gli utenti guest di Azure AD sono supportati per gli account di accesso di Istanza gestita solo quando vengono aggiunti in un gruppo di Azure AD. Per utente guest di Azure AD si intende un account invitato da un'altra istanza di Azure AD all'istanza di Azure AD cui appartiene Istanza gestita. Ad esempio, è possibile aggiungere joe@contoso.com (account Azure AD) o steve@outlook.com (account MSA) a un gruppo nell'istanza aadsqlmi di Azure AD. Dopo aver aggiunto gli utenti a un gruppo, è possibile creare un account di accesso nel database **master** di Istanza gestita per il gruppo usando la sintassi **CREATE LOGIN**. Gli utenti guest che fanno parte di questo gruppo possono connettersi a Istanza gestita usando gli account di accesso correnti, ad esempio joe@contoso.com o steve@outlook.com.

## <a name="create-an-azure-ad-user-from-the-azure-ad-login-and-give-permissions"></a>Creare un utente di Azure AD dall'account di accesso di Azure AD e concedere le autorizzazioni

Il funzionamento delle autorizzazioni per i singoli database in Istanza gestita è analogo a quello di SQL Server locale. È possibile creare un utente da un account di accesso esistente in un database e concedere le autorizzazioni per il database oppure aggiungerlo a un ruolo del database.

A questo punto, dopo aver creato un database denominato **MyMITestDB** e un account di accesso che dispone solo delle autorizzazioni predefinite, il passaggio successivo consiste nel creare un utente da tale account di accesso. Al momento, l'account di accesso può connettersi a Istanza gestita e visualizzare tutti i database, ma non può interagire con essi. Se si accede con l'account Azure AD con le autorizzazioni predefinite e si prova a espandere il database appena creato, verrà visualizzato l'errore seguente:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Per altre informazioni sulla concessione delle autorizzazioni per i database, vedere [Introduzione alle autorizzazioni del motore di database](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Creare un utente di Azure AD e una tabella di esempio

1. Accedere a Istanza gestita con un account `sysadmin` usando SQL Server Management Studio.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
1. Nella finestra di query usare la sintassi seguente per creare un utente di Azure AD da un account di accesso di Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    L'esempio seguente mostra come creare un utente bob@aadsqlmi.net dall'account di accesso bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. È anche possibile creare un utente di Azure AD da un account di accesso di Azure AD che corrisponde a un gruppo.

    L'esempio seguente mostra come creare un account di accesso per il gruppo di Azure AD _mygroup_ presente nell'istanza di Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Tutti gli utenti che appartengono a **mygroup** possono accedere al database **MyMITestDB**.

    > [!IMPORTANT]
    > Quando si crea un utente **USER** da un account di accesso di Azure AD, specificare per user_name lo stesso valore di login_name indicato in **LOGIN**.

    Per altre informazioni, vedere [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. In una nuova finestra di query creare una tabella di test usando il comando T-SQL seguente:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Creare una connessione in SQL Server Management Studio con l'utente che è stato creato. Si noterà che non è possibile visualizzare la tabella **TestTable** che è stato creato da `sysadmin` in precedenza. È necessario fornire all'utente le autorizzazioni per leggere i dati dal database.

1. È possibile verificare l'autorizzazione corrente disponibile per l'utente eseguendo il comando seguente:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Aggiungere utenti a ruoli a livello di database

Per consentire all'utente di visualizzare i dati nel database, è possibile concedergli i [ruoli a livello di database](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Accedere a Istanza gestita con un account `sysadmin` usando SQL Server Management Studio.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.

1. Concedere all'utente di Azure AD il ruolo del database `db_datareader` usando la sintassi T-SQL seguente:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    L'esempio seguente mostra come concedere all'utente bob@aadsqlmi.net e al gruppo _mygroup_ le autorizzazioni di `db_datareader` per il database **MyMITestDB**:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Per verificare l'esistenza dell'utente di Azure AD creato nel database, eseguire il comando seguente:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Creare una nuova connessione a Istanza gestita con l'utente che è stato aggiunto al ruolo `db_datareader`.
1. Espandere il database in **Esplora oggetti** per visualizzare la tabella.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Aprire una nuova finestra di query ed eseguire l'istruzione SELECT seguente:

    ```sql
    SELECT *
    FROM TestTable
    ```

    I dati della tabella sono visibili? Dovrebbe essere possibile visualizzare le colonne restituite.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Rappresentazione delle entità di livello server di Azure AD (account di accesso)

Istanza gestita supporta la rappresentazione di entità di livello server (account di accesso) di Azure AD.

### <a name="test-impersonation"></a>Testare la rappresentazione

1. Accedere a Istanza gestita con un account `sysadmin` usando SQL Server Management Studio.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.

1. Nella finestra di query usare il comando seguente per creare una nuova stored procedure:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Usare il comando seguente per verificare che l'utente rappresentato durante volta l'esecuzione della stored procedure sia **bob@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Per testare la rappresentazione, usare l'istruzione EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Solo le entità di livello server SQL (account di accesso) che fanno parte del ruolo `sysadmin` possono eseguire le operazioni seguenti destinate a entità di sicurezza di Azure AD: 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Uso di query tra database in Istanza gestita

Le query tra database sono supportate per gli account Azure AD con account di accesso di Azure AD. Per testare una query tra database con un gruppo di Azure AD, è necessario creare un altro database e un'altra tabella. È possibile non creare un altro database e un'altra tabella se esistono già.

1. Accedere a Istanza gestita con un account `sysadmin` usando SQL Server Management Studio.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
1. Nella finestra di query usare il comando seguente per creare un database denominato **MyMITestDB2** e una tabella denominata **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. In una nuova finestra di query eseguire il comando seguente per creare l'utente _mygroup_ nel nuovo database **MyMITestDB2** e concedere le autorizzazioni SELECT per tale database a _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Accedere a Istanza gestita usando SQL Server Management Studio come membro del gruppo di Azure AD _mygroup_. Aprire una nuova finestra di query ed eseguire l'istruzione SELECT tra database:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Dovrebbero essere visualizzati i risultati della tabella di **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-logins-public-preview"></a>Altri scenari supportati per gli account di accesso di Azure AD (anteprima pubblica) 

- Per gli account di accesso di Azure AD sono supportate l'esecuzione di processi e le operazioni di gestione dell'agente SQL.
- Le operazioni di backup e ripristino del database possono essere eseguite dagli account di accesso di Azure AD.
- [Controllo](sql-database-managed-instance-auditing.md) di tutte le istruzioni relative agli eventi di autenticazione e agli account di accesso di Azure AD.
- Connessione amministrativa dedicata per gli account di accesso di Azure AD che fanno parte del ruolo del server `sysadmin`.
- Gli account di accesso di Azure AD sono supportati con l'[utilità sqlcmd](/sql/tools/sqlcmd-utility) e lo strumento [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Sono supportati i trigger di accesso per gli eventi di accesso provenienti dagli account di accesso di Azure AD.
- Usando gli account di accesso di Azure AD è possibile configurare Service Broker e la posta elettronica del database.


## <a name="next-steps"></a>Passaggi successivi

### <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Vedere l'articolo seguente sulle [funzionalità di sicurezza delle capacità di Istanza gestita](sql-database-managed-instance.md#azure-sql-database-security-features) per un elenco completo di soluzioni per proteggere il database. Sono illustrate le funzionalità di sicurezza seguenti:

- [Controllo di Istanza gestita](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Rilevamento delle minacce](sql-database-managed-instance-threat-detection.md) 
- [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) 
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Capacità di Istanza gestita

Per una panoramica completa delle capacità di Istanza gestita di database SQL di Azure, vedere:

> [!div class="nextstepaction"]
> [Capacità di Istanza gestita](sql-database-managed-instance.md)