---
title: Inizializzare MSAL.NET applicazioni client . Azure
titleSuffix: Microsoft identity platform
description: Informazioni sull'inizializzazione di applicazioni client e client riservate pubbliche utilizzando la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084004"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inizializzare le applicazioni client utilizzando MSAL.NET
In questo articolo viene descritta l'inizializzazione di applicazioni client e client riservate tramite Microsoft Authentication Library for .NET (MSAL.NET).  Per ulteriori informazioni sui tipi di applicazione client e sulle opzioni di configurazione dell'applicazione, leggere la [panoramica](msal-client-applications.md).

Con MSAL.NET 3.x, il modo consigliato per creare un'istanza di un'applicazione consiste nell'utilizzare i generatori di applicazioni: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder`. Offrono un potente meccanismo per configurare l'applicazione dal codice, o da un file di configurazione, o anche mescolando entrambi gli approcci.

## <a name="prerequisites"></a>Prerequisiti
Prima di inizializzare un'applicazione, devi prima [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft.  Dopo la registrazione, potrebbero essere necessarie le informazioni seguenti (che possono essere trovate nel portale di Azure):After registration, you may need the following information (which can be found in the Azure portal):

- L'ID client (una stringa che rappresenta un GUID)
- L'URL del provider di identità (denominato l'istanza) e il gruppo di destinatari di accesso per l'applicazione. Questi due parametri sono collettivamente noti come l'autorità.
- ID tenant se si scrive un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione single-tenant).
- Il segreto dell'applicazione (stringa del segreto client) o certificato (di tipo X509Certificate2) se si tratta di un'app client riservata.
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), devi anche impostare redirectUri in cui il provider di identità contatterà nuovamente l'applicazione con i token di sicurezza.

## <a name="ways-to-initialize-applications"></a>Modi per inizializzare le applicazioni
Esistono molti modi diversi per creare istanze di applicazioni client.

### <a name="initializing-a-public-client-application-from-code"></a>Inizializzazione di un'applicazione client pubblica dal codice

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti che accedono nel cloud pubblico di Microsoft Azure, con i relativi account aziendali e dell'istituto di istruzione o gli account Microsoft personali.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inizializzazione di un'applicazione client riservata dal codice

Allo stesso modo, il codice seguente crea un'istanza `https://myapp.azurewebsites.net`di un'applicazione riservata (un'app Web situata in ) che gestisce i token degli utenti nel cloud pubblico di Microsoft Azure, con i relativi account aziendali e dell'istituto di istruzione o gli account Microsoft personali. L'applicazione viene identificata con il provider di identità condividendo un segreto client:The application is identified with the identity provider by sharing a client secret:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Come si potrebbe sapere, nell'ambiente di produzione, anziché usare un segreto client, è possibile condividere con Azure AD un certificato. Il codice sarebbe quindi il seguente:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client pubblica dalle opzioni di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che può essere compilato a livello di codice o letto da un file di configurazione:The following code instantiates a public client application from a configuration object, which could be filled-in programmatically or read from a configuration file:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client riservata dalle opzioni di configurazione

Lo stesso tipo di modello si applica alle applicazioni client riservate. È anche possibile aggiungere `.WithXXX` altri parametri utilizzando i modificatori (in questo caso un certificato).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificatori del generatore

Nei frammenti di codice che utilizzano `.With` i generatori di applicazioni, `.WithCertificate` è `.WithRedirectUri`possibile applicare diversi metodi come modificatori, ad esempio e . 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificatori comuni alle applicazioni client pubbliche e riservate

I modificatori che è possibile impostare in un client pubblico o in un generatore di applicazioni client riservate sono:The modifiers you can set on a public client or confidential client application builder are:

|Modificatore | Descrizione|
|--------- | --------- |
|`.WithAuthority()`7 sostituzioni | Imposta l'autorità predefinita dell'applicazione su un'autorità di Azure AD, con la possibilità di scegliere il cloud di Azure, il gruppo di destinatari, il tenant (ID tenant o nome di dominio) o fornire direttamente l'URI dell'autorità.|
|`.WithAdfsAuthority(string)` | Imposta l'autorità predefinita dell'applicazione come un'autorità ADFS.|
|`.WithB2CAuthority(string)` | Imposta l'autorità predefinita dell'applicazione come autorità B2C di Azure AD.|
|`.WithClientId(string)` | Esegue l'override dell'ID client.|
|`.WithComponent(string)` | Imposta il nome della libreria utilizzando MSAL.NET (per motivi di telemetria). |
|`.WithDebugLoggingCallback()` | Se viene chiamato, `Debug.Write` l'applicazione chiamerà semplicemente abilitando le tracce di debug. Per altre informazioni, vedere [Registrazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging).|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Impostare i parametri di query aggiuntivi a livello di applicazione che verranno inviati in tutte le richieste di autenticazione. Questo è sottoponibile a override a `.WithExtraQueryParameters pattern`livello di metodo di acquisizione di token (con lo stesso ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Consente scenari avanzati come la configurazione per un proxy HTTP o l'obbligo per MSAL di utilizzare un particolare HttpClient (ad esempio in ASP.NET app Web/API di base).|
|`.WithLogging()` | Se viene chiamato, l'applicazione chiamerà un callback con le tracce di debug. Per altre informazioni, vedere [Registrazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging).|
|`.WithRedirectUri(string redirectUri)` | Esegue l'override dell'URI di reindirizzamento predefinito. Nel caso di applicazioni client pubbliche, questo sarà utile per gli scenari che coinvolgono il broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Imposta il delegato utilizzato per inviare dati di telemetria.|
|`.WithTenantId(string tenantId)` | Sostituisce l'ID tenant o la descrizione del tenant.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificatori specifici delle applicazioni Xamarin.iOS

I modificatori che è possibile impostare in un generatore di applicazioni client pubblico in Xamarin.iOS sono:The modifiers you can set on a public client application builder on Xamarin.iOS are:

|Modificatore | Descrizione|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Solo Xamarin.iOS**: Imposta il gruppo di sicurezza della catena di chiavi iOS (per la persistenza della cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificatori specifici delle applicazioni client riservate

I modificatori che è possibile impostare in un generatore di applicazioni client riservate sono:The modifiers you can set on a confidential client application builder are:

|Modificatore | Descrizione|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Imposta il certificato che identifica l'applicazione con Azure AD.|
|`.WithClientSecret(string clientSecret)` | Imposta il segreto client (password dell'app) che identifica l'applicazione con Azure AD.|

Questi modificatori si escludono a vicenda. Se si specificano entrambi, MSAL genererà un'eccezione significativa.

### <a name="example-of-usage-of-modifiers"></a>Esempio di utilizzo dei modificatori

Si supponga che l'applicazione sia un'applicazione line-of-business, che è solo per l'organizzazione.  Quindi puoi scrivere:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Laddove diventa interessante è che la programmazione per i cloud nazionali è ora semplificata. Se si desidera che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, è possibile scrivere, ad esempio:If you want your application to be a multi-tenant application in a national cloud, you could write, for esempio:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Esiste anche una sostituzione per ADFS (ADFS 2019 non è attualmente supportato):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si è uno sviluppatore B2C di Azure AD, è possibile specificare il tenant in questo modo:Finally, if you are an Azure AD B2C developer, you can specify your tenant like this:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
