---
title: 'Azure Active Directory B2C: proteggere i servizi RESTful con l''autenticazione di base HTTP'
description: Informazioni su come proteggere gli scambi di attestazioni dell'API REST personalizzati in Azure AD B2C con l'autenticazione di base HTTP
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C: proteggere i servizi RESTful con l'autenticazione di base HTTP
In un [articolo correlato](active-directory-b2c-custom-rest-api-netfw.md) è stato creato un servizio RESTful (API Web) integrato nei percorsi utente di Azure AD B2C senza autenticazione. Questo articolo illustra come aggiungere l'autenticazione di base HTTP al servizio RESTful in modo che solo gli utenti verificati, tra cui B2C, possano accedere all'API. Con l'autenticazione di base HTTP è necessario impostare le credenziali dell'utente (ID e segreto dell'app) nei criteri personalizzati per usare le credenziali. 

> [!NOTE]
>
>L'articolo successivo descrive l'[autenticazione di base nell'API Web ASP.NET](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication) in maggiore dettaglio.

## <a name="prerequisites"></a>Prerequisiti
Completare i passaggi precedenti nell'articolo [Integrare scambi di attestazioni API REST nei percorsi utente di Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md)

## <a name="step-1-add-authentication-support"></a>Passaggio 1: Aggiungere il supporto per l'autenticazione

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>Passaggio 1.1 Aggiungere le impostazioni dell'applicazione al file web.config del progetto
1. Aprire il progetto di Visual Studio creato in precedenza 
2. Aggiungere le impostazioni dell'applicazione seguenti al file web.config nell'elemento `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Creare una password e impostare il valore `WebApp:ClientSecret`

    Per generare una password completa, è possibile eseguire i comandi PowerShell seguenti, anche se si può usare qualsiasi valore arbitrario.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Passaggio 1.2 Installare le librerie OWIN
Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti di Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>Passaggio 1.3 Aggiungere la classe del middleware di autenticazione
Aggiungere la classe `ClientAuthMiddleware.cs` nella cartella `App_Start`. Fare clic con il pulsante destro del mouse sulla cartella `App_Start`, scegliere **Aggiungi** e quindi selezionare **classe**.

![Aggiungere la classe ClientAuthMiddleware.cs nella cartella App_Start.](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

Digitare `ClientAuthMiddleware.cs` come nome della classe.

![Creare una nuova classe C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

Aprire il file `App_Start\ClientAuthMiddleware.cs` e sostituire il contenuto del file con il codice seguente:


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

### <a name="step-14-add-an-owin-startup-class"></a>Passaggio 1.4 Aggiungere una classe di avvio OWIN
Aggiungere una classe di avvio OWIN all'API denominata `Startup.cs`. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Nuovo elemento** e cercare "OWIN".

![Aggiungere una classe di avvio OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

Aprire il file `Startup.cs` e sostituire il contenuto del file con il codice seguente:

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

### <a name="step-15-protect-identity-api-class"></a>Passaggio 1.5 Proteggere la classe dell'API di identità
Aprire Controllers\IdentityController.cs e aggiungere il tag `[Authorize]` alla classe controller. Il tag `[Authorize]` limita l'accesso al controller agli utenti che soddisfano i requisiti di autorizzazione.

![aggiungere il tag [Authorize] al controller](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Passaggio 2: Pubblicare in Azure
Per pubblicare il progetto, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e scegliere **Pubblica**.

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>Passaggio 3: Aggiungere ID e segreto dell'app dei servizi RESTful ad Azure AD B2C
Dopo aver protetto il servizio RESTful con ID client (nomeutente) e segreto client, è necessario archiviare le credenziali nel tenant di Azure AD B2C. I criteri personalizzati forniscono le credenziali durante la chiamata dei servizi RESTful.  

### <a name="step-31-add-restful-services-client-id"></a>Passaggio 3.1 Aggiungere l'ID client dei servizi RESTful
1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `B2cRestClientId`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere l'ID dell'app definito in precedenza.
7.  In **Uso chiave** usare **Secreto**.
8.  Fare clic su **Crea**
9.  Confermare di avere creato la chiave `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-restful-services-client-secret"></a>Passaggio 3.2 Aggiungere il segreto client dei servizi RESTful
1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Manuale**.
5.  Per **Nome** usare `B2cRestClientSecret`.  
    È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
6.  Nella casella **Segreto** immettere il segreto dell'app definito in precedenza.
7.  In **Uso chiave** usare **Secreto**.
8.  Fare clic su **Crea**
9.  Confermare di avere creato la chiave `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>Passaggio 4: Modificare `TechnicalProfile` per supportare l'autenticazione di base nei criteri di estensione
1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro.
2.  Trovare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`
3.  Individuare l'elemento `<Metadata>`
4.  Modificare `AuthenticationType` in `Basic`
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  Aggiungere il frammento XML seguente subito dopo la chiusura dell'elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
Dopo l'aggiunta dei frammenti XML, `TechnicalProfile` dovrebbe avere questo aspetto:

![Aggiungere gli elementi XML dell'autenticazione di base](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passaggio 5: Caricare i criteri nel tenant

1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e fare clic su **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Fare clic su **Tutti i criteri**.
4.  Selezionare **Carica il criterio**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passaggio 6: Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >
    >    Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. 
    >    Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3.  Provare a immettere "Test" nel campo **Nome**. B2C visualizza un messaggio di errore nella parte superiore della pagina.

    ![Testare l'identità dell'API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Provare a immettere un nome (diverso da "test") nel campo **Nome**. B2C effettua l'iscrizione dell'utente e invia quindi loyaltyNumber all'applicazione. Prendere nota del numero in questo token JWT in questo esempio.

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

## <a name="next-steps"></a>Passaggi successivi
* [Usare i certificati client per proteggere l'API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facoltativo] Scaricare i file dei criteri e il codice completi
* Anziché usare i file di esempio, per creare lo scenario è consigliabile usare i file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* È possibile scaricare il codice completo dalla [soluzione di Visual Studio di esempio per riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)