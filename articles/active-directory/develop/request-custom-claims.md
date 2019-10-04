---
title: Come richiedere attestazioni personalizzate usando MSAL per iOS e macOS | Piattaforma di identità Microsoft
description: Informazioni su come richiedere attestazioni personalizzate.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e09d58742bffd74f07f79b3ec55c1e81533632
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268986"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Procedura: Richiedere attestazioni personalizzate usando MSAL per iOS e macOS

OpenID Connect consente di richiedere facoltativamente la restituzione di singole attestazioni dall'endpoint UserInfo e/o nel token ID. Una richiesta di attestazioni viene rappresentata come un oggetto JSON che contiene un elenco di attestazioni richieste. Per altri dettagli, vedere [OpenID Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) .

Microsoft Authentication Library (MSAL) per iOS e macOS consente di richiedere attestazioni specifiche negli scenari di acquisizione di token interattivi e invisibili. Questa operazione viene eseguita tramite `claimsRequest` il parametro.

Sono disponibili più scenari in cui è necessario. Esempio:

- Richiesta di attestazioni al di fuori del set standard per l'applicazione.
- Richiesta di combinazioni specifiche delle attestazioni standard che non possono essere specificate usando gli ambiti per l'applicazione. Se ad esempio un token di accesso viene rifiutato a causa di attestazioni mancanti, l'applicazione può richiedere le attestazioni mancanti usando MSAL.

> [!NOTE]
> MSAL ignora la cache dei token di accesso ogni volta che viene specificata una richiesta di attestazione. È importante specificare `claimsRequest` solo il parametro quando sono necessarie attestazioni aggiuntive (invece di fornire sempre lo stesso `claimsRequest` parametro in ogni chiamata all'API MSAL).

`claimsRequest`può essere specificato in `MSALSilentTokenParameters` e `MSALInteractiveTokenParameters`:

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

Swift

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Può anche essere modificato richiedendo attestazioni specifiche aggiuntive:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift

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

Swift

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [flussi di autenticazione e sugli scenari di applicazione](authentication-flows-app-scenarios.md)
