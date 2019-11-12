---
title: Inizializzare le applicazioni client (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
description: Informazioni sull'inizializzazione di applicazioni client pubbliche e riservate con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8cf7b7004097ef5a4d915d8fdff60cc9606c5be
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927069"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inizializzare applicazioni client usando MSAL.NET
Questo articolo descrive l'inizializzazione di applicazioni client pubbliche e riservate con Microsoft Authentication Library per .NET (MSAL.NET).  Per altre informazioni sui tipi di applicazioni client e sulle opzioni di configurazione dell'applicazione, vedere la [Panoramica](msal-client-applications.md).

Con MSAL.NET 3. x, la modalità consigliata per creare un'istanza di un'applicazione consiste nell'usare i generatori di applicazioni: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder`. Offrono un meccanismo potente per configurare l'applicazione dal codice, da un file di configurazione o persino combinando entrambi gli approcci.

## <a name="prerequisites"></a>prerequisiti
Prima di inizializzare un'applicazione, è prima necessario [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft.  Dopo la registrazione, è possibile che siano necessarie le informazioni seguenti (disponibili nella portale di Azure):

- ID client (una stringa che rappresenta un GUID)
- URL del provider di identità (denominato istanza) e destinatari dell'accesso per l'applicazione. Questi due parametri sono collettivamente noti come autorità.
- ID tenant se si sta scrivendo un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione a tenant singolo).
- Il segreto dell'applicazione (stringa del segreto client) o il certificato (di tipo X509Certificate2) se si tratta di un'app client riservata.
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), è anche possibile impostare il redirectUri in cui il provider di identità contatterà l'applicazione con i token di sicurezza.

## <a name="ways-to-initialize-applications"></a>Modalità di inizializzazione delle applicazioni
Esistono molti modi diversi per creare un'istanza delle applicazioni client.

### <a name="initializing-a-public-client-application-from-code"></a>Inizializzazione di un'applicazione client pubblica dal codice

Il codice seguente crea un'istanza di un'applicazione client pubblica, gli utenti che accedono all'Microsoft Azure cloud pubblico, con gli account aziendali e dell'Istituto di istruzione o con gli account Microsoft personali.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inizializzazione di un'applicazione client riservata dal codice

Analogamente, il codice seguente crea un'istanza di un'applicazione riservata, ovvero un'app Web situata in `https://myapp.azurewebsites.net`, che gestisce i token degli utenti nel cloud Microsoft Azure pubblico, con gli account aziendali e dell'Istituto di istruzione o con gli account Microsoft personali. L'applicazione viene identificata con il provider di identità condividendo un segreto client:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Come si può capire, in produzione, anziché usare un segreto client, è possibile condividere con Azure AD un certificato. Il codice sarà quindi il seguente:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client pubblica dalle opzioni di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che può essere compilato a livello di codice o letto da un file di configurazione:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inizializzazione di un'applicazione client riservata dalle opzioni di configurazione

Lo stesso tipo di modello si applica alle applicazioni client riservate. È anche possibile aggiungere altri parametri usando `.WithXXX` modificatori (in questo caso un certificato).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificatori di generatore

Nei frammenti di codice che usano i generatori di applicazioni è possibile applicare un numero di metodi di `.With` come modificatori (ad esempio, `.WithCertificate` e `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificatori comuni a applicazioni client pubbliche e riservate

I modificatori che è possibile impostare in un client pubblico o in un generatore di applicazioni client riservate sono:

|Modificatore | DESCRIZIONE|
|--------- | --------- |
|override di `.WithAuthority()` 7 | Imposta l'autorità predefinita dell'applicazione su un'autorità di Azure AD, con la possibilità di scegliere il cloud di Azure, i destinatari, il tenant (ID tenant o nome di dominio) o fornire direttamente l'URI dell'autorità.|
|`.WithAdfsAuthority(string)` | Imposta l'autorità predefinita dell'applicazione in modo che sia un'autorità ADFS.|
|`.WithB2CAuthority(string)` | Imposta l'autorità predefinita dell'applicazione come Azure AD B2C autorità.|
|`.WithClientId(string)` | Esegue l'override dell'ID client.|
|`.WithComponent(string)` | Imposta il nome della libreria utilizzando MSAL.NET (per motivi di telemetria). |
|`.WithDebugLoggingCallback()` | Se viene chiamato, l'applicazione chiamerà `Debug.Write` semplicemente abilitando le tracce di debug. Per ulteriori informazioni, vedere [registrazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Imposta i parametri di query aggiuntivi a livello di applicazione che verranno inviati in tutte le richieste di autenticazione. Questa operazione viene sottoponibile a override a ogni livello di metodo di acquisizione di token (con lo stesso `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Abilita scenari avanzati, ad esempio la configurazione di un proxy HTTP, oppure per forzare l'uso di un determinato HttpClient (ad esempio in ASP.NET Core app/API Web).|
|`.WithLogging()` | Se chiamato, l'applicazione chiamerà un callback con tracce di debug. Per ulteriori informazioni, vedere [registrazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithRedirectUri(string redirectUri)` | Esegue l'override dell'URI di reindirizzamento predefinito. Nel caso di applicazioni client pubbliche, questo sarà utile per gli scenari che coinvolgono il broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Imposta il delegato usato per inviare i dati di telemetria.|
|`.WithTenantId(string tenantId)` | Esegue l'override dell'ID tenant o della descrizione del tenant.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificatori specifici per le applicazioni Novell. iOS

I modificatori che è possibile impostare in un generatore di applicazioni client pubbliche in Novell. iOS sono:

|Modificatore | DESCRIZIONE|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Solo Novell. iOS**: imposta il gruppo di sicurezza della catena di chiavi iOS (per la persistenza della cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificatori specifici delle applicazioni client riservate

I modificatori che è possibile impostare in un generatore di applicazioni client riservate sono:

|Modificatore | DESCRIZIONE|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Imposta il certificato che identifica l'applicazione con Azure AD.|
|`.WithClientSecret(string clientSecret)` | Imposta il segreto client (password dell'app) che identifica l'applicazione con Azure AD.|

Questi modificatori si escludono a vicenda. Se si specificano entrambi, MSAL genererà un'eccezione significativa.

### <a name="example-of-usage-of-modifiers"></a>Esempio di utilizzo dei modificatori

Si supponga che l'applicazione sia un'applicazione line-of-business, che è solo per la propria organizzazione.  Quindi è possibile scrivere:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Il punto in cui diventa interessante è che la programmazione per i cloud nazionali è stata semplificata. Se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, è possibile scrivere, ad esempio:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

È disponibile anche una sostituzione per ADFS (ADFS 2019 non è attualmente supportata):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si è uno sviluppatore di Azure AD B2C, è possibile specificare il tenant come segue:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
