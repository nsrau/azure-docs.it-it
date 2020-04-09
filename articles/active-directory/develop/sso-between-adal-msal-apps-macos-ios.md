---
title: SSO tra le app ADAL & MSAL (iOS/macOS) - Piattaforma di identità Microsoft Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880751"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Procedura: SSO tra le app ADAL e MSAL su macOS e iOS

Microsoft Authentication Library (MSAL) per iOS può condividere lo stato SSO con [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) tra le applicazioni. Puoi eseguire la migrazione delle tue app a MSAL al tuo ritmo, assicurandoti che gli utenti possano comunque beneficiare di SSO tra app, anche con una combinazione di app basate su ADAL e MSAL.

Per informazioni sulla configurazione di SSO tra app con MSAL SDK, vedere [SSO silenzioso tra più app.](single-sign-on-macos-ios.md#silent-sso-between-apps) Questo articolo è incentrato su SSO tra ADAL e MSAL.

Le specifiche di implementazione SSO dipendono dalla versione ADAL in uso.

## <a name="adal-27x"></a>ADAL 2.7.x

Questa sezione illustra le differenze SSO tra MSAL e ADAL 2.7.x

### <a name="cache-format"></a>Formato cache

ADAL 2.7.x può leggere il formato della cache MSAL. Non è necessario fare nulla di speciale per cross-app SSO con la versione ADAL 2.7.x. Tuttavia, è necessario essere consapevoli delle differenze negli identificatori di account che supportano queste due librerie.

### <a name="account-identifier-differences"></a>Differenze dell'identificatore dell'account

MSAL e ADAL utilizzano identificatori di account diversi. ADAL utilizza UPN come identificatore di account principale. MSAL utilizza un identificatore di account non visualizzabile basato su un ID `sub` oggetto e un ID tenant per gli account AAD e una richiesta per altri tipi di account.

Quando si `MSALAccount` riceve un oggetto nel risultato MSAL, `identifier` contiene un identificatore di account nella proprietà. L'applicazione deve utilizzare questo identificatore per le richieste invisibile all'utente successive.

Oltre a `identifier` `MSALAccount` , l'oggetto contiene `username`un identificatore visualizzabile denominato . Che si `userId` traduce in ADAL. `username`non è considerato un identificatore univoco e può essere modificato in qualsiasi momento, pertanto deve essere utilizzato solo per scenari di compatibilità con le versioni precedenti con ADAL. MSAL supporta le query `username` `identifier`della cache utilizzando `identifier` o , in cui è consigliabile eseguire query in base a .

Nella tabella seguente sono riepilogate le differenze di identificatore di account tra ADAL e MSAL:

| Identificatore account                | MSAL                                                         | ADAL 2.7.x      | ADAL più vecchio (prima di ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificatore visualizzabile            | `username`                                                   | `userId`        | `userId`                       |
| identificatore univoco non visualizzabile | `identifier`                                                 | `homeAccountId` | N/D                            |
| Nessun id account noto               | Interroga relè tutti gli account tramite `allAccounts:` API in`MSALPublicClientApplication` | N/D             | N/D                            |

Questa è `MSALAccount` l'interfaccia che fornisce tali identificatori:This is the interface providing those identifiers:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO da MSAL ad ADAL

Se si dispone di un'app MSAL e di un'app ADAL e l'utente accede innanzitutto all'app `MSALAccount` basata su MSAL, è possibile `userId`ottenere SSO nell'app ADAL salvando l'oggetto `username` dall'oggetto e passandolo all'app basata su ADAL come . ADAL può quindi trovare le informazioni `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` sull'account in modo invisibile all'utente con l'API.

### <a name="sso-from-adal-to-msal"></a>SSO da ADAL a MSAL

Se si dispone di un'app MSAL e di un'app ADAL e l'utente accede per la prima volta all'app basata su ADAL, è possibile usare gli identificatori utente ADAL per le ricerche di account in MSAL. Questo vale anche quando si esegue la migrazione da ADAL a MSAL.

#### <a name="adals-homeaccountid"></a>homeAccountId di ADAL

ADAL 2.7.x `homeAccountId` restituisce `ADUserInformation` l'oggetto nell'oggetto nel risultato tramite questa proprietà:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`in ADAL è equivalente `identifier` a in MSAL. È possibile salvare questo identificatore da utilizzare in `accountForIdentifier:error:` MSAL per le ricerche di account con l'API.

#### <a name="adals-userid"></a>ADAL`userId`

Se `homeAccountId` non è disponibile o si dispone solo dell'identificatore `userId` visualizzabile, è possibile utilizzare ADAL per cercare l'account in MSAL.

In MSAL, prima cercare `username` un `identifier`account da o . Utilizzare `identifier` sempre per l'esecuzione di query `username` se è disponibile e utilizzare solo come fallback. Se l'account viene trovato, `acquireTokenSilent` utilizzare l'account nelle chiamate.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



API di ricerca account supportate da MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

In questa sezione vengono illustrate le differenze SSO tra MSAL e ADAL 2.x-2.6.6.

Le versioni ADAL meno recenti non supportano in modo nativo il formato della cache MSAL. Tuttavia, per garantire una migrazione senza problemi da ADAL a MSAL, MSAL può leggere il formato della cache ADAL precedente senza richiedere nuovamente le credenziali utente.

Poiché `homeAccountId` non è disponibile nelle versioni precedenti di ADAL, `username`è necessario cercare gli account utilizzando:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Ad esempio:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



In alternativa, è possibile leggere tutti gli account, che leggeranno anche le informazioni sull'account da ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
