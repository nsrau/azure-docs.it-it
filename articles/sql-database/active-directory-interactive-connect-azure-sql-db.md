---
title: Utilizzo della modalità ActiveDirectoryInteractive per la connessione a SQL | Microsoft Docs
description: Esempio di codice C#, con spiegazioni, per la connessione al database SQL di Microsoft Azure tramite la modalità SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 04/06/2018
manager: craigg
ms.openlocfilehash: 80944e73f21d75943d4fa71c7ac9500e47bab250
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055527"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Utilizzo della modalità ActiveDirectoryInteractive per la connessione al database SQL di Microsoft Azure

Questo articolo include un esempio di codice C# eseguibile che si connette al database SQL di Microsoft Azure. Il programma in C# usa la modalità di autenticazione interattiva, che supporta l'autenticazione a più fattori (MFA, Multi-Factor Authentication) di Azure Active Directory. Un tentativo di connessione può ad esempio includere un codice di verifica che viene inviato al telefono cellulare.

Per altre informazioni sul supporto MFA per gli strumenti SQL, vedere [Supporto di Azure Active Directory in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>Valore di enumerazione .ActiveDirectoryInteractive di SqlAuthenticationMethod

A partire da .NET Framework versione 4.7.2, il valore di enumerazione [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ha un nuovo valore, **.ActiveDirectoryInteractive**. Quando usato da un programma client in C#, questo valore di enumerazione indica al sistema di usare la modalità interattiva di Azure AD che supporta MFA per l'autenticazione con il database SQL di Azure. L'utente che esegue il programma visualizza quindi le finestre di dialogo seguenti:

1. Una finestra di dialogo che visualizza il nome di un utente di Azure AD e che ne richiede la password.
    - Questa finestra di dialogo non viene visualizzata se non è necessaria alcuna password. La password non serve se il dominio dell'utente è federato con Azure AD.

    Se l'autenticazione MFA è un requisito imposto all'utente dai criteri di Azure AD, vengono successivamente visualizzate le finestre di dialogo seguenti.

2. Solo la prima volta in cui l'utente usa lo scenario di autenticazione MFA il sistema visualizza una finestra di dialogo aggiuntiva. In questa finestra di dialogo viene richiesto di indicare un numero di telefono cellulare a cui inviare i messaggi di testo. Ogni messaggio specifica un *codice di verifica* che l'utente deve immettere nella finestra di dialogo successiva.

3. In un'altra finestra di dialogo viene chiesto di immettere il codice di verifica MFA che il sistema ha inviato a al telefono cellulare.

Per informazioni su come configurare Azure AD per la richiesta dell'autenticazione MFA, vedere [Introduzione ad Azure Multi-Factor Authentication nel cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Per gli screenshot di queste finestre di dialogo, vedere [Configurare Multi-Factor Authentication per SQL Server Management Studio e Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> La pagina di ricerca generale per tutti i tipi di API .NET Framework è disponibile tramite il collegamento seguente allo strumento **Browser API .NET**:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Se si aggiunge il nome del tipo al parametro facoltativo **?term=** accodato, i risultati vengono subito visualizzati nella pagina di ricerca:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Preparativi per C# tramite il portale di Azure

Si supponga di avere già un [server di database SQL di Azure](sql-database-get-started-portal.md) pronto e disponibile.


### <a name="a-create-an-app-registration"></a>R. Creare la registrazione di un'app

Per usare l'autenticazione di Azure AD, il programma client in C# deve specificare un GUID come *clientId* quando tenta di connettersi. Il completamento della registrazione dell'app genera e visualizza il GUID nel portale di Azure, con l'etichetta **ID applicazione**. Di seguito sono descritti i passaggi di navigazione:

1. Portale di Azure&gt; **Azure Active Directory** &gt; **Registrazione app**

    ![Registrazione delle app](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. Viene generato e visualizzato il valore di **ID applicazione**.

    ![ID applicazione visualizzato](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **App registrata** &gt; **Impostazioni** &gt; **Autorizzazioni necessarie** &gt; **Aggiungi**

    ![Impostazioni delle autorizzazioni per l'app registrata](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Autorizzazioni necessarie** &gt; **Aggiungi accesso all'API** &gt; **Seleziona API** &gt; **Database SQL di Azure**

    ![Aggiungere l'accesso all'API per il database SQL di Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Accesso all'API** &gt; **Selezionare le autorizzazioni** &gt; **Autorizzazioni delegate**

    ![Delegare le autorizzazioni all'API per il database SQL di Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Impostare l'amministratore di Azure AD nel server di database SQL

Ogni server di database SQL di Azure ha un proprio server logico SQL di Azure AD. Per questo scenario in C# è necessario impostare un amministratore di Azure AD per il server SQL di Azure.

1. **SQL Server** &gt; **Amministratore di Active Directory** &gt; **Imposta amministratore**

    - Per altre informazioni sugli amministratori e gli utenti di Azure AD per il database SQL di Azure, vedere gli screenshot in [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) nella relativa sezione **Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Preparare un utente di Azure AD per la connessione a un database specifico

In Azure AD specifico per il server di database SQL di Azure in uso, è possibile aggiungere un utente che abbia accesso a un database specifico.

Per altre informazioni, vedere [Usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Aggiungere un utente non amministratore ad Azure AD

L'amministratore di Azure AD del server di database SQL può essere usato per connettersi al server di database SQL. La situazione più comune consiste invece nell'aggiungere un utente non amministratore ad Azure AD. Quando l'utente non amministratore viene usato per la connessione, la sequenza MFA viene richiamata se l'autenticazione a più fattori è imposta a questo utente da Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

Il programma in C# si basa sullo spazio dei nomi **Microsoft.IdentityModel.Clients.ActiveDirectory**. Le classi per questo spazio dei nomi si trovano nell'assembly con lo stesso nome.

- Usare NuGet per scaricare e installare l'assembly.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Aggiungere un riferimento all'assembly, per supportare una compilazione del programma in C#.




## <a name="sqlauthenticationmethod-enum"></a>Enumerazione SqlAuthenticationMethod

Uno degli spazi dei nomi su cui si basa l'esempio in C# è **System.Data.SqlClient**. Di particolare interesse è l'enumerazione **SqlAuthenticationMethod**. Questa enumerazione contiene i valori seguenti:

- **SqlAuthenticationMethod.ActiveDirectory *Interactive***:&nbsp; usare questo valore con un nome utente di Azure AD per raggiungere l'autenticazione a più fattori.
    - Questo valore è il punto focale del presente articolo. Genera un'esperienza interattiva visualizzando le finestre di dialogo per la password dell'utente e successivamente per la convalida MFA se a questo utente è imposta l'autenticazione a più fattori.
    - Questo valore è disponibile a partire da .NET Framework versione 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory *Integrated***:&nbsp; usare questo valore per un account *federated*. Per un account federato, il nome utente è noto al dominio di Windows. Questo metodo non supporta l'autenticazione a più fattori.

- **SqlAuthenticationMethod.ActiveDirectory *Password***:&nbsp; usare questo valore per l'autenticazione che richiede un utente di Azure AD e la password dell'utente. Il database SQL di Azure esegue l'autenticazione. Questo metodo non supporta l'autenticazione a più fattori.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Preparare i valori dei parametri in C# dal portale di Azure

Per un'esecuzione corretta del programma in C#, è necessario assegnare i valori appropriati ai campi statici seguenti. Questi campi statici si comportano come parametri nel programma. I campi vengono mostrati qui con valori fittizi. Vengono inoltre visualizzate le posizioni nel portale di Azure da dove è possibile ottenere i valori appropriati:


| Nome campo statico | Valore fittizio | Posizione nel portale di Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **Server SQL** &gt; **Filter by name** (Filtra per nome) |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **Utente** &gt; **Nuovo utente guest** |
| Initial_DatabaseName | "master" | **Server SQL** &gt; **Database SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **Registrazioni per l'app**<br /> &nbsp; &nbsp; &gt; **Search by name** (Cerca per nome) &gt; **Application ID** (ID applicazione) |
| RedirectUri | nuovo Uri( "https://bing.com/") | **Azure Active Directory** &gt; **Registrazioni per l'app**<br /> &nbsp; &nbsp; &gt; **Search by name** (Cerca per nome) &gt; *[Regis-app-in uso]* &gt;<br /> &nbsp; &nbsp; **Impostazioni** &gt; **URI di reindirizzamento**<br /><br />Ai fini di questo articolo, per l'URI di reindirizzamento va bene qualsiasi valore. Il valore non viene usato effettivamente in questo caso. |
| &nbsp; | &nbsp; | &nbsp; |


A seconda dello scenario specifico, potrebbe non essere necessario usare valori per tutti i parametri nella tabella precedente.




## <a name="run-ssms-to-verify"></a>Eseguire SSMS per la verifica

Prima di eseguire il programma in C#, è utile eseguire SQL Server Management Studio (SSMS). SSMS verifica che le varie configurazioni siano corrette. Qualsiasi errore nel programma in C# può così essere limitato al solo codice sorgente.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificare gli indirizzi IP dei firewall del database SQL

Eseguire SSMS dallo stesso computer, nello stesso edificio, che in un secondo momento eseguirà il programma in C#. È possibile usare qualsiasi modalità di **autenticazione** che si ritiene più facile. Se viene segnalato che il firewall del server di database non accetta l'indirizzo IP specificato, è possibile correggerlo come indicato in [Azure SQL Database server-level and database-level firewall rules](sql-database-firewall-configure.md) (Regole di firewall a livello di database e a livello di server del database SQL di Azure).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Verificare l'autenticazione a più fattori (MFA) per Azure AD

Eseguire di nuovo SSMS, questa volta con **Autenticazione** impostata su **Active Directory - Universal with MFA support** (Active Directory - Universale con supporto MFA). Per questa opzione è necessario disporre di SSMS 17.5 o versione successiva.

Per altre informazioni, vedere [Configurare l'autenticazione a più fattori per SSMS e Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Esempio di codice C#

Per compilare questo esempio in C#, è necessario aggiungere un riferimento all'assembly di DLL denominato **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Documentazione di riferimento

- Spazio dei nomi **System.Data.SqlClient**:
    - Cercare:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Diretto:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- Spazio dei nomi **Microsoft.IdentityModel.Clients.ActiveDirectory**:
    - Cercare:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Diretto:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Codice sorgente C# in due parti

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Seconda metà del programma in C#

Per agevolarne la visione, il programma in C# è suddiviso in due blocchi di codice. Per eseguire il programma, incollare i due blocchi di codice insieme.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
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
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Output del test effettivo da C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Passaggi successivi

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

