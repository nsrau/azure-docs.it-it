---
title: 'Azure Active Directory B2C: proteggere il servizio RESTful usando certificati client'
description: Proteggere gli scambi di attestazioni dell'API REST personalizzate in Azure AD B2C usando certificati client
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
ms.openlocfilehash: 582aadd35821779e307ac285804e3b7fe5c24abd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteggere il servizio RESTful usando certificati client
In un articolo correlato si [crea un servizio RESTful](active-directory-b2c-custom-rest-api-netfw.md) che interagisce con Azure Active Directory B2C (Azure AD B2C).

In questo articolo viene illustrato come limitare l'accesso all'app Web di Azure (API RESTful) usando un certificato client. Questo meccanismo è denominato autenticazione reciproca TLS o *autenticazione del certificato client*. Solo i servizi con i certificati appropriati, ad esempio Azure AD B2C, possono accedere al servizio.

>[!NOTE]
>È anche possibile proteggere il servizio RESTful usando [l'autenticazione di base HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). L'autenticazione di base HTTP è tuttavia considerata meno sicura rispetto a un certificato client. Si consiglia di proteggere il servizio RESTful usando l'autenticazione del certificato client, come descritto in questo articolo.

Questo articolo descrive in dettaglio le procedure per:
* Configurare un'app Web per l'uso dell'autenticazione del certificato client.
* Caricare il certificato nelle chiavi dei criteri di Azure AD B2C.
* Configurare i criteri personalizzati per l'uso del certificato client.

## <a name="prerequisites"></a>prerequisiti
* Eseguire i passaggi dell'articolo [Integrare scambi di attestazioni API REST](active-directory-b2c-custom-rest-api-netfw.md).
* Ottenere un certificato valido (un file con estensione pfx con una chiave privata).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Passaggio 1: Configurare un'app Web per l'autenticazione del certificato client
Per configurare il **servizio app di Azure** per richiedere i certificati client, l'impostazione del sito `clientCertEnabled` dell'app Web deve essere *true*. Per apportare questa modifica, è necessario usare l'API REST. L'impostazione è disponibile tramite l'esperienza di gestione nel portale di Azure. Per individuare l'impostazione, dal menu **Impostazioni** dell'applicazione Restful, in **Strumenti di sviluppo**, scegliere **Esplora risorse**.

>[!NOTE]
>Assicurarsi che il piano di servizio app di Azure sia Standard o superiore. Per altre informazioni, vedere [Panoramica approfondita dei piani di servizio app di Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Usare [Azure Resource Explorer (anteprima)](https://resources.azure.com) per impostare la proprietà **clientCertEnabled** su *true*, come illustrato nell'immagine seguente:

![Impostazione di clientCertEnabled tramite Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Per altre informazioni sull'impostazione della proprietà **clientCertEnabled**, vedere [Configurare l'autenticazione reciproca TLS per le app Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>In alternativa, per semplificare la definizione della chiamata API REST, è possibile usare lo strumento [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Passaggio 2: Caricare il certificato nelle chiavi dei criteri di Azure AD B2C
Dopo aver impostato `clientCertEnabled` su *true*, per la comunicazione con l'API RESTful è richiesto un certificato client. Per ottenere, caricare e archiviare il certificato client nel tenant di Azure AD B2C, seguire questa procedura: 
1. Nel tenant di Azure AD B2C selezionare **Impostazioni di Azure AD B2C** > **Framework dell'esperienza di gestione delle identità**.

2. Selezionare **Chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant.

3. Selezionare **Aggiungi**.  
    Si apre la finestra **Creare una chiave**.

4. Nella casella **Opzioni** selezionare **Carica**.

5. Nella casella **Nome** digitare **B2cRestClientCertificate**.  
    Il prefisso *B2C_1A_* viene aggiunto automaticamente.

6. Nella casella **Caricamento file** selezionare il file del certificato con estensione pfx con una chiave privata.

7. Nella casella **Password** digitare la password del certificato.

    ![Caricare la chiave dei criteri](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selezionare **Create**.

8. Per visualizzare le chiavi disponibili nel tenant e verificare di avere creato la chiave `B2C_1A_B2cRestClientCertificate`, selezionare **Chiavi dei criteri**.

## <a name="step-3-change-the-technical-profile"></a>Passaggio 3: Modificare il profilo tecnico
Per supportare l'autenticazione del certificato client nel criterio personalizzato, modificare il profilo tecnico seguendo questa procedura:

1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro.

2. Cercare il nodo `<TechnicalProfile>` che include `Id="REST-API-SignUp"`.

3. Individuare l'elemento `<Metadata>`.

4. Modificare *AuthenticationType* impostando *ClientCertificate*, come segue:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Aggiungere il frammento XML seguente subito dopo l'elemento `<Metadata>` di chiusura: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Dopo aver aggiunto il frammento di codice, il profilo tecnico dovrebbe essere simile al codice XML seguente:

    ![Impostare gli elementi XML di autenticazione ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Passaggio 4: Caricare i criteri nel tenant

1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi selezionare **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Tutti i criteri**.

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Passaggio 5: Testare i criteri personalizzati tramite Esegui adesso
1. Aprire **Impostazioni di Azure AD B2C** e quindi selezionare **Framework dell'esperienza di gestione delle identità**.

    >[!NOTE]
    >Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza e quindi selezionare **Esegui adesso**.

3. Testare il processo digitando **Test** nella casella **Nome**.  
    Azure AD B2C visualizza un messaggio di errore nella parte superiore della finestra.    

    ![Testare l'identità dell'API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Digitare un nome (diverso da "Test") nella casella **Nome**.  
    Azure AD B2C effettua l'iscrizione dell'utente e invia quindi un numero di identificazione personale (loyaltyNumber) all'applicazione. Prendere nota del numero in questo esempio di token JWT:

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

   >[!NOTE]
   >Se viene visualizzato il messaggio di errore *Nome non valido. Specificare un nome valido*, significa che Azure AD B2C ha chiamato il servizio RESTful durante la presentazione del certificato client. Il passaggio successivo prevede la convalida del certificato.

## <a name="step-6-add-certificate-validation"></a>Passaggio 6: Aggiungere la convalida del certificato
Il certificato client inviato al servizio RESTful da Azure AD B2C non viene convalidato in alcun modo dalla piattaforma App Web di Azure, se non per controllare se il certificato esiste. La convalida del certificato è responsabilità dell'app Web. 

In questa sezione si aggiunge un esempio di codice ASP.NET che convalida le proprietà del certificato a scopo di autenticazione.

> [!NOTE]
>Per altre informazioni sulla configurazione del servizio app di Azure per l'autenticazione del certificato client, vedere [Configurare l'autenticazione reciproca TLS per le app Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Aggiungere le impostazioni dell'applicazione al file web.config del progetto
Nel progetto di Visual Studio creato in precedenza aggiungere le impostazioni dell'applicazione seguenti al file *web.config* dopo l'elemento `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Sostituire i valori **Nome soggetto**, **Nome autorità emittente** e **Identificazione personale del certificato** con i propri valori.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Aggiungere la funzione IsValidClientCertificate
Aprire il file *Controllers\IdentityController.cs* e quindi aggiungere la classe controller `Identity` alla funzione seguente: 

```csharp
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

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
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
    
    // 3. Check the issuer name of the certificate
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

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
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

Nel codice di esempio precedente il certificato viene accettato come valido solo se vengono soddisfatte tutte le condizioni seguenti:
* Il certificato non è scaduto ed è attivo per l'ora corrente nel server.
* Il nome `Subject` del certificato è uguale al valore dell'impostazione applicazione `ClientCertificate:Subject`.
* Il nome `Issuer` del certificato è uguale al valore dell'impostazione applicazione `ClientCertificate:Issuer`.
* Il valore `thumbprint` del certificato è uguale al valore dell'impostazione applicazione `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>A seconda del livello di riservatezza del servizio, potrebbe essere necessario aggiungere altre convalide. Ad esempio, potrebbe essere necessario testare se il certificato è concatenato a un'autorità radice attendibile, a una convalida del nome dell'organizzazione dell'autorità emittente e così via.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Chiamare la funzione IsValidClientCertificate
Aprire il file *Controllers\IdentityController.cs* e quindi aggiungere il frammento di codice seguente all'inizio della funzione `SignUp()`: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Dopo aver aggiunto il frammento di codice, il controller `Identity` dovrebbe essere simile al codice seguente:

![Aggiungere il codice di convalida del certificato](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Passaggio 7: Pubblicare il progetto in Azure e testarlo
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contoso.AADB2C.API** e quindi scegliere **Pubblica**.

2. Ripetere il passaggio 6 e testare di nuovo il criterio personalizzato con la convalida del certificato. Provare a eseguire il criterio e verificare che tutto funzioni correttamente dopo aver aggiunto la convalida.

3. Nel file *web.config* modificare il valore di `ClientCertificate:Subject` impostandolo su **invalid**. Eseguire di nuovo il criterio. Dovrebbe essere visualizzato un messaggio di errore.

4. Reimpostare il valore su **valid** e verificare che il criterio possa chiamare l'API REST.

Se è necessario risolvere i problemi relativi a questo passaggio, vedere [Raccolta di log tramite Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facoltativo) Scaricare il codice e i file dei criteri completi
* Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* È possibile scaricare il codice completo da [Sample Visual Studio solution for reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API) (Soluzione di Visual Studio di esempio per riferimento). 
