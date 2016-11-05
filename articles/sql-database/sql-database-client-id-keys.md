---
title: Registrare l'applicazione e ottenere l'ID client e la chiave per la connessione al database SQL dal codice | Microsoft Docs
description: Come ottenere l'ID client e la chiave per l'accesso al database SQL dal codice.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/06/2016
ms.author: sstein

---
# Ottenere l'ID client e la chiave per la connessione al database SQL dal codice
Per creare e gestire database SQL dal codice è necessario registrare l'app nel dominio di Azure Active Directory (AAD) associato alla sottoscrizione in cui sono state create le risorse di Azure. Quando si registra l'applicazione, Azure genera un ID client e una chiave da utilizzare nel codice di autenticazione dell'app. Per altre informazioni, vedere [Azure Active Directory - Documentazione](https://azure.microsoft.com/documentation/services/active-directory/).

## Registrare un'applicazione client nativa e ottenere l'ID client
Per creare una nuova applicazione e registrarla procedere come descritto di seguito:

1. Accedere al [portale classico](https://manage.windowsazure.com/) (attualmente la registrazione delle applicazioni deve essere eseguita nel portale classico).
2. Individuare **Active Directory** nel menu e selezionarlo.
   
    ![AAD][1]
3. Selezionare la directory per l'autenticazione dell'applicazione e fare clic sul relativo **nome**.
   
    ![Directory][4]
4. Nella pagina della directory fare clic su **APPLICAZIONI**.
   
    ![Applicazioni][5]
5. Fare clic su **AGGIUNGI** per creare una nuova applicazione.
   
    ![Aggiunta di un'applicazione][6]
6. Fornire un **NOME** per l'app e selezionare **APPLICAZIONE CLIENT NATIVA**.
   
    ![Aggiunta di un'applicazione][7]
7. Specificare un **URI DI REINDIRIZZAMENTO**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.
   
    ![Aggiunta di un'applicazione][8]
8. Completare la creazione dell'applicazione, fare clic su **CONFIGURA** e copiare l’**ID CLIENT** (il valore che sarà necessario nel codice).
   
    ![acquisisci ID client][9]
9. Scorrere verso il basso e fare clic su **Aggiungi applicazione**.
10. Selezionare **App Microsoft**.
11. Selezionare **API di gestione del servizio Microsoft Azure** e quindi completare la procedura guidata.
12. Nella sezione **Autorizzazioni per altre applicazioni** individuare l'**API di gestione del servizio Microsoft Azure** e fare clic su **Autorizzazioni delegate**.
13. Selezionare **Accesso a Gestione dei servizi di Azure...**.
    
     ![autorizzazioni][2]
14. Fare clic su **SAVE** nella parte inferiore della pagina.

## Registrare un'app Web (o API Web) e ottenere l'ID client e la chiave
Per creare una nuova applicazione e registrarla nell’active directory corrente procedere come descritto di seguito:

1. Accedere al [portale classico](https://manage.windowsazure.com/).
2. Individuare **Active Directory** nel menu e selezionarlo.
   
    ![AAD][1]
3. Selezionare la directory per l'autenticazione dell'applicazione e fare clic sul relativo **nome**.
   
    ![Directory][4]
4. Nella pagina della directory fare clic su **APPLICAZIONI**.
   
    ![Applicazioni][5]
5. Fare clic su **AGGIUNGI** per creare una nuova applicazione.
   
    ![Aggiunta di un'applicazione][6]
6. Fornire un **NOME** per l'applicazione e selezionare **APPLICAZIONE WEB E/O API WEB**.
   
    ![Aggiunta di un'applicazione][10]
7. Fornire un **URL DI ACCESSO** e un **URI ID APP**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.
   
    ![Aggiunta di un'applicazione][11]
8. Completare la creazione dell'applicazione, quindi fare clic su **CONFIGURA**.
   
    ![configure][12]
9. Scorrere fino alla sezione delle **chiavi** e selezionare **1 anno** nell'elenco **Seleziona durata**. Dopo il salvataggio verrà visualizzato il valore della chiave, che sarà copiato in un secondo momento.
   
    ![imposta durata chiave][13]
10. Scorrere verso il basso e fare clic su **Aggiungi applicazione**.
11. Selezionare **App Microsoft**.
12. Individuare e selezionare **API di gestione del servizio Microsoft Azure**, quindi completare la procedura guidata.
13. Nella sezione **Autorizzazioni per altre applicazioni** individuare l'**API di gestione del servizio Microsoft Azure** e fare clic su **Autorizzazioni delegate**.
14. Selezionare **Accesso a Gestione dei servizi di Azure...**.
    
     ![autorizzazioni][2]
15. Fare clic su **SAVE** nella parte inferiore della pagina.
16. Dopo il salvataggio individuare e salvare l'ID CLIENT e la chiave:
    
     ![segreti app Web][14]

## Ottenere il nome di dominio
Il nome di dominio è talvolta richiesto per il codice di autenticazione. Un modo semplice per identificare il nome di dominio corretto è:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare il mouse sul proprio nome nell'angolo superiore destro e osservare il dominio visualizzato nella finestra popup.
   
    ![Identificare il nome di dominio][3]

## Applicazione console di esempio
Per ottenere le librerie di gestione richieste è necessario installare i pacchetti seguenti tramite la [Console di Gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**):

    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Creare un'app console **SqlDbAuthSample** e sostituire il contenuto del file **Program.cs** con il seguente:

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Per esempi di codice specifici relativi all'autenticazione di Azure AD, vedere il [blog sulla sicurezza di SQL Server](http://blogs.msdn.com/b/sqlsecurity/) su MSDN.

## Vedere anche
* [Usare C# per creare un database SQL](sql-database-get-started-csharp.md)
* [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](sql-database-aad-authentication.md)

<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->