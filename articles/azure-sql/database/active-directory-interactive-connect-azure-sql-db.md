---
title: ActiveDirectoryInteractive si connette a SQL
description: Esempio di codice C#, con spiegazioni, per la connessione al database SQL di Azure tramite la modalità SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 93831ec4c1dc3e34c2ea144e71b67dae711ee870
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841649"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Connettersi al database SQL di Azure con Azure AD Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo fornisce un programma C# che si connette al database SQL di Azure. Il programma usa l'autenticazione in modalità interattiva, che supporta [Azure AD multi-factor authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Per ulteriori informazioni sul supporto Multi-Factor Authentication per gli strumenti SQL, vedere [supporto di Azure Active Directory in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication per il database SQL di Azure

A partire da .NET Framework versione 4.7.2, l'enumerazione [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ha un nuovo valore: `ActiveDirectoryInteractive` . In un programma client C# il valore enum indica al sistema di usare la modalità interattiva Azure Active Directory (Azure AD) che supporta Multi-Factor Authentication per la connessione al database SQL di Azure. L'utente che esegue il programma vede le finestre di dialogo seguenti:

* Una finestra di dialogo che mostra il nome di un utente di Azure AD e che ne richiede la password.

   Se il dominio dell'utente è federato con Azure AD, questa finestra di dialogo non viene visualizzata, perché non è necessaria alcuna password.

   Se il criterio di Azure AD impone Multi-Factor Authentication per l'utente, verranno visualizzate le due finestre di dialogo successive.

* La prima volta che un utente passa attraverso Multi-Factor Authentication, il sistema Visualizza una finestra di dialogo che richiede un numero di telefono cellulare a cui inviare messaggi di testo. Ogni messaggio specifica un *codice di verifica* che l'utente deve immettere nella finestra di dialogo successiva.

* Finestra di dialogo in cui viene richiesto un codice di verifica Multi-Factor Authentication, inviato dal sistema a un telefono cellulare.

Per informazioni su come configurare Azure AD per richiedere Multi-Factor Authentication, vedere [Getting Started with Azure AD multi-factor authentication nel cloud](../../active-directory/authentication/howto-mfa-getstarted.md).

Per gli screenshot di queste finestre di dialogo, vedere [configurare l'autenticazione a più fattori per SQL Server Management Studio e Azure ad](authentication-mfa-ssms-configure.md).

> [!TIP]
> È possibile cercare .NET Framework API con la [pagina dello strumento Visualizzatore API .NET](/dotnet/api/).
>
> È anche possibile eseguire la ricerca direttamente con il [ &lt; &gt; parametro facoltativo? termini = valore di ricerca](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurare l'applicazione C# nel portale di Azure

Prima di iniziare, è necessario disporre di un [server SQL logico](logical-servers.md) creato e disponibile.

### <a name="register-your-app-and-set-permissions"></a>Registrare l'app e impostare le autorizzazioni

Per usare l'autenticazione di Azure AD, il programma in C# deve registrarsi come applicazione Azure AD. Per registrare un'app è necessario essere un amministratore di Azure AD o un utente con il ruolo *Sviluppatore applicazioni* di Azure AD. Per ulteriori informazioni sull'assegnazione di ruoli, vedere [assegnare ruoli di amministratore e non amministratore agli utenti con Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Il completamento di una registrazione dell'app genera e visualizza un **ID applicazione**. Il programma deve includere l'ID per la connessione.

Per registrare e impostare le autorizzazioni necessarie per l'applicazione:

1. Nella portale di Azure selezionare **Azure Active Directory**  >  **registrazioni app**  >  **nuova registrazione**.

    ![Registrazione delle app](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Dopo la creazione della registrazione dell'app, viene generato e visualizzato il valore dell' **ID applicazione** .

    ![ID applicazione visualizzato](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**.

    ![Impostazioni delle autorizzazioni per l'app registrata](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selezionare le **API utilizzate dall'organizzazione** > digitare il **database SQL di Azure** nel > di ricerca e selezionare **database SQL di Azure**.

    ![Aggiungere l'accesso all'API per il database SQL di Azure](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selezionare **autorizzazioni delegate**  >  **user_impersonation**  >  **Aggiungi autorizzazioni**.

    ![Delegare le autorizzazioni all'API per il database SQL di Azure](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Impostare un amministratore Azure AD per il server

Per eseguire il programma C#, un amministratore di [SQL Server logico](logical-servers.md) deve assegnare un Azure ad amministratore per il server.

Nella pagina **SQL Server** selezionare **Active Directory amministratore**  >  **imposta amministratore**.

Per altre informazioni sugli amministratori e gli utenti di Azure AD per il database SQL di Azure, vedere le schermate in [configurare e gestire l'autenticazione Azure Active Directory con il database SQL](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Aggiungere un utente non amministratore a un database specifico (facoltativo)

Un amministratore Azure AD per un [server SQL logico](logical-servers.md) può eseguire il programma di esempio C#. Un utente di Azure AD può eseguire il programma se si trova nel database. Un amministratore del database SQL di Azure o un utente di Azure AD che esiste già nel database e ha l'autorizzazione `ALTER ANY USER` per il database può aggiungere un utente.

È possibile aggiungere un utente al database con il comando SQL [`Create User`](/sql/t-sql/statements/create-user-transact-sql) . Un esempio è `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Per altre informazioni, vedere [usare l'autenticazione Azure Active Directory per l'autenticazione con il database SQL, istanza gestita o Azure sinapsi Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Nuovo valore di enumerazione dell'autenticazione

L'esempio C# si basa sullo [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) spazio dei nomi. Di particolare interesse per Multi-Factor Authentication è l'enumerazione `SqlAuthenticationMethod` , che presenta i valori seguenti:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Utilizzare questo valore con un nome utente Azure AD per implementare Multi-Factor Authentication. Questo valore è il punto focale del presente articolo. Produce un'esperienza interattiva visualizzando le finestre di dialogo per la password utente e quindi, per Multi-Factor Authentication convalida, se viene imposto Multi-Factor Authentication per questo utente. Questo valore è disponibile a partire da .NET Framework versione 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Usare questo valore per un account *federato*. Per un account federato, il nome utente è noto al dominio di Windows. Questo metodo di autenticazione non supporta Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Usare questo valore per l'autenticazione che richiede un nome utente di Azure AD e la password. Il database SQL di Azure esegue l'autenticazione. Questo metodo non supporta Multi-Factor Authentication.

> [!NOTE]
> Se si usa .NET Core, sarà possibile usare lo spazio dei nomi [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) . Per ulteriori informazioni, vedere il [Blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)seguente.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Impostare i valori dei parametri in C# dal portale di Azure

Per l'esecuzione corretta del programma in C# è necessario assegnare i valori appropriati ai campi statici. Di seguito sono illustrati i campi con valori di esempio. Sono inoltre illustrati i percorsi di portale di Azure in cui è possibile ottenere i valori necessari.

| Nome campo statico | Valore di esempio | Posizione nel portale di Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Server SQL**  >  **Filtra per nome** |
| AzureAD_UserID | "Auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Utente**  >  di **Nuovo utente Guest** |
| Initial_DatabaseName | "myDatabase" | **Server SQL**  >  **Database SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **Registrazioni app**  >  **Cerca per nome**  >  **ID applicazione** |
| RedirectUri | nuovo URI ("https://mywebserver.com/") | **Azure Active Directory**  >  **Registrazioni app**  >  **Cerca per nome**  >  *[Your-app-Registration]*  >  **Impostazioni**  >  di **RedirectURIs**<br /><br />Per questo articolo, qualsiasi valore valido è adatto per RedirectUri, perché non viene usato qui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifica con SQL Server Management Studio

Prima di eseguire il programma C#, è consigliabile verificare che la configurazione e le configurazioni siano corrette in SQL Server Management Studio (SSMS). In questo modo, qualsiasi errore del programma in C# potrà essere ricondotto al codice sorgente.

### <a name="verify-server-level-firewall-ip-addresses"></a>Verificare gli indirizzi IP del firewall a livello di server

Eseguire SSMS dallo stesso computer e nello stesso edificio in cui si intende eseguire il programma in C#. Per questo test, qualsiasi modalità di **autenticazione** è accettabile. Se viene indicato che il server non accetta l'indirizzo IP, vedere regole del firewall a livello di [Server e di database](firewall-configure.md) per la guida.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verificare Azure Active Directory Multi-Factor Authentication

Eseguire di nuovo SSMS, questa volta con **l'autenticazione** impostata su **Azure Active Directory universale con** autenticazione a più fattori. Questa opzione richiede SSMS 17.5 o versione successiva.

Per altre informazioni, vedere [configurare multi-factor authentication per SSMS e Azure ad](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Se si è un utente Guest nel database, è necessario specificare anche il nome di dominio Azure ad per il database: selezionare **Opzioni**  >  **nome dominio Active Directory o ID tenant**. Per trovare il nome di dominio nella portale di Azure, selezionare **Azure Active Directory**  >  **nomi di dominio personalizzati**. Nel programma di esempio in C#, fornire un nome di dominio non è necessario.

## <a name="c-code-example"></a>Esempio di codice C#

> [!NOTE]
> Se si usa .NET Core, sarà possibile usare lo spazio dei nomi [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) . Per ulteriori informazioni, vedere il [Blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)seguente.

Il programma in C# di esempio si basa sull'assembly di DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Per installare questo pacchetto, in Visual Studio selezionare **progetto**  >  **Gestisci pacchetti NuGet**. Cercare e installare **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Questo è un esempio di codice sorgente C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Questo è un esempio di output di test di C#.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)