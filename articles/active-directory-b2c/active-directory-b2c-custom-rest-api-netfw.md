---
title: 'Azure Active Directory B2C: integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell''input utente'
description: Esempio di come integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C: integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente
Il framework dell'esperienza di gestione delle identità alla base di Azure Active Directory B2C (Azure AD B2C) consente di eseguire un'integrazione con un'API RESTful in un percorso utente. Questa procedura dettagliata illustra come B2C interagisce con i servizi RESTful di .NET Framework (API Web).

## <a name="introduction"></a>Introduzione
Azure Active Directory B2C consente di aggiungere la logica di business in un percorso utente chiamando il servizio RESTful. Il framework dell'esperienza di gestione delle identità invia dati al servizio RESTful nella raccolta di **attestazioni di input** e riceve i dati da RESTful nella raccolta di **attestazioni di output**. Con l'integrazione del servizio RESTful, è possibile eseguire queste operazioni:

* Convalidare i dati di input utente. Questa azione consente di evitare che i dati in formato non corretto vengano resi permanenti in Azure AD. Se il valore immesso dall'utente non è valido, il servizio RESTful restituisce il messaggio di errore che indica all'utente di specificare una voce. È possibile ad esempio verificare che l'indirizzo di posta elettronica indicato dall'utente sia presente nel database di clienti.
* Sovrascrivere attestazioni di input. Se ad esempio l'utente immette il nome in lettere minuscole o maiuscole, è possibile formattare il nome e modificare in maiuscola solo la prima lettera.
* Arricchire i dati utente eseguendo un'integrazione più approfondita con applicazioni line-of-business. Il servizio RESTful può ricevere l'indirizzo di posta elettronica utente, eseguire query sul database dei clienti e restituire il numero fedeltà dell'utente a B2C. Le attestazioni restituite possono essere archiviate nell'account AAD dell'utente, valutato nei **passaggi di orchestrazione** successivi o incluso nel token di accesso.
* Eseguire logica di business personalizzata. È possibile inviare notifiche push, aggiornare i database dell'azienda ed eseguire processi di migrazione utente, gestione delle autorizzazioni, controllo e altro ancora.

L'integrazione con i servizi RESTful può essere progettata come uno scambio di attestazioni o come un profilo tecnico di convalida.

* **Profilo tecnico di convalida** - La chiamata al servizio RESTful viene eseguita in un elemento ValidationTechnicalProfile di un determinato elemento TechnicalProfile. Il profilo tecnico convalida i dati specificati dall'utente prima che il percorso utente proceda. Il profilo tecnico di convalida consente di eseguire queste operazioni:
  * Inviare attestazioni di input
  * Convalidare le attestazioni di input e generare messaggi di errore personalizzati
  * Restituire attestazioni di output

* **Scambio di attestazioni** - Analogo al metodo precedente, ma eseguito in un passaggio di orchestrazione. Questa definizione è limitata alle operazioni seguenti:
   * Inviare attestazioni di input
   * Restituire attestazioni di output

## <a name="restful-walkthrough"></a>Procedura dettagliata su RESTful
Questa procedura dettagliata consente di sviluppare un'API Web di .NET Framework che convalida l'input utente e indica il numero di fedeltà dell'utente. L'applicazione può concedere ad esempio l'accesso a benefit più vantaggiosi in base al numero di fedeltà.

Panoramica
*   Sviluppo del servizio RESTful (API Web di .NET Framework)
*   Uso del servizio RESTful nel percorso utente
*   Invio di attestazioni di input e lettura delle attestazioni nel codice
*   Convalida del nome dell'utente
*   Restituzione del numero di fedeltà 
*   Aggiunta del numero di fedeltà al token Web JSON (JWT)

## <a name="prerequisites"></a>Prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Passaggio 1: Creare un'API Web ASP.NET
1.  In Visual Studio creare un progetto selezionando **File > Nuovo > Progetto**.
2.  Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web > Applicazione Web ASP.NET (.NET Framework)**.
3.  Assegnare un nome all'applicazione, ad esempio Contoso.AADB2C.API, e quindi selezionare **OK**.

    ![Creare un nuovo progetto di Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  Selezionare il modello **API Web** o **App per le API di Azure**
5.  Verificare che l'autenticazione sia impostata su **Nessuna autenticazione**

    ![Selezionare il modello API Web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  Fare clic su **OK** per creare il progetto

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Passaggio 2: Preparare l'endpoint dell'API REST

### <a name="step-21-add-data-models"></a>Passaggio 2.1 Aggiungere modelli di dati
I modelli rappresentano i dati delle attestazioni di input e output presenti nel servizio RESTful. Il codice legge i dati di input per deserializzare il modello di attestazioni di input dalla stringa JSON nell'oggetto C# (modello dell'utente). L'API Web ASP.NET deserializza automaticamente il modello di attestazioni di output in JSON e quindi scrivere i dati serializzati nel corpo del messaggio di risposta HTTP. Iniziamo creando un modello che rappresenta le attestazioni di input.

1.  Se Esplora soluzioni non è ancora visibile, fare clic sul menu **Visualizza** e quindi selezionare **Esplora soluzioni**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella Modelli. Nel menu di scelta rapida selezionare **Aggiungi** e quindi selezionare **Classe**.

    ![Aggiungi modello](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  Assegnare un nome alla classe `InputClaimsModel` e quindi aggiungere le proprietà seguenti alla classe `InputClaimsModel`

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  Creare un nuovo modello `OutputClaimsModel` e aggiungere le proprietà seguenti alla classe `OutputClaimsModel`

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  Creare un altro modello `B2CResponseContent`. Questo modello viene usato per generare messaggi di errore di convalida dell'input. Aggiungere le proprietà seguenti alla classe `B2CResponseContent`, specificare i riferimenti mancanti e quindi salvare il file.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Passaggio 2.2 Aggiungere un controller
Nell'API Web un _controller_ è un oggetto che gestisce richieste HTTP. Viene aggiunto un controller che restituisce le attestazioni di output oppure, se il nome non è valido, genera un messaggio di errore di conflitto HTTP.

1.  In **Esplora soluzioni** fare clic sulla cartella Controller. Selezionare **Aggiungi** e quindi selezionare **Controller**.

    ![Aggiungere un nuovo controller](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller Web API 2 - Vuoto**. Fare clic su **Aggiungi**.

    !![Selezionare Controller Web API 2 - Vuoto](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  Nella finestra di dialogo **Aggiungi controller** assegnare un nome al controller `IdentityController` e quindi fare clic su **Add**.

    ![Digitare il nome del controller](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Lo scaffolding consente di creare un file denominato IdentityController.cs nella cartella Controller.

4.  Se non è già aperto, fare doppio clic sul file per aprirlo. Sostituire il codice in questo file con le righe di codice seguenti:

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>Passaggio 3: Pubblicare in Azure
1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e quindi scegliere **Pubblica**.

    ![Pubblicare in Servizio app di Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  Verificare che **Servizio app di Microsoft Azure** sia selezionato e scegliere **Pubblica**.

    ![Creare un nuovo Servizio app di Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Viene visualizzata la finestra di dialogo **Crea servizio app**, che consente di creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

> [!NOTE]
>Per altre informazioni sulle modalità di pubblicazione, vedere [Creare un'app Web ASP.NET in Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  In **Nome app Web** digitare un nome univoco per l'app (i caratteri validi sono `a-z`, `0-9` e `-`). L'URL dell'app Web è `http://<app_name>.azurewebsites.NET`, dove `<app_name>` è il nome dell'app Web. È possibile accettare il nome generato automaticamente, che è univoco.

    Selezionare **Crea** per avviare la creazione delle risorse di Azure.

    ![Specificare proprietà del servizio app](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  Al termine della procedura guidata, l'app Web ASP.NET viene pubblicata in Azure e avviata nel browser predefinito. A questo punto copiare l'URL.

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passaggio 4: Aggiungere la nuova attestazione `loyaltyNumber` allo schema del file TrustFrameworkExtensions.xml
L'attestazione `loyaltyNumber` non è ancora definita nello schema. In tal caso, aggiungere una definizione all'interno dell'elemento `<BuildingBlocks>`. È possibile trovare questo elemento all'inizio del file TrustFrameworkExtensions.xml.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>Passaggio 5: Aggiungere un provider di attestazioni 
Ogni provider di attestazioni deve disporre di uno o più profili tecnici che determinano gli endpoint e i protocolli necessari per comunicare con il provider stesso. Un provider di attestazioni può avere più profili tecnici per molti motivi, ad esempio perché il provider di attestazioni considerato supporta più protocolli o vari endpoint con funzionalità diverse o rilascia attestazioni diverse a livelli di garanzia distinti. Può essere accettabile rilasciare attestazioni sensibili in un percorso utente, ma non in un altro. 

Il frammento di codice XML seguente contiene il nodo `ClaimsProvider` con due `TechnicalProfile`:
* `<TechnicalProfile Id="REST-API-SignUp">` definisce il servizio RESTful. L'elemento `Proprietary` viene descritto come protocollo per un protocollo basato su RESTful. L'elemento `InputClaims` definisce le attestazioni che verranno inviate da B2C al servizio REST. In questo esempio il contenuto dell'attestazione `givenName` viene inviato al servizio REST come `firstName`, il contenuto dell'attestazione `surename` viene inviato al servizio REST come `lastName`, `email` viene inviato così com'è. L'elemento `OutputClaims` definisce le attestazioni che recuperano dati dal servizio RESTful e li restituisce a B2C.

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` aggiunge profilo tecnico di convalida al profilo di tecnico esistente (definito nei criteri di base). Durante la fase di iscrizione il profilo tecnico di convalida richiama il profilo tecnico precedente. Se un servizio RESTful restituisce errori HTTP 409 (errori di conflitto), il messaggio di errore viene visualizzato all'utente. 

Individuare il nodo `<ClaimsProviders>` e aggiungere il frammento di codice XML seguente nel nodo `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passaggio 6: Aggiungere l'attestazione `loyaltyNumber` al file dei criteri relying party in modo che l'attestazione venga inviata all'applicazione
Modificare il file relying party SignUpOrSignIn.xml e modificare l'elemento `<TechnicalProfile Id="PolicyProfile">` per aggiungere l'elemento seguente: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Dopo aver aggiunto la nuova attestazione, l'elemento `RelyingParty` è simile al seguente:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Passaggio 7: Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e aprire **Azure AD B2C**
2.  Selezionare **Framework dell'esperienza di gestione delle identità**
3.  Aprire **Tutti i criteri** 
4.  Selezionare **Carica il criterio**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida
7.  Ripetere l'ultimo passaggio e caricare il file SignUpOrSignIn.xml

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Passaggio 8: Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

> [!NOTE]
>
>Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).


2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  Provare a immettere "Test" nel campo **Nome**. B2C visualizza il messaggio di errore nella parte superiore della pagina

    ![Verificare i criteri](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Provare a immettere un nome (diverso da "Test") nel campo **Nome**. B2C effettua l'iscrizione dell'utente e quindi invia l'elemento loyaltyNumber all'applicazione. Prendere nota del numero in questo token JWT.

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facoltativo] Scaricare i file e il codice dei criteri completi.
* Anziché usare i file di esempio, per creare lo scenario è consigliabile usare i file di criteri personalizzati dopo aver completato la procedura di introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* È possibile scaricare il codice completo in [Sample visual studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/) (Soluzione di Visual Studio di esempio per riferimento)
    
## <a name="next-steps"></a>Passaggi successivi
1.  [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Proteggere l'API RESTful con l'atenticazione di base - nome utente e password)
2.  [Secure your RESTful API with client certificate](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Proteggere l'API RESTful con il certificato client)