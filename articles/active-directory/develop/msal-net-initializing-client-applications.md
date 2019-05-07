---
title: Inizializzare le applicazioni client (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sull'inizializzazione client pubblico e le applicazioni client riservato usando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6197d472bdfaf03c9f99baa7691354e735cc91e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075806"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inizializzare le applicazioni client tramite MSAL.NET
Questo articolo descrive l'inizializzazione client pubblico e le applicazioni client riservato usando Microsoft Authentication Library per .NET (MSAL.NET).  Per altre informazioni sui tipi di applicazioni client e le opzioni di configurazione dell'applicazione, vedere la [Panoramica](msal-client-applications.md).

Con MSAL.NET 3.x, il metodo consigliato per creare un'istanza di un'applicazione consiste nell'usare i generatori di applicazione: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder`. Queste connessioni offrono un meccanismo efficace per configurare l'applicazione dal codice, o da un file di configurazione o anche di combinazione di entrambi gli approcci.

## <a name="prerequisites"></a>Prerequisiti
Prima di inizializzare un'applicazione, è necessario innanzitutto [registrarlo](quickstart-register-app.md) in modo che l'app può essere integrato con la piattaforma delle identità Microsoft.  Dopo la registrazione, potrebbe essere necessario le informazioni seguenti (che sono reperibile nel portale di Azure):

- L'ID client (una stringa che rappresenta un GUID)
- URL di provider di identità (l'istanza denominata) e i destinatari di accesso per l'applicazione. Questi due parametri sono noti collettivamente come autorità.
- L'ID tenant se si sta scrivendo un'applicazione line of business esclusivamente per l'organizzazione (anche denominati applicazione a tenant singolo).
- Il segreto dell'applicazione (stringa segreto client) o il certificato (di tipo X509Certificate2) se si tratta di un'app client riservato.
- Per le app web e talvolta per le app client pubblico (in particolare quando l'app deve usare un broker), verranno inoltre impostate l'URI di reindirizzamento in cui il provider di identità contatterà back dell'applicazione con i token di sicurezza.

## <a name="ways-to-initialize-applications"></a>Metodi per l'inizializzazione delle applicazioni
Esistono diversi modi per creare un'istanza di applicazioni client.

### <a name="initializing-a-public-client-application-from-code"></a>Inizializzazione di un'applicazione client pubblica dal codice

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti di accesso nel cloud pubblico di Microsoft Azure, con il proprio lavoro e dell'istituto di istruzione o l'account Microsoft personale.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inizializzazione di un'applicazione client riservata dal codice

Nello stesso modo, il codice seguente crea un'istanza di un'applicazione riservata (un'app Web disponibile all'indirizzo `https://myapp.azurewebsites.net`) gestisce i token dagli utenti nel cloud pubblico di Microsoft Azure, con il proprio lavoro e dell'istituto di istruzione o l'account Microsoft personale. L'applicazione viene identificata con il provider di identità tramite la condivisione di un segreto client:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Come saprete che nell'ambiente di produzione, anziché tramite un segreto client, si potrebbe voler condividere con Azure AD un certificato. Il codice quindi sarebbe il seguente:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client pubblica tra opzioni di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che potrebbe essere compilato in a livello di codice o letto da un file di configurazione:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client riservata tra opzioni di configurazione

Lo stesso tipo di modello si applica alle applicazioni client riservato. È anche possibile aggiungere altri parametri utilizzando `.WithXXX` modificatori (un certificato in questo caso).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificatori generatore

Nei frammenti di codice usando i generatori di applicazione, numerosi `.With` possono essere applicati metodi come modificatori (ad esempio, `.WithCertificate` e `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificatori comuni nelle applicazioni client pubblici e riservate

I modificatori che è possibile impostare per un client pubblico o un compilatore di applicazioni client riservati sono:

|Parametro | DESCRIZIONE|
|--------- | --------- |
|`.WithAuthority()` 7 sostituzioni | Imposta l'autorità predefinito dell'applicazione da un'autorità di Azure AD, con la possibilità di scelta del Cloud di Azure, i destinatari, il tenant (tenant ID o il dominio nome), o che forniscono direttamente l'URI dell'autorità.|
|`.WithAdfsAuthority(string)` | Imposta l'autorità predefinito dell'applicazione da un'autorità di ad FS.|
|`.WithB2CAuthority(string)` | Imposta l'autorità predefinito dell'applicazione da un'autorità di Azure AD B2C.|
|`.WithClientId(string)` | Sostituisce l'ID client.|
|`.WithComponent(string)` | Imposta il nome della libreria tramite MSAL.NET (per motivi di dati di telemetria). |
|`.WithDebugLoggingCallback()` | Se viene chiamato, l'applicazione chiamerà `Debug.Write` semplicemente abilitare le tracce di debug. Visualizzare [Logging](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) per altre informazioni.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Impostare i parametri di query aggiuntiva a livello di applicazione che verranno inviati in tutte le richieste di autenticazione. Ciò è sottoponibile a override in ogni livello di metodo di acquisizione dei token (con lo stesso `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Abilita scenari come la configurazione per un proxy HTTP o per forzare MSAL usare HttpClient specifico (ad esempio in ASP.NET Core web App/API) avanzati.|
|`.WithLogging()` | Se viene chiamato, l'applicazione chiamerà un callback con le tracce di debug. Visualizzare [Logging](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) per altre informazioni.|
|`.WithRedirectUri(string redirectUri)` | Sostituisce l'URI di reindirizzamento predefiniti. Nel caso di applicazioni client pubblico, questo sarà utile per gli scenari che coinvolgono il broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Imposta il delegato utilizzato per inviare dati di telemetria.|
|`.WithTenantId(string tenantId)` | Sostituisce l'ID tenant o la descrizione del tenant.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificatori specifici per le applicazioni xamarin. IOS

I modificatori che è possibile impostare per un compilatore di applicazioni client pubblico in xamarin. IOS sono:

|Parametro | DESCRIZIONE|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin. IOS solo**: Imposta il gruppo di sicurezza di iOS Keychain (per il salvataggio permanente della cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificatori specifici per le applicazioni client riservato

I modificatori che è possibile impostare per un compilatore di applicazioni client riservati sono:

|Parametro | DESCRIZIONE|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Imposta il certificato che identifica l'applicazione con Azure AD.|
|`.WithClientSecret(string clientSecret)` | Imposta il segreto client (password dell'app) che identifica l'applicazione con Azure AD.|

Questi modificatori si escludono a vicenda. Se si specificano entrambi, MSAL genererà un'eccezione significativa.

### <a name="example-of-usage-of-modifiers"></a>Esempio di utilizzo di modificatori

Si supponga che l'applicazione è un'applicazione line-of-business, che è solo per l'organizzazione.  È quindi possibile scrivere:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

In cui diventa interessante è che la programmazione per i cloud nazionali è ora semplificata. Se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, è possibile scrivere, ad esempio:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

È inoltre disponibile una sostituzione per ad FS (ad FS 2019 attualmente non è supportato):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se sei uno sviluppatore di Azure AD B2C, è possibile specificare il tenant simile al seguente:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
