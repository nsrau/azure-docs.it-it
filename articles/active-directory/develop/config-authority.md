---
title: Configurare MSAL per iOS e macOS per l'uso di provider di identità diversi | Piattaforma di identità Microsoft
description: Informazioni su come usare autorità diverse, ad esempio B2C, cloud sovrani e utenti guest, con MSAL per iOS e macOS.
services: active-directory
documentationcenter: ''
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114e67e2dca7ba304cb92b21a894e045cbe0c9e9
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269090"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Procedura: Configurare MSAL per iOS e macOS per l'uso di provider di identità diversi

In questo articolo viene illustrato come configurare l'app Microsoft Authentication Library per iOS e macOS (MSAL) per diverse autorità, ad esempio Azure Active Directory (Azure AD), business-to-consumer (B2C), cloud sovrani e utenti guest.  In questo articolo, in genere è possibile considerare un'autorità come un provider di identità.

## <a name="default-authority-configuration"></a>Configurazione dell'autorità predefinita

`MSALPublicClientApplication`viene configurato con un URL di autorità predefinito `https://login.microsoftonline.com/common`, adatto per la maggior parte degli scenari di Azure Active Directory (AAD). A meno che non si stiano implementando scenari avanzati, ad esempio i cloud nazionali, o lavorando con B2C, non è necessario modificarlo.

> [!NOTE]
> L'autenticazione moderna con Active Directory Federation Services come provider di identità (ADFS) non è supportata. per informazioni dettagliate, vedere [ADFS per sviluppatori](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers) . ADFS è supportato tramite la Federazione.

## <a name="change-the-default-authority"></a>Modificare l'autorità predefinita

In alcuni scenari, ad esempio business-to-consumer (B2C), potrebbe essere necessario modificare l'autorità predefinita.

### <a name="b2c"></a>B2C

Per lavorare con B2C, [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) richiede una configurazione di autorità diversa. MSAL riconosce il formato di un URL di autorità come B2C. Il formato dell'autorità B2C riconosciuto `https://<host>/tfp/<tenant>/<policy>`è ad esempio `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Tuttavia, è anche possibile usare qualsiasi altro URL di autorità B2C supportato dichiarando l'autorità come autorità B2C in modo esplicito.

Per supportare un formato URL arbitrario per B2C, `MSALB2CAuthority` è possibile impostare un URL arbitrario, come segue:

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

Tutte le autorità B2C che non usano il formato predefinito dell'autorità B2C devono essere dichiarate come autorità note.

Aggiungere ogni autorità B2C diversa all'elenco di autorità note anche se le autorità sono diverse solo nei criteri.

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

Per configurare un'applicazione B2C, impostare `@property MSALAuthority *authority` con un'istanza di `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` prima della `MSALPublicClientApplication`creazione, come segue:

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

### <a name="sovereign-clouds"></a>Cloud sovrani

Se l'app viene eseguita in un cloud sovrano, potrebbe essere necessario modificare l'URL dell'autorità `MSALPublicClientApplication`nella. L'esempio seguente imposta l'URL dell'autorità per l'uso con il cloud AAD tedesco:

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

Potrebbe essere necessario passare ambiti diversi a ogni cloud sovrano. Gli ambiti da inviare dipendono dalla risorsa che si sta usando. Ad esempio, è possibile usare `"https://graph.microsoft.com/user.read"` nel cloud in tutto il `"https://graph.microsoft.de/user.read"` mondo e nel cloud tedesco.

### <a name="signing-a-user-into-a-specific-tenant"></a>Firma di un utente in un tenant specifico

Quando l'URL dell'autorità è impostato `"login.microsoftonline.com/common"`su, l'utente verrà connesso al tenant Home. Tuttavia, alcune app possono richiedere l'accesso dell'utente in un tenant diverso e alcune app funzionano solo con un singolo tenant.

Per accedere all'utente in un tenant specifico, configurare `MSALPublicClientApplication` con un'autorità specifica. Esempio:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Di seguito viene illustrato come firmare un utente in un tenant specifico:

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

La `MSALAuthority` classe è la classe astratta di base per le classi dell'autorità MSAL. Non tentare di crearne un'istanza usando `alloc` o `new`. In alternativa, creare una delle relative sottoclassi direttamente (`MSALAADAuthority`, `MSALB2CAuthority`) o usare il metodo `authorityWithURL:error:` Factory per creare sottoclassi usando un URL dell'autorità.

Utilizzare la `url` proprietà per ottenere un URL di autorità normalizzato. I parametri e i frammenti di percorso aggiuntivi che non fanno parte dell'autorità non saranno inclusi nell'URL dell'autorità normalizzata restituita.

Di seguito sono riportate le `MSALAuthority` sottoclassi di che è possibile creare un'istanza a seconda dell'autorità che desidera utilizzare.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`rappresenta un'autorità AAD. L'URL dell'autorità deve avere il formato seguente, dove `<port>` è facoltativo:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`rappresenta un'autorità B2C. Per impostazione predefinita, l'URL dell'autorità B2C deve avere il formato seguente, `<port>` dove è facoltativo `https://<host>:<port>/tfp/<tenant>/<policy>`:. Tuttavia, MSAL supporta anche altri formati di autorità B2C arbitrari.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [flussi di autenticazione e sugli scenari di applicazione](authentication-flows-app-scenarios.md)
