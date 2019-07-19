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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277831"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App daemon che chiama API Web-configurazione del codice

Informazioni su come configurare il codice per l'applicazione daemon che chiama le API Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Librerie MSAL che supportano le app daemon

Le librerie Microsoft che supportano le app daemon sono:

  Libreria MSAL | DESCRIZIONE
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Le piattaforme supportate per compilare un'applicazione daemon sono .NET Framework e le piattaforme .NET Core (non UWP, Novell. iOS e Novell. Android perché queste piattaforme vengono usate per compilare applicazioni client pubbliche)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Sviluppo in corso-anteprima pubblica
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Sviluppo in corso-anteprima pubblica

## <a name="configuration-of-the-authority"></a>Configurazione dell'autorità

Dato che le applicazioni daemon non usano autorizzazioni delegate, ma le autorizzazioni dell'applicazione, il *tipo di account supportato* non può essere *account in alcuna directory organizzativa e account Microsoft personali (ad esempio, Skype, Xbox, Outlook.com)* . In realtà, non esiste alcun amministratore tenant per concedere il consenso all'applicazione daemon per gli account personali Microsoft. È necessario scegliere gli *account nell'organizzazione* o gli *account di qualsiasi organizzazione*.

Pertanto, l'autorità specificata nella configurazione dell'applicazione deve essere tenant-ed (specificando un ID tenant o un nome di dominio associato all'organizzazione).

Se si è un ISV e si vuole fornire uno strumento multi-tenant, è possibile usare `organizations`. Tenere tuttavia presente che è anche necessario spiegare ai clienti come concedere il consenso dell'amministratore. Per informazioni dettagliate, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Esiste attualmente una limitazione in MSAL che `organizations` è consentita solo quando le credenziali client sono un segreto dell'applicazione (non un certificato). Vedere [#891 di bug MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Configurazione e creazione di istanze dell'applicazione

Nelle librerie MSAL le credenziali client (Secret o certificate) vengono passate come parametro della costruzione di applicazioni client riservate.

> [!IMPORTANT]
> Anche se l'applicazione è un'applicazione console in esecuzione come servizio, se si tratta di un'applicazione daemon è necessario che sia un'applicazione client riservata.

### <a name="msalnet"></a>MSAL.NET

Aggiungere il pacchetto NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) all'applicazione.

Usa spazio dei nomi MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

L'applicazione daemon verrà visualizzata da un`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Ecco il codice per compilare un'applicazione con un segreto dell'applicazione:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Ecco il codice per compilare un'applicazione con un certificato:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Infine, anziché un segreto client o un certificato, l'applicazione client riservata può anche dimostrare la propria identità usando le asserzioni client. Questo scenario avanzato è dettagliato in [asserzioni client](msal-net-client-assertions.md)


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon-acquisizione di token per l'app](./scenario-daemon-acquire-token.md)
