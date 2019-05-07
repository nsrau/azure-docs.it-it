---
title: Daemon app chiamare le API web (configurazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione daemon che chiama web API (configurazione dell'app)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075326"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App daemon che chiama l'API - configurazione del codice web

Informazioni su come configurare il codice per l'applicazione daemon che chiama l'API web.

## <a name="msal-libraries-supporting-daemon-apps"></a>App daemon supporta librerie MSAL

Le librerie di Microsoft che supportano le app daemon sono:

  Libreria MSAL | DESCRIZIONE
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Piattaforme supportate per compilare un'applicazione daemon sono le piattaforme .NET Framework e .NET Core (non UWP, xamarin. IOS e xamarin. Android come tali piattaforme vengono utilizzati per compilare applicazioni client pubblico)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Sviluppo in corso - disponibile in anteprima pubblica
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Sviluppo in corso - disponibile in anteprima pubblica

## <a name="configuration-of-the-authority"></a>Configurazione dell'autorità

Dato che le applicazioni daemon non utilizzare autorizzazioni delegate, ma le autorizzazioni dell'applicazione, loro *tipo di account supportato* non può essere *personale Microsoft e account in qualsiasi directory dell'organizzazione account ( ad esempio, Skype, Xbox, Outlook.com)*. In effetti, non vi è alcun amministratore del tenant di concedere il consenso all'applicazione daemon per gli account personali Microsoft. È necessario scegliere *gli account dell'organizzazione* oppure *gli account in qualsiasi organizzazione*.

Pertanto l'autorità specificata nella configurazione dell'applicazione deve essere tenant-ed (specificando un ID Tenant o un nome di dominio associato all'organizzazione). Se sei un fornitore di software indipendente e si desidera fornire uno strumento di multi-tenant, è possibile usare `organizations`. Tuttavia, tenere presente che è necessario anche per spiegare ai clienti di concedere il consenso dell'amministratore. Visualizzare [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) per informazioni dettagliate

## <a name="application-configuration-and-instantiation"></a>Creazione di un'istanza e configurazione dell'applicazione

Nelle librerie MSAL, le credenziali del client (certificato o segreto) vengono passate come parametro della costruzione dell'applicazione client riservato.

> [!IMPORTANT]
> Anche se l'applicazione è un'applicazione console in esecuzione come servizio, se si tratta di un'applicazione daemon deve essere un'applicazione client riservata.

### <a name="msalnet"></a>MSAL.NET

Aggiungere il [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) pacchetto NuGet per l'applicazione.

Usare lo spazio dei nomi MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

L'applicazione daemon verrà presentata da un `IConfidentialClientApplication`

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
> [App daemon: l'acquisizione dei token per l'app](./scenario-daemon-acquire-token.md)