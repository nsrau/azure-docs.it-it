---
title: Richiesta di attestazioni personalizzate (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Scopri come richiedere attestazioni personalizzate.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883097"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Procedura: richiedere attestazioni personalizzate usando MSAL per iOS e macOSHow to: Request custom claims using MSAL for iOS and macOS

OpenID Connect consente di richiedere facoltativamente la restituzione di singole attestazioni dall'endpoint UserInfo e/o nel token ID. Una richiesta di attestazioni è rappresentata come un oggetto JSON che contiene un elenco di attestazioni richieste. Vedere [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) per ulteriori dettagli.

Microsoft Authentication Library (MSAL) per iOS e macOS consente di richiedere attestazioni specifiche in scenari di acquisizione di token interattivi e silenziosi. Lo fa attraverso `claimsRequest` il parametro.

Esistono più scenari in cui questo è necessario. Ad esempio:

- Richiesta di attestazioni al di fuori del set standard per l'applicazione.
- Richiesta di combinazioni specifiche delle attestazioni standard che non possono essere specificate utilizzando gli ambiti per l'applicazione. Ad esempio, se un token di accesso viene rifiutato a causa di attestazioni mancanti, l'applicazione può richiedere le attestazioni mancanti utilizzando MSAL.

> [!NOTE]
> MSAL ignora la cache dei token di accesso ogni volta che viene specificata una richiesta di attestazioni. È importante fornire `claimsRequest` il parametro solo quando sono necessarie attestazioni `claimsRequest` aggiuntive (anziché fornire sempre lo stesso parametro in ogni chiamata all'API MSAL).

`claimsRequest`può essere `MSALSilentTokenParameters` specificato `MSALInteractiveTokenParameters`in e :

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`può essere costruito da una rappresentazione NSString della richiesta di attestazioni JSON. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Può anche essere modificato richiedendo ulteriori attestazioni specifiche:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`deve quindi essere impostato nei parametri del token e fornito a una delle API di acquisizione di token MSAL:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
