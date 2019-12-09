---
title: Guida alla migrazione da ADAL a MSAL per Java | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione dell'app java ADAL (Azure Active Directory Authentication Library) a Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e99cee3f21a4e0088fa97dcbec8fdcfdf982d80
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917387"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guida alla migrazione da ADAL a MSAL per Java

Questo articolo evidenzia le modifiche che è necessario eseguire per eseguire la migrazione di un'app che usa la libreria di autenticazione Azure Active Directory (ADAL) per usare Microsoft Authentication Library (MSAL).

Microsoft Authentication Library per Java (MSAL4J) e Autenticazione di Azure AD Library per Java (ADAL4J) vengono usati per autenticare Azure AD entità e i token di richiesta da Azure AD. Fino ad ora, la maggior parte degli sviluppatori ha collaborato con Azure AD per sviluppatori Platform (v 1.0) per autenticare le identità di Azure AD (account aziendali e dell'Istituto di istruzione) richiedendo token tramite la libreria Autenticazione di Azure AD (ADAL).

MSAL offre i vantaggi seguenti:

- Poiché usa l'endpoint della piattaforma di identità Microsoft più recente, è possibile autenticare un set più ampio di identità Microsoft, ad esempio Azure AD identità, account Microsoft e account social e locali tramite Azure AD business to consumer (B2C).
- Gli utenti otterranno la migliore esperienza Single Sign-on.
- L'applicazione può abilitare il consenso incrementale e il supporto dell'accesso condizionale è più semplice.

MSAL per Java è la libreria di autenticazione che è consigliabile usare con la piattaforma di identità Microsoft. In ADAL4J non verranno implementate nuove funzionalità. Tutti gli sforzi in futuro si concentrano sul miglioramento di MSAL.

## <a name="differences"></a>Differenze

Se si è lavorato con l'endpoint Azure AD for Developers (v 1.0) (e ADAL4J), potrebbe essere necessario leggere [le differenze relative all'endpoint della piattaforma Microsoft Identity (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Ambiti e non risorse

ADAL4J acquisisce i token per le risorse, mentre MSAL per Java acquisisce i token per gli ambiti. Un numero di MSAL per le classi Java richiede un parametro Scopes. Questo parametro è un elenco di stringhe che dichiarano le autorizzazioni e le risorse desiderate richieste. Vedere [ambiti di Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) per visualizzare gli ambiti di esempio.

## <a name="core-classes"></a>Classi di base

In ADAL4J la classe `AuthenticationContext` rappresenta la connessione al servizio token di sicurezza (STS) o al server di autorizzazione tramite un'autorità. Tuttavia, MSAL per Java è progettato per le applicazioni client. Fornisce due classi separate: `PublicClientApplication` e `ConfidentialClientApplication` per rappresentare le applicazioni client.  Il secondo, `ConfidentialClientApplication`, rappresenta un'applicazione progettata per gestire in modo sicuro un segreto, ad esempio un identificatore dell'applicazione per un'app daemon.

La tabella seguente illustra il mapping delle funzioni ADAL4J alla nuova MSAL per le funzioni Java:

| Metodo ADAL4J| Metodo MSAL4J|
|------|-------|
|acquireToken (stringa Resource, ClientCredential Credential, AuthenticationCallback callback) | acquireToken (ClientCredentialParameters)|
|acquireToken (stringa Resource, ClientAssertion Assertion, AuthenticationCallback callback)|acquireToken (ClientCredentialParameters)|
|acquireToken (stringa Resource, AsymmetricKeyCredential Credential, AuthenticationCallback callback)|acquireToken (ClientCredentialParameters)|
|acquireToken (stringa Resource, stringa ClientID, stringa nomeutente, stringa password, callback AuthenticationCallback)| acquireToken (UsernamePasswordParameters)|
|acquireToken (stringa Resource, stringa ClientID, stringa nomeutente, stringa password = null, callback AuthenticationCallback)|acquireToken (IntegratedWindowsAuthenticationParameters)|
|acquireToken (stringa Resource, UserAssertion userAssertion, ClientCredential Credential, AuthenticationCallback callback)| acquireToken (OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode () | acquireToken (AuthorizationCodeParameters) |
| acquireDeviceCode () e acquireTokenByDeviceCode ()| acquireToken (DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount anziché IUser

ADAL4J utenti modificati. Anche se un utente rappresenta un singolo agente software o umano, può avere uno o più account nel sistema di identità Microsoft. Ad esempio, un utente può disporre di più account Azure AD, Azure AD B2C o personali Microsoft.

MSAL per Java definisce il concetto di account tramite l'interfaccia `IAccount`. Si tratta di una modifica sostanziale rispetto a ADAL4J, ma è una scelta efficace, perché acquisisce il fatto che lo stesso utente può avere più account e forse anche in directory Azure AD diverse. MSAL per Java fornisce informazioni migliori negli scenari Guest perché vengono fornite informazioni sull'account Home.

## <a name="cache-persistence"></a>Persistenza della cache

ADAL4J non dispone del supporto per la cache dei token.
MSAL per Java aggiunge una [cache di token](msal-acquire-cache-tokens.md) per semplificare la gestione delle durate dei token aggiornando automaticamente i token scaduti quando possibile e evitando richieste non necessarie all'utente di fornire le credenziali quando possibile.

## <a name="common-authority"></a>Autorità comune

In v 1.0, se si usa l'autorità di `https://login.microsoftonline.com/common`, gli utenti possono accedere con qualsiasi account di Azure Active Directory (AAD) (per qualsiasi organizzazione).

Se si usa l'autorità `https://login.microsoftonline.com/common` nella versione 2.0, gli utenti possono accedere con qualsiasi organizzazione AAD o anche un account Microsoft personale (MSA). In MSAL per Java, se si vuole limitare l'accesso a qualsiasi account di AAD, è necessario usare l'autorità `https://login.microsoftonline.com/organizations` (che è lo stesso comportamento di ADAL4J). Per specificare un'autorità, impostare il parametro `authority` nel metodo [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) quando si crea la classe `PublicClientApplication`.

## <a name="v10-and-v20-tokens"></a>Token v1.0 e v2.0

L'endpoint v1.0 (usato da ADAL) genera solo token v1.0.

L'endpoint v 2.0 (usato da MSAL) può creare token v 1.0 e v 2.0. Una proprietà del manifesto dell'applicazione dell'API Web consente agli sviluppatori di scegliere quale versione del token viene accettata. Vedere `accessTokenAcceptedVersion` nella documentazione di riferimento del [manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) .

Per ulteriori informazioni sui token v 1.0 e v 2.0, vedere [Azure Active Directory token di accesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Migrazione da ADAL a MSAL

In ADAL4J sono stati esposti i token di aggiornamento, che hanno consentito agli sviluppatori di memorizzarli nella cache. Utilizzeranno quindi `AcquireTokenByRefreshToken()` per abilitare soluzioni quali l'implementazione di servizi con esecuzione prolungata che aggiornano i dashboard per conto dell'utente quando l'utente non è più connesso.

MSAL per Java non espone i token di aggiornamento per motivi di sicurezza. Al contrario, MSAL gestisce i token di aggiornamento automaticamente.

MSAL per Java include un'API che consente di migrare i token di aggiornamento acquisiti con ADAL4j in ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Con questo metodo, è possibile fornire il token di aggiornamento usato in precedenza insieme a tutti gli ambiti (risorse) desiderati. Il token di aggiornamento verrà scambiato per uno nuovo e memorizzato nella cache per l'uso da parte dell'applicazione.

Il frammento di codice seguente mostra un codice di migrazione in un'applicazione client riservata:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Il `IAuthenticationResult` restituisce un token di accesso e un token ID, mentre il nuovo token di aggiornamento viene archiviato nella cache. L'applicazione conterrà ora anche un IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Per usare i token attualmente presenti nella cache, chiamare:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
