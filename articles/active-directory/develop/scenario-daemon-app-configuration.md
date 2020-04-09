---
title: Configurare le app daemon che chiamano le API Web - Piattaforma di identità Microsoft Azure
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
ms.custom: aaddev
ms.openlocfilehash: bb2cc90e586f71bb7c90efd62ba3705cd9223bc5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885498"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App Daemon che chiama API Web - configurazione del codice

Informazioni su come configurare il codice per l'applicazione daemon che chiama le API Web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Librerie MSAL che supportano le app daemon

Queste librerie Microsoft supportano le app daemon:

  Libreria MSAL | Descrizione
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme .NET Framework e .NET Core sono supportate per la creazione di applicazioni daemon. (UWP, Xamarin.iOS e Xamarin.Android non sono supportati perché tali piattaforme vengono utilizzate per compilare applicazioni client pubbliche.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Supporto per applicazioni daemon in Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Supporto per applicazioni daemon in Java.

## <a name="configure-the-authority"></a>Configurare l'autorità

Le applicazioni Daemon utilizzano le autorizzazioni dell'applicazione anziché le autorizzazioni delegate. Pertanto, il tipo di account supportato non può essere un account in alcuna directory aziendale o account Microsoft personale (ad esempio, Skype, Xbox Outlook.com). Non è disponibile alcun amministratore tenant per concedere il consenso a un'applicazione daemon per un account personale Microsoft.There's no tenant admin to grant consent to a daemon application for a Microsoft personal account. È necessario scegliere *gli account nell'organizzazione* o *gli account in qualsiasi organizzazione.*

Pertanto, l'autorità specificata nella configurazione dell'applicazione deve essere sottoposta a tenant (specificando un ID tenant o un nome di dominio associato all'organizzazione).

Se sei un ISV e vuoi fornire uno strumento `organizations`multi-tenant, puoi utilizzare . Ma tieni presente che dovrai anche spiegare ai tuoi clienti come concedere il consenso dell'amministratore. Per informazioni dettagliate, vedere [Richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Inoltre, esiste attualmente una limitazione `organizations` in MSAL: è consentito solo quando le credenziali client sono un segreto dell'applicazione (non un certificato).

## <a name="configure-and-instantiate-the-application"></a>Configurare e creare un'istanza dell'applicazione

Nelle librerie MSAL, le credenziali client (segreto o certificato) vengono passate come parametro della costruzione dell'applicazione client riservata.

> [!IMPORTANT]
> Anche se l'applicazione è un'applicazione console che viene eseguita come servizio, se si tratta di un'applicazione daemon, deve essere un'applicazione client riservata.

### <a name="configuration-file"></a>File di configurazione

Il file di configurazione definisce:

- L'autorità o l'istanza cloud e l'ID tenant.
- ID client ottenuto dalla registrazione dell'applicazione.
- Un segreto client o un certificato.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) dall'esempio di [daemon della console .NET Core.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Si fornisce `ClientSecret` un `CertificateName`oggetto . Queste impostazioni sono esclusive.

# <a name="python"></a>[Python](#tab/python)

Quando si compila un client riservato con segreti client, il file di configurazione [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) nell'esempio [di daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) è il seguente:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Quando si compila un client riservato con certificati, il file di configurazione [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) nell'esempio [di daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) è il seguente:

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

Per creare un'istanza dell'applicazione MSAL, è necessario aggiungere, fare riferimento o importare il pacchetto MSAL (a seconda della lingua).

La costruzione è diversa, a seconda che si utilizzino segreti client o certificati (o, come scenario avanzato, asserzioni firmate).

#### <a name="reference-the-package"></a>Riferimento al pacchetto

Fare riferimento al pacchetto MSAL nel codice dell'applicazione.

# <a name="net"></a>[.NET](#tab/dotnet)

Aggiungere il pacchetto [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet all'applicazione.
In MSAL.NET, l'applicazione client `IConfidentialClientApplication` riservata è rappresentata dall'interfaccia.
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

Ecco il codice per creare un'istanza dell'applicazione client riservata con un segreto client:Here's the code to instantiate the confidential client application with a client secret:

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

Ecco il codice per compilare un'applicazione con un certificato:Here's the code to build an application with a certificate:

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

In MSAL Java, sono disponibili due generatori per creare un'istanza dell'applicazione client riservata con certificati:

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

Invece di un segreto client o di un certificato, l'applicazione client riservata può anche dimostrare la propria identità utilizzando asserzioni client.

MSAL.NET dispone di due metodi per fornire asserzioni firmate all'app client riservata:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando si `WithClientAssertion`utilizza , è necessario fornire un token JWT firmato. Questo scenario avanzato è descritto in [Asserzioni](msal-net-client-assertions.md)client .

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando si `WithClientClaims`usa , MSAL.NET produrrà un'asserzione firmata contenente le attestazioni previste da Azure AD, oltre a attestazioni client aggiuntive che si desidera inviare.
Questo codice mostra come eseguire questa operazione:This code shows how to do that:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Anche in questo caso, per informazioni dettagliate, vedere [Asserzioni client](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

In MSAL Python, è possibile fornire attestazioni client utilizzando `ConfidentialClientApplication`le attestazioni che verranno firmate da questa chiave privata.

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
> [App Daemon - acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App Daemon - acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App Daemon - acquisizione di token per l'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
