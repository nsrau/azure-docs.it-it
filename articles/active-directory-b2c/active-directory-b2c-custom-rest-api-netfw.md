---
title: 'Azure Active Directory B2C: integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell''input utente'
description: Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente.
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
ms.openlocfilehash: e9a5b6ffdf2a2c30ae1bcb2bd8f91adb12f35266
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrare scambi di attestazioni API REST nel percorso utente di Azure AD B2C come convalida dell'input utente
Con il framework dell'esperienza di gestione delle identità, che è alla base di Azure Active Directory B2C (Azure AD B2C), è possibile eseguire un'integrazione con un'API RESTful in un percorso utente. In questa procedura dettagliata verrà illustrato come Azure AD B2C interagisce con i servizi RESTful di .NET Framework (API Web).

## <a name="introduction"></a>Introduzione
Usando Azure AD B2C, è possibile aggiungere la logica di business a un percorso utente chiamando il servizio RESTful. Il framework dell'esperienza di gestione delle identità invia dati al servizio RESTful in una raccolta di *attestazioni di input* e riceve i dati da RESTful in una raccolta di *attestazioni di output*. Con l'integrazione del servizio RESTful, è possibile eseguire queste operazioni:

* **Convalidare i dati di input utente**: questa azione evita che i dati in formato non corretto vengano resi permanenti in Azure AD. Se il valore immesso dall'utente non è valido, il servizio RESTful restituisce un messaggio di errore che indica all'utente di specificare una voce. È possibile ad esempio verificare che l'indirizzo di posta elettronica indicato dall'utente sia presente nel database del cliente.
* **Sovrascrivere le attestazioni di input**: se ad esempio un utente immette il nome in lettere tutte minuscole o tutte maiuscole, è possibile formattare il nome con solo la prima lettera maiuscola.
* **Arricchire i dati utente con un'ulteriore integrazione con le applicazioni line-of-business aziendali**: il servizio RESTful può ricevere l'indirizzo di posta elettronica dell'utente, eseguire una query sul database del cliente e restituire il numero di programma fedeltà dell'utente ad Azure AD B2C. Le attestazioni restituite possono essere archiviate nell'account Azure AD dell'utente, valutato nei *passaggi di orchestrazione* successivi o incluso nel token di accesso.
* **Eseguire la logica di business personalizzata**: è possibile inviare notifiche push, aggiornare i database aziendali, eseguire un processo di migrazione utente, gestire le autorizzazioni, controllare i database ed eseguire altre azioni.

È possibile progettare l'integrazione con i servizi RESTful nei modi seguenti:

* **Profilo tecnico di convalida**: la chiamata al servizio RESTful viene eseguita nel profilo tecnico di convalida del profilo tecnico specificato. Il profilo tecnico convalida i dati specificati dall'utente prima che il percorso utente proceda. Il profilo tecnico di convalida consente di eseguire queste operazioni:
   * Inviare attestazioni di input.
   * Convalidare le attestazioni di input e generare messaggi di errore personalizzati.
   * Restituire attestazioni di output.

* **Scambio di attestazioni**: questa progettazione è simile al profilo tecnico di convalida, ma viene eseguita in un passaggio di orchestrazione. Questa definizione è limitata alle operazioni seguenti:
   * Inviare attestazioni di input.
   * Restituire attestazioni di output.

## <a name="restful-walkthrough"></a>Procedura dettagliata su RESTful
Questa procedura dettagliata consente di sviluppare un'API Web di .NET Framework che convalida l'input utente e indica il numero di fedeltà di un utente. L'applicazione può concedere ad esempio l'accesso a *benefit più vantaggiosi* in base al numero di fedeltà.

Panoramica
* Sviluppare il servizio RESTful (API Web di .NET Framework).
* Usare il servizio RESTful nel percorso utente.
* Inviare attestazioni di input e leggerle nel codice.
* Convalidare il nome dell'utente.
* Inviare un numero di programma fedeltà. 
* Aggiungere il numero di fedeltà a un token JSON Web (JWT).

## <a name="prerequisites"></a>Prerequisiti
Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Passaggio 1: Creare un'API Web ASP.NET

1. In Visual Studio creare un progetto selezionando **File** > **Nuovo** > **Progetto**.

2. Nella finestra **Nuovo progetto** selezionare **Visual C#** > **Web** > **Applicazione Web ASP.NET (.NET Framework)**.

3. Nella casella **Nome** digitare un nome per l'applicazione (ad esempio *Contoso.AADB2C.API*) e quindi fare clic su **OK**.

    ![Creare un nuovo progetto di Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Nella finestra **Nuova applicazione Web ASP.NET** selezionare un modello **API Web** o **App per le API di Azure**.

    ![Selezionare il modello API Web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Verificare che l'autenticazione sia impostata su **Nessuna autenticazione**.

6. Selezionare **OK** per creare il progetto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Passaggio 2: Preparare l'endpoint dell'API REST

### <a name="step-21-add-data-models"></a>Passaggio 2.1: Aggiungere modelli di dati
I modelli rappresentano i dati delle attestazioni di input e output presenti nel servizio RESTful. Il codice legge i dati di input per deserializzare il modello di attestazioni di input da una stringa JSON in un oggetto C# (modello dell'utente). L'API Web ASP.NET deserializza automaticamente il modello di attestazioni di output in JSON e quindi scrive i dati serializzati nel corpo del messaggio di risposta HTTP. 

Creare un modello che rappresenta le attestazioni di input seguendo questa procedura:

1. Se Esplora soluzioni non è già aperto, selezionare **Visualizza** > **Esplora soluzioni**. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi fare clic su **Classe**.

    ![Aggiungi modello](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Assegnare alla classe il nome `InputClaimsModel` e quindi aggiungere le proprietà seguenti alla classe `InputClaimsModel`:

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

4. Creare un nuovo modello, `OutputClaimsModel`, e quindi aggiungere le proprietà seguenti alla classe `OutputClaimsModel`:

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Creare un altro modello, `B2CResponseContent`, che verrà usato per generare messaggi di errore di convalida dell'input. Aggiungere le proprietà seguenti alla classe `B2CResponseContent`, specificare i riferimenti mancanti e quindi salvare il file:

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

### <a name="step-22-add-a-controller"></a>Passaggio 2.2: Aggiungere un controller
Nell'API Web un _controller_ è un oggetto che gestisce richieste HTTP. Il controller restituisce le attestazioni di output oppure, se il nome non è valido, genera un messaggio di errore di conflitto HTTP.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi fare clic su **Controller**.

    ![Aggiungere un nuovo controller](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Nella finestra **Aggiungi scaffolding** selezionare **Controller Web API 2 - Vuoto** e quindi selezionare **Aggiungi**.

    ![Selezionare Controller Web API 2 - Vuoto](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Nella finestra **Aggiungi controller** assegnare al controller il nome **IdentityController** e quindi selezionare **Aggiungi**.

    ![Digitare il nome del controller](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Lo scaffolding consente di creare un file denominato *IdentityController.cs* nella cartella *Controllers*.

4. Se il file *IdentityController.cs* non è già aperto, fare doppio clic su di esso e sostituire il codice nel file con il codice seguente:

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
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Passaggio 3: Pubblicare il progetto in Azure
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e quindi scegliere **Pubblica**.

    ![Pubblicare in Servizio app di Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Nella finestra **Pubblica** selezionare **Servizio app di Microsoft Azure** e quindi fare clic su **Pubblica**.

    ![Creare un nuovo Servizio app di Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Verrà visualizzata la finestra **Crea servizio app**, in cui si creano tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET in Azure.

    > [!NOTE]
    >Per altre informazioni sulle modalità di pubblicazione, vedere [Creare un'app Web ASP.NET in Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Nella casella **Nome app Web** digitare un nome univoco per l'app. I caratteri validi sono a-z, 0-9 e i trattini (-). L'URL dell'app Web è http://<nome_app>.azurewebsites.NET, dove *nome_app* è il nome dell'app Web. È possibile accettare il nome generato automaticamente, che è univoco.

    ![Specificare le proprietà del servizio app](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Per avviare la creazione delle risorse di Azure, selezionare **Crea**.  
    Al termine della creazione dell'app Web ASP.NET, la procedura guidata la pubblica in Azure e quindi avvia l'app nel browser predefinito.

6. Copiare l'URL dell'app Web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passaggio 4: Aggiungere la nuova attestazione `loyaltyNumber` allo schema del file TrustFrameworkExtensions.xml
L'attestazione `loyaltyNumber` non è ancora definita nello schema. Aggiungere una definizione nell'elemento `<BuildingBlocks>`, che è possibile trovare all'inizio del file *TrustFrameworkExtensions.xml*.

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

## <a name="step-5-add-a-claims-provider"></a>Passaggio 5: Aggiungere un provider di attestazioni 
Ogni provider di attestazioni deve disporre di uno o più profili tecnici, che determinano gli endpoint e i protocolli necessari per comunicare con il provider stesso. 

Un provider di attestazioni può avere più profili tecnici per molti motivi, ad esempio perché il provider di attestazioni supporta più protocolli, gli endpoint possono avere funzionalità diverse o le versioni possono contenere attestazioni con livelli di garanzia distinti. Potrebbe essere accettabile rilasciare attestazioni sensibili in un percorso utente, ma non in un altro. 

Il frammento di codice XML seguente contiene un nodo di un provider di attestazioni con due profili tecnici:

* **TechnicalProfile Id="REST-API-SignUp"**: definisce il servizio RESTful. 
   * `Proprietary` viene descritto come protocollo per un provider basato su RESTful. 
   * `InputClaims` definisce le attestazioni che verranno inviate da Azure AD B2C al servizio REST. 

   In questo esempio il contenuto dell'attestazione `givenName` viene inviato al servizio REST come `firstName`, il contenuto dell'attestazione `surname` viene inviato al servizio REST come `lastName` e `email` viene inviato così com'è. L'elemento `OutputClaims` definisce le attestazioni che vengono recuperate dal servizio RESTful e restituite ad Azure AD B2C.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**: aggiunge un profilo tecnico di convalida a un profilo tecnico esistente (definito nei criteri di base). Durante la fase di iscrizione il profilo tecnico di convalida richiama il profilo tecnico precedente. Se il servizio RESTful restituisce un errore HTTP 409 (un errore di conflitto), il messaggio di errore viene visualizzato dall'utente. 

Individuare il nodo `<ClaimsProviders>` e quindi aggiungere il frammento XML seguente nel nodo `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
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
Modificare il file relying party *SignUpOrSignIn.xml* e modificare l'elemento TechnicalProfile Id="PolicyProfile" per aggiungere `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Dopo aver aggiunto la nuova attestazione, il codice della relying party è simile al seguente:

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

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi aprire **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Aprire **Tutti i criteri**. 

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare il file TrustFrameworkExtensions.xml e assicurarsi che superi la convalida.

7. Ripetere il passaggio precedente con il file SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Passaggio 8: Testare i criteri personalizzati tramite Esegui adesso
1. Selezionare **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

    > [!NOTE]
    > Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza, quindi selezionare **Esegui adesso**.

    ![Finestra B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testare il processo digitando **Test** nella casella **Nome**.  
    Azure AD B2C visualizza un messaggio di errore nella parte superiore della finestra.

    ![Verificare i criteri](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Digitare un nome (diverso da "Test") nella casella **Nome**.  
    Azure AD B2C effettua l'iscrizione dell'utente e invia quindi un numero di identificazione personale (loyaltyNumber) all'applicazione. Prendere nota del numero in questo token JWT.

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facoltativo) Scaricare il codice e i file dei criteri completi
* Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* È possibile scaricare il codice completo dalla [soluzione di Visual Studio di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Passaggi successivi
* [Secure your RESTful API with basic authentication (username and password)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Proteggere l'API RESTful con l'atenticazione di base - nome utente e password)
* [Proteggere l'API RESTful con certificati client](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
