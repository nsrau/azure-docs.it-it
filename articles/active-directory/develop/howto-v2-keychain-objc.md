---
title: Configurare il keychain
titleSuffix: Microsoft identity platform
description: Scopri come configurare il portachiavi in modo che l'app possa memorizzare nella cache i token nel portachiavi.
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
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085857"
---
# <a name="configure-keychain"></a>Configurare il keychain

Quando [Microsoft Authentication Library per iOS e macOS (MSAL)](msal-overview.md) firma in un utente o aggiorna un token, tenta di memorizzare nella cache i token nel portachiavi. La memorizzazione nella cache dei token nel portachiavi consente a MSAL di fornire l'accesso Single Sign-On (SSO) invisibile all'utente tra più app distribuite dallo stesso sviluppatore Apple. SSO si ottiene tramite la funzionalità di gruppi di accesso portachiavi. Per ulteriori informazioni, consulta la documentazione Apple [Keychain Items](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc).

Questo articolo illustra come configurare i permessi per le app in modo che MSAL possa scrivere token memorizzati nella cache nel portachiavi iOS e macOS.

## <a name="default-keychain-access-group"></a>Gruppo di accesso al portachiavi predefinito

### <a name="ios"></a>iOS

MSAL su iOS `com.microsoft.adalcache` utilizza il gruppo di accesso per impostazione predefinita. Si tratta del gruppo di accesso condiviso usato dagli SDK MSAL e Azure AD Authentication Library (ADAL) e garantisce la migliore esperienza Single Sign-On (SSO) tra più app dello stesso autore.

In iOS, `com.microsoft.adalcache` aggiungi il gruppo portachiavi al diritto dell'app in XCode in **Impostazioni** > progetto**Funzionalità** > **condivisione portachiavi**

### <a name="macos"></a>macOS

MSAL su macOS utilizza `com.microsoft.identity.universalstorage` il gruppo di accesso per impostazione predefinita.

A causa delle limitazioni del portachiavi `access group` di macOS, MSAL non si traduce direttamente nell'attributo del gruppo di accesso al portachiavi (vedere [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) su macOS 10.14 e versioni precedenti. Tuttavia, si comporta in modo simile dal punto di vista SSO assicurando che più applicazioni distribuite dallo stesso sviluppatore Apple possono avere SSO silenzioso.

Su macOS 10.15 in poi (macOS Catalina), MSAL utilizza l'attributo del gruppo di accesso al portachiavi per ottenere SSO silenzioso, in modo simile a iOS.

## <a name="custom-keychain-access-group"></a>Gruppo di accesso portachiavi personalizzato

Se si desidera utilizzare un gruppo di accesso portachiavi diverso, `MSALPublicClientApplicationConfig` è `MSALPublicClientApplication`possibile passare il gruppo personalizzato durante la creazione prima della creazione , in questo modo:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Disabilitare la condivisione del portachiavi

Se non vuoi condividere lo stato SSO tra più app o utilizzare qualsiasi gruppo di accesso al portachiavi, disabilita la condivisione del portachiavi passando l'ID del pacchetto dell'applicazione come keychainGroup:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Errore handle -34018 (impossibile impostare l'elemento nel portachiavi)

Errore -34018 normalmente significa che il portachiavi non è stato configurato correttamente. Assicurarsi che il gruppo di accesso portachiavi configurato in MSAL corrisponda a quello configurato nei permessi.

## <a name="ensure-your-application-is-properly-signed"></a>Assicurarsi che l'applicazione sia firmata correttamente

In macOS, le applicazioni possono essere eseguite senza essere firmate dallo sviluppatore. Mentre la maggior parte delle funzionalità di MSAL continuerà a funzionare, SSO tramite l'accesso portachiavi richiede l'applicazione per essere firmato. Se vengono visualizzati più prompt del portachiavi, verificare che la firma dell'applicazione sia valida.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui gruppi di accesso al portachiavi nell'articolo [Condivisione dell'accesso agli elementi del portachiavi di](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) Apple tra una raccolta di app.
