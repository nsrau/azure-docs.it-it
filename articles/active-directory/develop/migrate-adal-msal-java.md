---
title: Guida alla migrazione da ADAL a MSAL (MSAL4j) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione dell'app Java di Azure Active Directory Authentication Library (ADAL) a Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7ba845e79074313f0ccf2c066ba016bd72d46efe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534568"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guida alla migrazione da ADAL a MSAL per Java

Questo articolo evidenzia le modifiche che è necessario apportare per eseguire la migrazione di un'app che usa Azure Active Directory Authentication Library (ADAL) per usare microsoft Authentication Library (MSAL).

Sia Microsoft Authentication Library per Java (MSAL4J) che Azure AD Authentication Library per Java (ADAL4J) vengono usati per autenticare le entità di Azure AD e i token di richiesta da Azure AD. Fino ad ora, la maggior parte degli sviluppatori ha lavorato con la piattaforma Azure AD per sviluppatori (v1.0) per autenticare le identità di Azure AD (account aziendali e dell'istituto di istruzione) richiedendo token tramite Azure AD Authentication Library (ADAL).

MSAL offre i seguenti vantaggi:

- Poiché usa l'endpoint della piattaforma di identità Microsoft più recente, è possibile autenticare un set più ampio di identità Microsoft, ad esempio identità di Azure AD, account Microsoft e account social e locali tramite Azure AD Business to Consumer (B2C).
- Gli utenti avranno la migliore esperienza Single Sign-On.
- L'applicazione può abilitare il consenso incrementale e il supporto dell'accesso condizionale è più semplice.

MSAL per Java è la libreria di autenticazione che si consiglia di utilizzare con la piattaforma di identità Microsoft. Non verranno implementate nuove funzionalità su ADAL4J. Tutti gli sforzi in corso sono focalizzati sul miglioramento msAL.

## <a name="differences"></a>Differenze

Se si è lavorato con l'endpoint di Azure AD per sviluppatori (v1.0) (e ADAL4J), è possibile leggere [What's different about the Microsoft identity platform (v2.0)endpoint?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Ambiti e non risorse

ADAL4J acquisisce token per le risorse, mentre MSAL per Java acquisisce i token per gli ambiti. Un numero di MSAL per le classi Java richiedono un parametro scopes. Questo parametro è un elenco di stringhe che dichiarano le autorizzazioni e le risorse desiderate richieste. Vedere [gli ambiti di Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) per visualizzare gli ambiti di esempio.

## <a name="core-classes"></a>Classi di base

In ADAL4J, `AuthenticationContext` la classe rappresenta la connessione al servizio token di sicurezza (STS, Security Token Service) o al server di autorizzazione, tramite un'autorità. Tuttavia, MSAL per Java è progettato intorno alle applicazioni client. Fornisce due classi `PublicClientApplication` separate: e `ConfidentialClientApplication` per rappresentare le applicazioni client.  Quest'ultimo, `ConfidentialClientApplication`, rappresenta un'applicazione progettata per mantenere in modo sicuro un segreto, ad esempio un identificatore dell'applicazione per un'applicazione daemon.

La tabella seguente mostra come le funzioni ADAL4J eseguono il mapping al nuovo MSAL per le funzioni Java:

| Metodo ADAL4J| MSAL4J (metodo)|
|------|-------|
|acquireToken(risorsa stringa, credenziale ClientCredential, callback AuthenticationCallback) | acquireToken(ClientCredentialParameters)|
|acquireToken(risorsa Stringa, asserzione ClientAssertion, callback AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(risorsa stringa, credenziale AsymmetricKeyCredential, callback AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(risorsa stringa, stringa clientId, nome utente stringa, password stringa, callback AuthenticationCallback)| acquireToken(UsernamePasswordParameters)|
|acquireToken(risorsa stringa, stringa identità, nome utente stringa, stringa password-null, callback di AuthenticationCallback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(risorsa Stringa, UserAssertion userAssertion, credenziale ClientCredential, callback AuthenticationCallback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() e acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount anziché IUser

ADAL4J ha manipolato gli utenti. Anche se un utente rappresenta un singolo utente o agente software, può avere uno o più account nel sistema di identità Microsoft. Ad esempio, un utente può avere diversi account personali di Azure AD, Azure AD B2C o Microsoft.For example, a user may have several Azure AD, Azure AD B2C, or Microsoft personal accounts.

MSAL per Java definisce il `IAccount` concetto di Account tramite l'interfaccia. Si tratta di una modifica sostanziale rispetto ad ADAL4J, ma è una buona idea perché acquisisce il fatto che lo stesso utente può avere più account e forse anche in diverse directory di Azure AD. MSAL per Java fornisce informazioni migliori negli scenari guest perché vengono fornite informazioni sull'account domestico.

## <a name="cache-persistence"></a>Persistenza della cache

ADAL4J non disponeva del supporto per la cache dei token.
MSAL per Java aggiunge una cache dei [token](msal-acquire-cache-tokens.md) per semplificare la gestione della durata dei token aggiornando automaticamente i token scaduti quando possibile e impedendo all'utente di fornire le credenziali quando possibile.

## <a name="common-authority"></a>Autorità comune

Nella versione 1.0, se `https://login.microsoftonline.com/common` si usa l'autorità, gli utenti possono accedere con qualsiasi account Azure Active Directory (AAD) (per qualsiasi organizzazione).

Se si `https://login.microsoftonline.com/common` utilizza l'autorità nella versione 2.0, gli utenti possono accedere con qualsiasi organizzazione AAD o anche con un account personale Microsoft (MSA). In MSAL per Java, se si desidera limitare l'accesso `https://login.microsoftonline.com/organizations` a qualsiasi account AAD, è necessario utilizzare l'autorità (che è lo stesso comportamento di ADAL4J). Per specificare un'autorità, impostare il `authority` parametro nel metodo [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) quando si crea la `PublicClientApplication` classe.

## <a name="v10-and-v20-tokens"></a>Token v1.0 e v2.0

L'endpoint v1.0 (usato da ADAL) genera solo token v1.0.

L'endpoint v2.0 (utilizzato da MSAL) può generare token v1.0 e v2.0. Una proprietà del manifesto dell'applicazione dell'API Web consente agli sviluppatori di scegliere quale versione del token viene accettata. Vedere `accessTokenAcceptedVersion` nella documentazione di riferimento del [manifesto dell'applicazione.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Per altre informazioni sui token v1.0 e v2.0, vedere Token di accesso di Azure Active Directory.For more information about v1.0 and v2.0 [tokens,](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)see Azure Active Directory access tokens .

## <a name="adal-to-msal-migration"></a>Migrazione da ADAL a MSAL

In ADAL4J, i token di aggiornamento sono stati esposti, il che ha permesso agli sviluppatori di memorizzarli nella cache. Vengono quindi `AcquireTokenByRefreshToken()` utilizzate per abilitare soluzioni quali l'implementazione di servizi a esecuzione prolungata che aggiornano i dashboard per conto dell'utente quando l'utente non è più connesso.

MSAL per Java non espone i token di aggiornamento per motivi di sicurezza. Al contrario, MSAL gestisce automaticamente i token di aggiornamento.

MSAL per Java dispone di un'API che consente di eseguire la migrazione dei token di aggiornamento acquisiti con ADAL4j in ClientApplication: [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Con questo metodo, è possibile fornire il token di aggiornamento usato in precedenza insieme a tutti gli ambiti (risorse) desiderati. Il token di aggiornamento verrà scambiato con uno nuovo e memorizzato nella cache per l'utilizzo da parte dell'applicazione.

Il frammento di codice seguente mostra del codice di migrazione in un'applicazione client riservata:The following code snippet shows some migration code in a confidential client application:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Restituisce `IAuthenticationResult` un token di accesso e un token ID, mentre il nuovo token di aggiornamento viene archiviato nella cache.
L'applicazione conterrà anche un IAccount:The application will also now contain an IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Per utilizzare i token che si trovano ora nella cache, chiamare:To use the tokens that are now in the cache, call:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
