---
title: Garantire la sicurezza dell'istanza gestita SQL con le entità server (account di accesso) di Azure AD
description: Informazioni sulle tecniche e le funzionalità per proteggere un'istanza gestita di SQL di Azure e usare le entità server (account di accesso) di Azure AD
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 5152f78b428008d3f9a76264f2253167e6c5a138
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219805"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Esercitazione: Garantire la sicurezza dell'istanza gestita di SQL di Azure con le entità server (account di accesso) di Azure AD
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

L'istanza gestita di SQL di Azure offre quasi tutte le funzionalità di sicurezza incluse nella versione più recente del motore di database di SQL Server (Enterprise Edition):

- Limitare l'accesso in un ambiente isolato
- Usare meccanismi di autenticazione che verificano l'identità: Azure Active Directory (Azure AD) e Autenticazione SQL
- Concedere autorizzazioni tramite appartenenze e autorizzazioni basate sui ruoli
- Abilitare le funzionalità di sicurezza

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un'entità server (account di accesso) di Azure AD per un'istanza gestita
> - Concedere le autorizzazioni alle entità server (account di accesso) di Azure AD in un'istanza gestita
> - Creare utenti di Azure AD da entità server (account di accesso) di Azure AD
> - Assegnare autorizzazioni agli utenti di Azure AD e gestire la sicurezza dei database
> - Usare la rappresentazione con utenti di Azure AD
> - Usare query tra database con utenti di Azure AD
> - Informazioni sulle funzionalità di sicurezza come la protezione da minacce, il controllo, il data masking e la crittografia

Per altre informazioni, vedere [Panoramica dell'istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di avere i prerequisiti seguenti:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Istanza gestita
  - Consultare questo articolo: [Avvio rapido: Creare un'istanza gestita](instance-create-quickstart.md)
- Possibilità di accedere all'istanza gestita e di effettuare il [provisioning di un amministratore Azure AD per l'istanza gestita](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Per altre informazioni, vedere:
  - [Connettere l'applicazione a un'istanza gestita](connect-application-instance.md)
  - [Architettura della connettività delle istanze gestite di SQL](connectivity-architecture-overview.md)
  - [Configurare e gestire l'autenticazione di Azure Active Directory con SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Limitare l'accesso 

Le istanze gestite sono accessibili tramite un indirizzo IP privato. Analogamente a quanto avviene con un ambiente SQL Server isolato, le applicazioni o gli utenti devono accedere alla rete dell'istanza gestita di SQL (rete virtuale) prima di poter stabilire una connessione. Per altre informazioni, vedere [Connettere l'applicazione a Istanza gestita di SQL di Azure](connect-application-instance.md).

È anche possibile configurare nell'istanza gestita un endpoint servizio che consente le connessioni pubbliche, in modo analogo al database SQL di Azure.
Per altre informazioni, vedere [Configurare l'endpoint pubblico nell'istanza gestita di SQL di Azure](public-endpoint-configure.md).

> [!NOTE]
> Le [regole del firewall del database SQL di Azure ](../database/firewall-configure.md) non si applicano neanche in caso di endpoint servizio abilitati. Per gestire la connettività, l'istanza gestita di SQL di Azure è dotata di un proprio [firewall predefinito](management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Creare un'entità server (account di accesso) di Azure AD con SSMS

La prima entità server (account di accesso) di Azure AD può essere creata tramite l'account amministratore SQL standard (non Azure AD), ovvero `sysadmin`, o l'amministratore di Azure AD per l'istanza gestita creata durante il processo di provisioning. Per altre informazioni, vedere [Effettuare il provisioning di un amministratore di Azure Active Directory per l'istanza gestita di SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Per esempi relativi alla connessione all'istanza gestita di SQL, vedere gli articoli seguenti:

- [Avvio rapido: Configurare una macchina virtuale di Azure per la connessione a un'istanza gestita di SQL](connect-vm-instance-configure.md)
- [Avvio rapido: Configurare una connessione da punto a sito a un'istanza gestita di SQL da un computer locale](point-to-site-p2s-configure.md)

1. Accedere all'istanza gestita con un account di accesso SQL standard (non Azure AD), ovvero `sysadmin`, o un amministratore di Azure AD per l'istanza gestita di SQL usando [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

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

    ![native-login.png](./media/aad-security-configure-tutorial/native-login.png)

Per altre informazioni, vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="grant-permissions-to-create-logins"></a>Concedere le autorizzazioni per la creazione degli account di accesso

Per creare altre entità server (account di accesso) di Azure AD, è necessario concedere autorizzazioni o ruoli di SQL Server all'entità di sicurezza (SQL o Azure AD).

### <a name="sql-authentication"></a>Autenticazione SQL

- Se l'account di accesso è un'entità di sicurezza SQL, solo gli account di accesso che fanno parte del ruolo `sysadmin` possono usare il comando per creare gli account di accesso per un account Azure AD.

### <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

- Per consentire all'entità server (account di accesso) di Azure AD appena creata di creare altri account di accesso per altri utenti, gruppi o applicazioni di Azure AD, concedere all'account di accesso il ruolo del server `sysadmin` o `securityadmin`.
- Per consentire all'entità server (account di accesso) di Azure AD di creare altre entità server (account di accesso) di Azure AD, è necessario concedere almeno l'autorizzazione **ALTER ANY LOGIN**.
- Per impostazione predefinita, le autorizzazioni standard concesse alle entità server (account di accesso) di Azure AD appena create nel database master sono: **CONNECT SQL** e **VIEW ANY DATABASE**.
- Il ruolo del server `sysadmin` può essere concesso a più entità server (account di accesso) AD Azure all'interno di un'istanza gestita.

Per aggiungere l'account di accesso al ruolo del server `sysadmin`:

1. Accedere di nuovo all'istanza gestita oppure usare la connessione esistente con l'amministratore di Azure AD o un'entità di sicurezza SQL, ovvero `sysadmin`.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.

1. Concedere all'entità server (account di accesso) di Azure AD il ruolo del server `sysadmin` usando la sintassi T-SQL seguente:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    L'esempio seguente mostra come concedere il ruolo del server `sysadmin` all'account di accesso nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Creare altre entità server (account di accesso) AD Azure con SQL Server Management Studio

Dopo aver creato l'entità server (account di accesso) di Azure AD e aver concesso i privilegi di `sysadmin`, tale account può creare altri account di accesso usando la clausola **FROM EXTERNAL PROVIDER** con **CREATE LOGIN**.

1. Connettersi all'istanza gestita con l'entità server (account di accesso) di Azure AD usando SQL Server Management Studio. Immettere il nome host dell'istanza gestita di SQL. Per l'autenticazione in SSMS è possibile scegliere tra tre opzioni quando si esegue l'accesso con un account Azure AD:

   - Active Directory - Universale con supporto MFA
   - Active Directory - Password
   - Active Directory - Integrata </br>

     ![ssms-login-prompt.png](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Per altre informazioni, vedere [Autenticazione universale (supporto di SSMS per Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Selezionare **Active Directory - Universale con supporto MFA**. Verrà visualizzata una finestra di accesso per Multi-Factor Authentication (MFA). Accedere con la password di Azure AD.

    ![mfa-login-prompt.png](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. In **Esplora oggetti** di SSMS fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
1. Nella finestra di query usare la sintassi seguente per creare un account di accesso per un altro account Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Questo esempio mostra come creare un account di accesso per l'utente di Azure AD bob@aadsqlmi.net, il cui dominio aadsqlmi.net è federato con il dominio aadsqlmi.onmicrosoft.com di Azure AD.

    Eseguire il comando T-SQL seguente. Gli account Azure AD federati sono le sostituzioni dell'istanza gestita di SQL per account di accesso e utenti di Windows locali.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Creare un database nell'istanza gestita usando la sintassi [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Questo database verrà usato per testare gli account di accesso nella sezione successiva.
    1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
    1. Nella finestra di query usare la sintassi seguente per creare un database denominato **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Creare un account di accesso dell'istanza gestita SQL per un gruppo in Azure AD. Il gruppo deve essere già presente in Azure AD prima di poter aggiungere l'account di accesso all'istanza gestita di SQL. Vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Creare un gruppo _mygroup_ e aggiungere membri a tale gruppo.

1. Aprire una nuova finestra di query in SQL Server Management Studio.

    Questo esempio presuppone l'esistenza di un gruppo denominato _mygroup_ in Azure AD. Eseguire il comando seguente:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. A scopo di test, accedere all'istanza gestita con l'account di accesso o il gruppo appena creato. Aprire una nuova connessione all'istanza gestita e usare il nuovo account di accesso per l'autenticazione.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query** per la nuova connessione.
1. Per verificare le autorizzazioni del server per l'entità server (account di accesso) di Azure AD appena creata, eseguire il comando seguente:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Gli utenti guest di Azure AD sono supportati per gli account di accesso dell'istanza gestita di SQL solo quando vengono aggiunti come parte di un gruppo di Azure AD. Per utente guest di Azure AD si intende un account invitato da un'altra istanza di Azure AD all'istanza di Azure AD cui appartiene l'istanza gestita. Ad esempio, è possibile aggiungere joe@contoso.com (account Azure AD) o steve@outlook.com (account Microsoft) a un gruppo nell'istanza aadsqlmi di Azure AD. Dopo aver aggiunto gli utenti a un gruppo, è possibile creare un account di accesso nel database **master** dell'istanza gestita di SQL per il gruppo usando la sintassi **CREATE LOGIN**. Gli utenti guest che fanno parte di questo gruppo possono connettersi all'istanza gestita usando gli account di accesso correnti, ad esempio joe@contoso.com o steve@outlook.com.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Creare un utente di Azure AD dall'entità server (account di accesso) di Azure AD

Il funzionamento delle autorizzazioni per i singoli database nell'istanza gestita di SQL è analogo a quello delle autorizzazioni per i database in SQL Server. È possibile creare un utente da un account di accesso esistente in un database e concedere le autorizzazioni per il database oppure aggiungerlo a un ruolo del database.

A questo punto, dopo aver creato un database denominato **MyMITestDB** e un account di accesso che dispone solo delle autorizzazioni predefinite, il passaggio successivo consiste nel creare un utente da tale account di accesso. Al momento, l'account di accesso può connettersi all'istanza gestita e visualizzare tutti i database, ma non può interagire con essi. Se si accede con l'account Azure AD con le autorizzazioni predefinite e si prova a espandere il database appena creato, verrà visualizzato l'errore seguente:

![ssms-db-not-accessible.png](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Per altre informazioni sulla concessione delle autorizzazioni per i database, vedere [Introduzione alle autorizzazioni del motore di database](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Creare un utente di Azure AD e una tabella di esempio

1. Accedere all'istanza gestita con un account `sysadmin` usando SQL Server Management Studio.
1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul server e scegliere **Nuova query**.
1. Nella finestra di query usare la sintassi seguente per creare un utente di Azure AD da un'entità server (account di accesso) di Azure AD:

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

1. È anche possibile creare un utente di Azure AD da un'entità server (account di accesso) di Azure AD che corrisponde a un gruppo.

    L'esempio seguente mostra come creare un account di accesso per il gruppo di Azure AD _mygroup_ presente nell'istanza di Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Tutti gli utenti che appartengono a *mygroup* possono accedere al database **MyMITestDB**.

    > [!IMPORTANT]
    > Quando si crea un utente **USER** da un'entità server (account di accesso) di Azure AD, specificare per user_name lo stesso valore di login_name indicato in **LOGIN**.

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

1. Accedere all'istanza gestita con un account `sysadmin` usando SQL Server Management Studio.

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

1. Per verificare l'esistenza dell'utente di Azure AD creato nel database, eseguire questo comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Creare una nuova connessione all'istanza gestita con l'utente che è stato aggiunto al ruolo `db_datareader`.
1. Espandere il database in **Esplora oggetti** per visualizzare la tabella.

    ![ssms-test-table.png](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Aprire una nuova finestra di query ed eseguire l'istruzione SELECT seguente:

    ```sql
    SELECT *
    FROM TestTable
    ```

    I dati della tabella sono visibili? Dovrebbe essere possibile visualizzare le colonne restituite.

    ![ssms-test-table-query.png](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Rappresentare le entità di livello server (account di accesso) di Azure AD

L'istanza gestita di SQL supporta la rappresentazione di entità di livello server (account di accesso) di Azure AD.

### <a name="test-impersonation"></a>Testare la rappresentazione

1. Accedere all'istanza gestita con un account `sysadmin` usando SQL Server Management Studio.

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

1. Usare il comando seguente per verificare che l'utente rappresentato durante l'esecuzione della stored procedure sia **bob\@aadsqlmi.net**.

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
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>Usare le query tra database

Le query tra database sono supportate per gli account Azure AD con entità server (account di accesso) di Azure AD. Per testare una query tra database con un gruppo di Azure AD, è necessario creare un altro database e un'altra tabella. È possibile non creare un altro database e un'altra tabella se esistono già.

1. Accedere all'istanza gestita con un account `sysadmin` usando SQL Server Management Studio.
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

1. Accedere all'istanza gestita usando SQL Server Management Studio come membro del gruppo di Azure AD _mygroup_. Aprire una nuova finestra di query ed eseguire l'istruzione SELECT tra database:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Dovrebbero essere visualizzati i risultati della tabella di **TestTable2**.

## <a name="additional-supported-scenarios"></a>Altri scenari supportati

- Per le entità server (account di accesso) di Azure AD sono supportate la gestione dell'agente SQL e le esecuzioni di processi.
- Le operazioni di backup e ripristino del database possono essere eseguite dalle entità server (account di accesso) di Azure AD.
- [Controllo](auditing-configure.md) di tutte le istruzioni relative agli eventi di autenticazione e alle entità server (account di accesso) di Azure AD.
- Connessione amministrativa dedicata per le entità server (account di accesso) di Azure AD che fanno parte del ruolo del server `sysadmin`.
- Le entità server (account di accesso) di Azure AD sono supportate con l'[utilità sqlcmd](/sql/tools/sqlcmd-utility) e lo strumento [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Sono supportati i trigger di accesso per gli eventi di accesso provenienti dalle entità server (account di accesso) di Azure AD.
- Usando le entità server (account di accesso) di Azure AD è possibile configurare Service Broker e la posta elettronica del database.

## <a name="next-steps"></a>Passaggi successivi

### <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Vedere l'articolo sulle [funzionalità di sicurezza delle istanze gestite di SQL](sql-managed-instance-paas-overview.md#security-features) per un elenco completo di soluzioni per proteggere il database. Sono illustrate le funzionalità di sicurezza seguenti:

- [Controllo delle istanze gestite di SQL](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Introduzione al rilevamento delle minacce](threat-detection-configure.md)
- [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking)
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Funzionalità delle istanze gestite di SQL

Per una panoramica completa delle funzionalità delle istanze gestite di SQL, vedere:

> [!div class="nextstepaction"]
> [Funzionalità delle istanze gestite di SQL](sql-managed-instance-paas-overview.md)
