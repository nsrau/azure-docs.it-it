---
title: Utilizzo della modalità ActiveDirectoryInteractive per la connessione a SQL | Microsoft Docs
description: Esempio di codice C#, con spiegazioni, per la connessione al database SQL di Azure tramite la modalità SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: c55fa6d58109345a0c600bd0c1c76c5a229c03bc
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554497"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Connettersi al database SQL di Azure con Multi-Factor Authentication di Azure

Questo articolo fornisce un C# programma che si connette al database SQL di Azure. Il programma usa l'autenticazione in modalità interattiva che supporta [Azure multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Per ulteriori informazioni sul supporto Multi-Factor Authentication per gli strumenti SQL, vedere [supporto di Azure Active Directory in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication per il database SQL di Azure

A partire da .NET Framework versione 4.7.2, enum [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ha un nuovo valore: `ActiveDirectoryInteractive`. In un programma C# client, il valore enum indica al sistema di usare la modalità interattiva Azure Active Directory (Azure ad) che supporta multi-factor authentication per connettersi a un database SQL di Azure. L'utente che esegue il programma vede le finestre di dialogo seguenti:

* Una finestra di dialogo che mostra il nome di un utente di Azure AD e che ne richiede la password.

   Se il dominio dell'utente è federato con Azure AD, questa finestra di dialogo non viene visualizzata, perché non è necessaria alcuna password.

   Se il criterio di Azure AD impone Multi-Factor Authentication per l'utente, verranno visualizzate le due finestre di dialogo successive.

* La prima volta che un utente passa attraverso Multi-Factor Authentication, il sistema Visualizza una finestra di dialogo che richiede un numero di telefono cellulare a cui inviare messaggi di testo. Ogni messaggio specifica un *codice di verifica* che l'utente deve immettere nella finestra di dialogo successiva.

* Finestra di dialogo in cui viene richiesto un codice di verifica Multi-Factor Authentication, inviato dal sistema a un telefono cellulare.

Per informazioni su come configurare Azure AD per richiedere Multi-Factor Authentication, vedere la pagina relativa all' [Introduzione ad Azure multi-factor authentication nel cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Per gli screenshot di queste finestre di dialogo, vedere [configurare l'autenticazione a più fattori per SQL Server Management Studio e Azure ad](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> È possibile cercare .NET Framework API con la [pagina dello strumento Visualizzatore API .NET](https://docs.microsoft.com/dotnet/api/).
>
> È anche possibile eseguire la ricerca direttamente con il [parametro facoltativo? term = &lt;search valore &gt; parametro](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurare l'applicazione C# nel portale di Azure

Prima di iniziare, occorre avere un [server di database SQL di Azure](sql-database-get-started-portal.md) pronto e disponibile.

### <a name="register-your-app-and-set-permissions"></a>Registrare l'app e impostare le autorizzazioni

Per usare l'autenticazione di Azure AD, il programma in C# deve registrarsi come applicazione Azure AD. Per registrare un'app è necessario essere un amministratore di Azure AD o un utente con il ruolo *Sviluppatore applicazioni* di Azure AD. Per ulteriori informazioni sull'assegnazione di ruoli, vedere [assegnare ruoli di amministratore e non amministratore agli utenti con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Il completamento di una registrazione dell'app genera e visualizza un **ID applicazione**. Il programma deve includere l'ID per la connessione.

Per registrare e impostare le autorizzazioni necessarie per l'applicazione:

1. Nella portale di Azure selezionare **Azure Active Directory**  > **registrazioni app**  > **nuova registrazione**.

    ![Registrazione delle app](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Dopo la creazione della registrazione dell'app, viene generato e visualizzato il valore dell' **ID applicazione** .

    ![ID applicazione visualizzato](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selezionare **autorizzazioni API**  > **aggiungere un'autorizzazione**.

    ![Impostazioni delle autorizzazioni per l'app registrata](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selezionare le **API utilizzate dall'organizzazione** > digitare il **database SQL di Azure** nel > di ricerca e selezionare **database SQL di Azure**.

    ![Aggiungere l'accesso all'API per il database SQL di Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selezionare **autorizzazioni delegate**  > **user_impersonation**  > **Aggiungi autorizzazioni**.

    ![Delegare le autorizzazioni all'API per il database SQL di Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Impostare un amministratore di Azure AD per il server di database SQL

Per eseguire C# il programma, un amministratore del server SQL di Azure deve assegnare un amministratore Azure ad per il server di database SQL. 

Nella pagina **SQL Server** selezionare **Active Directory amministratore**  > **imposta amministratore**.

Per altre informazioni sugli amministratori e gli utenti di Azure AD per il database SQL di Azure, vedere le schermate in [configurare e gestire l'autenticazione Azure Active Directory con il database SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Aggiungere un utente non amministratore a un database specifico (facoltativo)

Un amministratore di Azure AD per un server di database SQL può eseguire il programma in C# di esempio. Un utente di Azure AD può eseguire il programma se si trova nel database. Un amministratore del database SQL di Azure o un utente di Azure AD che esiste già nel database e ha l'autorizzazione `ALTER ANY USER` per il database può aggiungere un utente.

È possibile aggiungere un utente al database con il comando SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql). Un esempio è `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Per altre informazioni, vedere [Usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nuovo valore di enumerazione dell'autenticazione

L'esempio in C# è basato sullo spazio dei nomi [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient). Di particolare interesse per Multi-Factor Authentication è l'enumerazione `SqlAuthenticationMethod`, che presenta i valori seguenti:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Utilizzare questo valore con un nome utente Azure AD per implementare Multi-Factor Authentication. Questo valore è il punto focale del presente articolo. Produce un'esperienza interattiva visualizzando le finestre di dialogo per la password utente e quindi, per Multi-Factor Authentication convalida, se viene imposto Multi-Factor Authentication per questo utente. Questo valore è disponibile a partire da .NET Framework versione 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Usare questo valore per un account *federato*. Per un account federato, il nome utente è noto al dominio di Windows. Questo metodo di autenticazione non supporta Multi-Factor Authentication.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Usare questo valore per l'autenticazione che richiede un nome utente di Azure AD e la password. Il database SQL di Azure esegue l'autenticazione. Questo metodo non supporta Multi-Factor Authentication.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Impostare i valori dei parametri in C# dal portale di Azure

Per l'esecuzione corretta del programma in C# è necessario assegnare i valori appropriati ai campi statici. Di seguito sono illustrati i campi con valori di esempio. Sono inoltre illustrati i percorsi di portale di Azure in cui è possibile ottenere i valori necessari.

| Nome campo statico | Valore di esempio | Posizione nel portale di Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL Server** > **Filtra per nome** |
| AzureAD_UserID | "Auser \@abc. onmicrosoft.com" | **Azure Active Directory** > **Utente** > **Nuovo utente guest** |
| Initial_DatabaseName | "myDatabase" | **SQL Server** > **Database SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **Registrazioni app** > **Cerca per nome** > **ID applicazione** |
| RedirectUri | nuovo URI ("https://mywebserver.com/") | **Azure Active Directory**  > **registrazioni app**  >  la**ricerca in base al nome**  > **le impostazioni** di  >  *[your-app-Registration]*  > **RedirectURIs**<br /><br />Per questo articolo, qualsiasi valore valido è adatto per RedirectUri, perché non viene usato qui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifica con SQL Server Management Studio

Prima di eseguire il C# programma, è consigliabile verificare che la configurazione e le configurazioni siano corrette in SQL Server Management Studio (SSMS). In questo modo, qualsiasi errore del programma in C# potrà essere ricondotto al codice sorgente.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificare gli indirizzi IP dei firewall del database SQL

Eseguire SSMS dallo stesso computer e nello stesso edificio in cui si intende eseguire il programma in C#. Per questo test, qualsiasi modalità di **autenticazione** è accettabile. Se viene segnalato che il firewall del server di database non accetta l'indirizzo IP specificato, vedere [Regole firewall a livello di database e di server di database SQL di Azure](sql-database-firewall-configure.md) per indicazioni.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verificare Azure Active Directory Multi-Factor Authentication

Eseguire di nuovo SSMS, questa volta con **Autenticazione** impostata su **Active Directory - Universal with MFA support** (Active Directory - Universale con supporto MFA). Questa opzione richiede SSMS 17.5 o versione successiva.

Per altre informazioni, vedere [configurare multi-factor authentication per SSMS e Azure ad](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se si è un utente Guest nel database, è necessario specificare anche il nome di dominio Azure AD per il database: selezionare le **opzioni**  > **nome di dominio di Active Directory o ID tenant**. Per trovare il nome di dominio nel portale di Azure, selezionare **Azure Active Directory** > **Nomi di dominio personalizzati**. Nel programma di esempio in C#, fornire un nome di dominio non è necessario.

## <a name="c-code-example"></a>Esempio di codice C#

Il programma in C# di esempio si basa sull'assembly di DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Per installare questo pacchetto, in Visual Studio selezionare **Progetto** > **Gestisci pacchetti NuGet**. Cercare e installare **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Questo è un esempio di C# codice sorgente.

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
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
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

Questo è un esempio di output C# del test.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
