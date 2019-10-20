---
title: App daemon che chiama le API Web (configurazione app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app daemon che chiama le API Web (configurazione dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9b1a54725dff8fd7b8a6c5023bc7eb0ba84fc8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596769"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App daemon che chiama API Web-configurazione del codice

Informazioni su come configurare il codice per l'applicazione daemon che chiama le API Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Librerie MSAL che supportano le app daemon

Le librerie Microsoft che supportano le app daemon sono:

  Libreria MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme supportate per compilare un'applicazione daemon sono .NET Framework e le piattaforme .NET Core (non UWP, Novell. iOS e Novell. Android perché queste piattaforme vengono usate per compilare applicazioni client pubbliche)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Python | Sviluppo in corso-anteprima pubblica
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Java | Sviluppo in corso-anteprima pubblica

## <a name="configuration-of-the-authority"></a>Configurazione dell'autorità

Dato che le applicazioni daemon non usano autorizzazioni delegate, ma le autorizzazioni dell'applicazione, il *tipo di account supportato* non può essere *account in alcuna directory organizzativa e account Microsoft personali (ad esempio, Skype, Xbox, Outlook.com)* . In realtà, non esiste alcun amministratore tenant per concedere il consenso all'applicazione daemon per gli account personali Microsoft. È necessario scegliere gli *account nell'organizzazione* o gli *account di qualsiasi organizzazione*.

Pertanto, l'autorità specificata nella configurazione dell'applicazione deve essere tenant-ed (specificando un ID tenant o un nome di dominio associato all'organizzazione).

Se si è un ISV e si vuole fornire uno strumento multi-tenant, è possibile usare `organizations`. Tenere tuttavia presente che è anche necessario spiegare ai clienti come concedere il consenso dell'amministratore. Per informazioni dettagliate, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Esiste attualmente una limitazione in MSAL: `organizations` è consentito solo quando le credenziali client sono un segreto dell'applicazione (non un certificato).

## <a name="application-configuration-and-instantiation"></a>Configurazione e creazione di istanze dell'applicazione

Nelle librerie MSAL le credenziali client (Secret o certificate) vengono passate come parametro della costruzione di applicazioni client riservate.

> [!IMPORTANT]
> Anche se l'applicazione è un'applicazione console in esecuzione come servizio, se si tratta di un'applicazione daemon è necessario che sia un'applicazione client riservata.

### <a name="configuration-file"></a>File di configurazione

Il file di configurazione definisce:

- l'autorità o l'istanza del cloud e tenantId
- ClientID ottenuti dalla registrazione dell'applicazione
- un segreto client o un certificato
 
# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) dall'esempio [daemon console di .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Specificare clientSecret o certificate. Entrambe le impostazioni sono esclusive.

# <a name="pythontabpython"></a>[Python](#tab/python)

Quando si compila un client riservato con i segreti client, il file di configurazione sarà simile a questo estratto dall'esempio [confidential_client_secret_sample. py # L4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/0.6.1/sample/confidential_client_secret_sample.py#L4-L15) in GitHub.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "secret": "The secret generated by AAD during your confidential app registration"
}
```

Quando si compila un client riservato con certificati, sarà simile a questo estratto dall'esempio [confidential_client_certificate_sample. py # L4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/4b34fd660e5574eb876cfba63a1b927ae375efd6/sample/confidential_client_certificate_sample.py#L4-L15) in GitHub.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
    "private_key_file": "filename.pem"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Di seguito è illustrata la classe usata negli esempi di msal4j dev per configurare gli esempi: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

È possibile specificare un CONFIDENTIAL_CLIENT_ID o CONFIDENTIAL_CLIENT_SECRET. Entrambe le impostazioni sono esclusive.

---

### <a name="instantiation-of-the-msal-application"></a>Creazione di un'istanza dell'applicazione MSAL

Per creare un'istanza dell'applicazione MSAL, è necessario:

- aggiungere, fare riferimento o importare il pacchetto MSAL (a seconda del linguaggio)
- La costruzione è diversa a seconda che si utilizzino i segreti o i certificati client (o, come scenario avanzato, le asserzioni firmate)

L'applicazione daemon verrà presentata da un `IConfidentialClientApplication`

#### <a name="reference-the-package"></a>Fare riferimento al pacchetto

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Aggiungere il pacchetto NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) all'applicazione.
In MSAL.NET, l'applicazione client riservata è rappresentata dall'interfaccia `IConfidentialClientApplication`.
Usa spazio dei nomi MSAL.NET nel codice sorgente

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Creare un'istanza dell'applicazione client riservata con i segreti client

Ecco il codice per creare un'istanza dell'applicazione client riservata con un segreto client:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Creare un'istanza dell'applicazione client riservata con il certificato client

Ecco il codice per compilare un'applicazione con un certificato:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

In MSAL. Java sono disponibili due generatori per creare un'istanza dell'applicazione client riservata con certificati:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

Oppure

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Scenario avanzato: creare un'istanza dell'applicazione client riservata con asserzioni client

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Anziché un segreto client o un certificato, l'applicazione client riservata può anche dimostrare la propria identità usando le asserzioni client.

MSAL.NET dispone di due metodi per fornire asserzioni firmate all'app client riservata:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando si usa `WithClientAssertion`, è necessario fornire un JWT con segno. Questo scenario avanzato è dettagliato in [asserzioni client](msal-net-client-assertions.md)

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando si usa `WithClientClaims`, MSAL.NET calcolerà se stessa un'asserzione firmata contenente le attestazioni previste da Azure AD più attestazioni client aggiuntive da inviare.
Ecco un frammento di codice per eseguire questa operazione:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Per informazioni dettagliate, vedere [asserzioni client](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

In MSAL Python è possibile fornire attestazioni client usando le attestazioni che verranno firmate dalla chiave privata della `ConfidentialClientApplication`.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {“client_ip”: “x.x.x.x”}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Per informazioni dettagliate, vedere MSAL. Documentazione di riferimento di Python per [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j è in anteprima pubblica. Le asserzioni firmate non sono ancora supportate.

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
