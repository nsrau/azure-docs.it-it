---
title: Configurare i provider di identità (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Scopri come usare diverse autorità come B2C, cloud sovrani e utenti guest, con MSAL per iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085218"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Procedura: Configurare MSAL per iOS e macOS per l'utilizzo di provider di identità diversiHow to: Configure MSAL for iOS and macOS to use different identity providers

Questo articolo illustra come configurare l'app della libreria di autenticazione Microsoft per iOS e macOS (MSAL) per diverse autorità come Azure Active Directory (Azure AD), Business-to-Consumer (B2C), cloud sovrani e utenti guest.  In questo articolo, è in genere possibile considerare un'autorità come un provider di identità.

## <a name="default-authority-configuration"></a>Configurazione dell'autorità predefinita

`MSALPublicClientApplication`è configurato con un `https://login.microsoftonline.com/common`URL di autorità predefinito di , adatto per la maggior parte degli scenari di Azure Active Directory (AAD). A meno che non si implementano scenari avanzati come i cloud nazionali o si lavora con B2C, non sarà necessario modificarlo.

> [!NOTE]
> L'autenticazione moderna con Active Directory Federation Services come provider di identità (ADFS) non è supportata (per informazioni [dettagliate,](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) vedere ADFS per gli sviluppatori). ADFS è supportato tramite federazione.

## <a name="change-the-default-authority"></a>Modificare l'autorità predefinita

In alcuni scenari, ad esempio business-to-consumer (B2C), potrebbe essere necessario modificare l'autorità predefinita.

### <a name="b2c"></a>B2C

Per utilizzare B2C, la libreria di [autenticazione Microsoft (MSAL)](reference-v2-libraries.md) richiede una configurazione di autorità diversa. MSAL riconosce un formato URL di autorità come B2C da solo. Il formato dell'autorità `https://<host>/tfp/<tenant>/<policy>`B2C `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`riconosciuto è , ad esempio . Tuttavia, è anche possibile utilizzare qualsiasi altro URL di autorità B2C supportato dichiarando l'autorità come autorità B2C in modo esplicito.

Per supportare un formato URL arbitrario per B2C, `MSALB2CAuthority` può essere impostato con un URL arbitrario, come questo:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Tutte le autorità B2C che non utilizzano il formato predefinito dell'autorità B2C devono essere dichiarate come autorità note.

Aggiungere ogni autorità B2C diversa all'elenco delle autorità note anche se le autorità differiscono solo per i criteri.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Quando l'app richiede un nuovo criterio, l'URL dell'autorità deve essere modificato perché l'URL dell'autorità è diverso per ogni criterio. 

Per configurare un'applicazione `@property MSALAuthority *authority` B2C, `MSALB2CAuthority` `MSALPublicClientApplicationConfig` impostare `MSALPublicClientApplication`con un'istanza di in prima della creazione , in questo modo:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Nubi sovrane

Se l'app viene eseguita in un cloud sovrano, potrebbe essere necessario modificare l'URL dell'autorità nel `MSALPublicClientApplication`file . L'esempio seguente imposta l'URL dell'autorità per l'utilizzo con il cloud AAD tedesco:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Potrebbe essere necessario passare ambiti diversi a ogni cloud sovrano. Gli ambiti da inviare dipendono dalla risorsa in uso. Ad esempio, è `"https://graph.microsoft.com/user.read"` possibile utilizzare `"https://graph.microsoft.de/user.read"` nel cloud in tutto il mondo e nel cloud tedesco.

### <a name="signing-a-user-into-a-specific-tenant"></a>Accesso di un utente a un tenant specificoSigning a user into a specific tenant

Quando l'URL dell'autorità è impostato su `"login.microsoftonline.com/common"`, l'utente verrà connesso al tenant principale. Tuttavia, alcune app potrebbero dover firmare l'utente in un tenant diverso e alcune app funzionano solo con un singolo tenant.

Per accedere all'utente in `MSALPublicClientApplication` un tenant specifico, configurare con un'autorità specifica. Ad esempio:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Di seguito viene illustrato come firmare un utente in un tenant specifico:The following shows how to sign a user into a specific tenant:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Autorità supportate

### <a name="msalauthority"></a>MSALAuthority

La `MSALAuthority` classe è la classe astratta di base per le classi di autorità MSAL. Non tentare di crearne `alloc` l'istanza utilizzando o `new`. Al contrario, creare una delle`MSALAADAuthority` `MSALB2CAuthority`relative sottoclassi direttamente `authorityWithURL:error:` ( , ) oppure utilizzare il metodo factory per creare sottoclassi utilizzando un URL di autorità.

Utilizzare `url` la proprietà per ottenere un URL di autorità normalizzato. I parametri aggiuntivi e i componenti di percorso o frammenti che non fanno parte dell'autorità non saranno nell'URL dell'autorità normalizzata restituito.

Di seguito sono `MSALAuthority` riportate le sottoclassi di cui è possibile creare un'istanza a seconda dell'autorità che si desidera utilizzare.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`rappresenta un'autorità AAD. L'URL dell'autorità deve essere `<port>` nel seguente formato, dove è facoltativo:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`rappresenta un'autorità B2C. Per impostazione predefinita, l'URL dell'autorità B2C deve essere nel seguente formato, dove `<port>` è facoltativo: `https://<host>:<port>/tfp/<tenant>/<policy>`. Tuttavia, MSAL supporta anche altri formati di autorità B2C arbitrari.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
