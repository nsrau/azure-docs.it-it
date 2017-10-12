---
title: 'Azure Active Directory B2C: gestire la personalizzazione dei token e SSO con i criteri personalizzati| Microsoft Docs'
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: gestire la personalizzazione dei token e SSO con i criteri personalizzati
L'uso dei criteri personalizzati offre lo stesso controllo sulle configurazioni di token, sessioni e Single Sign-On (SSO) dei criteri predefiniti.  Per informazioni sulle singole impostazioni, vedere la documentazione [qui](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configurazione delle attestazioni e delle durate dei token
Per modificare le impostazioni delle durate dei token, è necessario aggiungere un elemento `<ClaimsProviders>` nel file di relying party del criterio su cui si vuole intervenire.  `<ClaimsProviders>` è un elemento figlio di `<TrustFrameworkPolicy>`.  Sarà necessario inserirvi le informazioni che interessano le durate dei token.  Il codice XML è simile al seguente:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Durate dei token di accesso** La durata dei token di accesso può essere cambiata modificando il valore nell'elemento `<Item>` con Key="token_lifetime_secs" espresso in secondi.  Il valore predefinito è pari a 3600 secondi (60 minuti).

**Durata dei token di ID** La durata dei token di ID può essere cambiata modificando il valore nell'elemento `<Item>` con Key="id_token_lifetime_secs" espresso in secondi.  Il valore predefinito è pari a 3600 secondi (60 minuti).

**Durata del token di aggiornamento** La durata del token di aggiornamento può essere cambiata modificando il valore nell'elemento `<Item>` con Key="refresh_token_lifetime_secs" espresso in secondi.  Il valore predefinito è pari a 1209600 secondi (14 giorni).

**Durata della finestra temporale scorrevole del token di aggiornamento** Per impostare una durata della finestra temporale scorrevole per il token di aggiornamento, modificare il valore nell'elemento `<Item>` con Key="rolling_refresh_token_lifetime_secs" espresso in secondi.  Il valore predefinito è pari a 7776000 secondi (90 giorni).  Se non si vuole applicare una durata della finestra temporale scorrevole, sostituire questa riga con:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Attestazione autorità di certificazione (iss)** Per cambiare l'attestazione autorità di certificazione (iss), modificare il valore nell'elemento `<Item>` con Key="IssuanceClaimPattern".  I valori applicabili sono `AuthorityAndTenantGuid` e `AuthorityWithTfp`.

**Impostazione dell'attestazione che rappresenta l'ID criteri** Le opzioni per impostare questo valore sono TFP (Trust Framework Policy) e ACR (Authentication Context Reference).  
È consigliabile impostarlo su TFP. A questo scopo, assicurarsi che l'elemento `<Item>` con Key="AuthenticationContextReferenceClaimPattern" esista e il valore sia `None`.
Nell'elemento `<OutputClaims>`, aggiungere questo elemento:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Per ACR, rimuovere l'elemento `<Item>` con Key="AuthenticationContextReferenceClaimPattern".

**Attestazione soggetto (sub)** L'impostazione predefinita di questa opzione è ObjectID. Per impostarla su `Not Supported`, seguire questa procedura:

Sostituire questa riga 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
con la riga seguente:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportamento della sessione e SSO
Per modificare il comportamento della sessione e le configurazioni SSO, è necessario aggiungere un elemento `<UserJourneyBehaviors>` nell'elemento `<RelyingParty>`.  L'elemento `<UserJourneyBehaviors>` deve seguire immediatamente `<DefaultUserJourney>`.  Il contenuto dell'elemento `<UserJourneyBehavors>` sarà il seguente:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configurazione dell'accesso Single Sign-On** Per cambiare la configurazione dell'accesso Single Sign-On, è necessario modificare il valore di `<SingleSignOn>`.  I valori applicabili sono `Tenant`, `Application` `Policy` e `Disabled`. 

**Durata della sessione dell'app Web (minuti)** Per cambiare la durata della sessione dell'app Web, è necessario modificare il valore dell'elemento `<SessionExpiryInSeconds>`.  Il valore predefinito nei criteri predefiniti è pari a 86400 secondi (1440 minuti).

**Timeout della sessione dell'app Web** Per cambiare il timeout della sessione dell'app Web, è necessario modificare il valore di `<SessionExpiryType>`.  I valori applicabili sono `Absolute` e `Rolling`.
