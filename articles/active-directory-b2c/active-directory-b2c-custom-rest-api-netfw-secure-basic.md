---
title: 'Azure Active Directory B2C: proteggere i servizi RESTful con l''autenticazione di base HTTP'
description: Proteggere gli scambi di attestazioni dell'API REST personalizzati in Azure AD B2C con l'autenticazione di base HTTP
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: d65d94bb5c807abfd6cbb1fae786a02f179e93d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteggere i servizi RESTful tramite l'autenticazione di base HTTP
In un [articolo correlato relativo ad Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md) è stato creato un servizio RESTful (API Web) integrato nei percorsi utente di Azure Active Directory B2C (Azure AD B2C) senza autenticazione. 

Questo articolo illustra come aggiungere l'autenticazione di base HTTP al servizio RESTful in modo che solo gli utenti verificati, tra cui B2C, possano accedere all'API. Con l'autenticazione di base HTTP è necessario impostare le credenziali dell'utente (ID e segreto dell'app) nei criteri personalizzati. 

Per altre informazioni, vedere [Basic authentication in ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication) (Autenticazione di base nell'API Web ASP.NET).

## <a name="prerequisites"></a>Prerequisiti
Completare i passaggi nell'articolo [Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="step-1-add-authentication-support"></a>Passaggio 1: Aggiungere il supporto per l'autenticazione

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Passaggio 1.1: Aggiungere le impostazioni dell'applicazione al file web.config del progetto
1. Aprire il progetto di Visual Studio creato in precedenza. 

2. Aggiungere le impostazioni dell'applicazione seguenti al file web.config nell'elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Creare una password e quindi impostare il valore `WebApp:ClientSecret`.

    Per generare una password complessa, eseguire il codice seguente di PowerShell. È possibile usare qualsiasi valore arbitrario.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Passaggio 1.2: Installare le librerie OWIN
Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti di Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Passaggio 1.3: Aggiungere una classe del middleware di autenticazione
Aggiungere la classe `ClientAuthMiddleware.cs` nella cartella *App_Start*. A tale scopo, procedere come segue:

1. Fare clic con il pulsante destro del mouse sulla cartella *App_Start*, scegliere **Aggiungi** e quindi selezionare **Classe**.

   ![Aggiungere la classe ClientAuthMiddleware.cs nella cartella App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Nella casella **Nome** digitare **ClientAuthMiddleware.cs**.

   ![Creare una nuova classe C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Aprire il file *App_Start\ClientAuthMiddleware.cs* e sostituire il contenuto del file con il codice seguente:

    ```C#
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Passaggio 1.4: Aggiungere una classe di avvio OWIN
Aggiungere una classe di avvio OWIN denominata `Startup.cs` all'API. A tale scopo, procedere come segue:
1. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** > **Nuovo elemento** e cercare **OWIN**.

   ![Aggiungere una classe di avvio OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Aprire il file *Startup.cs* e sostituire il contenuto del file con il codice seguente:

    ```C#
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Passaggio 1.5: Proteggere la classe dell'API di identità
Aprire Controllers\IdentityController.cs e aggiungere il tag `[Authorize]` alla classe del controller. Questo tag limita l'accesso al controller agli utenti che soddisfano i requisiti di autorizzazione.

![Aggiungere il tag Authorize al controller](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Passaggio 2: Pubblicare in Azure
Per pubblicare il progetto, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e quindi selezionare **Pubblica**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Passaggio 3: Aggiungere ID e segreto dell'app dei servizi RESTful ad Azure AD B2C
Dopo aver protetto il servizio RESTful con ID client (nomeutente) e segreto client, è necessario archiviare le credenziali nel tenant di Azure AD B2C. I criteri personalizzati forniscono le credenziali quando richiamano i servizi RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Passaggio 3.1: Aggiungere un ID client dei servizi RESTful
1. Nel tenant di Azure AD B2C selezionare **Impostazioni di Azure AD B2C** > **Framework dell'esperienza di gestione delle identità**.


2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.

3. Selezionare **Aggiungi**.

4. Selezionare **Manuale** in **Opzioni**.

5. Digitare **B2cRestClientId** in **Nome**.  
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.

6. Nella casella **Segreto** immettere l'ID dell'app definito in precedenza.

7. In **Uso chiave** selezionare **Secreto**.

8. Selezionare **Create**.

9. Verificare di avere creato la chiave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Passaggio 3.2: Aggiungere un segreto client dei servizi RESTful
1. Nel tenant di Azure AD B2C selezionare **Impostazioni di Azure AD B2C** > **Framework dell'esperienza di gestione delle identità**.

2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.

3. Selezionare **Aggiungi**.

4. Selezionare **Manuale** in **Opzioni**.

5. Digitare **B2cRestClientSecret** in **Nome**.  
    È possibile che il prefisso *B2C_1A_* venga aggiunto automaticamente.

6. Nella casella **Segreto** immettere il segreto dell'app definito in precedenza.

7. In **Uso chiave** selezionare **Secreto**.

8. Selezionare **Create**.

9. Verificare di avere creato la chiave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Passaggio 4: Modificare il profilo tecnico per supportare l'autenticazione di base nei criteri di estensione
1. Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) nella directory di lavoro.

2. Cercare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`.

3. Individuare l'elemento `<Metadata>`.

4. Modificare *AuthenticationType* impostando *Basic*, come segue:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Aggiungere il frammento XML seguente subito dopo l'elemento `<Metadata>` di chiusura: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Dopo aver aggiunto il frammento di codice, il profilo tecnico dovrebbe essere simile al codice XML seguente:
    
    ![Aggiungere gli elementi XML dell'autenticazione di base](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passaggio 5: Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi aprire **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Aprire **Tutti i criteri**.

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passaggio 6: Testare i criteri personalizzati tramite Esegui adesso
1. Aprire **Impostazioni di Azure AD B2C** e quindi selezionare **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza e quindi selezionare **Esegui adesso**.

3. Testare il processo digitando **Test** nella casella **Nome**.  
    Azure AD B2C visualizza un messaggio di errore nella parte superiore della finestra.

    ![Testare l'identità dell'API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Digitare un nome (diverso da "Test") nella casella **Nome**.  
    Azure AD B2C effettua l'iscrizione dell'utente e invia quindi un numero di identificazione personale (loyaltyNumber) all'applicazione. Prendere nota del numero in questo esempio:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facoltativo) Scaricare i file e il codice dei criteri completi
* Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* È possibile scaricare il codice completo da [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API) (Soluzione di Visual Studio di esempio per riferimento).

## <a name="next-steps"></a>Passaggi successivi
* [Usare i certificati client per proteggere l'API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

