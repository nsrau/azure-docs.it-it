---
title: 'Azure Active Directory B2C: proteggere i servizi RESTful con certificati client'
description: Informazioni su come proteggere gli scambi di attestazioni dell'API REST personalizzati in Azure AD B2C tramite certificati client
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
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C: proteggere i servizi RESTful con certificati client
Dopo aver [creato un servizio RESTful](active-directory-b2c-custom-rest-api-netfw.md) per interagire con Azure AD B2C, verranno descritte le procedure per limitare l'accesso all'app Web di Azure (API RESTful) tramite un certificato client. Questo meccanismo è denominato autenticazione reciproca TLS o **autenticazione del certificato client**.  Solo i servizi, ad esempio Azure AD B2C, con il certificato appropriato saranno in grado di accedere al servizio.

> [!NOTE]
>
>È anche possibile proteggere il servizio RESTful [tramite l'autenticazione di base HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). È comunque necessario tenere presente che l'autenticazione di base HTTP è considerata meno sicura rispetto al certificato client. Si consiglia di proteggere il servizio RESTful tramite l'autenticazione del certificato client, come descritto in questo articolo.

Questo articolo descrive in dettaglio le procedure per:
1. Configurare un'app Web per l'uso dell'autenticazione del certificato client
1. Caricare il certificato nelle chiavi dei criteri di Azure AD B2C
1. Configurare i criteri personalizzati per l'uso del certificato client

## <a name="prerequisites"></a>Prerequisiti
* Eseguire i passaggi precedenti nell'articolo [Integrare scambi di attestazioni API REST](active-directory-b2c-custom-rest-api-netfw.md)
* È necessario un certificato valido (file con estensione pfx con chiave privata)

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>Passaggio 1: Configurare l'app Web per l'autenticazione del certificato client
Per configurare il **Servizio app di Azure** per richiedere i certificati client, l'impostazione del sito `clientCertEnabled` dell'app Web deve essere true. Questa impostazione non è attualmente disponibile tramite l'esperienza di gestione nel portale di Azure ed è necessario usare l'API REST a questo scopo.

> [!NOTE]
>
>Assicurarsi che il piano del Servizio app di Azure sia Standard o superiore. Per altre informazioni, vedere [Panoramica approfondita dei piani per Servizio app di Azure](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)


Usare [Azure Resource Explorer (anteprima)](https://resources.azure.com) per impostare la proprietà clientCertEnabled su true. Lo screenshot seguente illustra come impostare il valore clientCertEnabled tramite Azure Resource Explorer ![Impostazione di clientCertEnabled tramite Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>Per altre informazioni su come impostare l'attributo clientCertEnabled, vedere: [Come configurare l'autenticazione reciproca TLS per un'app Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)


> [!TIP]
>
>In alternativa, per semplificare la definizione della chiamata all'API REST, è possibile usare lo strumento [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Passaggio 2: Caricare il certificato nelle chiavi dei criteri di Azure AD B2C
Dopo aver impostato `clientCertEnabled` su `true`, per la comunicazione con l'API RESTful è ora richiesto un certificato client. A tale scopo, è necessario caricare e archiviare il certificato client nel tenant di Azure AD B2C.   
1.  Passare al tenant di Azure AD B2C e selezionare **B2C Settings** (Impostazioni B2C) > **Framework dell'esperienza di gestione delle identità**
2.  Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.
3.  Fare clic su **+Aggiungi**.
4.  Per **Opzioni** usare **Carica**.
5.  Per **Nome** usare `B2cRestClientCertificate`.  
    Il prefisso `B2C_1A_` viene aggiunto automaticamente.
6.  In **Caricamento file** selezionare il file del certificato con estensione pfx con la chiave privata e specificare la **password** del certificato.

    ![Caricare la chiave dei criteri](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  Fare clic su **Crea**
8.  Per verificare di aver creato la chiave `B2C_1A_B2cRestClientCertificate`, selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>Passaggio 3: Modificare `TechnicalProfile` per supportare l'autenticazione del certificato client nei criteri di estensione
1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro.
2.  Trovare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`
3.  Individuare l'elemento `<Metadata>`
4.  Modificare `AuthenticationType` in `ClientCertificate`

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  Aggiungere il frammento XML seguente subito dopo la chiusura dell'elemento `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

Dopo l'aggiunta dei frammenti XML, `TechnicalProfile` dovrebbe avere questo aspetto:

![Impostare gli elementi XML di autenticazione ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Passaggio 4: Caricare i criteri nel tenant

1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant di Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e fare clic su **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Fare clic su **Tutti i criteri**.
4.  Selezionare **Carica il criterio**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Passaggio 5: Testare i criteri personalizzati tramite Esegui adesso
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

   > [!NOTE]
   >Se viene visualizzato il messaggio di errore di cui sopra, significa che Azure AD B2C ha chiamato correttamente il servizio RESTful durante la presentazione del certificato client. Il passaggio successivo prevede la convalida del certificato.

## <a name="step-6-adding-certificate-validation"></a>Passaggio 6: Aggiunta della convalida del certificato
Il certificato client inviato al servizio RESTful da Azure AD B2C non viene convalidato in alcun modo dalla piattaforma delle app Web di Azure, se non con il controllo dell'esistenza del certificato. La convalida del certificato è responsabilità dell'app Web. Ecco un esempio di codice ASP.NET che convalida le proprietà del certificato a scopo di autenticazione.

> [!NOTE]
>Per altre informazioni su come configurare l'app del servizio Azure per l'autenticazione del certificato client, vedere: [Come configurare l'autenticazione reciproca TLS per un'app Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 Aggiungere le impostazioni dell'applicazione al file web.config del progetto
Aprire il progetto di Visual Studio creato in precedenza e aggiungere le impostazioni dell'applicazione seguenti nel file web.config nell'elemento `appSettings`.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Sostituire i valori seguenti per il certificato:
* Nome del soggetto
* Nome dell'autorità emittente
* Identificazione personale del certificato

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 Aggiungere la funzione IsValidClientCertificate
Aprire Controllers\IdentityController.cs e aggiungere la funzione seguente alla classe controller `Identity`. 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Nell'**esempio** precedente il certificato viene accettato come valido solo se vengono soddisfatte tutte le condizioni:
1. Il certificato non è scaduto ed è attivo per l'ora corrente nel server.
2. Il valore `Subject` del certificato ha un nome comune uguale al valore dell'impostazione dell'applicazione `ClientCertificate:Subject`
3. Il valore `Issuer` del certificato ha un nome comune uguale al valore dell'impostazione dell'applicazione `ClientCertificate:Issuer`
4. Il valore `thumbprint` del certificato è uguale al valore dell'impostazione dell'applicazione `ClientCertificate:Thumbprint`

> [!IMPORTANT]
>
>A seconda del livello di riservatezza del servizio, potrebbe essere necessario aggiungere altre convalide. Ad esempio, verificare se la catena di certificati conduce a un'autorità radice attendibile, convalidare il nome dell'organizzazione dell'autorità emittente e così via.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 Aggiungere la funzione IsValidClientCertificate
Aprire Controllers\IdentityController.cs all'inizio della funzione `SignUp()` per aggiungere le righe di codice seguenti. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Dopo l'aggiunta dei frammenti di codice, il controller `Identity` dovrebbe avere questo aspetto:

![Aggiungere il codice di convalida del certificato](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>Passaggio 7: Pubblicare in Azure e testare
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e scegliere **Pubblica**.
2. Ripetere il passaggio 6 e testare di nuovo i criteri personalizzati con la convalida del certificato. Provare a eseguire i criteri e verificare che tutto funzioni correttamente dopo aver aggiunto la convalida.
3. A questo punto, nel file web.config modificare il valore di `ClientCertificate:Subject` impostando **invalid**. Eseguire di nuovo i criteri. Dovrebbe essere visualizzato un messaggio di errore.
4. Riportare il valore su **valid** e assicurarsi che i criteri possano chiamare l'API REST.
5. Risoluzione dei problemi tramite la [raccolta di log con Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facoltativo] Scaricare i file dei criteri e il codice completi
* Anziché usare i file di esempio, per creare lo scenario è consigliabile usare i file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* È possibile scaricare il codice completo dalla [soluzione di Visual Studio di esempio per riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)