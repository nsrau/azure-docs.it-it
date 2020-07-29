---
title: Configurare le app daemon che chiamano API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come configurare il codice per l'applicazione daemon che chiama le API Web (configurazione dell'app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 921015d6aa7acd840a4a231a899217daafe3525b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558558"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App daemon che chiama API Web-configurazione del codice

Informazioni su come configurare il codice per l'applicazione daemon che chiama le API Web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Librerie MSAL che supportano le app daemon

Queste librerie Microsoft supportano le app daemon:

  Libreria MSAL | Descrizione
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme .NET Framework e .NET Core sono supportate per la creazione di applicazioni daemon. (UWP, Novell. iOS e Novell. Android non sono supportati perché queste piattaforme vengono usate per compilare applicazioni client pubbliche).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Supporto per le applicazioni daemon in Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Supporto per le applicazioni daemon in Java.

## <a name="configure-the-authority"></a>Configurare l'autorità

Le applicazioni daemon utilizzano le autorizzazioni dell'applicazione anziché le autorizzazioni delegate. Il tipo di account supportato, quindi, non può essere un account in qualsiasi directory aziendale o qualsiasi account Microsoft personale (ad esempio, Skype, Xbox, Outlook.com). Non è disponibile alcun amministratore tenant per concedere il consenso a un'applicazione daemon per un account personale Microsoft. È necessario scegliere gli *account nell'organizzazione* o gli *account di qualsiasi organizzazione*.

Quindi, l'autorità specificata nella configurazione dell'applicazione deve essere tenant (specificando un ID tenant o un nome di dominio associato all'organizzazione).

Se si è un ISV e si vuole fornire uno strumento multi-tenant, è possibile usare `organizations` . Tenere tuttavia presente che è anche necessario spiegare ai clienti come concedere il consenso dell'amministratore. Per informazioni dettagliate, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Inoltre, esiste attualmente una limitazione in MSAL: `organizations` è consentito solo quando le credenziali client sono un segreto dell'applicazione (non un certificato).

## <a name="configure-and-instantiate-the-application"></a>Configurare e creare un'istanza dell'applicazione

Nelle librerie MSAL le credenziali client (Secret o certificate) vengono passate come parametro della costruzione di applicazioni client riservate.

> [!IMPORTANT]
> Anche se l'applicazione è un'applicazione console che viene eseguita come servizio, se si tratta di un'applicazione daemon, deve essere un'applicazione client riservata.

### <a name="configuration-file"></a>File di configurazione

Il file di configurazione definisce:

- L'autorità o l'istanza cloud e l'ID tenant.
- ID client ottenuto dalla registrazione dell'applicazione.
- Un segreto client o un certificato.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.js](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) dall'esempio [daemon console di .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

È possibile specificare un oggetto `ClientSecret` o un oggetto `CertificateName` . Queste impostazioni sono esclusive.

# <a name="python"></a>[Python](#tab/python)

Quando si compila un client riservato con i segreti client, il [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) nel file di configurazione nell'esempio [Python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) è il seguente:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Quando si compila un client riservato con certificati, il [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) nel file di configurazione nell'esempio [Python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) è il seguente:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Creare un'istanza dell'applicazione MSAL

Per creare un'istanza dell'applicazione MSAL, è necessario aggiungere, fare riferimento o importare il pacchetto MSAL (a seconda del linguaggio).

La costruzione è diversa, a seconda che si utilizzino i segreti o i certificati client (o, come scenario avanzato, le asserzioni firmate).

#### <a name="reference-the-package"></a>Fare riferimento al pacchetto

Fare riferimento al pacchetto MSAL nel codice dell'applicazione.

# <a name="net"></a>[.NET](#tab/dotnet)

Aggiungere il pacchetto NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) all'applicazione.
In MSAL.NET, l'applicazione client riservata è rappresentata dall' `IConfidentialClientApplication` interfaccia.
Usare lo spazio dei nomi MSAL.NET nel codice sorgente.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Creare un'istanza dell'applicazione client riservata con un segreto client

Ecco il codice per creare un'istanza dell'applicazione client riservata con un segreto client:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Creare un'istanza dell'applicazione client riservata con un certificato client

Ecco il codice per compilare un'applicazione con un certificato:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

In MSAL Java sono disponibili due generatori per creare un'istanza dell'applicazione client riservata con certificati:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

o

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Scenario avanzato: creare un'istanza dell'applicazione client riservata con asserzioni client

# <a name="net"></a>[.NET](#tab/dotnet)

Anziché un segreto client o un certificato, l'applicazione client riservata può anche dimostrare la propria identità usando asserzioni client.

MSAL.NET dispone di due metodi per fornire asserzioni firmate all'app client riservata:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando si usa `WithClientAssertion` , è necessario specificare un JWT con segno. Questo scenario avanzato è descritto in dettaglio nelle [asserzioni client](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando si usa `WithClientClaims` , MSAL.NET produrrà un'asserzione firmata che contiene le attestazioni previste da Azure ad, oltre ad altre attestazioni client che si desidera inviare.
Questo codice illustra come eseguire questa operazione:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Per informazioni dettagliate, vedere [asserzioni client](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

In MSAL Python è possibile fornire attestazioni client usando le attestazioni che verranno firmate da questa `ConfidentialClientApplication` chiave privata.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Per informazioni dettagliate, vedere la documentazione di riferimento di MSAL Python per [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
